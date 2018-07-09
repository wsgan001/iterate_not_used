# Bitmap

## 方法介绍
### 什么是Bit-map
所谓的Bit-map就是用一个bit位来标记某个元素对应的Value， 而Key即是该元素。由于采用了Bit为单位来存储数据，因此在存储空间方面，可以大大节省。

来看一个具体的例子，假设我们要对0-7内的5个元素(4,7,2,5,3)排序（这里假设这些元素没有重复）。那么我们就可以采用Bit-map的方法来达到排序的目的。要表示8个数，我们就只需要8个Bit（1Bytes），首先我们开辟1Byte的空间，将这些空间的所有Bit位都置为0(如下图：)

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180708/dg8l27hk57.gif)

然后遍历这5个元素，首先第一个元素是4，那么就把4对应的位置为1（可以这样操作 p+(i/8)|(0×01<<(i%8)) 当然了这里的操作涉及到Big-ending和Little-ending的情况，这里默认为Big-ending）,因为是从零开始的，所以要把第五位置为一（如下图）：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180708/a2bHHFLf5a.gif)

然后再处理第二个元素7，将第八位置为1,，接着再处理第三个元素，一直到最后处理完所有的元素，将相应的位置为1，这时候的内存的Bit位的状态如下：

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180708/iAIC39F3DJ.gif)

然后我们现在遍历一遍Bit区域，将该位是一的位的编号输出（2，3，4，5，7），这样就达到了排序的目的。下面的代码给出了一个BitMap的用法：排序。

```c
//定义每个Byte中有8个Bit位
#include ＜memory.h＞
#define BYTESIZE 8
void SetBit(char *p, int posi)
{
    for(int i=0; i < (posi/BYTESIZE); i++)
    {
        p++;
    }

    *p = *p|(0x01<<(posi%BYTESIZE));//将该Bit位赋值1
    return;
}

void BitMapSortDemo()
{
    //为了简单起见，我们不考虑负数
    int num[] = {3,5,2,10,6,12,8,14,9};

    //BufferLen这个值是根据待排序的数据中最大值确定的
    //待排序中的最大值是14，因此只需要2个Bytes(16个Bit)
    //就可以了。
    const int BufferLen = 2;
    char *pBuffer = new char[BufferLen];

    //要将所有的Bit位置为0，否则结果不可预知。
    memset(pBuffer,0,BufferLen);
    for(int i=0;i<9;i++)
    {
        //首先将相应Bit位上置为1
        SetBit(pBuffer,num[i]);
    }

    //输出排序结果
    for(int i=0;i<BufferLen;i++)//每次处理一个字节(Byte)
    {
        for(int j=0;j<BYTESIZE;j++)//处理该字节中的每个Bit位
        {
            //判断该位上是否是1，进行输出，这里的判断比较笨。
            //首先得到该第j位的掩码（0x01＜＜j），将内存区中的
            //位和此掩码作与操作。最后判断掩码是否和处理后的
            //结果相同
            if((*pBuffer&(0x01<<j)) == (0x01<<j))
            {
                printf("%d ",i*BYTESIZE + j);
            }
        }  
        pBuffer++;
    }
}

int _tmain(int argc, _TCHAR* argv[])
{
    BitMapSortDemo();
    return 0;
}
```
可进行数据的快速查找，判重，删除，一般来说数据范围是int的10倍以下

### 基本原理及要点

使用bit数组来表示某些元素是否存在，比如8位电话号码.

## 问题实例

**1、在2.5亿个整数中找出不重复的整数，注，内存不足以容纳这2.5亿个整数**

**解法一**：采用2-Bitmap（每个数分配2bit，00表示不存在，01表示出现一次，10表示多次，11无意义）进行，共需内存2^32 * 2 bit=1 GB内存，还可以接受。然后扫描这2.5亿个整数，查看Bitmap中相对应位，如果是00变01，01变10，10保持不变。所描完事后，查看bitmap，把对应位是01的整数输出即可。

**解法二**：也可采用与第1题类似的方法，进行划分小文件的方法。然后在小文件中找出不重复的整数，并排序。然后再进行归并，注意去除重复的元素。”

**2、给40亿个不重复的unsigned int的整数，没排过序的，然后再给一个数，如何快速判断这个数是否在那40亿个数当中？**

**解法一**：可以用位图/Bitmap的方法，申请512M的内存，一个bit位代表一个unsigned int值。读入40亿个数，设置相应的bit位，读入要查询的数，查看相应bit位是否为1，为1表示存在，为0表示不存在。
