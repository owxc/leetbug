# LeetCode-CPP

## 收藏

* [递归如何转换为非递归 \| Veaxen's](https://www.veaxen.com/%E9%80%92%E5%BD%92%E5%A6%82%E4%BD%95%E8%BD%AC%E6%8D%A2%E4%B8%BA%E9%9D%9E%E9%80%92%E5%BD%92.html)
* STL [前言 · STL源码分析 · 看云](https://www.kancloud.cn/digest/mystl/192529)

## LeetCode技巧合集

来自:

* [Cspiration 留学两年多刷题过三千，教你刷题不再困难；全美唯一Java版本Leetcode视频讲解](https://cspiration.com/leetcodeClassification)
* [算法 ：LeetCode刷题\_哔哩哔哩 \(゜-゜\)つロ 干杯~-bilibili](https://www.bilibili.com/video/av29912609)

视频:

* DFS[\[LeetCode刷题\] 02/07/2020 直播录播\_哔哩哔哩 \(゜-゜\)つロ 干杯~-bilibili](https://www.bilibili.com/video/av87537108/)

列表:

* 股票类问题: LC121, LC122
* 迷宫问题
* 双指针:
  * 参考:[\[LeetCode刷题\] 02/21/2020 直播录播\_哔哩哔哩 \(゜-゜\)つロ 干杯~-bilibili](https://www.bilibili.com/video/av90893381/)
  * 同向: 283
  * 双向: 125

## 2. 两数相加    60min + 10min

===========================================================

几个问题:

* 要不要另建新节点?
* 循环终止的条件是什么? 是p-&gt;next判空还是p判空?
* 任务可以分成阶段

### 错解

```cpp
/*错误: 由于不是另外创建新节点的情况, 最后p和q都走到null时无法再创建那个由于可能进位而产生的新节点了*/
class Solution {
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        if(l1==nullptr || l2==nullptr) return nullptr;
        ListNode* p=l1, *q=l2;    // 最后要返回, 所以要留着l1和l2
        bool c=false;
        bool ifl1=true;

        // 将任务分为三个阶段, 1.相同长度的部分;2.l1或l2多出的一部分;3.可能有的最后进位
        while(p!=nullptr && q!=nullptr) {    // 是判断p的next还是p
            p->val+=c?(q->val+1):(q->val);
            if(c)c=false;
            if(p->val>=10) {    // 记录进位
                p->val%=10;
                c=true;
            }
            q->val=p->val;

            p=p->next; q=q->next;
        }

        while(p!=nullptr) {
            p->val+=c?1:0;
            if(c)c=false;
            if(p->val>=10) {
                p->val%=10;
                c=true;
            }

            p=p->next;
        }
        while(q!=nullptr) {
            q->val+=c?1:0;
            if(c)c=false;
            if(q->val>=10) {
                q->val%=10;
                c=true;
            }
            if(ifl1) ifl1=false;

            q=q->next;
        }

        if(c) {
            ListNode* end = ifl1?p:q;
            end = new ListNode(1);
        }

        return ifl1?l1:l2;
    }
};
```

### 通过

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        if(l1==nullptr || l2==nullptr) return nullptr;
        ListNode* p=l1, *q=l2;
        bool c=false;
        bool ifl1=true;

        // 将任务分为三个阶段, 1.相同长度的部分;2.l1或l2多出的一部分;3.可能有的最后进位
        while(p!=nullptr && q!=nullptr) {
            p->val+=c?(q->val+1):(q->val);
            if(c)c=false;
            if(p->val>=10) {    // 记录进位
                p->val%=10;
                c=true;
            }
            q->val=p->val;  //

            p=p->next; q=q->next;
        }

        while(p!=nullptr) {
            p->val+=c?1:0;
            if(c)c=false;
            if(p->val>=10) {
                p->val%=10;
                c=true;
            }

            p=p->next;
        }
        while(q!=nullptr) {
            q->val+=c?1:0;
            if(c)c=false;
            if(q->val>=10) {
                q->val%=10;
                c=true;
            }
            if(ifl1) ifl1=false;

            q=q->next;
        }

        if(c) {
            ListNode* end = ifl1?p:q;
            end = new ListNode(1);
        }

        return ifl1?l1:l2;
    }
};
```

把共同的那部分长度和l1或l2多出来的那部分长度对应的代码写到一个循环中也是可行的; 不过, 同时完成"不另创建节点"和"单循环"比较麻烦;

### 改进

```text
TODO
```

## 3. 无重复字符的最长子串 30min

===========================================================

* 因为是子串, 所以必须是连续的;
* 只需要求长度, 所以不需要记录子串具体是什么;

技巧:

* "访问表"除了自然使用Hash之外, 可以简单地采用int或bool数组;
* 滑动窗口;[这个题解](https://leetcode-cn.com/problems/longest-substring-without-repeating-characters/solution/hua-dong-chuang-kou-by-powcai/)

先朴素地解:

```cpp
class Solution {
public:
    int lengthOfLongestSubstring(string s) {

        // 初始化一个字母表
        int len=s.length();
        int cnt=0;

        for(int i=0; i<len; ++i) {
            // 结果测试用例里竟然有空格...
            bool alpbt[256]={0};
            int curlen = 1;
            int idx = s[i];
            alpbt[idx]=true;
            for(int j=i+1; j<len; ++j) {
                int curidx = s[j];
                if(alpbt[curidx]) {// 如果这个字母已经出现
                    break;
                }else {
                    alpbt[curidx]=true;
                    ++curlen;
                }
            }
            cnt = (curlen>cnt) ? curlen : cnt;
        }
        return cnt;
    }
};
```

## 4. 寻找两个有序数组的中位数 60min

朴素的想法:

* 把一个数组的元素插入到另一个里面;
  * 给的是vector, 插入需要移动很多元素;
* 找到中位数;

```cpp
class Solution {
public:

    double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {
        int total = nums1.size() + nums2.size();

        multiset<int> ms;   /// 因为可能有重复元素, 所以需要multiset
        for(int i=0; i<nums1.size(); ++i) ms.insert(nums1[i]);
        for(int i=0; i<nums2.size(); ++i) ms.insert(nums2[i]);

        multiset<int>::iterator ite=ms.begin();
        int limit=round((double)total/2); /// 四舍五入; 且需要类型转换, 否则total/2会丢小数;
        for(int i=1; i<limit; ++i) { /// 共total个, "中间的"在从1起total/2处, 或从0起total/2-1处
            ite++;
        }
        int tmp=*(ite);
        double mid = (total%2==0) ? (double)(tmp+*(++ite))/2 : tmp; /// 注意类型转换
        return mid;
    }
};
```

上面的方法是使用自动排序的容器实现有序的, 由于给的两个数组是有序的, 想到二分法也是比较自然的方法;

## 5. 最长回文子串

* 最后要输出最长的回文子串, 可以记录下当前最长的

```cpp
class Solution {
public:
    bool isPalindrome(string s) {
        int len = s.length();
        if(len==0 || len==1) return true;
        int i=0;
        int j=s.length()-1;
        while(i<j) {
            if(s[i]!=s[j]) return false;
            ++i;
            --j;
        }
        return true;
    }
    string longestPalindrome(string s) {
        int len = s.length();
        int max = 0;
        string res;
        int i = 0;
        int j = 0;
        while(i<len && j<len) {
            int n = j-i+1;
            string sub = s.substr(i, n);
            if(isPalindrome(sub)) {
                ++j;
                if(n>max) {
                    max = n;
                    res = sub;
                }
            }else {
                ++i;    /// 这样的代码是不对的, 因为若当前sub不是回文, 可以步进j也可以步进i
            }
        }
        return res;
    }
};
```

```cpp
class Solution {
public:
    bool isPalindrome(string s) {
        int len = s.length();
        if(len==0 || len==1) return true;
        int i=0;
        int j=s.length()-1;
        while(i<j) {
            if(s[i]!=s[j]) return false;
            ++i;
            --j;
        }
        return true;
    }
    string longestPalindrome(string s) {
        int len = s.length();
        int max = 0;
        string res;
        int i = 0;
        int j = 0;
        while(i<len && j<len) {
            int n = j-i+1;
            string sub = s.substr(i, n);
            if(isPalindrome(sub)) {
                ++j;
                if(n>max) {
                    max = n;
                    res = sub;
                }
            }else {
                ++j;    /// 修改为++j肯定也是不对的
            }
        }
        return res;
    }
};
```

### 穷举

```cpp
class Solution {
public:
    bool isPalindrome(string s) {
        int len = s.length();
        if(len==0 || len==1) return true;
        int i=0;
        int j=s.length()-1;
        while(i<j) {
            if(s[i]!=s[j]) return false;
            ++i;
            --j;
        }
        return true;
    }
    string longestPalindrome(string s) {
        int len = s.length();
        if(len==0 || len==1) return s;

        int max = 0;
        string res;
        for(int i = 0; i < len; ++i) {
            for(int j = i + 1; j < len; ++j) {    /// 错误: j从i+1开始, 即是默认要从长度至少为2的子串算起
                string sub = s.substr(i, j-i+1);
                if(isPalindrome(sub)) {
                    int subLen = sub.length();
                    if(subLen>max) {
                        max = subLen;
                        res = sub;
                    }
                }
            }
        }
        return res;
    }
};
```

改成j从i开始, 也不通过, 会超时

```cpp
for(int i = 0; i < len; ++i) {
    for(int j = i; j < len; ++j) {
        ///...
    }
}
```

### 动态规划

动态规划即利用之前记录的信息做新的判断;

在这道题里即, 判断一个子串\[i,j\]是不是回文, 只要判断比它两端各少一个字符是否是回文, 少的这个字符是否相同;

P\[i,j\] = P\[i+1,j-1\] +1 IF s\[i\]==s\[j\]

## 6. Z字形变换

### 按行访问 30min

如果要输出三行, 那么偏移量形如:

```text
+0, +4, +4, +4, ...
+1, +2, +2, +2, ...
+2, +4, +4, +4, ...
```

如果要输出4行, 偏移量形如:

```text
+0, +6, +6, +6, ...
+1, +4, +2, +4, +2, +4...
+2, +2, +4, +2, +4, +2...
+3, +6, +6, +6, ...
```

找规律

## 7. 整数反转

### 借助标准库

```cpp
class Solution {
public:
    int reverse(int x) {
        int sign = 1;
        if(x<0) {
            x=-x;
            sign = -1;
        }
        string str = to_string(x);
        int d = str.length();
        string res = sign>=0 ? "" : "-";
        int pos = sign>=0 ? 0 : 1;
        for(int i=d-1; i>=0; --i) {
            res[pos] = str[i];
            ++pos;
        }
        res[pos] = '\0';
        int num = atoi(res.c_str());
        if(num<0) return 0;
        else return num*sign;
    }
};
```

这个解有问题, 它的结果不可控, 因为r14用res\[pos\]这样赋值的内存还没分配;

* 而且使用atoi的结果是否异号判断是否溢出是不可靠的
  * 只有在类似从合法输入开始逐渐以以1步进自增的输入这样的情况下才会观测到从同号到异号的转变;
  * 一旦输入的是x=9646324351这样的数则输出1056389759, 仍然同号, 因为x=2\*\(-INT\_MIN\) + 1056389759, 已经"溢出了好几轮";
  * \(int32位, 有2147483648个非负数, 2147483648个负数, 一共4294967296个数\)
  * 所以不要使用atoi\(\);

使用strtol的过程也不是一帆风顺:

```cpp
class Solution {
public:
    int reverse(int x) {
        int sign = 1;
        if(x<0) {
            x=-x;
            sign = -1;
        }
        string str = to_string(x);
        int d = str.length();
        //string res = sign>=0 ? "" : "-";
        char res[34];
        res[0] = (sign>=0) ? '\0':'-';
        int pos = sign>=0 ? 0 : 1;
        for(int i=d-1; i>=0; --i) {
            res[pos] = str[i];
            ++pos;
        }
        res[pos] = '\0';

        int num = strtol(res, NULL, 10);

        if(errno==ERANGE) {
            return 0;
        }else return num;
    }
};
```

输入2147483647, 在我的电脑运行, 正常输出0;但是在LC提交, 输出-1126087180;

* strtol与atoi的行为不同, strtol溢出之后不是直接返回0, 而是返回溢出突破的界限;

### 自己实现

```cpp
class Solution {
public:
    int reverse(int x) {
        int sign = 1;
        if(x<0) {
            x=-x;
            sign = -1;
        }
        string str = to_string(x);
        int d = str.length();
        long long int res = 0;
        for(int i=0; i<d; ++i) {
            res+=(str[i]-'0')*pow(10, i);   // 10^i
        }
        if(res>INT32_MAX || res<INT32_MIN) {  // 这里可以等于
            return 0;
        }
        return res*sign;
    }
};
```

上面有问题, 当输入-2147483648时在r6会报错: `Line 7: Char 14: runtime error: negation of -2147483648 cannot be represented in type 'int'; cast to an unsigned type to negate this value to itself (solution.cpp)`, 因为INT\_MIN的相反数不能用int表示;

我在本地用Clang调试没有报错, 但x=INT\_MIN时, 执行完x=-x之后x没变; 不知道如何解释;

可以用long long中转, 这样就对了:

```cpp
class Solution {
public:
    int reverse(int x) {
        int sign = 1;
        long long int num = x;
        if(x<0) {
            num*=-1;
            sign = -1;
        }
        string str = to_string(num);
        int d = str.length();
        long long int res = 0;
        for(int i=0; i<d; ++i) {
            res+=(str[i]-'0')*pow(10, i);
        }
        if(res>=INT32_MAX || res<=INT32_MIN) {
            return 0;
        }
        return res*sign;
    }
};
```

* 实际上除了INT\_MIN其他数的相反数都还在int范围内;
* 使用x^y判断是否异号, 防止x\*y有可能溢出;

## 8. 字符串转换整数 60min

```cpp
class Solution {
public:
    int myAtoi(string str) {
        int len = str.length();
        int i=0;
        long long int num=0;
        int sign=1;
        int start = 0,end = 0;
        while(i<len && isspace(str[i])) {
            ++i;
        }
        if(i>=len) return 0;
        switch(str[i]) {
            case '+': {
                sign = 1;
                break;
            }
            case '-': {
                sign = -1;
                break;
            }
            default: {
                if(isdigit(str[i])) {
                    start = i;
                    ++i;
                }else return 0;
            }
        }
        for(; i<len; ++i) {
            if(isdigit(str[i])) {
                end = i;
                continue;
            }else {
                break;
            }
        }
        for(int j = 0; end-j>=start; ++j) {
            num += (str[end-j]-'0')*pow(10, j);
        }
        num*=sign;
        if(num < INT32_MIN) return INT32_MIN;
        if(num > INT32_MAX) return INT32_MAX;
        return num;
    }
};
```

输入"-45", 输出3;

因为忘了管理start

```cpp
class Solution {
public:
    int myAtoi(string str) {
        int len = str.length();
        int i=0;
        long long int num=0;
        int sign=1;
        int start = 0,end = 0;
        while(i<len && isspace(str[i])) {
            ++i;
        }
        if(i>=len) return 0;
        switch(str[i]) {
            case '+': {
                sign = 1;
                i++;
                start = i;// 准备从符号的下一个开始
                break;
            }
            case '-': {
                sign = -1;
                i++;
                start = i;// 准备从符号的下一个开始
                break;
            }
            default: {
                if(isdigit(str[i])) {
                    start = i;// 从第一个数字开始
                    i++;
                }else return 0;
            }
        }
        for(; i<len; ++i) {
            if(isdigit(str[i])) {
                end = i;
                continue;
            }else {
                break;
            }
        }
        if(end<start) return 0;
        for(int j = 0; end-j>=start; ++j) {
            num += (str[end-j]-'0')*pow(10, j);
        }
        num*=sign;
        if(num < INT32_MIN) return INT32_MIN;
        if(num > INT32_MAX) return INT32_MAX;
        return num;
    }
};
```

然而还没有AC;

输入:"20000000000000000000"时没有返回正确的一端;20000000000000000000=2×10^19; INT64=9,223,372,036,854,775,808=9.2...×10^18; UINT64=18,446,744,073,709,551,616‬=1.8...×10^19; unsigned long long也不够用了;

换成浮点数可以;

```cpp
class Solution {
public:
    int myAtoi(string str) {
        int len = str.length();
        int i=0;
        long double num=0;
        int sign=1;
        int start = 0,end = 0;
        while(i<len && isspace(str[i])) {
            ++i;
        }
        if(i>=len) return 0;
        switch(str[i]) {
            case '+': {
                sign = 1;
                i++;
                start = i;// 准备从符号的下一个开始
                break;
            }
            case '-': {
                sign = -1;
                i++;
                start = i;// 准备从符号的下一个开始
                break;
            }
            default: {
                if(isdigit(str[i])) {
                    start = i;// 从第一个数字开始
                    end = start;// #包含只有一位数字的情况
                    i++;
                }else return 0;
            }
        }
        for(; i<len; ++i) {
            if(isdigit(str[i])) {
                end = i;
                continue;
            }else {
                break;
            }
        }
        if(end<start) return 0;
        for(int j = 0; end-j>=start; ++j) {
            num += (str[end-j]-'0')*pow(10, j);
        }
        num*=sign;
        if(num < INT32_MIN) return INT32_MIN;
        if(num > INT32_MAX) return INT32_MAX;
        return num;
    }
};
```

## 9. 回文数 10min+20min

可以转化成string处理

```cpp
class Solution {
public:
    bool isPalindrome(int x) {
        string str = to_string(x);
        int d = str.length();

        for(int i=0; i<d-1-i; ++i) {    // d-1-i可能比除法快一点
            if(str[i]!=str[d-1-i]) return false;
        }
        return true;
    }
};
```

```text
执行用时：8 ms        96%
内存消耗：8.2 MB        19%
```

不转化成string

把x逆置

```cpp
class Solution {
public:
    bool isPalindrome(int x) {
        if(x<0) return false;   // 负数因为有负号肯定不是回文数

        int y = x;
        int r = 0;
        while(y) {
            r*=10;
            r+=(y%10);
            y/=10;
        }

        return x==r;
    }
};
```

由于x是int, 那么它逆置后有可能超出int 的范围;

这样就可以了:

```cpp
class Solution {
public:
    bool isPalindrome(int x) {
        if(x<0) return false;   // 负数因为有负号肯定不是回文数

        int y = x;
        long long int r = 0;    // # 用64位来装逆置数

        int i=0;
        while(y) {
            r*=10;
            r+=(y%10);
            y/=10;
        }

        return x==r;
    }
};
```

```text
执行用时：8 ms        96%
内存消耗：7.9 MB        88%
```

节省了许多空间

## 19. 删除链表的倒数第N个节点 20min

我的想法:

* 为了一遍完成, 就用空间换时间, 用vector存指针;
* 实际上为了省时间, vector应该赋一个合适的初始的容量;

```cpp
class Solution {
public:
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        ListNode pre(-1); /// 备份表头节点
        pre.next = head;    /// 要点1: 为了size个节点时删除倒数第size个, 需要多准备一个节点

        vector<ListNode*> v;
        v.push_back(&pre);
        while(head) {
            v.push_back(head);
            head = head->next;
        }
        int size = v.size();
        v[size-n-1]->next = v[size-n]->next; /// 要删倒数第n个, 即删去从1开始的第size-n+1个
        delete v[size-n]; /// 释放一下节点;
        return pre.next;
    }
};
```

## 26. 删除排序数组中的重复元素 16min

### 使用vector的erase

```cpp
class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
        int size = nums.size();
        for(int i=1; i<size; ++i) {
            if(nums[i]==nums[i-1]) {
                nums.erase(nums.begin() + i);
                --size;
                --i;
            }
        }
        return nums.size();
    }
};
```

```text
执行用时 :224 ms, 在所有 C++ 提交中击败了10.42%的用户
内存消耗 :15.3 MB, 在所有 C++ 提交中击败了5.36%的用户
```

性能较差;

使用erase应该需要把被所有删除项整体移动, 但是这道题不需要;

### 双指针

让快指针搜集不同的元素即可

```cpp
class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
        int size = nums.size();
        if(size==0) return 0;
        int i=0;
        for(int j=1; j<size; ++j) {
            if(nums[i]==nums[j]) {
                continue;
            }else {
                ++i;
                nums[i] = nums[j];
            }
        }
        return i+1;
    }
};
```

```text
执行用时 :8 ms, 在所有 C++ 提交中击败了99.64%的用户
内存消耗 :15.2 MB, 在所有 C++ 提交中击败了5.36%的用户
```

性能显著提升;

但是空间复杂度还很高, 尝试用了resize也还是5%;

## 46. 全排列

### 回溯

参照: [回溯算法详解 - 全排列 - 力扣（LeetCode）](https://leetcode-cn.com/problems/permutations/solution/hui-su-suan-fa-xiang-jie-by-labuladong-2/) 中回溯算法框架, 可以写出如下代码;

概括一下思想:

多叉树的

着重想如下关键问题:

* 路径: 已经确定位置的元素, 装在path里
* 选择列表: 这里就是nums
* 结束条件/递归退出条件: 当path的长度和nums相同, 所有的元素都选完时退出
* 选择策略: 只需要确保不加入重复元素, 题目也确保了测试用例没有重复元素; 在顺序上没有要求, i倒过来遍历也可以, 就是从从大到小的情况开始permute; 例如输出`[[3,2,1],[3,1,2],[2,3,1],[2,1,3],[1,3,2],[1,2,3]]`

```cpp
class Solution {
public:
    void backtrace(vector<vector<int>>& res, vector<int>& path, vector<int>& nums) {
        int n = nums.size();
        if(path.size() == n) {
            res.push_back(path);
            return;
        }
        for(int i=0; i<n; ++i) {
            if(find(path.begin(), path.end(), nums[i]) != path.end()) continue;
            path.push_back(nums[i]);
            backtrace(res, path, nums);
            path.pop_back();
        }
    }
    vector<vector<int>> permute(vector<int>& nums) {
        int n = nums.size();
        vector<vector<int>> res;
        if(n==0) return res;
        vector<int> path;

        backtrace(res, path, nums);
        return res;
    }
};
```

并没有STL的快

```text
执行用时 :8 ms, 在所有 C++ 提交中击败了70.19%的用户
内存消耗 :9.8 MB, 在所有 C++ 提交中击败了23.27%的用户
```

上解用的std::find\(\)查元素是否已选, 可以再用一个`bool checked[]`数组换一点时间;

```cpp
class Solution {
public:
    void backtrace(vector<vector<int>>& res, vector<int>& path, vector<int>& nums, vector<bool>& checked) {
        int n = nums.size();
        if(path.size() == n) {
            res.push_back(path);
            return;
        }
        for(int i=0; i<n; ++i) {
            if(checked[i]) continue;
            path.push_back(nums[i]);
            checked[i] = true;
            backtrace(res, path, nums, checked);
            path.pop_back();
            checked[i] = false;
        }
    }
    vector<vector<int>> permute(vector<int>& nums) {
        int n = nums.size();
        vector<vector<int>> res;
        if(n==0) return res;
        vector<int> path;
        vector<bool> checked(n, false);

        backtrace(res, path, nums, checked);
        return res;
    }
};
```

效果显著

```text
执行用时 :4 ms, 在所有 C++ 提交中击败了93.02%的用户
内存消耗 :10 MB, 在所有 C++ 提交中击败了17.70%的用户
```

### STL

```cpp
class Solution {
public:
    vector<vector<int>> permute(vector<int>& nums) {
        int n = nums.size();
        vector<vector<int>> res;
        if(n==0) return res;
        do {
            res.push_back(nums);
        } while(next_permutation(nums.begin(), nums.end()));

        return res;
    }
};
```

这个代码看似是正确的, 然而由于初始的nums排列不一定是"第一个排列", 所以有可能输出不全;

比如:

```text
输入:
[0,-1,1]
输出
[[0,-1,1],[0,1,-1],[1,-1,0],[1,0,-1]]
预期结果
[[0,-1,1],[0,1,-1],[-1,0,1],[-1,1,0],[1,0,-1],[1,-1,0]]
```

正解是先从小到大排一下序, 虽然顺序可能与标准答案不同, 但给过了:

```cpp
class Solution {
public:
    vector<vector<int>> permute(vector<int>& nums) {
        int n = nums.size();
        vector<vector<int>> res;
        sort(nums.begin(), nums.end());
        if(n==0) return res;
        do {
            res.push_back(nums);
        } while(next_permutation(nums.begin(), nums.end()));

        return res;
    }
};
```

```text
执行用时 :4 ms, 在所有 C++ 提交中击败了93.02%的用户
内存消耗 :9.8 MB, 在所有 C++ 提交中击败了23.18%的用户
```

## [53. 最大子序和](https://leetcode-cn.com/problems/maximum-subarray/)

这道题中文题目名有点误导人, 其实要求的是最大和的连续子数组;

### 穷举

以9个元素为例, 它长为1的子数组有9个, 长为2的子数组有8个...; 可以得到n个元素, 有$\sum\limits\_{i=1}^n {i} = \frac{\(1+n\)n}{2}$个不同的子数组; 复杂度为$O\(n^2\)$;

```cpp
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        int n = nums.size();
        int max = INT_MIN;
        for(int i=0;i<n;++i) {
            int sum = 0;
            for(int j=i;j<n;++j) {
                sum+=nums[j];
                if(sum > max) max = sum;
            }
        }
        return max;
    }
};
```

十分缓慢; 这还是避免了重复计算从i到j$O\(n^3\)$复杂度之后的结果;

```text
执行用时 :516 ms, 在所有 C++ 提交中击败了5.06%的用户
内存消耗 :15.4 MB, 在所有 C++ 提交中击败了5.06%的用户
```

### 双指针动态和

* 有两个动作可以选择, i++或者j++;
* 判断

```cpp
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        int len = nums.size();
        int i=0, j=0;
        int sum = nums[i];
        while(i<len && j<len) {
            if(sum+nums[j]<sum-nums[i]) {
                sum = sum-nums[i];
                ++i;
            }else {
                sum = sum+nums[j];
                ++j;
            }
        }
        return sum;
    }
};
```

### 动态规划

* 以i结尾的最大子数组$dp\[i\] = max\(dp\[i-1\] + nums\[i\], nums\[i\]\);$ 即, 如果之前的子数组求和dp\[i-1\]如果对当前的$dp\[i\]$没有增益, 那么$dp\[i\]$就重新从$nums\[i\]$开始算;
* $dp\[i-1\]$简化为上一次循环的$dp$;

```cpp
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        if(nums.size()==0) return 0;
        int m = INT_MIN;
        int dp = 0;
        for(int i=0; i<nums.size(); ++i) {
            dp = max(nums[i], dp + nums[i]);
            m = max(m, dp);
        }
        return m;
    }
};
```

```text
执行用时 :4 ms, 在所有 C++ 提交中击败了98.08%的用户
内存消耗 :14.5 MB, 在所有 C++ 提交中击败了5.20%的用户
```

### 贪心算法?

```cpp
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        int n = nums.size();
        int currSum = 0, maxSum = nums[0];
        for(int i=1; i<n; ++i) {
            currSum = max(nums[i], currSum + nums[i]);
            maxSum = max(maxSum, currSum);
        }
        return maxSum;
    }
};
```

## 92. 反转链表 II 60min+60min

错解分析1:

```cpp
class Solution {
public:
    ListNode* reverseBetween(ListNode* head, int m, int n) {
        ListNode* res = head;   /// 备份原来head
        ListNode* left = head;
        int i=1;
        for(;i<m;++i) { /// 移动head到从第1个起第m个
            left = head;    /// 连接
            head = head->next;
        }/// i<m的条件说明循环完成时i=m

        ListNode* p = head;
        while(head!=nullptr && i<n) {
            ListNode* nxt = head->next;
            head->next = p;
            p = head;
            head = nxt;
            ++i;
        }
        ListNode* right = p;
        while(right->next) {
            right = right->next;        /*#ERROR#:会卡在这里无限循环*/
        }
        right->next = head;
        return res;
    }
};
```

错解分析2:

```cpp
class Solution {
public:
    ListNode* reverseBetween(ListNode* head, int m, int n) {
        ListNode* res = head;   /// 备份原来head
        ListNode* left = head;
        int i=1;
        for(;i<m;++i) { /// 移动head到从第1个起第m个
            left = head;    /// 逆置部分原头节点的前驱
            head = head->next;
        }/// i<m的条件说明循环完成时i=m
        ListNode* r = head; /// #改进# 逆置部分原头节点, 最后要作为逆置部分逆置后尾节点连接后方节点

        ListNode* p = head;
        while(head!=nullptr && i<n) {
            ListNode* nxt = head->next;
            head->next = p;
            p = head;
            head = nxt;
            ++i;
        }
        r->next = head;
        return res;
    }
};
```

输入:\[1,2,3,4,5\] 2 4; 输出:\[1,2,4,5\], 而不是:\[1,4,3,2,5\];

在纸上画图分析, 理清最后的"归位"操作, 改为:

```cpp
class Solution {
public:
    ListNode* reverseBetween(ListNode* head, int m, int n) {
        ListNode* res = head;   /// 备份原来head
        ListNode* left = head;
        int i=1;
        for(;i<m;++i) { /// 移动head到从第1个起第m个
            left = head;    /// 逆置部分原头节点的前驱
            head = head->next;
        }/// i<m的条件说明循环完成时i=m
        ListNode* r = head; /// 逆置部分原头节点, 最后要作为逆置部分逆置后尾节点连接后方节点

        ListNode* p = nullptr;
        while(head!=nullptr && i<=n) { 
            /// #明确# 中间有n-m+1个节点, 这个循环需要执行n-m+1次, 从i=m到n-1执行了n-m次
            ListNode* nxt = head->next;
            head->next = p;
            p = head;
            head = nxt;
            ++i;
        }
        left->next = p; /// #明确#逆置部分逆置后头节点就绪
        r->next = head; /// #明确#逆置部分逆置后尾结点就绪
        return res;
    }
};
```

但输入:\[1,2,3,4,5\] 1 5;时错误

对m是否等于1, n是否等于最后一个的用例分别分析, 最终:

```cpp
class Solution {
public:
    ListNode* reverseBetween(ListNode* head, int m, int n) {
        ListNode* res = head;   /// 备份原来head
        ListNode* left = nullptr; /// #改动#
        int i=1;
        for(;i<m;++i) { /// 移动head到从第1个起第m个
            left = head;    /// 逆置部分原头节点的前驱
            head = head->next;
        }/// i<m的条件说明循环完成时i=m
        ListNode* r = head; /// 逆置部分原头节点, 最后要作为逆置部分逆置后尾节点连接后方节点

        ListNode* p = nullptr;
        while(head!=nullptr && i<=n) {
            ///中间有n-m+1个节点, 这个循环需要执行n-m+1次, 从i=m到n-1执行了n-m次
            ListNode* nxt = head->next;
            head->next = p;
            p = head;
            head = nxt;
            ++i;
        }
        /// #改动# 注意到当m=1与否情况下, 最后归位的操作不同
        if(left) {/// 如果left非空, 即m不是第一个
            left->next = p; /// 逆置部分逆置后头节点就绪
        }else {/// 如果m是第一个
            res = p;
        }
        r->next = head; /// 逆置部分逆置后尾结点就绪
        return res;
    }
};
```

## 94. 二叉树的中序遍历

递归解法, 在144基础上改动;

## 101. 对称二叉树

```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    bool isSymmetric(TreeNode* root) {
        if(root==nullptr) return false;
        if(root->left != root->right) return false;

        queue<TreeNode*> q;
        q.push(root);
        while(!q.empty()) {
            int size = q.size();
            vector<TreeNode*> v(2*size);
            for(int i=0; i<size; ++i) {
                TreeNode* t = q.front();
                q.pop();

                if(t->left!=nullptr) {
                    q.push(t->left);
                    v.push_back(t->left);
                }else v.push_back(nullptr);
                if(t->right!=nullptr) {
                    q.push(t->right);
                    v.push_back(t->right);
                }else v.push_back(nullptr);
            }
            int vsize =v.size();
            for(int i=0; i<vsize-i-1; ++i) {
                if(v[i]==nullptr && v[vsize-i-1]==nullptr) continue;
                if(v[i]!=nullptr) {
                    if(v[vsize-i-1]==nullptr) return false;
                    if(v[i]->val!=v[vsize-i-1]->val) return false;
                    else continue;
                }
            }
        }
        return true;
    }
};
```

90min

### 递归解法

* 一个树是对称的:=它的左子树和右子树互为镜像;
* 树A和树B互为镜像:=树A根节点的值与树B根节点相等 && A的左子树与B的右子树互为镜像 && A的右子树与B的左子树互为镜像;

```cpp
class Solution {
public:
    bool isMirror(TreeNode* l, TreeNode* r) {
        if(l->val!=r->val) return false;    // 如果值不相等, 不互为镜像;
        if(l->left!=nullptr && r->right!=nullptr) {
            if(l->right!=nullptr && r->left!=nullptr) {
                return isMirror(l->left, r->right) && isMirror(l->right, r->left);
            }else if(l->right==nullptr && r->left==nullptr) return isMirror(l->left, r->right);
            else return false;
        }else if(l->left==nullptr && r->right==nullptr) {
            if(l->right!=nullptr && r->left!=nullptr) {
                return isMirror(l->right, r->left);
            }else if(l->right==nullptr && r->left==nullptr) return true;
            else return false;
        }else return false;
    }
    bool isSymmetric(TreeNode* root) {
        if(root==nullptr) return true;
        if(root->left!=nullptr && root->right!=nullptr) 
            return isMirror(root->left, root->right);
        if(root->left==nullptr && root->right==nullptr)
            return true;
        return false;
    }
};
```

```text
执行用时 :12 ms, 在所有 C++ 提交中击败了28.94%的用户
内存消耗 :18.6 MB, 在所有 C++ 提交中击败了5.06%的用户
```

上面的代码中isMirror\(l,r\)其实是针对非空情况来设计的; 对可以为空的参数l和r考虑:

```cpp
class Solution {
public:
    bool isMirror(TreeNode* l, TreeNode* r) {
        if(l==nullptr && r==nullptr) return true;
        if(l==nullptr || r==nullptr) return false;
        return l->val==r->val && isMirror(l->left, r->right) && isMirror(l->right, r->left);
    }
    bool isSymmetric(TreeNode* root) {
        if(root==nullptr) return true;
        return isMirror(root->left, root->right);
    }
};
```

性能没有显著变化;

### 迭代解法

## 102. 二叉树的层序遍历 50min

### 迭代解法

用队列实现, 教科书解法, 很容易就能写出层序遍历;

* 但这道题还需要把不同的层放到不同vector里

  只层序遍历没法同一层打印一次

```cpp
class Solution {
public:
    queue<TreeNode*> q;
    vector<vector<int>> levelOrder(TreeNode* root) {
        q.push(root);    /// 要点1: 循环之前把root入队
        while(!q.empty()) {
            TreeNode* t = q.front();
            q.pop();
            if(t->left) q.push(t->left);    /// 要点2:注意这里要判空
            if(t->right) q.push(t->right);
        }
        ///...
    }
};
```

关于C/C++的备注:

* C++容器判空的标准写法就是if\(!t.empty\(\)\)
* C++的stack和queue都用push\(x\)和pop\(\); 而且pop\(\)都只弹元素不返回元素;

正解:

注释标记了其他6个要点;

```cpp
class Solution {
public:
    queue<TreeNode*> q;
    vector<vector<int>> levelOrder(TreeNode* root) {
        vector<vector<int>> res;
        if(root==nullptr) return res;
        q.push(root);
        vector<int> rval;
        rval.push_back(root->val);
        res.push_back(rval);    /// 先把根节点输出
        while(!q.empty()) {
            vector<int> v;
            int size = q.size();    /// 当前队列里的元素数量
/// 把"入队"操作放在size控制的循环里面, 而不是直接放到每次while循环中, 即一次性可得到在同一层的结点  
            for(int i = 0; i<size; ++i) {
                TreeNode* t = q.front();
                q.pop();
                if(t->left)  {  /// 这里假设输出时不需要输出null元素
                    q.push(t->left);
                    v.push_back(t->left->val);
                }
                if(t->right) {
                    q.push(t->right);
                    v.push_back(t->right->val);
                }
            }
            if(!v.empty()) res.push_back(v);    /// 最后一层可能会产生一个空vector, 忽略
            /// C++的二维vector不能像Java引用那样使用, 因为是按值传递(?), push_back要放在修改v之后, 否则v是空的;
            /// 而且还要放在for循环外面
        }
        return res;
    }
};
```

发现上面那个方法比较愚蠢, 没有必要把v.push\_back放在检测子树的地方;

```cpp
class Solution {
public:
    queue<TreeNode*> q;
    vector<vector<int>> levelOrder(TreeNode* root) {
        vector<vector<int>> res;
        if(root==nullptr) return res;
        q.push(root);
        while(!q.empty()) {
            vector<int> v;
            int size = q.size();    /// 当前队列里的元素数量
            for(int i = 0; i<size; ++i) {
                TreeNode* t = q.front();
                q.pop();
                v.push_back(t->val);
                if(t->left) q.push(t->left);
                if(t->right) q.push(t->right);
            }
            res.push_back(v);
        }
        return res;
    }
};
```

```text
执行用时：8 ms        64%
内存消耗：15 MB        9%
```

### 递归解法

## 103. 二叉树的锯齿形层序遍历

* 使用一个bool记录方向;
* C++ STL中的双向链表list较为合适;
* 注意

```cpp
class Solution {
public:
    vector<vector<int>> zigzagLevelOrder(TreeNode* root) {
        bool dlr = true;    // direction left->right
        vector<vector<int>> res;
        if(root==nullptr) return res;
        list<TreeNode*> q;
        q.push_back(root);
        while(!q.empty()) {
            int size = q.size();
            vector<int> v;
            for(int i=0; i<size; ++i) {
                if(dlr) {// left-->right
                    TreeNode* t = q.back();
                    q.pop_back();
                    v.push_back(t->val);
                    if(t->left) q.push_front(t->left);
                    if(t->right) q.push_front(t->right);
                }else { // left<--right
                    TreeNode* t = q.front();
                    q.pop_front();
                    v.push_back(t->val);
                    if(t->right) q.push_back(t->right);
                    if(t->left) q.push_back(t->left);
                }
            }
            res.push_back(v);
            dlr = !dlr;
        }
        return res;
    }
};
```

```text
执行用时 :8 ms, 在所有 C++ 提交中击败了53.99%的用户
内存消耗 :14.5 MB, 在所有 C++ 提交中击败了5.03%的用户
```

## 104. 二叉树的最大深度

### 递归\(递归实现DFS\)

非空二叉树的深度等于$1+ \max \(左子树高度, 右子树高度\)$

```cpp
class Solution {
public:
    int helper(TreeNode* tree) {
        if(tree==nullptr) return 0;
        return 1+max(helper(tree->left), helper(tree->right));
    }
    int maxDepth(TreeNode* root) {
        return helper(root);
    }
};
```

```text
执行用时 :16 ms, 在所有 C++ 提交中击败了43.66%的用户
内存消耗 :21.7 MB, 在所有 C++ 提交中击败了5.05%的用户
```

### 迭代\(用栈实现DFS\) 但是没法记录深度

```cpp
class Solution {
public:
    int maxDepth(TreeNode* root) {
        if(root==nullptr) return 0;
        stack<TreeNode*> s;
        int maxDep = 0;
        int curDep = 0;
        s.push(root);
        while(!s.empty()) {
            TreeNode* t = s.peek();
            s.pop();
            ++curDep;
            maxDep = max(maxDep, curDep);
            if(t->right) s.push(t->right);
            if(t->left) s.push(t->left);
        }
    }
};
```

### 迭代\(用队列实现BFS\)

这样其实比较自然

```cpp
class Solution {
public:
    int maxDepth(TreeNode* root) {
        if(root==nullptr) return 0;
        queue<TreeNode*> q;
        int maxDep = 0;
        q.push(root);
        while(!q.empty()) {
            int size = q.size();
            for(int i=0; i<size; ++i) {
                TreeNode* t = q.front();
                q.pop();
                if(t->left) q.push(t->left);
                if(t->right) q.push(t->right);
            }
            ++maxDep;
        }
        return maxDep;
    }
};
```

效率没有什么变化

```text
执行用时 :16 ms, 在所有 C++ 提交中击败了43.66%的用户
内存消耗 :21.3 MB, 在所有 C++ 提交中击败了5.05%的用户
```

## 110. 平衡二叉树

```cpp
class Solution {
public:
    int helper(TreeNode* t) {
        int l = (t->left) ? helper(t->left) : 0;
        int r = (t->right) ? helper(t->right) : 0;
        return l-r;
    }
    bool isBalanced(TreeNode* root) {

    }
};
```

平衡二叉树它的左子树和右子树平衡因子之差的绝对值小于2

## 121. 买卖股票的最佳时机

### 枚举

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int n = prices.size();
        int max = 0;
        for(int i=0; i<n; ++i) {
            for(int j=i+1; j<n; ++j) {
                int profit = prices[j]-prices[i];
                if(profit>max) max = profit;
            }
        }
        return max;
    }
};
```

略

```text
执行用时 :1256 ms, 在所有 C++ 提交中击败了9.94%的用户
内存消耗 :15.1 MB, 在所有 C++ 提交中击败了5.09%的用户
```

不成熟的想法:

* 本题可以理解为求一个数组中各元素的极差$a\_i-a\_j$, 但要求$i&lt;j$
* 如果是单纯求极差, 那么过一遍数组就可以发现最小值最大值求出

### 动态规划

* 参照[121. 买卖股票的最佳时机 - dp 7 行简约风格 - 买卖股票的最佳时机 - 力扣（LeetCode）](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock/solution/121-mai-mai-gu-piao-de-zui-jia-shi-ji-dp-7-xing-ji/)
* 这道题包含了[53. 最大子序和](https://leetcode-cn.com/problems/maximum-subarray/)的解题思路;
* 概括一下: 求数组中$a_i-a\_j, \(i&lt;j\)$可以转化为求数组差分在区间上的和$\int\_i^j f\(x\) = \sum\limits_{k=i}^j diff\(k\)$; 例如`[7,1,5,3,6,4]`就得到`diff=[ ,-6,4,-2,3,-2]`;

用这样的方法来枚举, 可使用如下代码

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int n = prices.size();
        int m = 0;
        vector<int> diff(n);
        for(int i=1; i<n; ++i) {
            diff[i] = prices[i] - prices[i-1];
        }
        for(int i=0; i<n; ++i) {
            int sum = 0;
            for(int j=i+1; j<n; ++j) {
                sum += diff[j];
                m = max(sum, m);
            }
        }
        return m;
    }
};
```

* 上面这些枚举都是遍历了每对$\(i,j\)$找最大的, 但实际上本题要求的是整个数组中的$max\(a_i-a\_j\), \(i&lt;j\)$, 也就是$max\[\sum\limits_{k=i}^j diff\(k\)\]$即最大连续子数组的和;
* 设以第$i$个元素结尾※的最大连续子数组的和为$dp\[i\]$, 有$dp\[i\] = max\(0, dp\[i-1\]+diff\[i\]\)$ 就是说, 以$i-1$结尾的和$dp\[i-1\]$加上$diff\[i\]$如果不能为现在的$dp\[i\]$带来增益, 那么不如重新从$i$开始求和\($dp\[i\]$这个和取0\)★;
  * ※注: 包括第i个元素
  * ★注: 为什么取0而不是取$diff\[i\]$? 这道题最后求后一天减前一天价格的差值, 不能是同一天; 于是通过在确定$dp\[i\]$的值时不取$diff\[i\]$来排除"当天买当天卖"的非法情况; 这也是本题与[53. 最大子序和](https://leetcode-cn.com/problems/maximum-subarray/)的一个区别, 注意到LC53中的$dp\[i\] = max\(dp\[i-1\] + nums\[i\], nums\[i\]\)$ , 这才是常规情况;
* 而且得到$dp\[i\]$只依赖它之前的一项$dp\[i-1\]$, 所以没有必要存储整个dp数组, 只保存上一项即可;

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int n = prices.size();
        int m = 0;
        vector<int> diff(n);
        for(int i=1; i<n; ++i) {
            diff[i] = prices[i] - prices[i-1];
        }
        int dp = 0;
        for(int i=1; i<n; ++i) {
            dp = max(0, dp + diff[i]);
            m = max(m, dp);
        }
        return m;
    }
};
```

```text
执行用时 :12 ms, 在所有 C++ 提交中击败了42.83%的用户
内存消耗 :15.3 MB, 在所有 C++ 提交中击败了5.09%的用户
```

而$diff\[i\]$实际上也只依赖$prices\[i\]$和它的前一项$prices\[i-1\]$, 可以把计算都集中到一次遍历;

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int m = 0;

        int dp = 0;
        for(int i=1; i<prices.size(); ++i) {
            dp = max(0, dp + prices[i] - prices[i-1]);
            m = max(m, dp);
        }
        return m;
    }
};
```

```text
执行用时 :8 ms, 在所有 C++ 提交中击败了78.91%的用户
内存消耗 :14.5 MB, 在所有 C++ 提交中击败了5.09%的用户
```

## 125. 验证回文串 30min+30min

### while-while派系

超时了, 死循环;

而且忘了考虑数字了;

```cpp
class Solution {
public:
    bool isPalindrome(string s) {
        int n = s.length();
        int i=0, j=n-1;
        while(i<j) {
            //i向右找到下一个字母
            while(i<j && !isalpha(s[i])) ++i;
            //j向左找到下一个字母
            while(i<j && !isalpha(s[j])) --j;
            // 注意判等于需要注意大小写
            s[i] = isupper(s[i]) ? tolower(s[i]):s[i];
            s[j] = isupper(s[j]) ? tolower(s[j]):s[j];
            if(s[i]!=s[j]) return false;
        }
        return true;
    }
};
```

这样是对的:

```cpp
class Solution {
public:
    bool isPalindrome(string s) {
        int n = s.length();
        int i=0, j=n-1;
        while(i<j) {
            //i向右找到下一个字母或数字--在不越界的情况下, 如果既不是字母也不是数字, 就忽略
            while(i<j && (!isalpha(s[i]) && !isdigit(s[i]))) ++i;
            //j向左找到下一个字母或数字
            while(i<j && (!isalpha(s[j]) && !isdigit(s[j]))) --j;
            // 注意判等于需要注意大小写
            s[i] = isupper(s[i]) ? tolower(s[i]):s[i];
            s[j] = isupper(s[j]) ? tolower(s[j]):s[j];
            if(s[i]!=s[j]) return false;
            // 判断之后要移动, 否则死循环
            ++i; --j;
        }
        return true;
    }
};
```

```text
执行用时：12 ms        50%
内存消耗：9.4 MB        42%
```

回过头来, 因为tolower如果输入的不是大写字母就不改变, 所以去掉多余的判断:

```cpp
class Solution {
public:
    bool isPalindrome(string s) {
        int n = s.length();
        int i=0, j=n-1;
        while(i<j) {
            //i向右找到下一个字母或数字--在不越界的情况下, 如果既不是字母也不是数字, 就忽略
            while(i<j && (!isalpha(s[i]) && !isdigit(s[i]))) ++i;
            //j向左找到下一个字母或数字
            while(i<j && (!isalpha(s[j]) && !isdigit(s[j]))) --j;
            // 把大小写判断去掉, 简化为tolower
            if(tolower(s[i]) != tolower(s[j])) {
                return false;
            }else {
                ++i; --j;
            }
        }
        return true;
    }
};
```

```text
执行用时：8 ms        85%
内存消耗：9.5 MB        30%
```

### while-if派系

还是错误:

在输入'OP'时超时/死循环;

```cpp
class Solution {
public:
    bool isPalindrome(string s) {
        int n = s.length();
        int i=0, j=n-1;
        while(i<j) {
            //i向右找到下一个字母或数字
            if(!isalpha(s[i]) && !isdigit(s[i])) ++i;
            //j向左找到下一个字母或数字
            if(!isalpha(s[j]) && !isdigit(s[j])) --j;
            if(    //##这里错了
                (isalpha(s[i]) && isalpha(s[j]))
                ||(isdigit(s[i]) && isdigit(s[j]))
            ) {
                s[i] = isupper(s[i]) ? tolower(s[i]):s[i];
                s[j] = isupper(s[j]) ? tolower(s[j]):s[j];
                if(s[i]!=s[j]) return false;
                //判断完了一对都要移动
                ++i;
                --j;
            }
        }
        return true;
    }
};
```

改成这样终于对了:

```cpp
class Solution {
public:
    bool isPalindrome(string s) {
        int n = s.length();
        int i=0, j=n-1;
        while(i<j) {
            //i向右找到下一个字母或数字
            if(!isalpha(s[i]) && !isdigit(s[i])) ++i;
            //j向左找到下一个字母或数字
            if(!isalpha(s[j]) && !isdigit(s[j])) --j;
            if(//如果顺着这个思路写, 这里的条件应该兼容数字和字母的情况, 因为这个if之外是继续移动的路径;
                (isalpha(s[i]) || isdigit(s[i])) && (isalpha(s[j]) || isdigit(s[j]))
            ) {
                s[i] = isupper(s[i]) ? tolower(s[i]):s[i];
                s[j] = isupper(s[j]) ? tolower(s[j]):s[j];
                if(s[i]!=s[j]) return false;
                //判断完了一对都要移动
                ++i;
                --j;
            }
        }
        return true;
    }
};
```

```text
执行用时：8 ms        85%
内存消耗：9.4 MB        46%
```

上面的代码控制流有点而臃肿

改成下面, 不过好像变慢了

```cpp
class Solution {
public:
    bool isPalindrome(string s) {
        int n = s.length();
        int i=0, j=n-1;
        while(i<j) {
            //i向右找到下一个字母或数字--在不越界的情况下, 如果既不是字母也不是数字, 就忽略
            //之前的while-while中的两个while部分代码是重合的, 可以只使用一个while
            if(!isalpha(s[i]) && !isdigit(s[i])) {
                ++i;
                continue;
            }
            //j向左找到下一个字母或数字
            if(!isalpha(s[j]) && !isdigit(s[j])) {
                --j;
                continue;
            }
            if(
                ( isalpha(s[i]) || isdigit(s[i]) ) && ( isalpha(s[j]) || isdigit(s[j]) )
            ) {
                // 注意判等于需要注意大小写
                s[i] = isupper(s[i]) ? tolower(s[i]):s[i];
                s[j] = isupper(s[j]) ? tolower(s[j]):s[j];
                if(s[i]!=s[j]) return false;
                // 判断之后要移动, 否则死循环
                ++i; --j;
            }
        }
        return true;
    }
};
```

```text
执行用时 :16 ms, 在所有 C++ 提交中击败了17.54%的用户
内存消耗 :9.4 MB, 在所有 C++ 提交中击败了44.01%的用户
```

去除不必要的判断, 效率高多了:

```cpp
class Solution {
public:
    bool isPalindrome(string s) {
        int n = s.length();
        int i=0, j=n-1;
        while(i<j) {
            //i向右找到下一个字母或数字--在不越界的情况下, 如果既不是字母也不是数字, 就忽略
            if(!isalpha(s[i]) && !isdigit(s[i])) {
                ++i;
                continue;
            }
            //j向左找到下一个字母或数字
            if(!isalpha(s[j]) && !isdigit(s[j])) {
                --j;
                continue;
            }
            // 把是否是字母的判断去掉, 因为上面已经用continue截获了不参与判断的情况/中间状态
            // 把大小写判断也去掉, 简化为tolower
            if(tolower(s[i]) != tolower(s[j])) {
                return false;
            }else {
                ++i; --j;
            }
        }
        return true;
    }
};
```

```text
执行用时：8 ms        83%
内存消耗：9.5 MB        32%
```

while-while和while-if其实效率差不多;

## 144. 二叉树的前序遍历 10min+20min

### 递归解法

教科书解法:

```cpp
class Solution {
public:
    void preorder(TreeNode* root, vector<int> &v) {
        if(root) {    /// 要点1: 判空
            v.push_back(root->val);
            preorder(root->left, v);
            preorder(root->right, v);
        }
    }
    vector<int> preorderTraversal(TreeNode* root) {
        vector<int> v;
        preorder(root, v);
        return v;
    }
};
```

### 迭代解法

使用一个栈来模拟前序;

```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
private
    stack<TreeNode*> s;    /// 备注: 这个写函数前面还是后面等效; 且注意到这里存储的是指针;
public:
    vector<int> preorderTraversal(TreeNode* root) {
        vector<int> v;
        if(root){    /// 要点2: 判空
            s.push(root);
            while(!s.empty()) {
                TreeNode* t = s.top();
                s.pop();
                v.push_back(t->val);
                if(t->right) s.push(t->right);    /// 要点1: 先压右孩子, 后压左孩子;
                if(t->left) s.push(t->left);            
            }
        }

        return v;
    }
};
```

另一种迭代解法

```cpp
class Solution {
public:
    vector<int> preorderTraversal(TreeNode* root) {
        vector<int> res;
        if(root==nullptr) return res;
        stack<TreeNode*> s;

        TreeNode* t = root;
        s.push(t);
        while(!s.empty()) {
            while(t = s.top()) {    // 访问到t时, 将其左子树入栈, t迭代为栈顶; 由此向左子树迭代, 直到刚刚压入左子树是空的   
                //t = s.back();
                res.push_back(t->val);
                s.push(t->left);
            }
            s.pop();    // 把这个空的左子树弹出, 准备让t回溯
            if(!s.empty()) {
                t = s.top();
                s.pop();
                s.push(t->right);
            }
        }
        return res;
    }
};
```

```text
执行用时 :0 ms, 在所有 C++ 提交中击败了100.00%的用户
内存消耗 :10.9 MB, 在所有 C++ 提交中击败了8.67%的用户
```

## 145. 二叉树的后序遍历

递归解法, 在144基础上改动;

## 155. 最小栈 30min

### 维护一个最小值

```cpp
class MinStack {
public:
    /** initialize your data structure here. */
    MinStack() {
        p = -1;  /// 栈空时栈顶为-1
        minIdx=0;
    }

    void push(int x) {
        data[++p]=x;
        /// 在push和pop为Min分担工作, 仅当pop最小值时需要遍历
        if(x<data[minIdx]) minIdx=p;   /// 当push的新元素比当前min小, 更新
    }

    void pop() {
        if(p==minIdx) {
            int newMinIdx=0;
            for(int i=0; i<p; ++i)
                if(data[i]<data[newMinIdx]) newMinIdx=i;
            minIdx = newMinIdx;
        }
        --p;
    }

    int top() {
        return data[p];
    }

    int getMin() {
        return (p>=0)? data[minIdx]:-1;
    }
private:
    int p;
    int minIdx; // 维护一个最小值下标
    int data[10000];    // data[1000]卡在了一个7500个push的测试用例上
};

/**
 * Your MinStack object will be instantiated and called as such:
 * MinStack* obj = new MinStack();
 * obj->push(x);
 * obj->pop();
 * int param_3 = obj->top();
 * int param_4 = obj->getMin();
 */
```

### 两个栈

用一个最小值栈存储原栈每个状态下的最小值;

## 206. 反转链表

### 迭代

只是折腾指针也可以做;

反转链表可以理解为"以头插的方式建立新的链表", 虽然使用的空间与原来相同;

可以以遍历链表的代码为框架

```cpp
while(head) {
    ...
    head = head->next;
}
```

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* reverseList(ListNode* head) {        
        ListNode* p = nullptr;
        while(head!=nullptr) {
            ListNode* nxt=head->next;   /// 当前head所指的元素next域要改变, 所以备份当前head的next域
            head->next = p;
            p = head;
            head = nxt;
        }
        return p;
    }
};
```

```text
执行用时：8 ms        78%
内存消耗：9.3 MB        9%
```

### 递归

* 可以以单个参数的形式调用自身实现
* 递归思想: 逆置以第i个节点为头的链表结果r\(i\)就等于把第i个节点h\(i\)接在它后面的部分反转的结果r\(i+1\)的末尾;
* 递归出口: 如果r\(i\)只涉及一个节点或空节点那么原样返回这个头节点

错了:

```cpp
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        if(head==nullptr || head->next==nullptr)
            return head;
        ListNode* p = reverseList(head->next);
        ListNode* q = p;
        while(q->next) q = q->next;
        q->next = head;
        return p;
    }
};
```

```cpp
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        if(head==nullptr || head->next==nullptr)
            return head;
        ListNode* p = reverseList(head->next);
        // 注意这时的head的next还连着逆置后新头节点p的尾部
        head->next->next = head;    // 这是完成head尾插
        head->next = nullptr;
        return p;
    }
};
```

```text
执行用时 :4 ms, 在所有 C++ 提交中击败了97.40%的用户
内存消耗 :10.5 MB, 在所有 C++ 提交中击败了5.00%的用户
```

## 225. 使用队列实现栈 30min

### 用一个队列

我的思路:

```cpp
class MyStack {
public:
    queue<int> q;
    /** Initialize your data structure here. */
    MyStack() {

    }

    /** Push element x onto stack. */
    void push(int x) {
        q.push(x);
    }

    /** Removes the element on top of the stack and returns that element. */
    int pop() {
        /// 要弹出栈顶元素, 即是要弹出队尾元素
        int n=q.size();
        for(int i = 1; i<n; ++i) { /// n个元素, 进行n-1次移动
            int tmp = q.front(); /// 拿到队头元素
            q.pop();
            push(tmp); /// 重新放到队尾
        }
        int res = q.front();
        q.pop();
        return res;
    }

    /** Get the top element. */
    int top() {
        /// 查看栈顶元素就是队尾元素
        return q.back();
    }

    /** Returns whether the stack is empty. */
    bool empty() {
        return q.empty();
    }
};
```

性能堪忧

```text
执行用时 :8 ms, 在所有 C++ 提交中击败了6.66%的用户
内存消耗 :9.3 MB, 在所有 C++ 提交中击败了5.09%的用户
```

### 用两个队列

...

也可使用两个队列;

## 283. 移动0 20min+

```cpp
class Solution {
public:
    void moveZeroes(vector<int>& nums) {
        int len = nums.size();
        if(len==0 || len==1) return;
        for(int i = 0; i< len; ++i) {
            if(nums[i]==0) {
                nums.erase(nums.begin() + i);
                nums.push_back(0);
                --i;    // 由于nums.erase了, 第i个元素已经更新, 应该重新判断;
                --len;
            }
        }
    }
};
```

由inplace, 也可提示使用双指针;

一个错解

```cpp
class Solution {
public:
    void moveZeroes(vector<int>& nums) {
        int len = nums.size();
        if(len==0 || len==1) return;
        int i = 0, j = 1;
        while(j<len) {
            while(nums[j]==0 && j<len) ++j;
            std::swap(nums[i], nums[j]);
            i = j;
            ++j;
        }
    }
};
```

输出了: \[1,3,0,12,0\]

改成如下, 仍然有错

```cpp
class Solution {
public:
    void moveZeroes(vector<int>& nums) {
        int len = nums.size();
        if(len==0 || len==1) return;
        int i = 0, j = 1;
        while(j<len && i<j) {
            // i 需要找到最靠左的0
            while(nums[i]!=0 && i<len) ++i;
            j=i+1;
            while(nums[j]==0 && j<len) ++j;
            std::swap(nums[i], nums[j]);
            ++i; 
            ++j;
        }
    }
};
```

输入\[0,0\]时错误; r9和r11处应该先判断下标是否越界;

```cpp
class Solution {
public:
    void moveZeroes(vector<int>& nums) {
        int len = nums.size();
        if(len==0 || len==1) return;
        int i = 0, j = 1;
        while(j<len && i<j) {
            // 1.移动i: i需要找到最靠左的0
            while(i<len && nums[i]!=0) ++i;
            // 2.移动j: j需要移动到下一个非零
            j=i+1;// 这里也可以使用j=max(i,j);确保j在右边
            while(j<len && nums[j]==0) ++j;
            // 3.交换
            if(i<len && j<len) {
                int tmp = nums[j];
                nums[j] = nums[i];
                nums[i] = tmp;
            }
            // 4.由于i和j位置的元素交换了, i处现在是一个非零数, j处是一个0, 所以最后的移动不必要
            //++i; 
            //++j;
        }
    }
};
```

* 所有使用到下标的地方都要判断是否越界

## 322. 零钱兑换

写给连枚举算法都不会写的同学们

### 枚举&回溯

力扣官方题解方法一的解法, 即使是一个超时解法我都不知道该咋写出来;

就这个解法一:

```cpp
class Solution {
    int coinChange(int idxCoin, vector<int>& coins, int amount) {
        if (amount == 0) return 0;
        if (idxCoin < coins.size() && amount > 0) {
            int maxVal = amount / coins[idxCoin];
            int minCost = INT_MAX;
            for (int x = 0; x <= maxVal; x++) {
                if (amount >= x * coins[idxCoin]) {
                    int res = coinChange(idxCoin + 1, coins, amount - x * coins[idxCoin]);
                    if (res != -1) minCost = min(minCost, res + x);
                }
            }
            return minCost == INT_MAX ? -1: minCost;
        }
        return -1;
    }
public:
    int coinChange(vector<int>& coins, int amount) {
        return coinChange(0, coins, amount);
    }
};
```

对于满足$\sum\limits_{i=0}^{n-1} {x\_i \* Coin\_i} = S$的所有情况找到$\min\_x\(\sum\limits_{i=0}^{n-1} {x\_i}\)$

其中每个$x\_i$都可取$\[0, \frac{S}{Coin\_i}\]$中的整数; 要遍历就要取遍这个范围;

分析上面这个问题:

求最小值: 要有一层循环

* 路径/已经确定的元素: 用一个map存硬币面值和每个硬币有多少个
* 选择列表/包含所有可选元素: 就是coins
* 结束条件/递归退出条件: 当前"路径"中总金额等于amount就成功得到一个解, 超过amount就以失败结果返回;
* 选择策略: 可以重复; 但是这道题不是"选或不选", 而是决定数量;
* 得到解: 最后要得到最少的硬币个数, 

```cpp
class Solution {
public:
    void backtrace(vector<int>& coins, int amount, map<int, int>& m, )
    int coinChange(vector<int>& coins, int amount) {
        int min = -1;
        int n = coins.size();
        if(n==0) return min;
        map<int, int> m;

        for(int i=0;i<n;++i) {
            int money = 0;
            for(auto ite = m.begin(); ite!=m.end(); ++ite) {
                money += ite->first * ite->second;
            }
            if(money)
        }
    }
};
```

## 429. N叉树的层序遍历

* 注意判空

```cpp
class Solution {
public:
    vector<vector<int>> levelOrder(Node* root) {
        vector<vector<int>> res;
        if(root==nullptr) return res;
        queue<Node*> q;
        q.push(root);
        while(!q.empty()) {
            vector<int> v;
            int size = q.size();
            for(int i=0;i<size;++i) {
                Node* t = q.front();
                q.pop();
                v.push_back(t->val);
                vector<Node*> &ch = t->children;
                for(auto ite=ch.begin(); ite!=ch.end(); ++ite) {
                    q.push(*ite);
                }
            }
            res.push_back(v);
        }
        return res;
    }
};
```

```text
执行用时 :80 ms, 在所有 C++ 提交中击败了62.56%的用户
内存消耗 :57.4 MB, 在所有 C++ 提交中击败了21.74%的用户
```

## 589. N叉树的前序遍历

### 递归解法

```cpp
class Solution {
public:
    void helper(vector<int>& res, Node* t) {
        if(t==nullptr) return;
        res.push_back(t->val);
        vector<Node*>& ch = t->children;
        for(auto ite = ch.begin(); ite!=ch.end(); ++ite) {
            helper(res, *ite);
        }
    }
    vector<int> preorder(Node* root) {
        vector<int> res;
        helper(res, root);
        return res;
    }
};
```

```text
执行用时 :52 ms, 在所有 C++ 提交中击败了99.65%的用户
内存消耗 :57.2 MB, 在所有 C++ 提交中击败了18.11%的用户
```

### 迭代解法-栈模拟

```cpp
class Solution {
public:
    vector<int> preorder(Node* root) {
        vector<int> res;
        if(root==nullptr) return res;
        vector<Node*> s;
        s.push_back(root);
        while(!s.empty()) {
            Node* t = s.back();
            s.pop_back();
            res.push_back(t->val);
            int n = t->children.size();
            for(int i=n-1;i>=0;--i) {
                s.push_back(t->children[i]);
            }
        }
        return res;
    }
};
```

```text
执行用时 :64 ms, 在所有 C++ 提交中击败了94.52%的用户
内存消耗 :56.8 MB, 在所有 C++ 提交中击败了22.05%的用户
```

## 637. 二叉树的层平均值

* 注意溢出问题

```cpp
class Solution {
public:
    vector<double> averageOfLevels(TreeNode* root) {
        vector<double> res;
        if(root==nullptr) return res;
        queue<TreeNode*> q;
        q.push(root);
        while(!q.empty()) {
            double sum = 0; // 使用int溢出
            int size = q.size();
            for(int i=0; i<size; ++i) {
                TreeNode* t = q.front();
                q.pop();
                sum+=t->val;
                if(t->left) q.push(t->left);
                if(t->right) q.push(t->right);
            }
            res.push_back((double) sum/size);
        }
        return res;
    }
};
```

```text
执行用时 :16 ms, 在所有 C++ 提交中击败了93.78%的用户
内存消耗 :25.3 MB, 在所有 C++ 提交中击败了5.16%的用户
```

## 946. 验证栈序列 60min

使用一个栈和一个队列可以验证;

何时结束算法: 主要看栈是否空, 还有没有元素能压栈, 队列是否为空

* 何时返回true
* 何时返回false

分情况讨论:

* 栈空
  * 有元素压栈--压栈
  * 无元素压栈
    * 队列也空--true
    * 队列非空--false
* 栈非空
  * 栈顶与队头元素相同
    * 弹栈, 出队
  * 不同
    * 有元素压栈--压栈
    * 无元素压栈--false

主要花时间的地方

* 理清情况
* 栈空和栈非空时判断"是否还有元素用来压栈"的条件不同\*;

```cpp
class Solution {
public:
    bool validateStackSequences(vector<int>& pushed, vector<int>& popped) {
        /// 将pushed视为栈(end为栈顶), 将结果popped视为队列(begin为队头,end为队尾)

        int size=pushed.size();
        if(size==0 || popped.size()==0) return true;
        int top=0;/// 起初有一个元素在栈中
        int head=0;
        while(true) {
            if(top==-1) {   /// 如果栈空
                int ssize=pushed.size();
                if(ssize!=0) /// 还有元素压栈
                    ++top;
                else {
                    if(head==size)
                        return true;
                    else return false;
                }
            }else { /// 栈非空
                if(pushed[top]==popped[head]) { /// 如果相同, 那么弹栈且出队
                    vector<int>::iterator ite= pushed.begin();
                    pushed.erase(ite+top);    /// 这里应该直接用erase(pushed.begin()+top);
                    --top;
                    ++head;/// 队列实际上不需要删除元素
                }else {
                    if(top<pushed.size()-1)
                        ++top;/// 入栈
                    else return false;
                }
            }
        }
    }
};
```

## 994. 腐烂的橘子

只是遍历没有用, 无法有效应对有新鲜橘子永远不腐烂的情况;

```cpp
class Solution {
public:
    int orangesRotting(vector<vector<int>>& grid) {
        int size = grid.size();
        for(int t=0; ;++t) {
            int fresh = 0;
            for(int i=0; i<size; ++i) {
                for(int j=0; j<size; ++j) {
                    switch(grid[i][j]) {
                        case 0: continue;
                        case 1: {
                            ++fresh;
                            break;
                        }
                        case 2: {
                            if(i-1>0 && (grid[i-1][j]==1)) {    // 上
                                grid[i-1][j] = 2;
                                --fresh;
                            }
                            if(i+1<size && (grid[i+1][j]==1)) {    // 下
                                grid[i+1][j] = 2;
                                --fresh;
                            }
                            if(j-1>0 && (grid[i][j-1]==1)) {    // 左
                                grid[i][j-1] = 2;
                                --fresh;
                            }
                            if(j+1>0 && (grid[i][j+1]==1)) {    // 右
                                grid[i][j+1] = 2;
                                --fresh;
                            }
                        }
                    }
                }
            }
            if(fresh<=0) return t;
        }
    }
};
```

### BFS

自己试着写一下BFS;

```cpp
class Solution {
public:
    int orangesRotting(vector<vector<int>>& grid) {
        int size = grid.size();

        queue<pair<int, int>> q;
        int loop = 0;
        int fresh = 0;
        for(int i=0; i<size; ++i) {
            for(int j=0; j<size; ++j) {
                switch(grid[i][j]) {
                    case 1: {
                        ++fresh;
                        continue;
                    }
                    case 2: {
                        q.push(pair<int,int>(i, j));
                        continue;
                    }
                }
            }
        }
        while(fresh>0 && !q.empty()) {
            for(int k=0; k<q.size(); ++k) {
                pair<int, int> p = q.front();
                q.pop();
                int i = p.first;
                int j = p.second;
                if(i-1>0 && (grid[i-1][j]==1)) {    // 上
                    grid[i-1][j] = 2;
                    --fresh;
                    q.push(pair<int,int>(i-1, j));
                }
                if(i+1<size && (grid[i+1][j]==1)) {    // 下
                    grid[i+1][j] = 2;
                    --fresh;
                    q.push(pair<int,int>(i+1, j));
                }
                if(j-1>0 && (grid[i][j-1]==1)) {    // 左
                    grid[i][j-1] = 2;
                    --fresh;
                    q.push(pair<int,int>(i, j-1));
                }
                if(j+1<size && (grid[i][j+1]==1)) {    // 右
                    grid[i][j+1] = 2;
                    --fresh;
                    q.push(pair<int,int>(i, j+1));
                }
            }
            ++loop;
        }
        return (fresh>0)? -1 : loop;
    }
};
```

注意:

* 注意上下左右的下标越界判断, 特别注意要写在前面;
* 分钟数/轮次: 一开始存在的所有腐烂橘子都在队列里, 此时经过0分钟;
* while的终止条件不只是queue排空; 当fresh橘子数为0则停止;

错了: 输入\[\[0,1\]\]; \[\[1,2\]\]都是错解;

```cpp
class Solution {
public:
    int orangesRotting(vector<vector<int>>& grid) {
        int size = grid.size();

        queue<pair<int, int>> q;
        int loop = 0;
        int fresh = 0;
        for(int i=0; i<size; ++i) {
            for(int j=0; j<grid[i].size(); ++j) {
                switch(grid[i][j]) {
                    case 1: {
                        ++fresh;
                        continue;
                    }
                    case 2: {
                        q.push(pair<int,int>(i, j));
                        continue;
                    }
                }
            }
        }
        while(fresh>0 && !q.empty()) {
            for(int k=0; k<q.size(); ++k) {
                pair<int, int> p = q.front();
                q.pop();
                int i = p.first;
                int j = p.second;
                if(i-1>=0 && (grid[i-1][j]==1)) {    // 上 ##注意这里有等于
                    grid[i-1][j] = 2;
                    --fresh;
                    q.push(pair<int,int>(i-1, j));
                }
                if(i+1<size && (grid[i+1][j]==1)) {    // 下
                    grid[i+1][j] = 2;
                    --fresh;
                    q.push(pair<int,int>(i+1, j));
                }
                if(j-1>=0 && (grid[i][j-1]==1)) {    // 左 ##注意这里有等于
                    grid[i][j-1] = 2;
                    --fresh;
                    q.push(pair<int,int>(i, j-1));
                }
                if(j+1<grid[i].size() && (grid[i][j+1]==1)) {    // 右
                    grid[i][j+1] = 2;
                    --fresh;
                    q.push(pair<int,int>(i, j+1));
                }
            }
            ++loop;
        }
        return (fresh>0)? -1 : loop;
    }
};
```

```cpp
class Solution {
public:
    int orangesRotting(vector<vector<int>>& grid) {
        int size = grid.size();

        queue<pair<int, int>> q;
        int loop = 0;
        int fresh = 0;
        for(int i=0; i<size; ++i) {
            for(int j=0; j<grid[i].size(); ++j) {
                switch(grid[i][j]) {
                    case 1: {
                        ++fresh;
                        continue;
                    }
                    case 2: {
                        q.push(pair<int,int>(i, j));
                        continue;
                    }
                }
            }
        }
        while(fresh>0 && !q.empty()) {
            int level = q.size();   /// ##注意这里循环次数一定要提前取, 否则每次pop之后当前轮次的元素个数会变; 这里不是为了提高效率
            for(int k=0; k<level; ++k) {
                pair<int, int> p = q.front();
                q.pop();
                int i = p.first;
                int j = p.second;
                if(i-1>=0 && (grid[i-1][j]==1)) {    // 上
                    grid[i-1][j] = 2;
                    --fresh;
                    q.push(pair<int,int>(i-1, j));
                }
                if(i+1<size && (grid[i+1][j]==1)) {    // 下
                    grid[i+1][j] = 2;
                    --fresh;
                    q.push(pair<int,int>(i+1, j));
                }
                if(j-1>=0 && (grid[i][j-1]==1)) {    // 左
                    grid[i][j-1] = 2;
                    --fresh;
                    q.push(pair<int,int>(i, j-1));
                }
                if(j+1<grid[i].size() && (grid[i][j+1]==1)) {    // 右
                    grid[i][j+1] = 2;
                    --fresh;
                    q.push(pair<int,int>(i, j+1));
                }
            }
            ++loop;
        }
        return (fresh>0)? -1 : loop;
    }
};
```

```text
执行用时 :12 ms, 在所有 C++ 提交中击败了36.42%的用户
内存消耗 :15.3 MB, 在所有 C++ 提交中击败了5.07%的用户
```

## 1103. 分糖果 II 5min

### 朴素方法

```cpp
class Solution {
public:
    vector<int> distributeCandies(int candies, int num_people) {
        vector<int> res(num_people, 0);
        int c = 1;    // #没有必要用, 因为c=i+1
        int i = 0;
        while(true) {
            if(c<=candies) {
                res[i%num_people]+=c;
                candies-=c;
            }else {
                res[i%num_people]+=candies;
                break;
            }
            ++c;
            ++i;
        }
        return res;
    }
};
```

```text
执行用时 :4 ms, 在所有 C++ 提交中击败了72.19%的用户
内存消耗 :8.8 MB, 在所有 C++ 提交中击败了5.30%的用户
```

简化一下代码:

```cpp
class Solution {
public:
    vector<int> distributeCandies(int candies, int num_people) {
        vector<int> res(num_people, 0);
        int i = 0;
        while(candies>0) {
            res[i%num_people]+=min(candies, i+1);
            candies-=i+1;
            ++i;
        }
        return res;
    }
};
```

性能倒是没变;

