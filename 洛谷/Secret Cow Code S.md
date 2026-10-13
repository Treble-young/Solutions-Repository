# Secret Cow Code S
[题目链接](https://www.luogu.com.cn/problem/P3612)

## 题目描述
奶牛们正在实验秘密代码，并设计了一种方法用于生成无限长度的字符串，作为他们代码的一部分。

给定一个字符串 s，令 F(s) 为 s 后接 s 向右“旋转”一个字符的结果（在右旋转中，s 的最后一个字符旋转并成为新的第一个字符）。给定初始字符串 s，奶牛们通过重复应用 F 来构建他们的无限长度代码字符串；因此每一步都会使当前字符串的长度翻倍。

给定初始字符串和一个索引 N，请帮助奶牛计算无限代码字符串中第 N 个位置的字符。

请注意，N 可能太大，无法放入标准的 32 位整数中，因此你可能需要使用 64 位整数类型（例如，C/C++ 中的 "long long"）。

## 解题思路
假设n = 1e18，字符串长度每次翻倍。哪怕原始s只有1个字符，翻倍到超过1e18需要约60 次循环。

但问题是：实际存储的字符串长度会变成2^60字节，约等于 1EB（百亿亿字节），你的**电脑内存瞬间如奶油般化开**，程序直接被系统杀掉。

这道题的正确解法

我们不需要构建字符串，只需要**从目标位置n倒推**，看它在前一轮的哪个位置，直到退回到最初的原始字符串长度。

算法并不难，重要的是**从逆向的角度思考**

## 代码

```c++
#include<bits/stdc++.h>
using namespace std;
string s;long long len,cur_len;
int main(){
    long long n;cin>>s>>n;
    len = s.length();cur_len = len;
    while(cur_len < n){
        cur_len*=2;
    }
    while(cur_len>len){
        long long half = cur_len/2;
        if(n>half){
            if(n==half+1){
                n=half;
            }
            else{
                n=n-half-1;
            }
        }
        cur_len=half;
    }
    cout<<s[n-1];
    return 0;
}
```