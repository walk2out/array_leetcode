# array_leetcode
leetcode刷题总结，此部分是array

#第一题

问题：
Given an array of integers, return indices of the two numbers such that they add up to a specific target.

程序：

#include<vector>
using namespace std;
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
         vector<int> index;
        for(int i=0;i<nums.size();i++)
        {
            for(int j=i+1;j<nums.size();j++)
            {
                if(nums[i]+nums[j]==target)
                {
                    index.push_back(i);index.push_back(j);
                    return index;
                }
            }
        }
        
    }
};

#第二题

问题：
There are two sorted arrays nums1 and nums2 of size m and n respectively.
Find the median of the two sorted arrays. The overall run time complexity should be O(log (m+n)).

分析：一开始无从下手，看了前两个参考https://discuss.leetcode.com/topic/4996/share-my-o-log-min-m-n-solution-with-explanation，
然后梳理出思路

1、首先常见的思路是暴力算法，即将两个数组排序，时间复杂度是O(m+n)，显然这不是我们要的；

2、分析中位数的特性和找一个数组中的中位数有哪些算法，一个排序
好的数组中位数显然O(1)计算出来，而中位数是一个划分，使得左边集合元素都小于右边集合元素；

3、利用中位数和已排好序的特性，分别在两个数组中找划分，而因为
两个数组左右划分集合元素个数之和相等，所以需要在第一个数组中找到合适的划分；而判断划分是否合适需要常数时间；4、找划分实际上可以利用二分法搜索

具体三部分：

1、确定划分，第一个数组一共有n+1个划分位置，第一个数组划分位置为i，左侧集合E(L_A)，右侧集合E(R_A)，左侧最大元素为L_A=A[i-1]，右侧最小元素R_A=A[i]；
第二个数组划分j=(m+n)/2-i，左侧集合E(L_B)，右侧集合E(R_B)，左侧最大元素为L_B=B[j-1]，右侧最小元素R_B=A[j]；

2、判断划分是否满足要求： max(L_A,R_A) <= min(R_A,R_B)，

3、二分法搜索合适的i，若max(L_A,R_A)<=min(R_A,R_B)，则符合要求，否则max(L_A,R_A)>min(R_A,R_B)；而因为L_A<=R_A，且L_B<=R_B，所以L_A>R_B，或者L_B>R_B；若L_A>R_B，则E(L_A)元素偏大，则i需左移，即在左侧寻找，若L_B>R_A，则E(R_A)元素偏小，则i需右移，即在右侧寻找

4、细节调整
low=max(0,(n-m)/2), high=min(n,(m+n)/2)

程序：

class Solution {
public:
	double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {
		int m = nums1.size(), n = nums2.size();
		if (m > n) return findMedianSortedArrays(nums2, nums1);

		int low = 0, high = min(m, (m + n) / 2);
		int L_A, R_A, L_B, R_B;
		int left, right,i,j;
		while (low <= high)
		{
			i = (low + high) / 2;
		    j = (m + n) / 2 - i;
			//step1 划分赋值
			//左侧赋值
			if (i != 0) L_A = nums1[i - 1];
			if (i != m) R_A = nums1[i];
			//右侧赋值
			if (j !=0) L_B = nums2[j - 1];
			if (j != n) R_B = nums2[j];
			//step2  判断是否满足条件，若满足，则终止寻找，否则二分法寻找
			if (i > 0 && j<n && L_A>R_B)
				high = i - 1;
			else
				if (j > 0 && i<n && L_B>R_A)
					low = i + 1;
				else
					break;
			
		}
		//step3 根据奇偶判断返回
		if ((m + n) % 2 == 0)
		{
			//左侧集合最大值
			if (i == 0)
				left = L_B;
			else
				if (j == 0)
					left = L_A;
				else
					left = max(L_A, L_B);
			//右侧集合最小值
			if (i == m)
				right = R_B;
			else
				if (j == n)
					right = R_A;
				else
					right = min(R_A, R_B);
			//返回
			return((left+right) / 2.0);
		}
		else
			if (i == m)
				return R_B;
			else
				if(j==n)
					return R_A;
		    else
				return(min(R_A, R_B));
	}
};

第三题
问题：Given n non-negative integers a1, a2, ..., an, where each represents a point at coordinate (i, ai). n vertical lines are drawn such that the two endpoints of line i is at (i, ai) and (i, 0). Find two lines, which together with x-axis forms a container, such that the container contains the most water.

分析：条件反射是暴力求解，但是时间性能不允许；参考discussion，结合自己的理解，原来利用两端处，例如左端高度小于右端，由于容器装水依赖于次高，所以左端点不动，右端移动会导致宽度减少，并且次大肯定小于等于左端高度，所以左端点处不用再考虑，同理逐步移动，只需O(n)

程序：

class Solution {
        public:
            int maxArea(vector<int>& height) {
                int max = 0;
                int i = 0, j = height.size() - 1;
                while(i < j)
                {
                    max = max(max, min(height[i], height[j]) * (j - i));
                    if(height[i] < height[j])
                        i++;
                    else
                        j--;
                }
                
                return max;
            }
        };
        
第四题

问题：Given an array S of n integers, are there elements a, b, c in S such that a + b + c = 0? Find all unique triplets in the array which gives the sum of zero.

分析：既可以利用two sum的想法，也可以按照这位大神的做法https://discuss.leetcode.com/topic/8107/share-my-ac-c-solution-around-50ms-o-n-n-with-explanation-and-comments/2 ，先排序，然后利用木桶方法。

第五题

问题：Given an array S of n integers, find three integers in S such that the sum is closest to a given number, target. Return the sum of the three integers. You may assume that each input would have exactly one solution.

分析：还是可以利用3 sum来解决。

第42题：
问题：Given n non-negative integers representing an elevation map where the width of each bar is 1, compute how much water it is able to trap after raining.

分析：木桶盛水短板原理，从两边往中间逼近，当前两块板间的盛水高度依赖于较短的一块，慢慢往中间逼近。

程序：
class Solution {
public:
	int trap(vector<int>& height) {
		if (!height.size())
			return 0;
		//首先建立数组储存高度
		vector<int> height_new;
		height_new.push_back(height[0]);
		for (int i = 0;i < height.size()-1;i++)
		{
			height_new.push_back(max(height[i],height[i+1]));
		}
		height_new.push_back(height[height.size()-1]);
		//初始化
		int l = 0;int r = height_new.size()-1;
		int h_l = height_new[l];int h_r = height_new[r];
		int h = 0;
		int container=0;
		//循环
		while (l < r)
		{
			if (height_new[l] < height_new[r])
			{
				container = container + (height_new[l]-h) *(r - l);
				h = height_new[l];
				for (int i = l + 1;i <= r;i++)
				{
					if (height_new[i] >= height_new[l])
					{
						l = i;
						break;
					}
				}
			}
			else
			{
				container = container + (height_new[r]-h) * (r - l);
				h = height_new[r];
				for (int i = r - 1;i >= l;i--)
				{
					if (height_new[i] >= height_new[r]) {
						r = i;
						break;
					}
				}

			}
		}
		//除掉本身的水量
		for (int i = 0;i < height.size();i++)
			container = container - height[i];
		return container;
	}
};


第53题：

问题：Find the contiguous subarray within an array (containing at least one number) which has the largest sum.

分析：一种思路是直接所有相加，时间性能是O（n^2），时间不允许；另一种是所有和先相加，然后从中找出落差最大且是是上升段，即为最大区间，但是很多情况，不知道如何统一处理，无法继续下去;还有一种想法从左往右加，若和小于零，则丢掉这部分，但是受制于想要求出区间的想法，无法继续思考下去

参考：
1）最简单的程序
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        int ans=nums[0],i,j,sum=0;
        for(i=0;i<nums.size();i++){
            sum+=nums[i];
            ans=max(sum,ans);
            sum=max(sum,0);
        }
        return ans;
    }
};
这个程序每次求和从零开始，若和小于零，则再从零开始。我当时也有这个想法，但是不知道如何继续下去

2）求区间法程序
class Solution {
public:
    int maxSubArray(int A[], int n) {
        int sum = 0, min = 0, res = A[0];
        for(int i = 0; i < n; i++) {
            sum += A[i];
            if(sum - min > res) res = sum - min;
            if(sum < min) min = sum;
        }
        return res;
    }
};
我也是这么想的，但是就是无法简洁的写出来；实际上还是重新归零计算

这是我的程序
class Solution {
public:
	int maxSubArray(vector<int>& nums) {
	    std::vector<int>::iterator maxest = std::max_element(std::begin(nums), std::end(nums));
	    if(*maxest<0)
	        return *maxest;
		
		vector<int> sums;
		int sum = 0;
		sums.push_back(sum);
		for (int i = 0;i < nums.size();i++)
		{
			sum = sums[i]+nums[i];
			sums.push_back(sum);
		}
		int max=1;
		for(int i=2;i<sums.size();i++)
		    if(sums[i]>=sums[max]) max=i;
		int min=0;
		for(int i=1;i<=max;i++)
		    if(sums[i]<sums[min]) min=i;
        int res1=sums[max] - sums[min];
		
		min=0;
		for(int i=1;i<sums.size();i++)
		    if(sums[i]<sums[min]) min=i;
		max=min;
		for(int i=min;i<sums.size();i++)
		    if(sums[i]>=sums[max]) max=i;
        int res2=sums[max] - sums[min];
        
        if(res1<res2)
            return res2;
        else
            return res1;
	}
};

很复杂，结果还不对

3）总结原因
没有动态思想


第66题：
问题：Given a non-negative integer represented as a non-empty array of digits, plus one to the integer.
You may assume the integer do not contain any leading zero, except the number 0 itself.
The digits are stored such that the most significant digit is at the head of the list.

分析：一开始没看懂题意，直接copy别人的程序

程序：


第88题：
问题：Given two sorted integer arrays nums1 and nums2, merge nums2 into nums1 as one sorted array.

分析：本质没有什么问题，但是难点在于m和n给的让人摸不着头脑；意思是说nums1长度为m+n，但是初始m

程序：
class Solution {
public:
	void merge(vector<int>& nums1, int m, vector<int>& nums2, int n) {
		int i = m - 1, j = n - 1, k = m + n - 1;
		while (j>=0)
		{
			if (i >= 0 && nums1[i] >= nums2[j])
				nums1[k--] = nums1[i--];
			else
				nums1[k--] = nums2[j--];
		}
	}
};
