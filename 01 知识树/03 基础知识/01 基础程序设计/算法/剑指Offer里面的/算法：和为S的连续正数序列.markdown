# REF
- [CodingInterviews](https://github.com/gatieme/CodingInterviews)





# 题目 1

## 题目描述

输入一个递增排序的正数数组和一个数字 s，在数组中查找两个数，得它们的和正好是 s 。如果有多对数字的和等于 s ，输出乘积最小的即可。

例如：输入数组｛1 、2 、4、7 、11 、15 ｝和数字15。

由于4+ 11 = 15 ，因此输出4 和11 。

注意：序列是递增序列，而且元素都是正数。


# 题目解答 1




## 使用两个指针来靠近


**为什么这个地方牵涉到了韦达定理？看到说用了 韦达定理，确认下。**

OK，我们考虑两个数，如果他们的和为一个定值的话，那么他们接近相等的时候，他们的乘积最大，而越不相等，乘积越小。

因此，如果这两个数是一个有序序列中的数， 比如：1 2 3 4 5 6

那么，他们相隔的越远，乘积就越小 \(1*6 < 2 * 5 < 3 * 4\)，这样的话，我们从两端分别向中间走，那么先找到的那一对的乘积一定最小。

OK，我们可以用两个指针 i 和 j ，分别指向头和尾，然后不断往中间靠拢，具体如下：




  * 如果 \(a_i + a_j == sum\)，那么这个\(a_i\) 和 \(a_j\) 就是答案，不用再找了。（因为相差越远乘积越小）。


  * 如果这时候 \(a_i + a_j > sum\)，那么这时候的 \(a_j\) 肯定不是答案之一，因为前面的 \(a_i\) 已经看过了，后面的都是更大的，因此这时候 \(j -= 1\)。


  * 如果这时候若 \(a_i + a_j < sum\)，同样的，\(a_i\) 肯定不是答案之一，因为两个指针再往中间的话，\(a_j\) 是越来越小的，因此 \(i += 1\)。


OK，完整的代码如下：


    #include <iostream>
    #include <vector>
    #include <bitset>

    using namespace std;



    class Solution {
    public:
    	vector<int> FindNumbersWithSum(vector<int> data, int sum) {
    		vector<int> res;
    		if (data.size() < 2) {
    			return res;
    		}
    		int start = 0, end = data.size() - 1;
    		long curSum;
    		while (start < end) {
    			curSum = data[start] + data[end];
    			if (curSum == sum) {
    				//把取到的数存放起来，这个时候的这两个值的乘积肯定是最小的
    				res.push_back(data[start]);
    				res.push_back(data[end]);
    				break;
    			}
    			else if (curSum < sum) {
    				start++;//如果和比较小，就把小的数增大一些
    			}
    			else {
    				end--;//如果和比较大，就把大的数减小一些
    			}
    		}
    		return res;
    	}
    };

    int main() {
    	Solution solu;
    	int arr[] = { 1, 2, 4, 7, 11, 15, };
    	vector<int> vec(arr, arr + 6);
    	vector<int> res=solu.FindNumbersWithSum(vec, 15);
    	for each (int var in res) {
    		cout << var << "  ";
    	}
    	cout << endl;
    	return 0;
    }


输出：


    4  11











# 题目 2




## **题目描述**


输出所有和为 S 的连续正数序列。序列内按照从小至大的顺序，序列间按照开始数字从小到大的顺序。例如：输入 5，输出：2，3


# 题目解答 2




## 移动 start 和 end 来靠近


我们可以仿照上面的思路，移动这个两端。

OK，我们用两个数 start 和 end 分别表示序列的最小值和最大值。我们把 start 初始化为 1,  end 初始化为 2：




  * 如果从 start 到 end 的序列的和大于 s，我们可以从序列中去掉较小的值，也就是增大 start 的值。


  * 如果从 start 到 end 的序列的和小于 s，我们可以增大 end ，让这个序列包含更多的数字。


因为这个序列至少要有两个数字，我们一直增加 start 到 (1+s)/2 为止。

OK，下面，我们和为 9 的所有连续序列为例：


  * 我们先把 start 初始化为 1 ,  end 初始化为 2 ，这个时候介于start和end之间的序列是 {1,2}。


  * 由于此时的序列的和为 3，小于 9，所以我们下一步要让序列包含更多的数字。我们把 end 增加 1 变成 3，此时序列为 {I,2,3} 。


  * 由于序列的和是6，仍然小于9，我们接下来再增加 end 变成 4 ，介于 start 和 end 之间的序列也随之变成  {l,2,3,4}。


  * 由于序列的和 10 大于 9，因此我们要删去序列中的一些数字， 于是我们增加 start 变成 2 ，此时得到的序列是 {2, 3, 4} 。


  * 序列的和正好是 9 ，因此，我们找到了第一个和为 9 的连续序列，把它打印出来。


  * 接下来我们再增加 big ，重复前面的过程，可以找到第二个和为 9 的连续序列 {4,5} 。


  * 直到 start 到 (1+s)/2  或 end 到 s 为止。


代码如下：


    #include <iostream>
    #include <vector>
    using namespace std;


    class Solution {
    public:
    	vector< vector<int> > FindContinuousSequence(int sum) {
    		vector< vector<int> > res;//用来存放找到的队列
    		vector<int> currRes;//当前的队列
    		if (sum < 3) {
    			return res;
    		}
    		int start = 1, end = 2;
    		int mid = (sum + 1) / 2;//用来作为start的上限
    		int currSum = start + end;
    		while (start < mid && end < sum) {
    			if (currSum == sum) {
    				// 如果和是sum, 就存储下来
    				currRes.clear();
    				for (int i = start; i <= end; i++) {
    					cout << i << " ";
    					currRes.push_back(i);
    				}
    				cout << endl;
    				res.push_back(currRes);

    				// 存储完以后, 进一步往下走
    				end++;
    				currSum += end;
    			}
    			else if (currSum > sum) {
    				// 如果和太大了, 缩短起始位置
    				currSum -= start;
    				start++;
    			}
    			else if (currSum < sum) {
    				//  如果和太小了, 那么增加结束位置
    				end++;
    				currSum += end;
    			}
    		}
    		return res;
    	}
    };

    int main() {
    	Solution solu;
    	vector< vector<int> > res = solu.FindContinuousSequence(200);

    	//打印出结果
    	cout << "Total Count = " << res.size() << endl;
    	for (unsigned int i = 0; i < res.size(); i++) {
    		cout << "index = " << i << ", Size = " << res[i].size() << endl;
    		for (unsigned int j = 0; j < res[i].size(); j++) {
    			cout << res[i][j] << " ";
    		}
    		cout << endl;
    	}
    	return 0;
    }


输出：


    5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20
    38 39 40 41 42
    Total Count = 2
    index = 0, Size = 16
    5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20
    index = 1, Size = 5
    38 39 40 41 42


嗯，还是很厉害的，我之前还以为要按照 n(n+1)/2 来计算什么的，没想到是这种两边移动来靠近的，利害。

















* * *





# COMMENT
