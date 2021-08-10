**本次题目:[leetcode-28:实现strStr()](https://leetcode-cn.com/problems/implement-strstr/submissions/ )**

首先记住KMP算法的框架：

第一步是求得next数组。

**初学者在这里会迷惑，求next数组是有什么用？求解next数组的方式是什么？**

你只需要记住：next数组是方便字符串进行匹配的一个工具即可！

```java
int k = 0;
for (int i = 1; i < n; ++i) {
    while(k>0&&s.charAt(i)!=s.charAt(k))
        k = next[k-1];
    if(s.charAt(i)==s.charAt(k))
        next[i] = ++k;
}
```

在动态规划中，可能只使用一维数组或者二维数组便将信息进行加入，但是在KMP算法中，使用的是一个延后指针K和一个记录相同前后缀的数组next，通过二者之间的结合，可以在O(N)的时间中对所需要匹配的字符串进行信息的加入。

在循环中，如果出现之后的字符串不配的情况，则需要将延后指针k进行回溯（即将前缀进行减小），减小的幅度通过前缀串在next数组中保存的数字进行判断，（即k = next[k-1]） 如果减小后匹配成功，则将对应的k加一放入结果中。

<img src="KMP字符串算法.assets/image-20210810162051068.png" alt="image-20210810162051068" style="zoom: 67%;" />

<img src="KMP字符串算法.assets/image-20210810162112883.png" alt="image-20210810162112883" style="zoom:67%;" />

然后对所要求解的文本进行遍历，k代表已经匹配的字符个数，如果匹配的个数等于needle的长度，则代表匹配成功。

如果字符匹配不相同，则将k带入到转移方程中，k = next[k-1]，继续进行匹配，如果最终都没有找到则返回-1即可。

```java
class Solution {
    public int strStr(String haystack, String needle) {
        int N = haystack.length();
        int M = needle.length();
        if(M==0) return 0;
        int next[] = new int[M];
        int k = 0;
        for(int i = 1;i < M;i++){
            while(k>0&&needle.charAt(i)!=needle.charAt(k))
                k=next[k-1];
            if(needle.charAt(i)==needle.charAt(k))
                k++;
            next[i] = k;
        }
        k = 0;
        for(int i = 0;i < N;i++){
            while(k>0&&haystack.charAt(i)!=needle.charAt(k))
                k = next[k-1];
            if(needle.charAt(k) == haystack.charAt(i))
                k++;
            if(k == M) return i-M+1;
        }
        return -1 ;
    }
}
```

