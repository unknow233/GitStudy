# 《剑指offer：专项训练》

## 整数

### 整数除法

`题目：输入2个int型整数，它们进行除法计算并返回商，要求不得使用乘号'*'、除号'/'及求余符号'%'。当发生溢出时，返回最大的整数值。假设除数不为0。例如，输入15和2，输出15/2的结果，即7。`

**解法1:被除数不断减除数**

时间复杂度:O(n) n为除的次数,当被除数为1时,情况最差

空间:O(1)

**解法2:被除数与除数*2^k比较**

先比较除数和被除数的大小关系

> 小于:除数*2^k(k递增)
>
> 等于:被除数-除数,跳出循环,return 目前的商之和
>
> 大于等于:被除数-=除数/2  (减去已知最大的情况),k归零,商+=2^k-1;

继续循环判断

#### 细节分析

1. 被除数为负数时,如果采用将所有负数变为正数的情况,-2^31转为正数为2^31,而整数的范围是2^31-1 ~ -2^31,直接给负肯定会溢出,所以最终考虑**将所有正数变为负数**
2. 导致溢出的运算 (-2^31)/(-1)=2^31,输入的数字一定不会是正数2^31

**数学基础:**

2+2=2*2=2^2;

2^3=2^2+2^2

2^4=2^3+2^3

....

不使用*时,加法可以实现n\*(2的次幂运算)

**English**

| 除数   | divisor  |
| ------ | -------- |
| 被除数 | dividend |
| 商     | consult  |
| 负数   | negative |
| 除法   | division |

#### 最终思路:

main:

1. 被除数为0x80000000(原码:10000......),除数为-1时 提示被除数太大
2. 被除数>0时,取反,除数>0,取反(需要根据取反次数判断最终是否应该再取反)
3. 进行负数的除法运算
4. 根据第二步进行判断是否对结果取反

负数的除法运算:

1. 需要的变量:商temp,除数temp

1. 先比较被除数和除数的大小关系

   > 小于:除数temp+=除数temp	商temp+=商temp
   >
   > 等于:跳出循环,return 商+商temp
   >
   > 大于:被除数-=除数 ,商+=商temp

继续循环判断

结束循环条件:被除数<=原本的除数

e.g.

> -15/-2
>
> ---------------------
>
> -15 < -2\*2^0	-15 <-2\*2^1	-15<-2*2^2	15>-2\*2^3 
>
> 被除数+=8	被除数=-7	商为2^2
>
> -7<-2	-7<-2*2^1	-7>-8
>
> 被除数+=4	被除数=-3	商为2^1
>
> 3>2	3<4
>
> 被除数-=-2	被除数=-1	商为1
>
> 被除数小于原来的除数,结束	商为4+2+1=7



#### 代码实现:

```c++
#include<iostream>
using namespace std;
//int negaDiv(int dividend, int divisor) {
//	int divisorTemp = divisor;
//	int resultTemp = 1;
//	int result = 0;
//	while (dividend < divisor) {
//		if (divisorTemp > dividend) {
//			divisorTemp += divisorTemp;
//			resultTemp += resultTemp;
//		}
//		else if (dividend == divisor) {
//			result += resultTemp;
//			break;
//		}
//		else {
//			dividend -= divisorTemp;
//			result += resultTemp;
//		}
//	}
//	return result;
//}
int negaDiv(int dividend, int divisor) {
	int result = 0;
	while (dividend<=divisor)
	{
		int value = divisor;
		int quotient = 1;
		while (value>=0xc0000000&&dividend<=value+value) {
			quotient += quotient;
			value += value;
		}
		result += quotient;
		dividend -= value;
	}
	return result;
}


int division(int dividend,int divisor) {
	if (dividend == 0x80000000 && divisor == -1) {
		printf("被除数太大");
		return 0;
	}
	int reverseFlag = 2;
	if (dividend > 0) {
		reverseFlag--;
		dividend = -dividend;
	}

	if (divisor > 0) {
		reverseFlag--;
		divisor = -divisor;
	}
	int result=negaDiv(dividend, divisor);
	if (reverseFlag == 1) {
		return 0 - result;
	}
	return result;
}

int main() {
	int dived = 0xf;
	int divnum = 3;
	int result = division(dived, divnum);
	cout << result << endl;
}
```

## 数组

### 双指针

#### 排序数组中两个数字之和

`已知:递增排序的数组和k,如何在数组中找到两个和为k的数字,并返回其下标(找到一组即可)`

思路:

初始化指针p1在第一个元素,p2在最后一个元素.当p1,p2之和大于k,说明p2应该左移,反之,p1右移

**pay attention**

双指针一般用特殊的形式和原来绑定,并不应该直接用双指针的变量去进行比较或其他操作

##### 实现代码

```c++
vector<int> twoSum(vector<int>& nums, int target) {
        int p1=0,p2=nums.size()-1;
        vector<int> result;
        while(p1<p2){
            if(nums[p1]+nums[p2]>target){
                p2--;
            }else if(nums[p1]+nums[p2]==target){
                result.push_back(nums[p1]);
                result.push_back(nums[p2]);
                return result;
            }else{
                p1++;
            }
        }
        return result;
    }
```

#### [三数之和](https://leetcode.cn/problems/1fGaJU/)

给定一个包含 `n` 个整数的数组 `nums`，判断 `nums` 中是否存在三个元素 `a` ，`b` ，`c` *，*使得 `a + b + c = 0` ？请找出所有和为 `0` 且 **不重复** 的三元组。

##### 思路

是  ` 排序数组中两个数字之和`的加强版,可以采用固定一个数a后,找另外两个和为-a的数,当遍历到当前元素大于0时,应该结束

细节分析:

如何避免重复的元素

> 固定位置的元素每次遇到和前一个位置相同的,就跳过,只比较不同的

vector<vector\<int\>> threeSum(vector\<int\>& nums):

1. 排序
2. 固定一个位置,比较后面是否有符合条件的元素,如果有就加入结果集,在SumNum()实现
3. 移动下一个位置(应该移动到元素不同时),回到2,直到该位置对应元素>0,或者到数组尾

SumNum(vector<int> nums,int i,vector<vector\<int\>>& result ):

1. 采用双指针形式:初始化p1=i+1,p2=nums.size()-1
2. 循环判断(p1<p2):
   1. sum<0:while(p1<p2&&nums[p1]==nums[p1+1])  p1++
   2. sum==0:push_back(),while(p1<p2&&nums[p2]==nums[p2-1]) p2--;
   3. sum>0:while(p1<p2&&nums[p2]==nums[p2-1]) p2--;

##### 实现代码:

```c++
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        vector<vector<int>> result;
        if(nums.size()>=3){
        sort(nums.begin(),nums.end());
        int i=0;
        while(i<nums.size()-2){
            if(nums[i]>0) break;
            FindTWO(nums,i,result);
            int temp=nums[i];
            while(i<nums.size()-2&&nums[i]==temp)  i++;
        }
        }
        return result;
    }
    void FindTWO(vector<int> nums,int i,vector<vector<int>>& result ){
int j=i+1;
int k=nums.size()-1;
while(j<k){
    if(SUM(nums[i],nums[j],nums[k])<0){
        int temp=nums[j];
        while(j<=k&&nums[j]==temp) 
        j++;
    }else if(SUM(nums[i],nums[j],nums[k])==0){
        vector<int> temp;
        temp.push_back(nums[i]);
        temp.push_back(nums[j]);
        temp.push_back(nums[k]);
        result.push_back(temp);
        int itemp=nums[k];
        while(j<=k&&nums[k]==itemp){
            k--;
        }
    }else{
        int itemp=nums[k];
        while(j<=k&&nums[k]==itemp){
            k--;
        }
    }
}
  return;
    }
    int SUM(int a,int b,int c){
     return a+b+c;
 }
};
```



##### 复杂度分析:

时间复杂度:

空间复杂度:

**English**

| fixed | 固定的 |
| ----- | ------ |



#### 和大于或等于k的最短子数组

给定一个含有 `n` 个正整数的数组和一个正整数 `target` **。**

找出该数组中满足其和 `≥ target` 的长度最小的 **连续子数组** `[numsl, numsl+1, ..., numsr-1, numsr]` ，并返回其长度**。**如果不存在符合条件的子数组，返回 `0` 。



思路:

1. 初始化指针p1,p2都指向第一个元素

2. 比较当前数组之和与k的大小关系

   1. p1,p2指向相同位置时,只用一个

   2. sum<k:应该增加一个数组,即p2++

   3. sum>=k:继续向后判断是否有更合适的,并记录当前的位置及长度

      > 向后判断:p1++后进入循环

   循环结束条件:p1<=p2

   p1>p2,存在没有完全遍历的情况,但是当p1>p2,说明当前已经存在最优解,长度仅为1

细节分析:

在记录当前长度时应该先判断是否是较小值,为了方便,len定义为unsigned类型,初始值为-1

因为存在没有这样子数组的情况,所以,当最后len仍为-1时,应该返回的是0

##### 实现代码:

```c++
nt SumArr(vector<int>& nums,int start,int end){
    int sum=0;
    for(int i=start;i<=end;i++){
        sum+=nums[i];
    }
    return sum;
}
    int minSubArrayLen(int target, vector<int>& nums) {
        int p1=0,p2=0;
        unsigned int len=-1;//方便之后取较小值
        int sum=0;
        while(p1<=p2&&p2<nums.size()){
            sum=SumArr(nums,p1,p2);
            if(sum<target) p2++;
            else{
                if(p2-p1+1<len) len=p2-p1+1;
                p1++;
            } 
        }
        if(len==-1) len=0;
        return len;
    }
```


