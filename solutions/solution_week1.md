## 题解：26,27,41,55,80,118,119,134,169,189,283,299,442,448

### 26.Remove Duplicates from Sorted Array

Solution：

```python
class Solution:
    def removeDuplicates(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        len_ = 1
        if len(nums)==0:
            return 0
        for i in range(1,len(nums)):
            if nums[i] != nums[i-1]:
                nums[len_] = nums[i]
                len_ +=1
        return len_
```

该解法核心在于记录两个指针，一个指针用于记录不重复的数（记为慢指针len_），一个指针用于遍历整个数组（记为快指针i）。慢指针和快指针一开始都在起点，快指针先跑，遇到与慢指针不同的数慢指针才往前跑并记录快指针的数，直到快指针跑到终点（数组尾）

这个方法简单易懂，两个指针的方法也具有在in-place问题上也有一定的泛用性。而个人认为这个方法的关键在于数组是已经排好序的。

### 27. Remove Element

  Solution：

```python
class Solution(object):
    def removeElement(self, nums, val):
        """
        :type nums: List[int]
        :type val: int
        :rtype: int
        """
        point = pos = 0
        while point < len(nums):
            if nums[point]==val:
                point+=1
            else:
                nums[pos]=nums[point]
                pos+=1
                point+=1
        return pos
```

该题与上题思路类似，也是用一个快指针一个慢指针来遍历数组，快指针找到与寻找值不相等的元素时，慢指针前移并且记录该元素的值，直至遍历结束即完成。

感觉in-place的这种题目都可以往这种思路上想。总体上是一个覆盖原数组的过程，关键在于快指针的判断条件能保证慢指针所覆盖上的新值都是满足条件的即可。

 

### 41. First Missing Positive：

Solution1：

```c++
class Solution {
public:
    int firstMissingPositive(vector<int>& nums) {

      int l=nums.size();

      for(int i=0;i<l;++i){
          while(nums[i]!=i+1&&nums[i]>0&&nums[i]<=l&&nums[nums[i]-1]!=nums[i]){
              int tmp;
              tmp=nums[nums[i]-1];
              nums[nums[i]-1]=nums[i];
              nums[i]=tmp;              
          }
      }

      for(int i=0;i<l;++i){
          if(nums[i]!=i+1)return i+1;
      }

      return l+1;
    
    }
};
```

首先感谢16同学的讲解，我大概懂了这个解法的思路。

首先我们是要找第一个缺失的正整数，我们会想到按照顺序寻找数组中的正整数，即整理后sums[0]应该是1，sums[1]应该是2，依次往后直到有正整数缺失。具体来说，把正整数从小到大排列，重复的不排列，比如[5,4,9,3,4,-1,2,1]排列成[1,2,3,4,5,…],省略号里的元素可以不管，5后面的元素是不符合规定的，然后我们程序返回6即可。

而最容易的想法是把数组的每一个位置寻找是否有满足条件的元素，但这样每找一个位置就需要遍历一次数组，时间复杂度很明显会到O（n2）

所以我们换种思路，把数组的每个元素进行整理，然后找缺失的正整数。我们从数组的第一个位置开始，看这个位置上的元素是否满足nums[i]=i+1,满足即表示是对的,而如果不满足就把这个元素放到对的位置，而程序中的nums[nums[i]-1]=nums[i]就会把这个元素放到正确的位置（这里可能要自己试一下就发现了），而我们是用交换的方法来把元素放到正确的位置的，交换过来肯定会有新的元素，我们就需要进行新的判断，这也是为什么程序要用while循环。

然后注意到有几种情况是不需要继续进行交换的，一种是该元素无法处理，比如是负数或者大于数组尺寸（这里我之前会想这个位置的元素是错的会不会换不过来，实际上遍历到后面会如果有满足的会把这个元素换走），还有一种不继续交换的情况就是nums[nums[i]-1]=nums[i]已经满足也不交换，因为相等了交换就没有意义还增加时间复杂度嘛。在第一个位置完成之后i就继续遍历剩下的数组，直至遍历完成再寻找缺失的即可。

这个排序可以借鉴的思路感觉是按照索引号排序，排序的方法十分巧妙，通过把元素放到正确的位置可以大大减少时间复杂度。

Solution2：

```python
class Solution(object):
    def firstMissingPositive(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        if nums == []:
            return 1
        maxn = max(nums)
        hashtable = {}
        for index, value in enumerate(nums):
            if value > 0:
                hashtable[value] = index+1
        for i in range(0,len(nums)):
            if i+1 not in hashtable:
                return i+1
        return maxn+1        
```

第二个方法思路就很简单了，通过建立一个哈希表来做，先把数组的元素都存入哈希表中，然后依次寻找1,2,3,4…是否在哈希表中，直到寻找到一个缺失的正整数。

这种解法的关键在于哈希表的in操作时间复杂度是O（1）

### 55. Jump Game

Solution1：

```python
class Solution(object):
    def canJump(self, nums):
        """
        :type nums: List[int]
        :rtype: bool
        """
        flag = 1
        if nums == [0]:
            return 1
        for i in range(len(nums)):
            if nums[i] == 0:
                if i!=len(nums)-1:
                    flag = 0
                    for j in range(1,i+1):
                        if nums[i-j]>j:
                            flag = 1
                    if(flag==0):
                        return flag
                elif (i==len(nums)-1) and (flag == 1):
                    flag = 0
                    for j in range(1,i+1):
                        if nums[i-j]>=j:
                            flag = 1
        return flag
```

第一种解法思路是寻找跳不过去的点，数组中出现5,4,3,2,1,0这种是跳不过去的，0前面这些位置有比他们小的元素也是跳不过去的，比如4,4,3,2,1,0这种。所以对于每一个数组中的0我们都需要对之前的元素依次进行判断来排查。如果数组中每个0都是可以跳过去的，则这个数组是满足的。需要注意的是这种解法需要考虑0出现在最后一个位置的特殊情况。

Solution2：

```python
class Solution:
    def canJump(self, nums: List[int]) -> bool:
        i, last_active = 0,0
        while i <= last_active:
            last_active = max(i + nums[i], last_active)
            if last_active >= len(nums)-1:
                return True
            else:
                i += 1
        return False
```

解法2则是找跳的到的点。思路是给数组每个节点都附加一个标记，如果能通过前面达到这个节点就是True，然后从第一个节点开始标记为True,看第一个节点的值，用当前节点的index加上他的值就是这个节点能到达的最远距离，这个距离里所有的节点都True，当把所有true的节点走一遍，看看走的过程中到达的地方是不是达到了最后一个节点，如果遍历完了还没到就返回False。个人感觉这个问题有一个点就是一个节点如果是true，则它之前的节点一定是true。

### 80. Remove Duplicates from Sorted Array II

Solution 1：

```python
class Solution(object):
    def removeDuplicates(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        counter = 1
        rlen = len(nums)
        i = 0
        while i < rlen-1:
            if nums[i+1] == nums[i]:
                counter+=1
                if counter >2:
                    for j in range(i+1,rlen-1):
                        nums[j] = nums[j+1]
                    rlen = rlen-1
                    counter = counter-1
                    i-=1
            else:
                counter = 1
            i+=1
        return rlen
```

这是最容易想到的解法，遍历数组然后用一个计数器来记录元素数量，超过两次的即删去（删去方法是元素整体前移），然后每次删去元素时需要把数组的有效长度减一。

Solution2：

```python
class Solution(object):
    def removeDuplicates(self, nums):
        '''
        image a 3-bit length slider that moves from the left to the right of the given list
        if the left side number of the slider equals to the right, then the slider moves to the next bit and does nothing;
        if not equal, then the left side number is an effective number.
        '''
        pos = 0  # recode effective numbers
        left = 0  # the left side of the slider
        right = 2  # the right side of the slider
        if len(nums) < 3:    # judge if it is the case that list length < 3
            return len(nums)

        while right < len(nums):
            if nums[left] != nums[right]:
                nums[pos] = nums[left]  # if not equal, the left side of the slider is effective, store it.
                pos += 1
            left += 1
            right += 1
        # the final step in the 'while' cycle is to compare nums[-3] and nums[-1], either equal or not equal,
        # this will makes sure the final 2 numbers be effective, store them.
        nums[pos] = nums[-2]
        nums[pos + 1] = nums[-1]
        return pos+2
```

 解法2的思路比较巧妙，其实也是之前26题的解法升级版，关键在于快指针变为了两个，一个left和一个right，而left始终比right要大于2，这样就能保证元素出现2次的情况是允许的。

对于这种排序好的数组删除元素，我觉得都可以用这种思路去想，而快指针用一个区间来表示的方法也很新颖。

### 118.Pascal‘s Triangle

Solution ：

```python
class Solution(object):
    def generate(self, numRows):
        """
        :type numRows: int
        :rtype: List[List[int]]
        """
        if numRows==0:
            return []
        if numRows==1:
            return [[1]]
        if numRows==2:
            return [[1], [1,1]]
        outarray = [[1], [1,1]]
        lastappend = [1,1]
        for i in range(2, numRows):
            appendarray = [1]
            for j in range(1, i):
                appendnum = lastappend[j-1]+lastappend[j]
                appendarray.append(appendnum)
            appendarray.append(1)
            outarray.append(appendarray)
            lastappend = appendarray
        return outarray
            
```

该题思路比较简单，根据上一行的数据生成下一行的数据，生成每一行的数据时用appendnum = lastappend[j-1]+lastappend[j]即可，每一行数据生成之后append到最终的输出数组即可。

### 119.Pascal's Triangle II

Solution：

```python
class Solution(object):
    def getRow(self, rowIndex):
        """
        :type rowIndex: int
        :rtype: List[int]
        """
        if rowIndex == 0:
            return [1]
        if rowIndex == 1:
            return [1,1]
        outputarray = [1,1]
        for i in range(1, rowIndex):
            for j in range(len(outputarray)-1,0,-1):
                outputarray[j] = outputarray[j] + outputarray[j-1]
            outputarray.append(1)
        return outputarray
```

该题方法与上题类似，只用一直更新一行的元素，那么我们只用在上一行元素的数组的基础上进行in-place的更新，需要注意的是我们更新元素的时候应该从后往前更新，防止出现元素覆盖的问题。

### 134. Gas Station

Solution:

```python
class Solution(object):
    def canCompleteCircuit(self, gas, cost):
        """
        :type gas: List[int]
        :type cost: List[int]
        :rtype: int
        """
        pos = 0
        mindif = curdif = gas[0]-cost[0]
        for i in range(1,len(gas)):
            curdif += (gas[i]-cost[i])
            if curdif < mindif:
                pos = i
                mindif = curdif
        if curdif<0:
            return -1
        return pos+1 if pos!=len(gas)-1 else 0
```

这道题感觉主要有三个点，第一个点是要理解题目的意思，加的油累计至需要一直大于消耗的油累计值。我们可以先把两个数组的值相减得到一个新数组，然后问题转化为：从新数组某一个值开始累计，只要累计值始终大于零即可。第二个点是如何找到开始累计的起始点呢，我们可以发现新数组元素从头开始相加，所得到的累计值最小的点之后的那个点就是起始点。第三个点是如何判断能跑完全程，把新数组所有元素相加，和如果小于0就不能跑完。

### 169. Majority Element

Solution1:

```python
class Solution(object):
    def majorityElement(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        countarray = {}
        for i in range(len(nums)):
            if nums[i] not in countarray:
                countarray[nums[i]] = 1
            else:
                countarray[nums[i]]+=1
            if countarray[nums[i]] > len(nums)/2:
                return nums[i]
```

我的解法思路依旧是求助于哈希表，在这种时间复杂度有要求的问题中感觉哈希表是很好用的。

Solution2:

```python
class Solution:
    def majorityElement(self, nums):
        count = 0
        candidate = None

        for num in nums:
            if count == 0:
                candidate = num
            count += (1 if num == candidate else -1)

        return candidate
```

该解法非常巧妙，运用了Majority Element的个数大于n/2的特征，那么我们把第一个元素选为candidate，之后遍历，遇到candidate就加一，不是candidate就减一，当计数器为零时就把当前遍历的数作为新的candidate，我感觉关键在于所有除了Majority Element的其他元素计数器最后一定会变为0，因为他们的数量一定小于n/2,那么最后留下的一定是Majority Element。

该解法十分巧妙，在遇到这种数量大于n/2的题目时可以往这个方向想。

### 189. Rotate Array

Solution1：

```python
class Solution(object):
    def rotate(self, nums, k):
        """
        :type nums: List[int]
        :type k: int
        :rtype: None Do not return anything, modify nums in-place instead.
        """
        rodic ={}
        for index,value in enumerate(nums):
            rodic[(index+k)%(len(nums))] = value
        for i in range(len(nums)):
            nums[i] = rodic[i]
```

该解法思路简单，这里使用字典进行的存储，也可以新建立一个数组进行存储，先把移动后的元素存起来，在更新原数组，思路简单但会有额外空间消耗。

Solution2：

```java
public class Solution {
    public void rotate(int[] nums, int k) {
        k = k % nums.length;
        int count = 0;
        for (int start = 0; count < nums.length; start++) {
            int current = start;
            int prev = nums[start];
            do {
                int next = (current + k) % nums.length;
                int temp = nums[next];
                nums[next] = prev;
                prev = temp;
                current = next;
                count++;
            } while (start != current);
        }
    }
}
```

该解法是这道题的最巧妙的解法，时间复杂度O（n），空间复杂度O（1）。这个方法的核心在于每次移动一批元素，具体思路如下图：

![image-20200321165142365](https://github.com/GAUnion/LeetCode-Weekly/blob/master/images/image-20200321165142365.png)

该方法先把红色箭头移动完，再移动红色箭头的元素，一批一批的移动，就不会出现元素覆盖的问题了。

### 283. Move Zeroes

Solution 1：

```python
class Solution(object):
    def moveZeroes(self, nums):
        """
        :type nums: List[int]
        :rtype: None Do not return anything, modify nums in-place instead.
        """
        # rlen = len(nums)
        if nums == []:
            return 0
        pos = point = 0
        while point < len(nums):
            if nums[point]!=0:
                nums[pos] = nums[point]
                pos+=1
            point+=1
        for i in range(pos, len(nums)):
            nums[i] = 0
```

该解法运用之前快慢指针的方法，而这次我们要移动零到数组尾，换句话说把非零元素都移动到数组前，那么我们可以用快指针遍历数组，慢指针存储非零元素，在快指针遍历完成后把慢指针之后的元素都置0即可。

### 299.Bulls and Cows

Solution 1:

```python
class Solution(object):
    def getHint(self, secret, guess):
        """
        :type secret: str
        :type guess: str
        :rtype: str
        """
        bull = cow = 0
        tmps = []
        tmpg = []
        for i in range(len(secret)):
            if secret[i] == guess[i]:
                bull += 1
                continue
            flag = 0
            if secret[i] in tmpg:
                cow += 1
                tmpg.remove(secret[i])
                tmpg.append(guess[i])
                flag = 1
            if guess[i] in tmps:
                cow += 1
                if flag == 0:
                    tmps.remove(guess[i])
                    tmps.append(secret[i])
                else:
                    tmps.remove(guess[i])
                    tmpg.remove(guess[i])
                flag = 1
            if flag == 0:
                tmps.append(secret[i])
                tmpg.append(guess[i])
        return str(bull)+'A'+str(cow)+'B'
```

该题目的核心在于遍历的时候要把元素存到数组中，之后再有满足条件的bull或者cow时进行元素的删减和新增，总体思路不难，但情况比较多需要细致一点把每种情况都考虑到。下面在提供一种用哈希表存储的方法。

Solution 2：

```c++
class Solution {
public:
    string getHint(string secret, string guess) {
        int bull=0,cow=0;
        unordered_map<char,int>mymap;

        for(int i=0;i<secret.length();++i)mymap[secret[i]]++;
        for(int i=0;i<guess.length();++i){
            if(secret[i]==guess[i]){
                bull++;
                if(mymap[secret[i]]>0) mymap[secret[i]]--;
                else cow--;               
            }
            else if(mymap.find(guess[i])!=mymap.end()){
                 
                if(mymap[guess[i]]>0){
                    mymap[guess[i]]--;
                    cow++;                    
                }
            }
        }
        
        return to_string(bull)+'A'+to_string(cow)+'B';

    }
};
```

### 442. Find All Duplicates in an Array

Solution：

```python
class Solution(object):
    def findDuplicates(self, nums):
        """
        :type nums: List[int]
        :rtype: List[int]
        """
        returnlist = [i for i in range(1,len(nums)+1)]
        outputarray = []
        for i in range(len(nums)):
            if returnlist[nums[i]-1]==0:
                outputarray.append(nums[i])
            if nums[i] == returnlist[nums[i]-1]:
                returnlist[nums[i]-1] = 0
        return outputarray
```

这道题的题目信息有两个关键点，一个是数组元素大小都是在[1,n]之内的，一个是元素要么出现一次要么出现两次。针对这两个特征，我先建立一个数组returnlist = [1,2,3,...,n]，之后遍历数组，对于每一个数（例如3），出现第一次把returnlist中相应元素置零，returnlist变为[1,2,0,...,n]，出现第二次检查相应位置，如果为0则将该数加入返回数组，遍历完成后返回数组即为答案。

### 448. Find All Numbers Disappeared in an Array

Solution：

```python
class Solution:
    def findDisappearedNumbers(self, nums: List[int]) -> List[int]:
		nums_out = []                       # Create the output list
        nums_1 = list(range(1,len(nums)+1)) # Create a list [1,2,3,...,n]
        for i in nums:
            nums_1[i-1]=0                   # for every element existing in nums, set the corresponding element in nums_1 = 0
        for i in range(0,len(nums_1)):
            if nums_1[i] !=0:               # Append all the non-zero elements to the output list
                nums_out.append(nums_1[i])
        return nums_out
```

该题与上题思路类似，只不过最后一步变为不为零的数加入到输出数组中。
