## 题解：11，35，56，57

#### 11. Container With Most Water

Solution:

```c++
class Solution {
public:
    int maxArea(vector<int>& height) {
        int res=0;
        if(height.size()==0)return res;
        int i=0,j=height.size()-1;
        while(i<j){
            res=max(res,min(height[i],height[j])*(j-i));
            if(height[i]>=height[j])j--;
            else i++;
        }
        return res;        
    }
};
```

这道题可以采用双指针法。值得注意的是，容器中的水容量取决于两个数的较小者及两个数索引的距离。因为采用两个指针，一个放置在数组的开头，一个在结尾。采用一个变量res去记录遍历过程中的最大值。每一个step都更新res并移动较小的数的指针至较大数的方向。

#### 35. Search Insert Position 

Solution:

```c++
class Solution {
public:
    int searchInsert(vector<int>& nums, int target) {

        if(nums.size()==0)return 0;
        int left=0,right=nums.size()-1;
        while(left<=right){
            int mid=(left+right)/2;
            if(nums[mid]==target)return mid;          
            else if(nums[mid]<target){
                left=mid+1;
            }            
            else{
                right=mid-1;
            }
        }        
        return left;
    }
};
```

因为该题中的数组为有序数组，因此可以考虑采用二分查找法。时间复杂度为$logN$. 其操作如下：

（1）设定左侧下标$left$及右侧下标$right$，并计算其中间值$mid$。

（2）将 $nums[mid]$与$target$进行比较，当$nums[mid]<target$时，更新$left=mid+1$; 当$nums[mid]>target$时，更新$right=mid-1$;当$nums[mid]=target$时，返回值。

   (3)   返回此时的$nums[left]$。

#### 56.  Merge Intervals

Solution:

```c++
class Solution {
public:
    vector<vector<int>> merge(vector<vector<int>>& intervals) {       
        vector<vector<int>>res;
        sort(intervals.begin(),intervals.end());
        for(int i=0;i<intervals.size();++i){
            if(res.empty()||res.back()[1]<intervals[i][0])res.push_back(intervals[i]);
            else if(res.back()[1]>=intervals[i][1])continue;
            else res.back()[1]=intervals[i][1];
        }
        return res;
    }
};
```

这道理很容易想到的方法就是直接遍历，判断是否有重合部分。但是这样的时间复杂度很高，因为比如$[[5,6],[3,4],[3,7]]$进行合并区间，遍历时$[3,7]$其实包含于其他区间 ，如果对区间头进行排序，时间复杂度可以从$O(N^2)$降低至$O(NlogN)$.

因此，先对每个区间的第一个数采用快速排序，再对区间和进行遍历，当前一个区间的最后一个数小于下一个区间第一个数时，直接添加下一个区间，反之将最后一个区间的最后一个数改为下一个区间的最后一个数。

#### 57. Insert Interval

Solution1:

```c++
class Solution {
public:
    vector<vector<int>> insert(vector<vector<int>>& intervals, vector<int>& newInterval) {        
        intervals.push_back(newInterval);
        merge(interval);
    }
    vector<vector<int>> merge(vector<vector<int>>& intervals) {       
        vector<vector<int>>res;
        sort(intervals.begin(),intervals.end());
        for(int i=0;i<intervals.size();++i){
            if(res.empty()||res.back()[1]<intervals[i][0])res.push_back(intervals[i]);
            else if(res.back()[1]>=intervals[i][1])continue;
            else res.back()[1]=intervals[i][1];
        }
        return res;
    }
};
```

解法一和56的解法相同，其实就是在$intervals$中插入$newInterval$再进行合并区间，该算法的时间复杂度有$O(NlogN)$.

Solution2:

```C++
class Solution {
public:
    vector<vector<int>> insert(vector<vector<int>>& intervals, vector<int>& newInterval) {  
        vector<vector<int>>res;
        int i=0;
        for(i;i<intervals.size()&&intervals[i][0]<=newInterval[0];++i){
            res.push_back(intervals[i]);    
        }        
        if(res.empty()||res.back()[1]<newInterval[0])res.push_back(newInterval);
        else if(res.back()[1]<newInterval[1])res.back()[1]=newInterval[1];
        for(i;i<intervals.size();++i){
            if(res.back()[1]<intervals[i][0])res.push_back(intervals[i]);
            else if(res.back()[1]<intervals[i][1])res.back()[1]=intervals[i][1];
        }
        return res;       
    }
};
```

与56不同，区间是有序区间，因为我们可以只遍历一次。其步骤如下：

（1）先插入$intervals$中小于$newInterval$的区间；

（2）采用与56一样的思想合并已经插入的区间和$res$和$newInterval$；

（3）同（2），合并$res$与剩余的区间。