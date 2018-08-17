

TODO

- 这个没怎么看懂。


这里我们使用的是原始的数据：

KNN in OpenCV3 的时候：

```cpp
Ptr<ml::KNearest> knn = ml::KNearest::create();
Ptr<ml::TrainData> trainData =
ml::TrainData::create(train_features,ml::SampleTypes::ROW_SAMPLE, labels);
knn->train(trainData);

Mat predictedLabels;
knn->findNearest(sample, K,predictedLabels);

float prediction = predictedLabels.at<float>(0,0);
```


KNN in OpenCV 2.4 的时候：

```cpp
int size = numRows*numCols;
Mat trainingVectors(numImages, size, CV_32FC1);
Mat trainingLabels(numImages, 1, CV_32FC1);
KNearest knn(trainingVectors, trainingLabels);
CvMat *currentTest = cvCreateMat(1, size,CV_32FC1);
CvMat *currentLabel = cvCreateMat(1, 1,CV_32FC1);
knn.find_nearest(currentTest, 5, currentLabel)
```

完整的如下：


```cpp
#include <stdio.h>
#include <stdlib.h>
#include <cv.h>
#include <highgui.h>
#include <ml.h>
#include <time.h>

using namespace cv;

int readFlippedInteger(FILE *);

int main()
{
	FILE *fp = fopen("..\\data\\train-images.idx3-ubyte", "rb");
	FILE *fp2 = fopen("..\\data\\train-labels.idx1-ubyte", "rb");

	if (!fp || !fp2)
	{
		//	cout << "Files not Found" << endl;
		return 0;
	}

	int magicNumber = readFlippedInteger(fp);
	int numImages = readFlippedInteger(fp);
	int numRows = readFlippedInteger(fp);
	int numCols = readFlippedInteger(fp);

	fseek(fp2, 0x08, SEEK_SET);

	int size = numRows*numCols;

	Mat trainingVectors(numImages, size, CV_32FC1);
	Mat trainingLabels(numImages, 1, CV_32FC1);
	//CvMat *trainingVectors = cvCreateMat(numImages, size, CV_32FC1);
	//CvMat *trainingLabels = cvCreateMat(numImages, 1, CV_32FC1);

	uchar *temp = new uchar[size];
	//unsigned char *temp = new unsigned char[size];

	uchar tempClass = 0;

	for (int i = 0; i < numImages; i++)
	{
		fread((void*)temp, size, 1, fp);
		fread((void*)(&tempClass), sizeof(uchar), 1, fp2);
		trainingLabels.at<float>(i, 0) = tempClass;
		Mat img(numRows, numCols, CV_32FC1);
		for (int k = 0; k < size; k++)
		{
			trainingVectors.at<float>(i, k) = temp[k];
			img.at<float>(k / numCols, k%numCols) = temp[k];
		}
		imshow("data", img);
		//waitKey(2);
	}

	KNearest knn(trainingVectors, trainingLabels);
	printf("Maximum k: %d\n", knn.get_max_k());//我们在设定 k 的时候可以参考这个值。

	fclose(fp);
	fclose(fp2);
	delete[] temp;

	fp = fopen("..\\data\\t10k-images.idx3-ubyte", "rb");
	fp2 = fopen("..\\data\\t10k-labels.idx1-ubyte", "rb");

	magicNumber = readFlippedInteger(fp);
	numImages = readFlippedInteger(fp);
	numRows = readFlippedInteger(fp);

	numCols = readFlippedInteger(fp);

	fseek(fp2, 0x08, SEEK_SET);
	CvMat *testVectors = cvCreateMat(numImages, size, CV_32FC1);
	CvMat *testLabels = cvCreateMat(numImages, 1, CV_32FC1);
	CvMat *actualLabels = cvCreateMat(numImages, 1, CV_32FC1);
	temp = new uchar[size];
	tempClass = 1;
	CvMat *currentTest = cvCreateMat(1, size, CV_32FC1);
	CvMat *currentLabel = cvCreateMat(1, 1, CV_32FC1);
	int totalCorrect = 0;

	for (int i = 0; i<numImages; i++)
	{
		fread((void*)temp, size, 1, fp);
		fread((void*)(&tempClass), sizeof(uchar), 1, fp2);
		actualLabels->data.fl[i] = (float)tempClass;
		for (int k = 0; k<size; k++)
		{
			testVectors->data.fl[i*size + k] = temp[k];
			currentTest->data.fl[k] = temp[k];
		}
		knn.find_nearest(currentTest, 5, currentLabel);
		testLabels->data.fl[i] = currentLabel->data.fl[0];
		if (currentLabel->data.fl[0] == actualLabels->data.fl[i])
			totalCorrect++;
	}
	printf("Time: %d Accuracy: %f ", (int)time, (double)totalCorrect * 100 / (double)numImages);
}

int readFlippedInteger(FILE *fp) {
	int ret = 0;
	uchar *temp;
	temp = (uchar*)(&ret);
	fread(&temp[3], sizeof(uchar), 1, fp);
	fread(&temp[2], sizeof(uchar), 1, fp);
	fread(&temp[1], sizeof(uchar), 1, fp);
	fread(&temp[0], sizeof(uchar), 1, fp);
	return ret;
}
```


<span style="color:red;">这个 KNearest 哪里来的？</span>


60000 train + 10000 test 96.88%
K = 5

![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180806/FfaCDFLk6F.png?imageslim)



![mark](http://pacdb2bfr.bkt.clouddn.com/blog/image/180806/6Hk6JA871g.png?imageslim)
