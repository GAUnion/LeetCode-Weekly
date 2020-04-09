# 题解42，81，128，152

## 42.接雨水

Solution：

```c++
class Solution {
public:
    int trap(vector<int>& height) {
        int len=height.size();
        int ret=0;
        int l,r,lmax,rmax;
        for (int i=0;i<len;i++)
        {
            lmax=height[i];
            rmax=height[i];
            for(l=i-1;l>=0;l--)
                if(height[l]>lmax)lmax=height[l];
            for(r=i+1;r<len;r++)
                if(height[r]>rmax)rmax=height[r];
            ret+=min(rmax,lmax)-height[i];
        }
        return ret;
    }
};
```

遍历数组，每一个位置能接的雨水的高度等于该位置左边最高的柱子以及右边最高的柱子的最小值减去当前位置柱子的高度。

若rmax代表右边最高的柱子，lmax代表左边最高的柱子，则当前位置雨水高度为$min(rmax,lmax)-height[i]$

## 81.搜索旋转排序数组II

Solution:

```c++
class Solution {
public:
    bool search(vector<int>& nums, int target) {
        bool ret=false;
        int l,r;
        int flag=0;
        l=0;r=nums.size()-1;
        if(l==r)
            if(nums[l]==target)ret=true;
        while (l<r)
        {
            if(r-l==1)
            {
                if(nums[l]==target||nums[r]==target)
                    ret=true;
                break;
            }
            int mid=(l+r)/2;
            if(target==nums[mid]||target==nums[l]||target==nums[r]){
                ret=true;
                break;
            }
            if(nums[l]<nums[r])flag=1;

            while(mid<r&&nums[mid]==nums[r])r--;
            mid=(l+r)/2;

            if(flag==0)
                if(nums[mid]<=nums[r]&&nums[mid]<nums[l])
                    if(target>nums[mid]&&target<nums[r])
                    {
                        flag=1;
                        l=mid;
                    }
                    else
                    {
                        flag=0;
                        r=mid;
                    }
                else if(nums[mid]>=nums[r])
                    if(target>nums[l]&&target<nums[mid])
                    {
                        flag=1;
                        r=mid;
                    }
                    else
                    {
                        flag=0;
                        l=mid;
                    }

            if(flag==1)
            {
                if(target>nums[l]&&target<nums[mid])r=mid;
                else if(target>nums[mid]&&target<nums[r])l=mid;
                else if(target>nums[r]||target<nums[l])break;
            }
        }

        return ret;
    }
};
```

首先想到的是对二分查找加以改变。如果在一段待查找的数组中，数组左端小于数组右端，则说明数组中不存在旋转，即完全升序排序，可以按照普通二分查找进行。此处使用flag来标记，flag为0时表示待查找的是旋转数组，flag为1表示带查找的是完全升序数组。

而对于一段旋转数组，有四种情况。如果数组中间小于数组右端且小于数组左端，此时数组左半部分是一个旋转数组，右半部分是一个升序数组。在这种情况下，若目标值大于数组中间且小于数组右端，则在右半部分继续查找，令flag为1；否则在左半部分继续查找，flag仍为0。另外两种情况是数组中间大于数组右端时，同理可知。

此处还有一个问题，由于数组中存在重复元素，可能使得数组中间与数组右端或左端（或同时）相等。此处进行判定，当数组中间等于数组右端时，将数组右端向左移动到第一个不同的值，重新进行查找。

## 128.最长连续子序列

Solution:

```c++
class Solution {
public:
    int longestConsecutive(vector<int>& nums) {
        unordered_set<int> st;
        for(int n: nums) st.insert(n);
        int ans = 0;
        for(int i: st){
            if(st.count(i-1))continue;

            int cnt = 1;
            while( st.count(i+1)){
                cnt ++;
                i++;
            }
            ans = max(ans, cnt);
        }
        return ans;
    }
};
```

使用哈希表记录每一个元素是否存在。

之后遍历数组，当一个元素存在比他小1的元素时，不可以作为序列开头。当不存在比其小1的元素时，累计得到以该元素为开头的最长连续序列。

## 152.乘积最大子数组

Solution：

```c++
class Solution {
public:
    int maxProduct(vector<int>& nums) {
        int len = nums.size();
        if(len == 1)return nums[0];
        
        int p = nums[0];
        int maxP = nums[0];
        int minP = nums[0];
        for(int i = 1; i < len; i++) {
            int tem = maxP;
            maxP = max(max(maxP * nums[i], nums[i]), minP * nums[i]);
            minP = min(min(tem * nums[i], nums[i]), minP * nums[i]);
            p = max(maxP, p);
        }
        return p;
    }
};
```

使用动态规划的方式，其中需要维护两个数组，分别是当前最大乘积和当前最小乘积，公式如下，分别为maxDP和minDP。因为负负得正，所以当遇到负整数时，当前最大乘积可以是前一步的最小乘积乘当前的负整数。当遇到0时，需要重新累计乘积。

$maxDP[i + 1] = max(maxDP[i] * nums[i + 1], nums[i + 1],minDP[i] * nums[i + 1])$
$minDP[i + 1] = min(minDP[i] * nums[i + 1], nums[i + 1],maxDP[i] * nums[i + 1])$
$dp[i + 1] = max(dp[i], maxDP[i + 1])$

