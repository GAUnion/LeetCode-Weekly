## 题解：10、12、25、36

### 10. 正则表达式匹配

Solution：

```python
class Solution:
    def dp(self,i,j):
        if (i,j) not in self.dic:
            if j == len(self.p):
                out = i == len(self.s)
            else:
                first = i < len(self.s) and self.p[j] in [self.s[i],'.']
                if j < len(self.p)-1 and self.p[j+1] == '*':
                    out = self.dp(i,j+2) or (first and self.dp(i+1,j))
                else:
                    out = first and self.dp(i+1,j+1)
            self.dic[(i,j)] = out
            return out

    def isMatch(self, s: str, p: str) -> bool:
        self.s = s
        self.p = p
        self.dic = {}
        return self.dp(0,0)
```

**本题可以使用动态规划进行计算。**    
对于输入s和p，假设已知s[i+1:]和p[j+1:]中任意两个子串是否匹配 *(s[i+1:]表示从第i+1位开始到最后的s子串,p[j+1:]表示从第j+1位开始到最后的p子串，匹配结果存储在字典dic中,dic[(i+1,j+1)]表示s[i+1:]与p[j+1:]的匹配情况)*，
要求判断s[i:]和p[j]的匹配结果dic[(i,j)]。   
分两种情况，p[j+1]是否等于'\*'：
1. 如果p[j+1]不为'\*'，则s[i:]与p[j]是否匹配取决于p[j]是否等于s[i]或者为'.'，以及s[i+1]与p[j+1:]是否匹配。
2. 如果p[j+1]为'\*'，则再分两种情况：
    1. 当p[j]的个数为0时，此时dic[(i,j)]等于dic[(i,j+2)],即跳过当前p[j:j+2]代表的各式为'_*'的元素；
    2. 当p[j]个数不为0时，则dic[(i,j)]等于当前s[i]与p[j]的匹配程度以及dic[(i+1,j)]的匹配结果，即判断当前两个字符是否匹配，然后看s[i+1:]和p[j:]的匹配结果。

当j为p的长度时，如果i的值不为s的长度(即s没有遍历完)则表示不匹配，返回False，以此递推，最终返回dic[(0,0)]，即s和p子串是否匹配的结果。


### 12. 整数转罗马数字

Solution：

```python
class Solution:
    def intToRoman(self, num: int) -> str:
        d = [1000,900,500,400,100,90,50,40,10,9,5,4,1]
        r = ['M','CM','D','CD','C','XC','L','XL','X','IX','V','IV','I']
        dr = {d[i]:r[i] for i in range(len(d))}
        rom = ''
        #print(dr)
        while num:
            tmp = -1
            for i in range(len(d)):
                if d[i] <= num:
                    tmp = i
                    break
            if tmp == -1:
                return -1
            else:
                rom += dr[d[tmp]]
                num -= d[tmp]  
        return rom
```

将罗马数字的组合由大到小全部列出来，并将其与对应的十进制数字配对成字典。从中选出第一个小于num的值，记录其对应的罗马数字表示，然后将num减去选出的值并重复以上步骤，直至最终num为0。此时的记录便是最终的罗马数字表示。

### 25. K 个一组翻转链表

Solution：

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None
class Solution:
    def reverseKGroup(self, head: ListNode, k: int) -> ListNode:
        cur = head
        cnt = 0
        while cur and cnt < k:
            cur = cur.next
            cnt += 1
        if cnt == k:
            cur = self.reverseKGroup(cur,k)
            while cnt:
                tmp = head.next
                head.next = cur
                cur = head
                head = tmp
                cnt -= 1
            head = cur
        return head
```

本题可以使用递归思路进行解答。  
对于一个ListNode,我已经将其从第k个节点到最后都变成最终格式，那么接下来只需要处理剩余的前k个节点。将前k的节点进行翻转，并将得到的从k个节点之后的节点连接到翻转后的第k-1个节点（即原来的head节点），此时便完成所有翻转。  
设置cur为当前节点地址，遍历k下（如果k下未满足，则不需要翻转，直接返回原ListNode），将ListNode分为前k个节点和后面部分，使用递归处理完后面部分得到cur，先保存head的next节点(因为后续操作会改变head.next的地址)，因为翻转后head变成k个节点的最后一个，所以将cur接到head后面，并将head赋给cur，继续遍历原head的下一个节点，并将此时的新的cur接到其后面。最后返回cur即为新的翻转后的节点。
```
初始状态：
    (head = node0) -> node1 -> ... -> nodek_1, nodek -> ... -> None
递归：
    1. 已经处理完nodek -> ... -> None得到新的输出cur = nodek' -> ... -> None
    2. 因为前k个节点最后会得到 nodek_1 -> ... node1 -> node0
       所以 cur接在node0（即head）后面
       然后head=node1，cur = node0 -> cur
       ...
    3. 重复以上步骤2，最终head = nodek_1，cur = nodek_2 -> ... -> node0 -> cur
```

### 36. 有效的数独

Solution:

```python
class Solution(object):
    class Solution:
    def isValidSudoku(self, board: List[List[str]]) -> bool:
        for i in board:
            if len(list(set(i)))-1 != (9-i.count('.')):
                return False
        for i in range(9):
            tmp = [board[j][i] for j in range(9)]
            if len(list(set(tmp)))-1 != (9-tmp.count('.')):
                return False
        for i in range(0,9,3):
            for j in range(0,9,3):
                tmp = [board[i][j],board[i][j+1],board[i][j+2],board[i+1][j],board[i+1][j+1],board[i+1][j+2],board[i+2][j],board[i+2][j+1],board[i+2][j+2]]
                if len(list(set(tmp)))-1 != (9-tmp.count('.')):
                    return False
        return True
```

分三步进行比较：
1. 每一行比较；
2. 每一列比较；
3. 每一个九宫格比较。

比较方法是获得当前9个元素的集合（不重复的数值+'.'），如果该集合的个数减一（即统计不重复数字个数）等于9减去9个元素里所有'.'的个数，则表明没有一个数字重复。如果存在一个重复则直接返回False，三步比较都完成且没有返回False则表示数独形式正确。
