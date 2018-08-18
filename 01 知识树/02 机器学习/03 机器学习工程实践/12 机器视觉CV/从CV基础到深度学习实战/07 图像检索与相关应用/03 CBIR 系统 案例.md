

TODO

- 这个 VisualSearchServer、DeepVideoAnalytics 一定要看下。

# CBIR 系统

刚才我们说了 CBIR 这个系统用 ANN 怎么去做一个快速的检索。

现在我们看一下，能不能搭出来一个简易的 CBIR 系统。

大概的效果是这样的：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180814/JchK60CgJE.png?imageslim)

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180814/eb60j3m1A8.png?imageslim)

要求给定一张图片，我们能得到这样的几张图片。

康奈尔有一个开源的 CBIR 系统

https://github.com/AKSHAYUBHAT/VisualSearchServer
上面这个不维护了，最新的是下面这个：
https://github.com/akshayubhat/DeepVideoAnalytics


这个 VisualSearchServer 是一个图像检索的 server ，他把一整套的 server 代码都写好了，包括后台的特征的抽取，前台的直接的先计算特征然后匹配的过程。以及前台的界面 html 也写好了。

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180814/BcKIFBLdFA.png?imageslim)

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180814/3h0LEc5dg1.png?imageslim)

可以看到对于每张图片，他会检索回来 12 张最接近的图片，可能因为他图片的数据集没有那么类似的，所以找回来这么一些。

最核心的代码走在 inception.py 里面：


```python

import time,glob,re,sys,logging,os,tempfile
import numpy as np
import tensorflow as tf   # 用了 tensorflow
from scipy import spatial #
from settings import AWS,INDEX_PATH,CONFIG_PATH,DATA_PATH
try:
    from settings import DEMO
except ImportError:
    DEMO = None
    pass
from tensorflow.python.platform import gfile
from nearpy import Engine  # 这个nearpy 就是我们之前讲的近似最近邻的一个库 老师认为 FLANN 会更好用一些。
from nearpy.hashes import RandomBinaryProjections   #同样是把 vector 映射成一个 01 串。
logging.basicConfig(level=logging.INFO,
                    format='%(asctime)s %(name)-12s %(levelname)-8s %(message)s',
                    datefmt='%m-%d %H:%M',
                    filename='logs/worker.log',
                    filemode='a')

DIMENSIONS = 2048
PROJECTIONBITS = 16
# 启动了这么一个 引擎，这个引擎是用来做近似最近邻检索的。你会发现他有 26 bit 的串，也有 2611 bit 的串，也有 261 bit的串。这个取决于你的样本量，如果你的样本量大，你可以多切几刀。
ENGINE = Engine(DIMENSIONS, lshashes=[RandomBinaryProjections('rbp', PROJECTIONBITS,rand_seed=2611),
                                      RandomBinaryProjections('rbp', PROJECTIONBITS,rand_seed=261),
                                      RandomBinaryProjections('rbp', PROJECTIONBITS,rand_seed=26)])




class NodeLookup(object):
    def __init__(self):
        label_lookup_path = CONFIG_PATH+'/data/imagenet_2012_challenge_label_map_proto.pbtxt'
        uid_lookup_path = CONFIG_PATH+'/data/imagenet_synset_to_human_label_map.txt'
        self.node_lookup = self.load(label_lookup_path, uid_lookup_path)

    # 这个 load 把该准备的准备好。
    def load(self, label_lookup_path, uid_lookup_path):
        proto_as_ascii_lines = gfile.GFile(uid_lookup_path).readlines()
        uid_to_human = {}
        p = re.compile(r'[n\d]*[ \S,]*')
        for line in proto_as_ascii_lines:
            parsed_items = p.findall(line)
            uid = parsed_items[0]
            human_string = parsed_items[2]
            uid_to_human[uid] = human_string
        node_id_to_uid = {}
        proto_as_ascii = gfile.GFile(label_lookup_path).readlines()
        for line in proto_as_ascii:
            if line.startswith('  target_class:'):
                target_class = int(line.split(': ')[1])
            if line.startswith('  target_class_string:'):
                target_class_string = line.split(': ')[1]
                node_id_to_uid[target_class] = target_class_string[1:-2]
        node_id_to_name = {}
        for key, val in node_id_to_uid.items():
            if val not in uid_to_human:
                tf.logging.fatal('Failed to locate: %s', val)
            name = uid_to_human[val]
            node_id_to_name[key] = name
        return node_id_to_name

    def id_to_string(self, node_id):
        if node_id not in self.node_lookup:
            return ''
        return self.node_lookup[node_id]


# 神经网络是要先搭建起来的，所以这个就是把一个 tf 的神经网络搭起来，一会有用户请求过来的时候我用这个神经网络去抽取特征。这个地方是把 pb 文件直接 load 进来。
def load_network(png=False):
    with gfile.FastGFile(CONFIG_PATH+'/data/network.pb', 'rb') as f:
        graph_def = tf.GraphDef()
        graph_def.ParseFromString(f.read())
        if png:
            png_data = tf.placeholder(tf.string, shape=[])
            decoded_png = tf.image.decode_png(png_data, channels=3)
            _ = tf.import_graph_def(graph_def, name='incept',input_map={'DecodeJpeg': decoded_png})
            return png_data
        else:
            _ = tf.import_graph_def(graph_def, name='incept')


# 他这里用的是第 3 个池化层的输出作为特征，即 feats_pool3 ，你可以选择其他的层作为特征，因为它这里面用的网络不是特别大。
# 这个 load_index 就是把这 100 万张图片都过一下这个神经网络，注意，这个神经网络是一个针对你当前的这些图像样本去做图像识别训练好的这么一个神经网络。
# 然后我用这么一个神经网络对图片抽取特征，抽取特征的过程就是做一遍 前向运算，所以这里面把做完前向运算的第三个池化层的输出拿出来作为 feature 存下来。数据量很大的话，我就一边读取，一边把数据加到刚才的近似最近邻的 engine 里面
def load_index():
    index,files,findex = [],{},0
    print "Using index path : {}".format(INDEX_PATH+"*.npy")
    for fname in glob.glob(INDEX_PATH+"*.npy"):
        logging.info("Starting {}".format(fname))
        try:
            t = np.load(fname)
            if max(t.shape) > 0:
                index.append(t)
            else:
                raise ValueError
        except:
            logging.error("Could not load {}".format(fname))
            pass
        else:
            for i,f in enumerate(file(fname.replace(".feats_pool3.npy",".files")).readlines()):
                files[findex] = f.strip()
                ENGINE.store_vector(index[-1][i,:],"{}".format(findex))
                findex += 1
            logging.info("Loaded {}".format(fname))
    index = np.concatenate(index)
    return index,files


# 这个函数做得事情很粗暴，我们知道他的demo 是根据一张图片返回12张图片，所以 nearest 这个函数做得就是对5w张图片直接调用  spatial 计算 cos 距离，然后把距离直接 append 到 dist 里面。然后根据这个距离调用了 argsort 获得排序。
def nearest(query_vector,index,files,n=12):
    query_vector= query_vector[np.newaxis,:]
    temp = []
    dist = []
    logging.info("started query")
    for k in xrange(index.shape[0]):
        temp.append(index[k])
        if (k+1) % 50000 == 0:
            temp = np.transpose(np.dstack(temp)[0])
            dist.append(spatial.distance.cdist(query_vector,temp))
            temp = []
    if temp:
        temp = np.transpose(np.dstack(temp)[0])
        dist.append(spatial.distance.cdist(query_vector,temp))
    dist = np.hstack(dist)
    ranked = np.squeeze(dist.argsort())
    logging.info("query finished")
    return [files[k] for i,k in enumerate(ranked[:n])]

# 这个就是 ANN 的方法，用的就是 nearpy 来找到这个 query_vector 的邻居。
def nearest_fast(query_vector,index,files,n=12):
    return [files[int(k)] for v,k,d in ENGINE.neighbours(query_vector)[:n]]


# 就是训练的图片可以一批一批的训练。
def get_batch(path,batch_size = 1000):
    """
    Args:
        path: directory containing images
    Returns: Generator with dictionary  containing image_file_nameh : image_data, each with size =  BUCKET_SIZE
    """
    path += "/*"
    image_data = {}
    logging.info("starting with path {}".format(path))
    for i,fname in enumerate(glob.glob(path)):
        try:
            image_data[fname] = gfile.FastGFile(fname, 'rb').read()
        except:
            logging.info("failed to load {}".format(fname))
            pass
        if (i+1) % batch_size == 0:
            logging.info("Loaded {}, with {} images".format(i,len(image_data)))
            yield image_data
            image_data = {}
    yield image_data


# 就是把图像的特征存下来。
# 这个地方看老师的 90 个 commit 的程序，这个函数还洗了 if AWS 的时候，把数据丢到 aws 的 s3 上面。
def store_index(features,files,count,index_dir):
    feat_fname = "{}/{}.feats_pool3.npy".format(index_dir,count)
    files_fname = "{}/{}.files".format(index_dir,count)
    logging.info("storing in {}".format(index_dir))
    with open(feat_fname,'w') as feats:
        np.save(feats,np.array(features))
    with open(files_fname,'w') as filelist:
        filelist.write("\n".join(files))

# 这个是图像的特征抽取，
def extract_features(image_data,sess):
    # 我们先得到  pool3
    pool3 = sess.graph.get_tensor_by_name('incept/pool_3:0')
    features = []
    files = []
    for fname,data in image_data.iteritems():
        try:
            # 我们对这幅图像做一个前项运算，就拿到了第三个池化层的 feature 然后，把这个向量存下啦。
            pool3_features = sess.run(pool3,{'incept/DecodeJpeg/contents:0': data})
            features.append(np.squeeze(pool3_features))
            files.append(fname)
        except:
            logging.error("error while processing fname {}".format(fname))
    return features,files

# 他提供了计算好的数据给大家，可以从服务器上拉下来。
def download(filename):
    if DEMO:
        command = 'aws s3api get-object --bucket aub3visualsearch --key "{}/{}" --request-payer requester appcode/static/examples/{}'.format(DEMO,filename,filename)
        logging.info(command)
        os.system(command)
    else:
        os.system("cp {}/{} appcode/static/examples/{}".format(DATA_PATH,filename.split("/")[-1],filename.split("/")[-1])) # this needlessly slows down the code, handle it elegantly by using the same directory as static dir in flask.


```


有的同学说，他也是直接抽取的 pooling 的输出作为特征，但是效果不是很好，这个老师说：这个网络是不是针对自己的场景训练的网络还是说直接 down 了一个 VGG 就直接用来抽特征。

老师说：他只能告诉你，这套东西，电商在用。电商体系的一部分，这个效果还是可以的。


<span style="color:red;">感觉这个项目还是很有必要自己搭建一下的，最好是使用的新的 DeepVideoAnalytics，最起码要fork 下来自己注释分析一遍，看看他的整个架构是怎么样的。因为我以后像这样的项目一定要部署到我自己的server 上的。</span>






## REF

- 七月在线 opencv计算机视觉
