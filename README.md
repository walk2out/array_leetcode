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

分析：一开始无从下手，看了前两个参考https://discuss.leetcode.com/topic/4996/share-my-o-log-min-m-n-solution-with-explanation，然后梳理出思路

1、首先常见的思路是暴力算法，即将两个数组排序，时间复杂度是O(m+n)，显然这不是我们要的；

2、分析中位数的特性和找一个数组中的中位数有哪些算法，一个排序
好的数组中位数显然O(1)计算出来，而中位数是一个划分，使得左边集合元素都小于右边集合元素；

3、利用中位数和已排好序的特性，分别在两个数组中找划分，而因为
两个数组左右划分集合元素个数之和相等，所以需要在第一个数组中找到合适的划分；而判断划分是否合适需要常数时间；4、找划分实际上可以利用二分法搜索

具体三部分：

1、确定划分，第一个数组一共有n+1个划分位置，第一个数组划分位置为i，左侧集合E(L_A)，右侧集合E(R_A)，左侧最大元素为L_A=A[i-1]，右侧最小元素R_A=A[i]；
第二个数组划分j=(m+n)/2-i，左侧集合E(L_B)，右侧集合E(R_B)，左侧最大元素为L_B=B[j-1]，右侧最小元素R_B=A[j]；

2、判断划分是否满足要求：max(L_A,R_A)<=min(R_A,R_B)，

3、二分法搜索合适的i，若max(L_A,R_A)<=min(R_A,R_B)，则符合要求，否则max(L_A,R_A)>min(R_A,R_B)；而因为L_A<=R_A，且L_B<=R_B，所以L_A>R_B，或者L_B
>R_B；若L_A>R_B，则E(L_A)元素偏大，则i需左移，即在左侧寻找，若L_B>R_A，则E(R_A)元素偏小，则i需右移，即在右侧寻找

程序：
