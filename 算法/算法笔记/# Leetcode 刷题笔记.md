# Leetcode 刷题笔记
## 数组
### 二分查找
 https://leetcode-cn.com/problems/binary-search/

- 注意区间的定义
	若为闭区间：
	-  范围限制：left<=right //mid左右区间边缘都可以取到，因此left==right有实际意义
	- 二分更新：
	```java
	right=mid-1;
	left=mid+1;
	```
	若为左闭右开：
	- 范围限制：left<right //left和right并不能同时取到 left<right时最后只剩下一个数
	- 二分更新：
	```java
	left=mid-1;
	right=mid; //right已经取不到，已经在范围之外
	```
	
### 搜索插入位置
https://leetcode-cn.com/problems/search-insert-position/

#### 解题思路
经典二分，最后再判断一下最终落点即可
二分的条件：有序数组
可能出现的情况：
- 目标值在数组所有元素之前
- 目标值等于数组中某一个元素
- 目标值插入数组中的位置
- 目标值在数组所有元素之后

 代码

```java
class Solution {
    public int searchInsert(int[] nums, int target) {
        int left=0;
        int right=nums.length-1;
        int mid=0;
        while(left<=right)
        {
             mid=left+(right-left)/2;
            if(nums[mid]==target)
            {
                return mid;
            }
            else if(nums[mid]>target)
            {
                right=mid-1;

            }
            else
            {
                left=mid+1;
            }
        }
        if(nums[mid]>target)
        {
            return mid;
        }
        else
        {
            return mid+1;
        }


    }
}
```
### 在排序数组中查找元素的第一个和最后一个位置
https://leetcode-cn.com/problems/find-first-and-last-position-of-element-in-sorted-array/

#### 方法一
利用二分找到中点后向左右去扩容至边界位置

 代码

```java
class Solution {
    public int[] searchRange(int[] nums, int target) {
        int lpos=-1;
        int rpos=-1;
        int left=0;
        int right=nums.length-1;
        int n=nums.length;
        while(left<=right)
        {
            int mid=left+(right-left)/2;
            if(nums[mid]==target)
            {
                lpos=mid;
                rpos=mid;
            
                while(lpos-1>=0&& lpos<n&&nums[lpos-1]==target)
                {
                    lpos--;
                }
                while(rpos>=0&&rpos<n-1&&nums[rpos+1]==target)
                {
                    rpos++;
                }
                return new int[]{lpos,rpos};
            }
            else if(nums[mid]>target)
            {
                right=mid-1;
            }
            else
            {
                left=mid+1;
            }

        }
        return new int[]{lpos,rpos};


    }
}
```

#### 方法二 
两次二分找到两个边界

可能出现的三种情况：
- 情况一：target 在数组范围的右边或者左边，例如数组{3, 4, 5}，target为2或者数组{3, 4, 5},target为6，此时应该返回{-1, -1}
- 情况二：target 在数组范围中，且数组中不存在target，例如数组{3,6,7},target为5，此时应该返回{-1, -1}
- 情况三：target 在数组范围中，且数组中存在target，例如数组{3,6,7},target为6，此时应该返回{1, 1}

寻找左边界时大于target和等于target可以归为一类，使右边界不断向左移动，直至找到左边界
寻找右边界时即为使左边界不断向右边界移动。

```java
class Solution {
    public int[] searchRange(int[] nums, int target) {
    
      if(nums.length==0) return new int[] {-1,-1};
    
        int l = 0, r = nums.length-1; //二分范围
        while( l < r)			        //查找元素的开始位置
        {
            int mid = (l + r )/2;
            if(nums[mid] >= target) r = mid;
            else l = mid + 1;
        }
        if( nums[r] != target) return new int[]{-1,-1};  //查找失败
        int L = r;
        l = 0; r = nums.length-1;     //二分范围
        while( l < r)                   //查找元素的结束位置
        {
            int mid = (l + r + 1)/2;
            if(nums[mid] <= target ) l = mid;
            else r = mid - 1;
        }
        return new int[]{L,r};
    }
   
    

    
    
    }
```

#### 方法三

与方法二思维类似，只是换为了闭区间。

```C++
Solution {
public:
    vector<int> searchRange(vector<int>& nums, int target) {
        int leftBorder = getLeftBorder(nums, target);
        int rightBorder = getRightBorder(nums, target);
        // 情况一
        if (leftBorder == -2 || rightBorder == -2) return {-1, -1};
        // 情况三
        if (rightBorder - leftBorder > 1) return {leftBorder + 1, rightBorder - 1};
        // 情况二
        return {-1, -1};
    }
private:
     int getRightBorder(vector<int>& nums, int target) {
        int left = 0;
        int right = nums.size() - 1;
        int rightBorder = -2; // 记录一下rightBorder没有被赋值的情况
        while (left <= right) {
            int middle = left + ((right - left) / 2);
            if (nums[middle] > target) {
                right = middle - 1;
            } else { // 寻找右边界，nums[middle] == target的时候更新left
                left = middle + 1;
                rightBorder = left;
            }
        }
        return rightBorder;
    }
    int getLeftBorder(vector<int>& nums, int target) {
        int left = 0;
        int right = nums.size() - 1;
        int leftBorder = -2; // 记录一下leftBorder没有被赋值的情况
        while (left <= right) {
            int middle = left + ((right - left) / 2);
            if (nums[middle] >= target) { // 寻找左边界，nums[middle] == target的时候更新right
                right = middle - 1;
                leftBorder = right;
            } else {
                left = middle + 1;
            }
        }
        return leftBorder;
    }
};


```

### Sqrt(x)
https://leetcode-cn.com/problems/sqrtx/
利用二分法去寻找平方后最接近x的数
```java
class Solution {
    public int mySqrt(int x) {
        int left=0;
        int right=x;
        int ans=-1;
        while(left<=right)
        {  int mid=left+(right-left)/2;
            if((long)mid*mid<=x)
            {
                ans=mid;
                left=mid+1;
            }
            else
            {
                right=mid-1;
            }
        }
        return ans;

    }
}

```
---
### 移除元素
https://leetcode-cn.com/problems/remove-element/submissions/
#### 双指针法
- right用于遍历整个数组，寻找所有不是val的数，并将它给left
- 只有right！=val时，left++，因此left与right之间刚好差了所有的val的个数，因此left对应的位置即为所求的长度
- 值为val的要么被替换要么被留在left之后


代码

```java
class Solution {
    public int removeElement(int[] nums, int val) {
       int left=0;
       int right=0;
       int n=nums.length;
       for(right=0;right<n;right++)
       {
           if(nums[right]!=val)
           {
               nums[left++]=nums[right];
           }

       }
       return left;

    }
}
```

### 删除排序数组中的重复项
https://leetcode-cn.com/problems/remove-duplicates-from-sorted-array/
双指针法，分为快慢指针，快指针用于遍历并于慢指针比较不同，慢指针用于定位至需要修改的地方
分为两种情况：
- 快慢指针内容相同：还未找到用于覆盖的元素，因此不做任何操作
- 快慢指针内容不同：找到可以用于覆盖的元素，保留当前元素，slow++，将多余元素覆盖
#### 代码部分
```java
class Solution {
    public int removeDuplicates(int[] nums) {
        int slow=0;
        int fast=0;
        for(fast=0;fast<nums.length;fast++)
        {
            if(nums[slow]!=nums[fast])
            {   slow++;
                nums[slow]=nums[fast];
            }
            
        }
        return slow+1;

    }
}
```
### 比较含退格的字符串

[844. 比较含退格的字符串 - 力扣（LeetCode） (leetcode-cn.com)](https://leetcode-cn.com/problems/backspace-string-compare/)

#### 方法一
经典双栈，不解释
```java

class Solution {

    public boolean backspaceCompare(String S, String T) {
        
        Stack<Character> s1=new Stack<>();
        Stack<Character> t1=new Stack<>();
        for(int i=0;i<S.length();i++)
        {
            if(S.charAt(i)!='#')
            {
                s1.push(S.charAt(i));
            }
            else
            {
                if(!s1.isEmpty())
                {
                    s1.pop();
                }
            }
        }
        for(int i=0;i<T.length();i++)
        {
            if(T.charAt(i)!='#')
            {
                t1.push(T.charAt(i));
            }
            else
            {
                if(!t1.isEmpty())
                {
                    t1.pop();
                }
            }
        }
        if(s1.size()!=t1.size())
        {
            return false;
        }
        while(!s1.isEmpty()&&!t1.isEmpty())
        {
            if(s1.pop()!=t1.pop())
            {
                return false;
            }
           
        }
        return true;
    }
}
```
#### 方法二
双指针
- 大致思路：
两个指针分别从尾部向前遍历，遇到退格符便删除掉前一个字符，最后比较剩下的字符
- 具体实现：
  创建两个指针用于遍历，两个常量nums,numt用于记录缓存中有几个退格符
  双层循环，外层循环用于比较每两个字符是否相等以及长度是否相等
  内层循环，用于判断退格和字符删除
    大致分为三种情况：
    - 是退格符：跳过该退格符，num++，准备删除下一个字符;
    - 不是退格符但缓存中有退格符，删除该字符，消耗掉一个退格符num--
    - 不是退格符且缓存中无退格符，删无可删，跳出内循环进行字符比较


```java
class Solution {

    public boolean backspaceCompare(String S, String T) {
        int sp=S.length()-1;
        int tp=T.length()-1;
        int nums=0;
        int numt=0;
        while(sp>=0||tp>=0)
        {
            while(sp>=0)
            {
                if(S.charAt(sp)=='#')
                {
                    sp--;
                    nums++;   
                }
                else if(nums>0)
                {
                   nums--;
                   sp--;
                }
                else
                {
                    break;
                }
            }
            while(tp>=0)
            {
                if(T.charAt(tp)=='#')
                {
                    tp--;
                    numt++;
                }
                else if(numt>0)
                {
                    tp--;
                    numt--;
                }
                else
                {
                    break;
                }
            }
            if(tp>=0&&sp>=0)
            {if(S.charAt(sp)!=T.charAt(tp))
            {
                return false;
            }
            }
            else{
                if(tp>=0||sp>=0)
                {
                    return false;
                }
            }
            tp--;
            sp--;
            

        }
        return true;
    }
}
```

### 有序数组的平方

[977. 有序数组的平方 - 力扣（LeetCode） (leetcode-cn.com)](https://leetcode-cn.com/problems/squares-of-a-sorted-array/)
#### 双指针加一次二合一排序
此题进阶要求为将用O(n)的时间复杂度
由于数组已经为有序升序排列,分为正负两部分,正的的平方为升序排列,负数平方为降序排列
若能找到正负分界出,从分界出向两边界遍历,则得到平方为升序的两个数组,进行二合一一次归并排序合成出答案即可.

```java
class Solution {
    public int[] sortedSquares(int[] nums) {
        int negative=-1;
        int n=nums.length;
        for(int i=0;i<n;i++)
        {
            if(nums[i]<0)
            {
                negative=i;
            }
            else
            {
                break;
            }
        }
        int j=negative+1;
        int i=negative;
        int[] ans=new int[n];
        int index=0;
        while(i>=0||j<n)
        {
            if(i<0)
            {
                ans[index]=nums[j]*nums[j];
                j++;
            }
           else if(j==n)
           {
               ans[index]=nums[i]*nums[i];
               i--;
           }
           else if(Math.abs(nums[i])>nums[j])
           {
               ans[index]=nums[j]*nums[j];
               j++;
           }
           else
           {
               ans[index]=nums[i]*nums[i];
               i--;
           }
           index++;
        }
        return ans;
    

    }
}
```
---

### 长度最小的子数组

[209. 长度最小的子数组 - 力扣（LeetCode） (leetcode-cn.com)](https://leetcode-cn.com/problems/minimum-size-subarray-sum/)

#### 滑动窗口
双指针的一种情况
在本题中实现滑动窗口，主要确定如下三点：
- 窗口内是什么？
- 如何移动窗口的起始位置？
- 如何移动窗口的结束位置？

sum即为窗口之中所有元素之和
终止位置不断向前遍历
起始位置用于控制窗口的长度刚好满足sum>=target,当满足条件之后,则证明窗口需要先前移动了,通过一个while来不断缩小窗口的长度,求得最小的范围,在此期间更新长度


```java
class Solution {
    public int minSubArrayLen(int target, int[] nums) {
        int n=nums.length;
        int sta=0;
        int minLen=100000;
        int sum=0;
        int count=0;
        for(int i=0;i<n;i++)
        {
            sum+=nums[i];
            while(sum>=target)
            {
                count=i-sta+1;
                if(count<minLen)
                {
                    minLen=count;
                }
                sum-=nums[sta];
                sta++;

            }
            
        }
        if(minLen==100000)return 0;
        else return minLen;

    }
}
```

### 水果成篮

[904. 水果成篮 - 力扣（LeetCode） (leetcode-cn.com)](

[)


#### 哈希表+滑动窗口
- 经典滑动窗口
- 通过哈希表来配合统计篮子中该种水果的数量,该水果数量清零时将该水果从篮子中彻底清除
- 窗口移动为跨过该颗树,并非删除一种水果


```java
class Solution {
    public int totalFruit(int[] fruits) {
       Map<Integer,Integer> basket=new HashMap<>();
       int result=0;
       int len=0;
       int left=0;
       for(int i=0;i<fruits.length;i++)
       {
           if(!basket.containsKey(fruits[i]))
           {
               basket.put(fruits[i],1);
           }
           else
           {
               Integer temp=basket.get(fruits[i]);
               temp++;
               basket.put(fruits[i],temp);  
           }
           len++;
           while(basket.size()>2)
           {
               int temp1=basket.get(fruits[left]);
               temp1--;
               
               if(temp1==0)
               {
                   basket.remove(fruits[left]);
               }
               else
               {
                   basket.put(fruits[left],temp1);
               }
               left++;
               len--;
               
           }
           if (result < len) {
               result=len;
            }

       }
       return result;

    }
}
```
### 最小覆盖子串

[76. 最小覆盖子串 - 力扣（LeetCode） (leetcode-cn.com)](https://leetcode-cn.com/problems/minimum-window-substring/)

#### 滑动窗口+哈希表
滑动窗口用于控制子字符串的长度,保证取到最小的覆盖范围
在isExist函数中,若t中的字符在s中都有对应并且哈希表中统计的出现次数大于t中,则为覆盖
在minWindow中,大致思路与水果成篮的思路一致,为left缩小窗口,哈希表统计好新的窗口中的出现次数传给isExist进行判断
与水果成篮找最多不同的是,此处找到是最小的覆盖长度,因此此题需要在已经经过isExist检验后的位置进行更新(防止在while之外一直len卡在1)
最后再提取子串即可

- 此外需要注意的是在容器传递的过程当中需要指定泛型。

```java
class Solution {
        Map<Character,Integer> mapt=new HashMap<>();
         Map<Character,Integer> maps=new HashMap<>();
    public String minWindow(String s, String t) {
        int left=0;
        int mlen=100000;
        int start=0;
         
        for(int i=0;i<t.length();i++)
        {
            if(mapt.containsKey(t.charAt(i)))
            {
                Integer temp=mapt.get(t.charAt(i));
                temp++;
                mapt.put(t.charAt(i),temp);
            }
            else
            {
                mapt.put(t.charAt(i),1);
            }
        }
        for(int i=0;i<s.length();i++)
        {
            if (mapt.containsKey(s.charAt(i))){
             if(maps.containsKey(s.charAt(i)))
            {
                int temp=maps.get(s.charAt(i));
                temp++;
                maps.put(s.charAt(i),temp);
            }
            else
            {
                maps.put(s.charAt(i),1);
            }
            }
            
            while(isExist())
            {
                int lens=i-left+1;
                if(lens<mlen)
            {
                mlen=lens;
                start=left;

            }
                if(maps.containsKey(s.charAt(left))){
                int temp=maps.get(s.charAt(left));
                temp--;
                if(temp==0)
                {
                    maps.remove(s.charAt(left));
                }
                else
                {
                    maps.put(s.charAt(left),temp);
                }
                }
                left++;
                
            }
            
            
        }
        if(mlen==100000)return "";
        else return s.substring(start, start + mlen);

    }
    boolean isExist()
    {   
        Iterator iterator = mapt.entrySet().iterator();
        while(iterator.hasNext()){
            Map.Entry entry = (Map.Entry)iterator.next();
            Character key = (Character) entry.getKey();
            Integer val = (Integer) entry.getValue();
            if (maps.getOrDefault(key, 0) < val) {
                return false;
            }

        }
        return true;
        
        
    }
}
```

另外一种写法

```java
class Solution {
    Map<Character, Integer> tString = new HashMap<Character, Integer>();
    Map<Character, Integer> sString = new HashMap<Character, Integer>();

    public String minWindow(String s, String t) {

        int left = 0;         //最开始左指针 右指针都指向0
        int right = 0;
        int length = Integer.MAX_VALUE;
        int lLocate = -1, rLocate = -1;     //记录字串左右位置，最后用subString()函数取得子串

        //将t字符串的字符以及数量记录在哈希表tString里
        for (int i = 0; i < t.length(); i++) {
            Character c = t.charAt(i);
            tString.put(c, tString.getOrDefault(c, 0) + 1);
        }

        //右指针是0开始
        for (right = 0; right < s.length(); right++) {
            //如果这个字符在t串中
            if (tString.containsKey(s.charAt(right))) {
                //就将这个字符记录在哈希表sString里并且个数+1
                sString.put(s.charAt(right), sString.getOrDefault(s.charAt(right), 0) + 1);
            }

            //如果这个字串是可行的，并且左指针小于等于右指针 那么缩小窗口
            while (check()) {
                //记录长度
                int subString = right - left + 1;
                //如果比length更小，就更新length
                if (subString < length) {
                    length = subString;
                    //记录字串位置
                    lLocate = left;
                    rLocate = left + length;
                }

                //如果当前字符是必要字符
                if (sString.containsKey(s.charAt(left))) {
                    //将该字符从sString哈希表中数量 -1
                    sString.put(s.charAt(left), sString.get(s.charAt(left)) - 1);
                }
                //左指针+1 缩小窗口
                left++;
            }
        }

        //
        return lLocate == -1 ? "" : s.substring(lLocate, rLocate);

    }

    public boolean check(){
        Iterator iterator = tString.entrySet().iterator();
        while(iterator.hasNext()){
            Map.Entry entry = (Map.Entry)iterator.next();
            Character key = (Character) entry.getKey();
            Integer val = (Integer) entry.getValue();
            if (sString.getOrDefault(key, 0) < val) {
                return false;
            }

        }
        return true;

    }
}


```
---
### 螺旋矩阵Ⅱ
#### 暴力模拟
该题并不涉及什么算法相关知识，主要是对于旋转过程的一个模拟，主要有以下几个关键点
- 方向的控制：设置directions数组存储对应的四个方向，通过+1%4的方式，遇到边界时切换方向
- 边界的判断：前进方向上超出数组范围和已经存储数据的单元为边界
- 边界的寻找：设置next坐标试探是否为边界，以判断是否需要更新方向
```java
class Solution {
    public int[][] generateMatrix(int n) {
        int[][]directions=new int[][]{{0,1},{1,0},{0,-1},{-1,0}};
        int count=1;
        int row=0;
        int col=0;
        int directionIndex=0;
        int[][] result=new int[n][n];
        while(count<=n*n)
        {
            result[row][col]=count;
            count++;
            int nextRow=row+directions[directionIndex][0];
            int nextCol=col+directions[directionIndex][1];
            if(nextRow>=n||nextRow<0||nextCol>=n||nextCol<0||result[nextRow][nextCol]!=0)
            {
                directionIndex=(directionIndex+1)%4;
                nextRow=row+directions[directionIndex][0];
                nextCol=col+directions[directionIndex][1];
            }
            row=nextRow;
            col=nextCol;

            
        }
        return result;

    }
    
}
```
---
## 链表
### 移除链表元素

[203. 移除链表元素 - 力扣（LeetCode） (leetcode-cn.com)](https://leetcode-cn.com/problems/remove-linked-list-elements/)

#### 解题思路

创建pre节点用于删除时重新连接链表，遍历时所选择删除的节点为head节点。
不需删除时pre与head一同自动向下遍历即可，保证始终位于head之前。
head在被删除时仍需++，否则与pre进度一致。
创建dummyHead节点便于对首节点进行操作


```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode removeElements(ListNode head, int val) {
        if(head==null)return null;
       ListNode dummyHead=new ListNode(0,head);
       ListNode pre=dummyHead;
      
       while(head!=null)
       {
           if(head.val==val)
           {
               pre.next=head.next;
           }
           else{
               pre=head;
           }
           head=head.next;

       }
      
       return dummyHead.next;

    }
}
```

### 反转链表

[剑指 Offer 24. 反转链表 - 力扣（LeetCode） (leetcode-cn.com)](https://leetcode-cn.com/problems/fan-zhuan-lian-biao-lcof/)
#### 方法一 新建链表从头插入
与普通的addFirst一致

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public ListNode reverseList(ListNode head) {
        if(head==null)return null;
        ListNode re=new ListNode(head.val);
        ListNode temp=null;
        head=head.next;
        while(head!=null)
        {
            temp=new ListNode(head.val);
            temp.next=re;
            re=temp;
            head=head.next;

        }
        return re;

    }
}
```


#### 方法二 直接翻转原链表
双指针法：temp保存head的下一步位置，便于head断开后指向pre之后的重新移动
          pre用于反向链接
基本步骤：
- 存储head的下一步位置，防止断开
- head与pre连接
- pre移动
- head移动
注：此顺序不可颠倒，否则链表断开
```java
class Solution {
    public ListNode reverseList(ListNode head) {
        if(head==null)return null;
        ListNode pre=null;
        ListNode temp;
        while(head!=null)
        {
            temp=head.next;
            head.next=pre;
            pre=head;
            head=temp;
            
            
        }
        return pre;

    }
}
```

### 两两交换链表中的节点

[24. 两两交换链表中的节点 - 力扣（LeetCode） (leetcode-cn.com)](https://leetcode-cn.com/problems/swap-nodes-in-pairs/)

#### 解题思路
分为两个步骤 一是进行节点交换，二是标记节点后移
- pre为前置节点

- current为第一个要交换的节点

- temp为第二个要交换的节点
  目标：pre-> temp-> current->temp.next
  交换过程中可以有多个节点指向同一个，但节点只能指向一个，指向另一个后原来的会断开
  更新：由于一次交换两个节点，因此pre和current均整体后移两个单位，解决方案：

- pre后移两个单位，current由于交换因此只一一个单位

- pre后移两个单位，重新定义current

  ![图解过程](https://code-thinking.cdn.bcebos.com/pics/24.%E4%B8%A4%E4%B8%A4%E4%BA%A4%E6%8D%A2%E9%93%BE%E8%A1%A8%E4%B8%AD%E7%9A%84%E8%8A%82%E7%82%B91.png)

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode swapPairs(ListNode head) {
        if(head==null)return null;
        ListNode dummyHead=new ListNode(-1,head);
        ListNode pre=dummyHead;
        ListNode current=head;
        ListNode temp;
        while(current!=null&&current.next!=null)
        {
            temp=current.next;
            current.next=temp.next;
            temp.next=current;
            pre.next=temp;
            //标记后移
            pre=pre.next.next;
            current=current.next;
        }
        return dummyHead.next;

    }
}
```
### 删除链表的倒数第N个节点

[剑指 Offer II 021. 删除链表的倒数第 n 个结点 - 力扣（LeetCode） (leetcode-cn.com)](https://leetcode-cn.com/problems/SLwz0R/)

#### 双指针
既然要找到倒数第N个，即令del与head延迟N+1步
head遍历到最后时删除del的下一个即可


```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        ListNode dummyHead=new ListNode(-1,head);
        ListNode del=dummyHead;
        for(int i=0;i<n;i++)
        {
            head=head.next;
        }
        while(head!=null)
        {
            head=head.next;
            del=del.next;
        }
        del.next=del.next.next;
        return dummyHead.next;

    }
}
```

![图解](https://code-thinking.cdn.bcebos.com/pics/19.%E5%88%A0%E9%99%A4%E9%93%BE%E8%A1%A8%E7%9A%84%E5%80%92%E6%95%B0%E7%AC%ACN%E4%B8%AA%E8%8A%82%E7%82%B91.png)

### 相交链表

[160. 相交链表 - 力扣（LeetCode） (leetcode-cn.com)](https://leetcode-cn.com/problems/intersection-of-two-linked-lists/)


#### 方法一
HashSet判断重复即可，不解释

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) {
 *         val = x;
 *         next = null;
 *     }
 * }
 */
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
       Set<ListNode> s1=new HashSet<>();
       while(headA!=null)
       {
           s1.add(headA);
           headA=headA.next;
       }
       while(headB!=null)
       {
           if(s1.contains(headB))
           {
               return headB;
           }
           headB=headB.next;
       }
       return null;
       
        
    }
}
```
#### 方法二
先求出两个链表的长度，求出长度查之后对齐，再从短的一端开始遍历找到相同的即可
```java
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        ListNode curA = headA;
        ListNode curB = headB;
        int lenA = 0, lenB = 0;
        while (curA != null) { // 求链表A的长度
            lenA++;
            curA = curA.next;
        }
        while (curB != null) { // 求链表B的长度
            lenB++;
            curB = curB.next;
        }
        curA = headA;
        curB = headB;
        // 让curA为最长链表的头，lenA为其长度
        if (lenB > lenA) {
            //1. swap (lenA, lenB);
            int tmpLen = lenA;
            <!-- lenA = lenB; -->
            lenB = tmpLen;
            //2. swap (curA, curB);
            ListNode tmpNode = curA;
            curA = curB;
            curB = tmpNode;
        }
        // 求长度差
        int gap = lenA - lenB;
        // 让curA和curB在同一起点上（末尾位置对齐）
        while (gap-- > 0) {
            curA = curA.next;
        }
        // 遍历curA 和 curB，遇到相同则直接返回
        while (curA != null) {
            if (curA == curB) {
                return curA;
            }
            curA = curA.next;
            curB = curB.next;
        }
        return null;
    }
    
}
```
### 环形链表 II

[142. 环形链表 II - 力扣（LeetCode） (leetcode-cn.com)](https://leetcode-cn.com/problems/linked-list-cycle-ii/)

#### 方法一：双指针
假设从头结点到环形入口节点 的节点数为x。 环形入口节点到 fast指针与slow指针相遇节点 节点数为y。 从相遇节点 再到环形入口节点节点数为 z。 如图所示：
![](https://img-blog.csdnimg.cn/20210318162938397.png)
那么相遇时： slow指针走过的节点数为: x + y， fast指针走过的节点数：x + y + n (y + z)，n为fast指针在环内走了n圈才遇到slow指针， （y+z）为 一圈内节点的个数A。

因为fast指针是一步走两个节点，slow指针一步走一个节点， 所以 fast指针走过的节点数 = slow指针走过的节点数 * 2：

(x + y) * 2 = x + y + n (y + z)

两边消掉一个（x+y）: x + y = n (y + z)

因为要找环形的入口，那么要求的是x，因为x表示 头结点到 环形入口节点的的距离。

所以要求x ，将x单独放在左面：x = n (y + z) - y ,

再从n(y+z)中提出一个 （y+z）来，整理公式之后为如下公式：x = (n - 1) (y + z) + z 注意这里n一定是大于等于1的，因为 fast指针至少要多走一圈才能相遇slow指针。

这个公式说明什么呢？

先拿n为1的情况来举例，意味着fast指针在环形里转了一圈之后，就遇到了 slow指针了。

当 n为1的时候，公式就化解为 x = z，

这就意味着，从头结点出发一个指针，从相遇节点 也出发一个指针，这两个指针每次只走一个节点， 那么当这两个指针相遇的时候就是 环形入口的节点。

也就是在相遇节点处，定义一个指针index1，在头结点处定一个指针index2。

让index1和index2同时移动，每次移动一个节点， 那么他们相遇的地方就是 环形入口的节点。
![](https://tva1.sinaimg.cn/large/008eGmZEly1goo58gauidg30fw0bi4qr.gif)
那么 n如果大于1是什么情况呢，就是fast指针在环形转n圈之后才遇到 slow指针。

其实这种情况和n为1的时候 效果是一样的，一样可以通过这个方法找到 环形的入口节点，只不过，index1 指针在环里 多转了(n-1)圈，然后再遇到index2，相遇点依然是环形的入口节点。
代码

```java
/**
 * Definition for singly-linked list.
 * class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) {
 *         val = x;
 *         next = null;
 *     }
 * }
 */
public class Solution {
    public ListNode detectCycle(ListNode head) {
      ListNode slow=head;
      ListNode fast=head;
      while(fast!=null&&fast.next!=null)
      {
          slow=slow.next;
          fast=fast.next.next;
          if(slow==fast)//有环
          {
              ListNode index1=fast;
              ListNode index2=head;
              while(index1!=index2)
              {
                  index2=index2.next;
                  index1=index1.next;

              }
              return index1;
          }
      }
      return null;
}
}
```
#### 方法二 HashMap查重
```java
public class Solution {
    public ListNode detectCycle(ListNode head) {
        Map<ListNode,Integer> m1 = new HashMap<>();
        while(head!=null)
        {
            if(!m1.containsKey(head))
            {
                m1.put(head,1);
            }
            else
            {
                return head;
            }
            head=head.next;

        }
        return null;
        
    }
}
```
---
## 哈希表
### 有效的字母异位词

[242. 有效的字母异位词 - 力扣（LeetCode） (leetcode-cn.com)](https://leetcode-cn.com/problems/valid-anagram/)

- 数组也为一个简单的哈希表
定义一个数组叫做record用来上记录字符串s里字符出现的次数。

需要把字符映射到数组也就是哈希表的索引下标上，因为字符a到字符z的ASCII是26个连续的数值，所以字符a映射为下标0，相应的字符z映射为下标25。

再遍历 字符串s的时候，只需要将 s[i] - ‘a’ 所在的元素做+1 操作即可，并不需要记住字符a的ASCII，只要求出一个相对数值就可以了。 这样就将字符串s中字符出现的次数，统计出来了。

那看一下如何检查字符串t中是否出现了这些字符，同样在遍历字符串t的时候，对t中出现的字符映射哈希表索引上的数值再做-1的操作。

那么最后检查一下，record数组如果有的元素不为零0，说明字符串s和t一定是谁多了字符或者谁少了字符，return false。

最后如果record数组所有元素都为零0，说明字符串s和t是字母异位词，return true。
![](https://tva1.sinaimg.cn/large/008eGmZEly1govxyg83bng30ds09ob29.gif)


利用数组实现
```java
class Solution {
    public boolean isAnagram(String s, String t) {
        if(s.length()!=t.length())
        {
            return false;
        }
        int[] record=new int[26];
        for(char c: s.toCharArray())
        {
            record[c-'a']++;
        }
        for(char c:t.toCharArray())
        {
            record[c-'a']--;
        }
        for(int i:record)
        {
            if(i!=0)
            {
                return false;
            }
        }
        return true;
       
    }
}
```


利用HashMap实现
```java
class Solution {
    public boolean isAnagram(String s, String t) {
        if(s.length()!=t.length())
        {
            return false;
        }
        Map<Character,Integer> map_s=new HashMap<>();
        Map<Character,Integer>map_t=new HashMap<>();
        for(int i=0;i<s.length();i++)
        {
            if(!map_s.containsKey(s.charAt(i)))
            {
                map_s.put(s.charAt(i),1);
            }
            else
            {
                Integer temp=map_s.get(s.charAt(i));
                temp++;
                map_s.put(s.charAt(i),temp);
            }
        }
        for(int i=0;i<t.length();i++)
        {
            if(!map_t.containsKey(t.charAt(i)))
            {
                map_t.put(t.charAt(i),1);
            }
            else
            {
                Integer temp=map_t.get(t.charAt(i));
                temp++;
                map_t.put(t.charAt(i),temp);
            }
        }
        if(map_t.equals(map_s))
        {
            return true;
        }
        else{
            return false;
        }

    }
}

```
### 字母异位词分组

[49. 字母异位词分组 - 力扣（LeetCode） (leetcode-cn.com)](https://leetcode-cn.com/problems/group-anagrams/)


#### 排序提取共同的key
通过一张哈希表进行统计，key为排序后的字符串，value是同一类的异位词的List
异位词排序后相同，只要相等就可证明为异位词，
通过共同key进行分类，将同一类的词放在同一个List之中
最后遍历key将value的List塞到外层List之中即可


```java
class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {
        Map<String,List<String>> map=new HashMap<>();

       for( String str:strs)
        {
            char[] array=str.toCharArray();
            Arrays.sort(array);
            String temp=new String(array);
            
            if(!map.containsKey(temp))
            {
                List<String> t=new LinkedList<>();
                t.add(str);
                map.put(temp,t);
            }
            else
            {
                List<String>e=map.get(temp);
                e.add(str);
                map.put(temp,e);
            }
        }
        List<List<String>>ans=new LinkedList<>();
        Set<String> keys=map.keySet();
        for(String key:keys)
        {
            List<String> tt=map.get(key);
            ans.add(tt);
        }
        return ans;

}
}
```
### 两个数组的交集

[349. 两个数组的交集 - 力扣（LeetCode） (leetcode-cn.com)](https://leetcode-cn.com/problems/intersection-of-two-arrays/)

#### 哈希表HashMap判重
由于返回结果只返回唯一值，因此在遍历往Map之中添加元素时，只添加一遍，重复的元素不做考虑，第二遍遍历寻找重复元素来确定最终数组长度时也只找出现次数为1的元素，避免重复统计



直接用Set也可

```java
class Solution {
    public int[] intersection(int[] nums1, int[] nums2) {
        Map<Integer,Integer> map=new HashMap<>();
        for(int i=0;i<nums1.length;i++)
        {
            if(!map.containsKey(nums1[i]))
            {
                map.put(nums1[i],1);
            }
            
        }
        int count=0;
        for(int i=0;i<nums2.length;i++)
        {
            if(map.containsKey(nums2[i]))
            {
                if(map.get(nums2[i])==1)
                count++;
                
                int temp=map.get(nums2[i]);
                temp++;
                map.put(nums2[i],temp);
                
            }
        }
            
        Set<Integer> keys=map.keySet();
        int[] ans=new int[count];
        int flag=0;
        for(Integer key:keys)
        {
            if(map.get(key)>=2)
            {
                ans[flag]=key;
                flag++;
            }
        }
        return ans;


    }
}
```

两个数组的交集 II

[350. 两个数组的交集 II - 力扣（LeetCode） (leetcode-cn.com)](https://leetcode-cn.com/problems/intersection-of-two-arrays-ii/)



#### 哈希表统计数量

- 首先统计在nums1中出现了多少次，遍历nums2时判断交集，存在这个key即为交集的一部分，同时在map之中减去该元素。

- 使用getOrDefault方法快速判断是否存在并插入，解决了使用containsKey的繁杂和直接get的空指针报错

- 创建一个长数组，然后使用copyofRange进行截取，解决了数组不能动态添加元素的问题



```java

class Solution {

  public int[] intersect(int[] nums1, int[] nums2) {

    if(nums1.length<nums2.length)

    {

      return intersect(nums2,nums1);

    }

    Map<Integer,Integer> map= new HashMap<>();

    for(int i=0;i<nums1.length;i++)

    {

      int temp=map.getOrDefault(nums1[i],0)+1;

      map.put(nums1[i],temp);

    }

    int[] intersection = new int[nums1.length];

    int index = 0;

    for (int num : nums2) {

      int count = map.getOrDefault(num, 0);

      if (count > 0) {

        intersection[index++] = num;

        count--;

        if (count > 0) {

          map.put(num, count);

        } else {

          map.remove(num);

        }

     }

    }

   return Arrays.copyOfRange(intersection, 0, index);





  }

}

```



### 两数之和

[1. 两数之和 - 力扣（LeetCode） (leetcode-cn.com)](https://leetcode-cn.com/problems/two-sum/)

#### 一次遍历哈希表

梦开始的地方

看似简单，需要的细节颇多

- 先判断再装入数据 防止num=target/2,自己与自己相等

map中键值对的考量

- nums[i],i:判断 target-num是否存在于存在于key之中

- value处必须放下表，保证提取返回结果

- target-num,i：无法得到两个下表，因此key必须为nums[i]



```java

class Solution {

  public int[] twoSum(int[] nums, int target) {

    Map<Integer,Integer> map=new HashMap<>();

    for(int i=0;i<nums.length;i++)

    {

      int temp=target-nums[i];
      
      if(map.containsKey(temp))

      {

        return new int[]{i,map.get(temp)};

      }

      map.put(nums[i],i);

    

    



    }



    return new int[2];

  }

}



```



### 四数相加
[454. 四数相加 II - 力扣（LeetCode） (leetcode-cn.com)](https://leetcode-cn.com/problems/4sum-ii/)
#### 哈希表统计数量
大概不存在O(n)的解法
O(n^2)的解法为遍历AB统计出A+B的各种组合的值和数量，再再CD之中找到A+B+C+D==0即可
- c,d的不同组合可能对应同一个值，因此不存在去重的问题，即统计之后无需-1或者remove掉


```java
class Solution {
    public int fourSumCount(int[] nums1, int[] nums2, int[] nums3, int[] nums4) {
        int count=0;
        Map<Integer,Integer> map=new HashMap<>();
        for(int a:nums1)
        {
            for(int b:nums2)
            {
                int temp=map.getOrDefault(a+b,0);
                temp++;
                map.put(a+b,temp);
            }
        }
        for(int c:nums3)
        {
            for(int d:nums4)
            {
                int temp1=c+d;
                if(map.containsKey(-temp1))
                {
                    int re=map.get(-temp1);
                    count+=re;
                    
                   
                }
            }
        }
        return count;


    }
}
```

### 三数之和

[15. 三数之和 - 力扣（LeetCode） (leetcode-cn.com)](https://leetcode-cn.com/problems/3sum/)
### 排序+双指针
- 去重

```java
class Solution {
    public List<List<Integer>> threeSum(int[] nums) {
        Arrays.sort(nums);
        List<List<Integer>> ans=new LinkedList<>();
        
        for(int i=0;i<nums.length;i++)
        {
            if(i>0&&nums[i]==nums[i-1]) continue;
            if(nums[i]>0)
            {
                return ans;
            }
            int j=i+1;
            int k=nums.length-1;
            while(j<k){
            if(nums[i]+nums[j]+nums[k]==0)
            {
               
                List<Integer> temp=new LinkedList();
                temp.add(nums[i]);
                temp.add(nums[j]);
                temp.add(nums[k]);
                 while(j< k && nums[j+1] == nums[j]) ++j;
                while (j < k && nums[k-1] == nums[k]) --k;
                j++;
                k--;
                ans.add(temp);
            }
            else if(nums[i]+nums[j]+nums[k]<0)
            {
                j++;
            }
            else
            {
                k--;
            }
            }
            
        }
        return ans;
    }
}
```
---
## 字符串
### 反转字符串Ⅱ

[541. 反转字符串 II - 力扣（LeetCode） (leetcode-cn.com)](https://leetcode-cn.com/problems/reverse-string-ii/)

#### 区域翻转
此题本身较为简单，但该题解有两个较为有意思的点
- Math.min寻找结尾处，在前几个2k区域内，length显然大于start+k,不影响结尾的寻找，最后2k的区域内，若再加k区域已经超出总长度，则结尾定在length-1即可，交换剩下全部字符，若为超出，表示k<x<2k，交换该k区域，剩下的不做操作
- 位异或运算得到交换：
以start=7(111)，end=4(100)为例：
第一步 start=011
第二步 end=111
第三步 start=100
实现了两数交换，无序额外空间



```java
class Solution {
    public String reverseStr(String s, int k) {

        char[] ch=s.toCharArray();
        for(int i=0;i<ch.length;i+=2*k)
        {
            int start=i;
            int end=Math.min(ch.length-1,start+k-1);
            while(start<end)
            {
                ch[start]^=ch[end];
                ch[end]^=ch[start];
                ch[start]^=ch[end];
                start++;
                end--;
            }
        }
        return new String(ch);

    }
}
```

---

## 二叉树
### 二叉树层序遍历
#### 过于简单，不多做赘述
### 翻转二叉树

[226. 翻转二叉树 - 力扣（LeetCode） (leetcode-cn.com)](https://leetcode-cn.com/problems/invert-binary-tree/)

#### 递归法
整体思路：对每个节点的左右子节点都进行一次交换
交换思路：swap函数中传入一个节点，将该节点的左右子女节点进行交换，并非传入两个节点，将两个节点进行交换，并不是对父节点进行重新赋值，即父节点的指向的地址并未发生改变，实际上未交换。


```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public TreeNode invertTree(TreeNode root) {
        if(root==null) return null;
        swap(root);
        root.left=invertTree(root.left);
        root.right=invertTree(root.right);
        return root;

    }
    public void swap(TreeNode root)
    {
        TreeNode temp=root.left;
        root.left=root.right;
        root.right=temp;
    }
}
```
### 对称二叉树

[剑指 Offer 28. 对称的二叉树 - 力扣（LeetCode） (leetcode-cn.com)](https://leetcode-cn.com/problems/dui-cheng-de-er-cha-shu-lcof/)

#### 递归

- 核心为确定好条件的顺序：先判断三种存在空的情况，然后判断左右不空但是值不等的情况，最后剩下左右相等并且值相同的情况，保证最后判断值的时候不会访问到空指针
- 左右相等值相同的情况不可返回true，否则会提前截断递归。
- 判断条件只需判断这两层的节点是否对称即可，再往下的通过递归来实现

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public boolean isSymmetric(TreeNode root) {
        if(root==null) return true;
        return Symmetric(root.left,root.right);
    }
    public boolean Symmetric(TreeNode L,TreeNode R)
    {
        
         if((L==null&&R!=null))
         {
             return false;
         }
          if(L!=null&&R==null)
         {
             return false;
         }
          
        if(L==null&&R==null)
        {
            return true;
        }
        if(L.val!=R.val)
        {
            return false;
        }

         
        boolean t1=Symmetric(L.right,R.left);
         boolean t2=Symmetric(R.right,L.left);
         return t1&&t2;
    }
    
}
```
### 二叉树的最大深度

[104. 二叉树的最大深度 - 力扣（LeetCode） (leetcode-cn.com)](https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/)

#### 方法一：递归法
整个递归的过程：
- 首先遍历到各个叶子节点
- 异步从每个叶子往上开始记录该条路线的长度
- 两条线路交汇是保留深度大的节点


```java

class Solution {
    public int maxDepth(TreeNode root) {
        if(root==null)
        {
            return 0;
        }
        int i=maxDepth(root.left);
        int j=maxDepth(root.right);
        return i>j?i+1:j+1;

    }
}
```
#### 方法二：层序遍历

每遍历一层高度加一

```java
class Solution {
    public int maxDepth(TreeNode root) {
        if (root == null)   return 0;
        Queue<TreeNode> que = new LinkedList<>();
        que.offer(root);
        int depth = 0;
        while (!que.isEmpty())
        {
            int len = que.size();
            while (len > 0)
            {
                TreeNode node = que.poll();
                if (node.left != null)  que.offer(node.left);
                if (node.right != null) que.offer(node.right);
                len--;
            }
            depth++;
        }
        return depth;
    }
}





```
### 二叉树的最小深度

[111. 二叉树的最小深度 - 力扣（LeetCode） (leetcode-cn.com)](https://leetcode-cn.com/problems/minimum-depth-of-binary-tree/)


#### 递归
分为四种情况：
- 左空右不空，表明不是最低点
- 左不空右空，表明不是最低点
- 叶子节点，高度为零，开始计数
- 交汇处保留最短的长度

```java

class Solution {

    public int minDepth(TreeNode root) {
        return getDepth(root);
        
        
    }
    public int getDepth(TreeNode root)
    {
        if(root==null) return 0;
        int i=minDepth(root.left);
        int j=minDepth(root.right);
        if(root.left==null&&root.right!=null)
        {
            return j+1;
        }
        if(root.right==null&&root.left!=null)
        {
            return i+1;
        }
        return Math.min(i,j)+1;

    }
}
```
#### 层序遍历
遇到叶子节点则表明已经到达最低点
```java
class Solution {
    public int minDepth(TreeNode root) {
        if(root==null)return 0;
        Deque<TreeNode> deque=new LinkedList<>();
        deque.add(root);
        int height=0;
        while(!deque.isEmpty())
        {
            height++;
            int size=deque.size();
            for(int i=0;i<size;i++)
            {
                TreeNode temp=deque.poll();
                if(temp.left!=null)
                {
                    deque.add(temp.left);
                }
                if(temp.right!=null)
                {
                    deque.add(temp.right);
                }
                if(temp.left==null&&temp.right==null)
                {
                    return height;
                }
            }
            

        }
        return -1;


    }
}
```

### 完全二叉树的节点个数

[222. 完全二叉树的节点个数 - 力扣（LeetCode） (leetcode-cn.com)](https://leetcode-cn.com/problems/count-complete-tree-nodes/)

#### 利用完全二叉树性质
-  若将叶子节点也认为是一个完全二叉树，则一个完全二叉树可以拆分成多个满的完全二叉树，通过公式计算出每个满完全二叉树的节点数，然后累加
- 完全二叉树从中间分开，可分为左边未满和右边未满的两种情况。
- 若左边最下面一层未满，则右边为全满，只不过比左边少一层，公式计算右边的总节点数，递归继续拆分左边
- 若右边最下面一层未满，则左边为全满，左右层数相同，公式计算左边的总节点数，递归继续拆分右边
- 由于完全二叉树未满层的节点均位于左边，因此不断向左遍历即可找出该层的最大深度，从而判断左右谁未满
- 若为满完全二叉树，则左边按公式，右边继续拆即可，不影响该算法的结果
- 移位运算符的使用：1<<x等价于2^x
![完全二叉树1](https://img-blog.csdnimg.cn/20201124092543662.png)
![完全二叉树2](https://img-blog.csdnimg.cn/20201124092634138.png)



```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public int countNodes(TreeNode root) {
        if(root==null) return 0;
        int leftDepth=getDepth(root.left);
        int rightDepth=getDepth(root.right);
        if(leftDepth==rightDepth)
        {
            return(1<<leftDepth)+countNodes(root.right);
        }
        else
        {
            return(1<<rightDepth)+countNodes(root.left);
        }
    

    }
    public int getDepth(TreeNode root){
        if(root==null) return 0;
        int count=0;
        while(root!=null)
        {
            count++;
            root=root.left;
        }
        return count;
    }
}
```

#### 暴力遍历：dfs/bfs
有手就行，不多赘述

### 平衡二叉树

[110. 平衡二叉树 - 力扣（LeetCode） (leetcode-cn.com)](https://leetcode-cn.com/problems/balanced-binary-tree/)

#### 辅助函数递归
递归过程当中只要找到一对高度差大于二的即可证明非平衡二叉树，因此可以设定一个标记，遇到>1时即标记为false
- 二叉树节点的深度：指从根节点到该节点的最长简单路径边的条数。
- 二叉树节点的高度：指从该节点到叶子节点的最长简单路径边的条数。
- ![](https://img-blog.csdnimg.cn/20210203155515650.png)

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public boolean res=true;
    public boolean isBalanced(TreeNode root) {
        height(root);
        return res;
        
        
    }
    public int height(TreeNode root)
    {
        if(root==null) return 0;
        int i=height(root.left);
        int j=height(root.right);
        if(Math.abs(i-j)>1)
        {
            res=false;
            
        }
        return Math.max(i,j)+1;
}
}
```
#### 递归
此题所要找到的节点有两个条件，分别为最左和最下，因此不可一味往左遍历，故采用回溯的方法，保证每条路都能访问的到
由于采用前序遍历，并且更新条件为leftLen>maxLen，因此左边节点优先访问，保证了同一层只会记录最左边的节点
```java

class Solution {
    public int maxLen=0;
    public int maxValue=0;
    public int findBottomLeftValue(TreeNode root) {
        dfs(root,1);
        return maxValue;
      

    }
    public void dfs(TreeNode root, int leftLen)
    {
        if(root.left==null&&root.right==null)
        {
            if(leftLen>maxLen)
            {
                maxLen=leftLen;
                maxValue=root.val;
            }
            return;
        }
        if(root.left!=null)
        {
            leftLen++;
            dfs(root.left,leftLen);
            leftLen--;
        }
        if(root.right!=null)
        {
            leftLen++;
            dfs(root.right,leftLen);
            leftLen--;
        }
    }

}
```
### 找树左下角的值

[513. 找树左下角的值 - 力扣（LeetCode） (leetcode-cn.com)](https://leetcode-cn.com/problems/find-bottom-left-tree-value/)

#### 层序遍历
找到最后一层的第一个即可，较为简单，不再赘述。
```java
class Solution {
    public int findBottomLeftValue(TreeNode root) {
        if(root==null) return 0;
        Deque<TreeNode> deque=new LinkedList<>();
        deque.add(root);
        int ans=0;
        while(!deque.isEmpty())
        {
            int size=deque.size();
            for(int i=0;i<size;i++)
            {
                TreeNode top=deque.poll();
                if(i==0)
                {
                    ans=top.val;

                }
                if(top.left!=null)
                {
                    deque.add(top.left);
                }
                if(top.right!=null)
                {
                    deque.add(top.right);
                }
            }
        }
        return ans;

    }
}
```

### 最大二叉树

[654. 最大二叉树 - 力扣（LeetCode） (leetcode-cn.com)](https://leetcode-cn.com/problems/maximum-binary-tree/)
#### 递归拆分数组构建
整体做法有点类似逆向归并
- 递归结束条件：数组内无元素时，即rightIndex<leftIndex,结束递归
- 单层递归逻辑：如果数组内只剩下一个节点，无需寻找，直接构建当前节点，否则遍历寻找出最大值，构建当前节点，保留最大值的位置。左右子树也构建完成后，返回答案。
- 下一层逻辑：根据最大值位置划分leftIndex和rightIndex，用于构建左子树和右子树。
	注意事项：
- 区间的选择，这里选择闭区间，因此
	- 区间更新时：leftIndex=maxpos+1，RightIndex=maxpos-1。
	- 递归结束时：rightIndex<leftIndex
	- 只剩一个元素时：rightIndex==leftIndex
- 如果利用给定函数的参数即可完成，则再本函数中完成即可，若需要额外的辅助参数，则需要构建辅助函数
```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public TreeNode constructMaximumBinaryTree(int[] nums) {
       return buildll(nums,0,nums.length-1);
    
    }
    public TreeNode buildll(int[]nums,int leftIndex,int rightIndex)
    {
        if(rightIndex-leftIndex<0)
        {
            return null;
        }
        if(rightIndex==leftIndex)
        {
            return new TreeNode(nums[leftIndex]);
        }
        int maxValue=0;
        int maxpos=0;
        for(int i=leftIndex;i<=rightIndex;i++)
        {
            if(nums[i]>maxValue)
            {
                maxValue=nums[i];
                maxpos=i;
            }
        }
        TreeNode root=new TreeNode(maxValue);
        root.left=buildll(nums,leftIndex,maxpos-1);
        root.right=buildll(nums,maxpos+1,rightIndex);
        return root;
    }
}
```
### 验证二叉搜索树

[98. 验证二叉搜索树 - 力扣（LeetCode） (leetcode-cn.com)](https://leetcode-cn.com/problems/validate-binary-search-tree/)


#### 递归中途判断
按照二叉搜索时的性质，中序遍历得到的应该为一个递增序列，因此在递归过程中只要找到比最大值小的即可返回false
#### 压缩成数组
遍历后得到一个数组判断是否为递增，复杂度较高，不做赘述
```java
class Solution {
    private long maxValue=Long.MIN_VALUE;
    public boolean isValidBST(TreeNode root) {
        if(root==null)return false;
       boolean f1=true;
        boolean f2=true;
        if(root.left!=null)
        {
            f1=isValidBST(root.left);
        }
        
        if(root.val>maxValue)
        {
            maxValue=root.val;
        }
        else
        {
            return false;
        }
        if(root.right!=null)
        {
            f2=isValidBST(root.right);
        }
        return f1&&f2;

    }
}
```








































---
## 存疑
### KMP
### 优先队列
### 从前序与中序遍历序列构造二叉树
### 从中序与后序遍历序列构造二叉树 

