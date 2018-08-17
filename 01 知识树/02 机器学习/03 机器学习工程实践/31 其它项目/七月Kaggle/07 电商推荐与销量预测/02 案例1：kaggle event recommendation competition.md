
## Kaggle event 推荐比赛
### By [@寒小阳](http://blog.csdn.net/han_xiaoyang)
本ipython notebook是对于[Kaggle event推荐比赛](https://www.kaggle.com/c/event-recommendation-engine-challenge)的一个参考解答，也简单展示了用分类(排序)模型完成推荐的一个思路。<br>
总共分为4个部分：
* 数据清洗与预处理
* 构建特征(包括协同过滤推荐度等复杂特征)
* 建模
* 格式调整(使得符合Kaggle提交的格式)

## 0.引入包


```python
from __future__ import division

import itertools
import cPickle
import datetime
import hashlib
import locale
import numpy as np
import pycountry
import scipy.io as sio
import scipy.sparse as ss
import scipy.spatial.distance as ssd

from collections import defaultdict
from sklearn.preprocessing import normalize
```

如果内存是充足的，就直接读入到内存中，如果内存是不足的，cPickle 可以保持你原来 python 的数据结构，然后dump 到本地，load 的时候也很快。这个是很常用的。在 python3 中是 pickle。

pycountry 因为我们并不知道有哪些国家，这个库就可以处理国家信息。

scipy.spatial.distance 可以求各种各样的距离。

## 1.数据清洗类


```python
class DataCleaner:
  """
  Common utilities for converting strings to equivalent numbers
  or number buckets.
  """
  def __init__(self):
    # 载入 locales
    self.localeIdMap = defaultdict(int)
    for i, l in enumerate(locale.locale_alias.keys()):
      self.localeIdMap[l] = i + 1
    # 载入 countries
    self.countryIdMap = defaultdict(int)
    ctryIdx = defaultdict(int)
    for i, c in enumerate(pycountry.countries):
      self.countryIdMap[c.name.lower()] = i + 1
      if c.name.lower() == "usa":
        ctryIdx["US"] = i
      if c.name.lower() == "canada":
        ctryIdx["CA"] = i
    for cc in ctryIdx.keys():
      for s in pycountry.subdivisions.get(country_code=cc):
        self.countryIdMap[s.name.lower()] = ctryIdx[cc] + 1
    # 载入 gender id 字典
    self.genderIdMap = defaultdict(int, {"male":1, "female":2})

  def getLocaleId(self, locstr):
    return self.localeIdMap[locstr.lower()]

  def getGenderId(self, genderStr):
    return self.genderIdMap[genderStr]

  def getJoinedYearMonth(self, dateString):
    dttm = datetime.datetime.strptime(dateString, "%Y-%m-%dT%H:%M:%S.%fZ")
    return "".join([str(dttm.year), str(dttm.month)])

  def getCountryId(self, location):
    if (isinstance(location, str)
        and len(location.strip()) > 0
        and location.rfind("  ") > -1):
      return self.countryIdMap[location[location.rindex("  ") + 2:].lower()]
    else:
      return 0

  def getBirthYearInt(self, birthYear):
    try:
      return 0 if birthYear == "None" else int(birthYear)
    except:
      return 0

  def getTimezoneInt(self, timezone):
    try:
      return int(timezone)
    except:
      return 0

  def getFeatureHash(self, value):
    if len(value.strip()) == 0:
      return -1
    else:
      return int(hashlib.sha224(value).hexdigest()[0:4], 16)

  def getFloatValue(self, value):
    if len(value.strip()) == 0:
      return 0.0
    else:
      return float(value)
```

<span style="color:red;">getFeatureHash 有时候数据量太大可能会做一个 hash 分桶，什么是hash 分桶？还是我听错了？</span>


## 2.处理 user 和 event 关联数据

他给的 user 和 event 的文件是一个大文件，里面包含了历史上所有的 user 和event ，但是实际上我们只关心这部分训练和测试的user和event。<span style="color:red;">没想到还有这个情况。</span>

但是这个有个问题，因为训练和测试的user 的朋友可能不在这个训练和测试里，所以`只关心这部分训练和测试的user和event`有可能会使得模型的准确率下降，所以大家的内存是够的情况下，可以用全量。

<span style="color:red;">一直想知道这种数据清洗的过程中内存放不下怎么办？</span>


这里面没有用 pandas 直接读，因为内存不太够，因此是自己手读的：<span style="color:red;">厉害的，也要掌握。</span>

```python
class ProgramEntities:
  """
  我们只关心 train 和 test 中出现的 user 和 event，因此重点处理这部分关联数据
  """
  def __init__(self):
    # 统计训练集中有多少独立的用户的events
    uniqueUsers = set()
    uniqueEvents = set()
    eventsForUser = defaultdict(set)
    usersForEvent = defaultdict(set)
    for filename in ["train.csv", "test.csv"]:
      f = open(filename, 'rb')
      f.readline().strip().split(",")
      for line in f:
        cols = line.strip().split(",")
        uniqueUsers.add(cols[0])
        uniqueEvents.add(cols[1])
        eventsForUser[cols[0]].add(cols[1])
        usersForEvent[cols[1]].add(cols[0])
      f.close()
    self.userEventScores = ss.dok_matrix((len(uniqueUsers), len(uniqueEvents)))
    self.userIndex = dict()
    self.eventIndex = dict()
    for i, u in enumerate(uniqueUsers):
      self.userIndex[u] = i
    for i, e in enumerate(uniqueEvents):
      self.eventIndex[e] = i
    ftrain = open("train.csv", 'rb')
    ftrain.readline()
    for line in ftrain:
      cols = line.strip().split(",")
      i = self.userIndex[cols[0]]
      j = self.eventIndex[cols[1]]
      self.userEventScores[i, j] = int(cols[4]) - int(cols[5])
    ftrain.close()
    sio.mmwrite("PE_userEventScores", self.userEventScores)
    # 为了防止不必要的计算，我们找出来所有关联的用户 或者 关联的event
    # 所谓的关联用户，指的是至少在同一个event上有行为的用户pair
    # 关联的event指的是至少同一个user有行为的event pair
    self.uniqueUserPairs = set()
    self.uniqueEventPairs = set()
    for event in uniqueEvents:
      users = usersForEvent[event]
      if len(users) > 2:
        self.uniqueUserPairs.update(itertools.combinations(users, 2))
    for user in uniqueUsers:
      events = eventsForUser[user]
      if len(events) > 2:
        self.uniqueEventPairs.update(itertools.combinations(events, 2))
    cPickle.dump(self.userIndex, open("PE_userIndex.pkl", 'wb'))
    cPickle.dump(self.eventIndex, open("PE_eventIndex.pkl", 'wb'))
```

## 3.用户与用户相似度矩阵


```python
class Users:
  """
  构建 user/user 相似度矩阵
  """
  def __init__(self, programEntities, sim=ssd.correlation):
    cleaner = DataCleaner()
    nusers = len(programEntities.userIndex.keys())
    fin = open("users.csv", 'rb')
    colnames = fin.readline().strip().split(",")
    self.userMatrix = ss.dok_matrix((nusers, len(colnames) - 1))
    for line in fin:
      cols = line.strip().split(",")
      # 只考虑train.csv中出现的用户
      if programEntities.userIndex.has_key(cols[0]):
        i = programEntities.userIndex[cols[0]]
        self.userMatrix[i, 0] = cleaner.getLocaleId(cols[1])
        self.userMatrix[i, 1] = cleaner.getBirthYearInt(cols[2])
        self.userMatrix[i, 2] = cleaner.getGenderId(cols[3])
        self.userMatrix[i, 3] = cleaner.getJoinedYearMonth(cols[4])
        self.userMatrix[i, 4] = cleaner.getCountryId(cols[5])
        self.userMatrix[i, 5] = cleaner.getTimezoneInt(cols[6])
    fin.close()
    # 归一化用户矩阵
    self.userMatrix = normalize(self.userMatrix, norm="l1", axis=0, copy=False)
    sio.mmwrite("US_userMatrix", self.userMatrix)
    # 计算用户相似度矩阵，之后会用到
    self.userSimMatrix = ss.dok_matrix((nusers, nusers))
    for i in range(0, nusers):
      self.userSimMatrix[i, i] = 1.0
    for u1, u2 in programEntities.uniqueUserPairs:
      i = programEntities.userIndex[u1]
      j = programEntities.userIndex[u2]
      if not self.userSimMatrix.has_key((i, j)):
        usim = sim(self.userMatrix.getrow(i).todense(),
          self.userMatrix.getrow(j).todense())
        self.userSimMatrix[i, j] = usim
        self.userSimMatrix[j, i] = usim
    sio.mmwrite("US_userSimMatrix", self.userSimMatrix)
```

## 4.用户社交关系挖掘


```python
class UserFriends:
  """
  找出某用户的那些朋友，想法非常简单
  1)如果你有更多的朋友，可能你性格外向，更容易参加各种活动
  2)如果你朋友会参加某个活动，可能你也会跟随去参加一下
  """
  def __init__(self, programEntities):
    nusers = len(programEntities.userIndex.keys())
    self.numFriends = np.zeros((nusers))
    self.userFriends = ss.dok_matrix((nusers, nusers))
    fin = open("user_friends.csv", 'rb')
    fin.readline()                # skip header
    ln = 0
    for line in fin:
      if ln % 200 == 0:
        print "Loading line: ", ln
      cols = line.strip().split(",")
      user = cols[0]
      if programEntities.userIndex.has_key(user):
        friends = cols[1].split(" ")
        i = programEntities.userIndex[user]
        self.numFriends[i] = len(friends)
        for friend in friends:
          if programEntities.userIndex.has_key(friend):
            j = programEntities.userIndex[friend]
            # the objective of this score is to infer the degree to
            # and direction in which this friend will influence the
            # user's decision, so we sum the user/event score for
            # this user across all training events.
            eventsForUser = programEntities.userEventScores.getrow(j).todense()
            score = eventsForUser.sum() / np.shape(eventsForUser)[1]
            self.userFriends[i, j] += score
            self.userFriends[j, i] += score
      ln += 1
    fin.close()
    # 归一化数组
    sumNumFriends = self.numFriends.sum(axis=0)
    self.numFriends = self.numFriends / sumNumFriends
    sio.mmwrite("UF_numFriends", np.matrix(self.numFriends))
    self.userFriends = normalize(self.userFriends, norm="l1", axis=0, copy=False)
    sio.mmwrite("UF_userFriends", self.userFriends)
```

## 5.构造event和event相似度数据


```python
class Events:
  """
  构建event-event相似度，注意这里有2种相似度：
  1）由用户-event行为，类似协同过滤算出的相似度
  2）由event本身的内容(event信息)计算出的event-event相似度
  """
  def __init__(self, programEntities, psim=ssd.correlation, csim=ssd.cosine):
    cleaner = DataCleaner()
    fin = open("events.csv", 'rb')
    fin.readline() # skip header
    nevents = len(programEntities.eventIndex.keys())
    self.eventPropMatrix = ss.dok_matrix((nevents, 7))
    self.eventContMatrix = ss.dok_matrix((nevents, 100))
    ln = 0
    for line in fin.readlines():
#      if ln > 10:
#        break
      cols = line.strip().split(",")
      eventId = cols[0]
      if programEntities.eventIndex.has_key(eventId):
        i = programEntities.eventIndex[eventId]
        self.eventPropMatrix[i, 0] = cleaner.getJoinedYearMonth(cols[2]) # start_time
        self.eventPropMatrix[i, 1] = cleaner.getFeatureHash(cols[3]) # city
        self.eventPropMatrix[i, 2] = cleaner.getFeatureHash(cols[4]) # state
        self.eventPropMatrix[i, 3] = cleaner.getFeatureHash(cols[5]) # zip
        self.eventPropMatrix[i, 4] = cleaner.getFeatureHash(cols[6]) # country
        self.eventPropMatrix[i, 5] = cleaner.getFloatValue(cols[7]) # lat
        self.eventPropMatrix[i, 6] = cleaner.getFloatValue(cols[8]) # lon
        for j in range(9, 109):
          self.eventContMatrix[i, j-9] = cols[j]
        ln += 1
    fin.close()
    self.eventPropMatrix = normalize(self.eventPropMatrix,
        norm="l1", axis=0, copy=False)
    sio.mmwrite("EV_eventPropMatrix", self.eventPropMatrix)
    self.eventContMatrix = normalize(self.eventContMatrix,
        norm="l1", axis=0, copy=False)
    sio.mmwrite("EV_eventContMatrix", self.eventContMatrix)
    # calculate similarity between event pairs based on the two matrices
    self.eventPropSim = ss.dok_matrix((nevents, nevents))
    self.eventContSim = ss.dok_matrix((nevents, nevents))
    for e1, e2 in programEntities.uniqueEventPairs:
      i = programEntities.eventIndex[e1]
      j = programEntities.eventIndex[e2]
      if not self.eventPropSim.has_key((i,j)):
        epsim = psim(self.eventPropMatrix.getrow(i).todense(),
          self.eventPropMatrix.getrow(j).todense())
        self.eventPropSim[i, j] = epsim
        self.eventPropSim[j, i] = epsim
      if not self.eventContSim.has_key((i,j)):
        ecsim = csim(self.eventContMatrix.getrow(i).todense(),
          self.eventContMatrix.getrow(j).todense())
        self.eventContSim[i, j] = epsim
        self.eventContSim[j, i] = epsim
    sio.mmwrite("EV_eventPropSim", self.eventPropSim)
    sio.mmwrite("EV_eventContSim", self.eventContSim)
```

## 6.活跃度/event热度 数据


```python
class EventAttendees():
  """
  统计某个活动，参加和不参加的人数，从而为活动活跃度做准备
  """
  def __init__(self, programEvents):
    nevents = len(programEvents.eventIndex.keys())
    self.eventPopularity = ss.dok_matrix((nevents, 1))
    f = open("event_attendees.csv", 'rb')
    f.readline() # skip header
    for line in f:
      cols = line.strip().split(",")
      eventId = cols[0]
      if programEvents.eventIndex.has_key(eventId):
        i = programEvents.eventIndex[eventId]
        self.eventPopularity[i, 0] = \
          len(cols[1].split(" ")) - len(cols[4].split(" "))
    f.close()
    self.eventPopularity = normalize(self.eventPopularity, norm="l1",
      axis=0, copy=False)
    sio.mmwrite("EA_eventPopularity", self.eventPopularity)
```

## 7.串起所有的数据处理和准备流程


```python
def data_prepare():
  """
  计算生成所有的数据，用矩阵或者其他形式存储方便后续提取特征和建模
  """
  print "第1步：统计user和event相关信息..."
  pe = ProgramEntities()
  print "第1步完成...\n"
  print "第2步：计算用户相似度信息，并用矩阵形式存储..."
  Users(pe)
  print "第2步完成...\n"
  print "第3步：计算用户社交关系信息，并存储..."
  UserFriends(pe)
  print "第3步完成...\n"
  print "第4步：计算event相似度信息，并用矩阵形式存储..."
  Events(pe)
  print "第4步完成...\n"
  print "第5步：计算event热度信息..."
  EventAttendees(pe)
  print "第5步完成...\n"

# 运行进行数据准备
data_prepare()
```

    第1步：统计user和event相关信息...


    /usr/local/lib/python2.7/site-packages/numpy/core/fromnumeric.py:2652: VisibleDeprecationWarning: `rank` is deprecated; use the `ndim` attribute or function instead. To find the rank of a matrix see `numpy.linalg.matrix_rank`.
      VisibleDeprecationWarning)


    第1步完成...

    第2步：计算用户相似度信息，并用矩阵形式存储...
    第2步完成...

    第3步：计算用户社交关系信息，并存储...
    Loading line:  0
    Loading line:  200
    Loading line:  400
    Loading line:  600
    Loading line:  800
    Loading line:  1000
    Loading line:  1200
    Loading line:  1400
    Loading line:  1600
    Loading line:  1800
    Loading line:  2000
    Loading line:  2200
    Loading line:  2400
    Loading line:  2600
    Loading line:  2800
    Loading line:  3000
    Loading line:  3200
    Loading line:  3400
    Loading line:  3600
    Loading line:  3800
    Loading line:  4000
    Loading line:  4200
    Loading line:  4400
    Loading line:  4600
    Loading line:  4800
    Loading line:  5000
    Loading line:  5200
    Loading line:  5400
    Loading line:  5600
    Loading line:  5800
    Loading line:  6000
    Loading line:  6200
    Loading line:  6400
    Loading line:  6600
    Loading line:  6800
    Loading line:  7000
    Loading line:  7200
    Loading line:  7400
    Loading line:  7600
    Loading line:  7800
    Loading line:  8000
    Loading line:  8200
    Loading line:  8400
    Loading line:  8600
    Loading line:  8800
    Loading line:  9000
    Loading line:  9200
    Loading line:  9400
    Loading line:  9600
    Loading line:  9800
    Loading line:  10000
    Loading line:  10200
    Loading line:  10400
    Loading line:  10600
    Loading line:  10800
    Loading line:  11000
    Loading line:  11200
    Loading line:  11400
    Loading line:  11600
    Loading line:  11800
    Loading line:  12000
    Loading line:  12200
    Loading line:  12400
    Loading line:  12600
    Loading line:  12800
    Loading line:  13000
    Loading line:  13200
    Loading line:  13400
    Loading line:  13600
    Loading line:  13800
    Loading line:  14000
    Loading line:  14200
    Loading line:  14400
    Loading line:  14600
    Loading line:  14800
    Loading line:  15000
    Loading line:  15200
    Loading line:  15400
    Loading line:  15600
    Loading line:  15800
    Loading line:  16000
    Loading line:  16200
    Loading line:  16400
    Loading line:  16600
    Loading line:  16800
    Loading line:  17000
    Loading line:  17200
    Loading line:  17400
    Loading line:  17600
    Loading line:  17800
    Loading line:  18000
    Loading line:  18200
    Loading line:  18400
    Loading line:  18600
    Loading line:  18800
    Loading line:  19000
    Loading line:  19200
    Loading line:  19400
    Loading line:  19600
    Loading line:  19800
    Loading line:  20000
    Loading line:  20200
    Loading line:  20400
    Loading line:  20600
    Loading line:  20800
    Loading line:  21000
    Loading line:  21200
    Loading line:  21400
    Loading line:  21600
    Loading line:  21800
    Loading line:  22000
    Loading line:  22200
    Loading line:  22400
    Loading line:  22600
    Loading line:  22800
    Loading line:  23000
    Loading line:  23200
    Loading line:  23400
    Loading line:  23600
    Loading line:  23800
    Loading line:  24000
    Loading line:  24200
    Loading line:  24400
    Loading line:  24600
    Loading line:  24800
    Loading line:  25000
    Loading line:  25200
    Loading line:  25400
    Loading line:  25600
    Loading line:  25800
    Loading line:  26000
    Loading line:  26200
    Loading line:  26400
    Loading line:  26600
    Loading line:  26800
    Loading line:  27000
    Loading line:  27200
    Loading line:  27400
    Loading line:  27600
    Loading line:  27800
    Loading line:  28000
    Loading line:  28200
    Loading line:  28400
    Loading line:  28600
    Loading line:  28800
    Loading line:  29000
    Loading line:  29200
    Loading line:  29400
    Loading line:  29600
    Loading line:  29800
    Loading line:  30000
    Loading line:  30200
    Loading line:  30400
    Loading line:  30600
    Loading line:  30800
    Loading line:  31000
    Loading line:  31200
    Loading line:  31400
    Loading line:  31600
    Loading line:  31800
    Loading line:  32000
    Loading line:  32200
    Loading line:  32400
    Loading line:  32600
    Loading line:  32800
    Loading line:  33000
    Loading line:  33200
    Loading line:  33400
    Loading line:  33600
    Loading line:  33800
    Loading line:  34000
    Loading line:  34200
    Loading line:  34400
    Loading line:  34600
    Loading line:  34800
    Loading line:  35000
    Loading line:  35200
    Loading line:  35400
    Loading line:  35600
    Loading line:  35800
    Loading line:  36000
    Loading line:  36200
    Loading line:  36400
    Loading line:  36600
    Loading line:  36800
    Loading line:  37000
    Loading line:  37200
    Loading line:  37400
    Loading line:  37600
    Loading line:  37800
    Loading line:  38000
    Loading line:  38200
    第3步完成...

    第4步：计算event相似度信息，并用矩阵形式存储...


    /System/Library/Frameworks/Python.framework/Versions/2.7/Extras/lib/python/scipy/spatial/distance.py:287: RuntimeWarning: invalid value encountered in double_scalars
      dist = 1.0 - np.dot(u, v) / (norm(u) * norm(v))


    第4步完成...

    第5步：计算event热度信息...
    第5步完成...



## 8.构建特征


```python
# 这是构建特征部分
from __future__ import division

import cPickle
import numpy as np
import scipy.io as sio

class DataRewriter:
  def __init__(self):
    # 读入数据做初始化
    self.userIndex = cPickle.load(open("PE_userIndex.pkl", 'rb'))
    self.eventIndex = cPickle.load(open("PE_eventIndex.pkl", 'rb'))
    self.userEventScores = sio.mmread("PE_userEventScores").todense()
    self.userSimMatrix = sio.mmread("US_userSimMatrix").todense()
    self.eventPropSim = sio.mmread("EV_eventPropSim").todense()
    self.eventContSim = sio.mmread("EV_eventContSim").todense()
    self.numFriends = sio.mmread("UF_numFriends")
    self.userFriends = sio.mmread("UF_userFriends").todense()
    self.eventPopularity = sio.mmread("EA_eventPopularity").todense()

  def userReco(self, userId, eventId):
    """
    根据User-based协同过滤，得到event的推荐度
    基本的伪代码思路如下：
    for item i
      for every other user v that has a preference for i
        compute similarity s between u and v
        incorporate v's preference for i weighted by s into running aversge
    return top items ranked by weighted average
    """
    i = self.userIndex[userId]
    j = self.eventIndex[eventId]
    vs = self.userEventScores[:, j]
    sims = self.userSimMatrix[i, :]
    prod = sims * vs
    try:
      return prod[0, 0] - self.userEventScores[i, j]
    except IndexError:
      return 0

  def eventReco(self, userId, eventId):
    """
    根据基于物品的协同过滤，得到Event的推荐度
    基本的伪代码思路如下：
    for item i
      for every item j tht u has a preference for
        compute similarity s between i and j
        add u's preference for j weighted by s to a running average
    return top items, ranked by weighted average
    """
    i = self.userIndex[userId]
    j = self.eventIndex[eventId]
    js = self.userEventScores[i, :]
    psim = self.eventPropSim[:, j]
    csim = self.eventContSim[:, j]
    pprod = js * psim
    cprod = js * csim
    pscore = 0
    cscore = 0
    try:
      pscore = pprod[0, 0] - self.userEventScores[i, j]
    except IndexError:
      pass
    try:
      cscore = cprod[0, 0] - self.userEventScores[i, j]
    except IndexError:
      pass
    return pscore, cscore

  def userPop(self, userId):
    """
    基于用户的朋友个数来推断用户的社交程度
    主要的考量是如果用户的朋友非常多，可能会更倾向于参加各种社交活动
    """
    if self.userIndex.has_key(userId):
      i = self.userIndex[userId]
      try:
        return self.numFriends[0, i]
      except IndexError:
        return 0
    else:
      return 0

  def friendInfluence(self, userId):
    """
    朋友对用户的影响
    主要考虑用户所有的朋友中，有多少是非常喜欢参加各种社交活动/event的
    用户的朋友圈如果都积极参与各种event，可能会对当前用户有一定的影响
    """
    nusers = np.shape(self.userFriends)[1]
    i = self.userIndex[userId]
    return (self.userFriends[i, :].sum(axis=0) / nusers)[0,0]

  def eventPop(self, eventId):
    """
    本活动本身的热度
    主要是通过参与的人数来界定的
    """
    i = self.eventIndex[eventId]
    return self.eventPopularity[i, 0]

  def rewriteData(self, start=1, train=True, header=True):
    """
    把前面user-based协同过滤 和 item-based协同过滤，以及各种热度和影响度作为特征组合在一起
    生成新的训练数据，用于分类器分类使用
    """
    fn = "train.csv" if train else "test.csv"
    fin = open(fn, 'rb')
    fout = open("data_" + fn, 'wb')
    # write output header
    if header:
      ocolnames = ["invited", "user_reco", "evt_p_reco",
        "evt_c_reco", "user_pop", "frnd_infl", "evt_pop"]
      if train:
        ocolnames.append("interested")
        ocolnames.append("not_interested")
      fout.write(",".join(ocolnames) + "\n")
    ln = 0
    for line in fin:
      ln += 1
      if ln < start:
        continue
      cols = line.strip().split(",")
      userId = cols[0]
      eventId = cols[1]
      invited = cols[2]
      if ln%500 == 0:
          print "%s:%d (userId, eventId)=(%s, %s)" % (fn, ln, userId, eventId)
      user_reco = self.userReco(userId, eventId)
      evt_p_reco, evt_c_reco = self.eventReco(userId, eventId)
      user_pop = self.userPop(userId)
      frnd_infl = self.friendInfluence(userId)
      evt_pop = self.eventPop(eventId)
      ocols = [invited, user_reco, evt_p_reco,
        evt_c_reco, user_pop, frnd_infl, evt_pop]
      if train:
        ocols.append(cols[4]) # interested
        ocols.append(cols[5]) # not_interested
      fout.write(",".join(map(lambda x: str(x), ocols)) + "\n")
    fin.close()
    fout.close()

  def rewriteTrainingSet(self):
    self.rewriteData(True)

  def rewriteTestSet(self):
    self.rewriteData(False)

# When running with cython, the actual class will be converted to a .so
# file, and the following code (along with the commented out import below)
# will need to be put into another .py and this should be run.

#import CRegressionData as rd

dr = DataRewriter()
print "生成训练数据...\n"
dr.rewriteData(train=True, start=2, header=True)
print "生成预测数据...\n"
dr.rewriteData(train=False, start=2, header=True)
```

    生成训练数据...

    train.csv:500 (userId, eventId)=(123290209, 1887085024)
    train.csv:1000 (userId, eventId)=(272886293, 199858305)
    train.csv:1500 (userId, eventId)=(395305791, 1582270949)
    train.csv:2000 (userId, eventId)=(527523423, 3272728211)
    train.csv:2500 (userId, eventId)=(651258472, 792632006)
    train.csv:3000 (userId, eventId)=(811791433, 524756826)
    train.csv:3500 (userId, eventId)=(985547042, 1269035551)
    train.csv:4000 (userId, eventId)=(1107615001, 173949238)
    train.csv:4500 (userId, eventId)=(1236336671, 3849306291)
    train.csv:5000 (userId, eventId)=(1414301782, 2652356640)
    train.csv:5500 (userId, eventId)=(1595465532, 955398943)
    train.csv:6000 (userId, eventId)=(1747091728, 2131379889)
    train.csv:6500 (userId, eventId)=(1914182220, 955398943)
    train.csv:7000 (userId, eventId)=(2071842684, 1076364848)
    train.csv:7500 (userId, eventId)=(2217853337, 3051438735)
    train.csv:8000 (userId, eventId)=(2338481531, 2525447278)
    train.csv:8500 (userId, eventId)=(2489551967, 520657921)
    train.csv:9000 (userId, eventId)=(2650493630, 87962584)
    train.csv:9500 (userId, eventId)=(2791418962, 4223848259)
    train.csv:10000 (userId, eventId)=(2903662804, 2791462807)
    train.csv:10500 (userId, eventId)=(3036141956, 3929507420)
    train.csv:11000 (userId, eventId)=(3176074542, 3459485614)
    train.csv:11500 (userId, eventId)=(3285425249, 2271782630)
    train.csv:12000 (userId, eventId)=(3410667855, 1063772489)
    train.csv:12500 (userId, eventId)=(3531604778, 2584839423)
    train.csv:13000 (userId, eventId)=(3686871863, 53495098)
    train.csv:13500 (userId, eventId)=(3833637800, 2415873572)
    train.csv:14000 (userId, eventId)=(3944021305, 2096772901)
    train.csv:14500 (userId, eventId)=(4075466480, 3567240505)
    train.csv:15000 (userId, eventId)=(4197193550, 1628057176)
    生成预测数据...

    test.csv:500 (userId, eventId)=(182290053, 2529072432)
    test.csv:1000 (userId, eventId)=(433510318, 4244463632)
    test.csv:1500 (userId, eventId)=(632808865, 2845303452)
    test.csv:2000 (userId, eventId)=(813611885, 2036538169)
    test.csv:2500 (userId, eventId)=(1010701404, 303459881)
    test.csv:3000 (userId, eventId)=(1210932037, 2529072432)
    test.csv:3500 (userId, eventId)=(1452921099, 2705317682)
    test.csv:4000 (userId, eventId)=(1623287180, 1626678328)
    test.csv:4500 (userId, eventId)=(1855201342, 2603032829)
    test.csv:5000 (userId, eventId)=(2083900381, 2529072432)
    test.csv:5500 (userId, eventId)=(2318415276, 2509151803)
    test.csv:6000 (userId, eventId)=(2528161539, 4025975316)
    test.csv:6500 (userId, eventId)=(2749110768, 4244406355)
    test.csv:7000 (userId, eventId)=(2927772127, 1532377761)
    test.csv:7500 (userId, eventId)=(3199685636, 1776393554)
    test.csv:8000 (userId, eventId)=(3393388475, 680270887)
    test.csv:8500 (userId, eventId)=(3601169721, 154434302)
    test.csv:9000 (userId, eventId)=(3828963415, 3067222491)
    test.csv:9500 (userId, eventId)=(4018723397, 2522610844)
    test.csv:10000 (userId, eventId)=(4180064266, 2658555390)


## 9.建模与预测
实际上在上述特征构造好了之后，我们有很多的办法去训练得到模型和完成预测，这里用了sklearn中的SGDClassifier
事实上xgboost有更好的效果（显然我们的特征大多是密集型的浮点数，很适合GBDT这样的模型）

注意交叉验证，我们这里用了10折的交叉验证


```python
# 建模与预测
from __future__ import division

import math

import numpy as np
import pandas as pd

from sklearn.cross_validation import KFold
from sklearn.linear_model import SGDClassifier

def train():
  """
  在我们得到的特征上训练分类器，target为1(感兴趣)，或者是0(不感兴趣)
  """
  trainDf = pd.read_csv("data_train.csv")
  X = np.matrix(pd.DataFrame(trainDf, index=None,
    columns=["invited", "user_reco", "evt_p_reco", "evt_c_reco",
    "user_pop", "frnd_infl", "evt_pop"]))
  y = np.array(trainDf.interested)
  clf = SGDClassifier(loss="log", penalty="l2")
  clf.fit(X, y)
  return clf

def validate():
  """
  10折的交叉验证，并输出交叉验证的平均准确率
  """
  trainDf = pd.read_csv("data_train.csv")
  X = np.matrix(pd.DataFrame(trainDf, index=None,
    columns=["invited", "user_reco", "evt_p_reco", "evt_c_reco",
    "user_pop", "frnd_infl", "evt_pop"]))
  y = np.array(trainDf.interested)
  nrows = len(trainDf)
  kfold = KFold(nrows, 10)
  avgAccuracy = 0
  run = 0
  for train, test in kfold:
    Xtrain, Xtest, ytrain, ytest = X[train], X[test], y[train], y[test]
    clf = SGDClassifier(loss="log", penalty="l2")
    clf.fit(Xtrain, ytrain)
    accuracy = 0
    ntest = len(ytest)
    for i in range(0, ntest):
      yt = clf.predict(Xtest[i, :])
      if yt == ytest[i]:
        accuracy += 1
    accuracy = accuracy / ntest
    print "accuracy (run %d): %f" % (run, accuracy)
    avgAccuracy += accuracy
    run += 1
  print "Average accuracy", (avgAccuracy / run)

def test(clf):
  """
  读取test数据，用分类器完成预测
  """
  origTestDf = pd.read_csv("test.csv")
  users = origTestDf.user
  events = origTestDf.event
  testDf = pd.read_csv("data_test.csv")
  fout = open("result.csv", 'wb')
  fout.write(",".join(["user", "event", "outcome", "dist"]) + "\n")
  nrows = len(testDf)
  Xp = np.matrix(testDf)
  yp = np.zeros((nrows, 2))
  for i in range(0, nrows):
    xp = Xp[i, :]
    yp[i, 0] = clf.predict(xp)
    yp[i, 1] = clf.decision_function(xp)
    fout.write(",".join(map(lambda x: str(x),
      [users[i], events[i], yp[i, 0], yp[i, 1]])) + "\n")
  fout.close()



clf = train()
test(clf)
```

## 10.生成要提交的文件


```python
# 处理成提交结果的格式
from __future__ import division

import pandas as pd

def byDist(x, y):
  return int(y[1] - x[1])

def generate_submition_file():
  # 输出文件
  fout = open("final_result.csv", 'wb')
  fout.write(",".join(["User", "Events"]) + "\n")
  resultDf = pd.read_csv("result.csv")
  # group remaining user/events
  grouped = resultDf.groupby("user")
  for name, group in grouped:
    user = str(name)
    tuples = zip(list(group.event), list(group.dist), list(group.outcome))
#    tuples = filter(lambda x: x[2]==1, tuples)
    tuples = sorted(tuples, cmp=byDist)
    events = "\"" + str(map(lambda x: x[0], tuples)) + "\""
    fout.write(",".join([user, events]) + "\n")
  fout.close()


generate_submition_file()
```


```python

```
