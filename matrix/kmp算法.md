# 困难字符串匹配
[题目链接](https://matrix.sysu.edu.cn/course/private/2460282/assignment/3074125)

如果没有matrix账号不一定能打开上面这个链接

## 题目描述
在目标串s中，查找模式串t的出现的次数以及位置。数据保证字符串只包含小写字母。

提示：若使用时间复杂度为O(∣t∣⋅∣s∣)的朴素匹配算法可能超时；使用KMP算法可以在O(∣t∣+∣s∣)的时间内解决此问题。

## 解题思路
在标准的 KMP 实现中，通常有两种 Next 数组定义：

整体右移版（**首元素为 -1**）：`next[j]` 表示 `t[0...j-1]` 的最长相等前后缀长度。

标准版（**首元素为 0**）：`next[j]` 表示 `t[0...j]` 的最长相等前后缀长度。

为了代码的易读性，我们选择标准版 Next 数组的定义

详细过程见下方的代码注释

## 代码

```c++
// 获取 Next 数组 (标准版: next[i] 表示子串 t[0...i] 的最长相等前后缀长度)
vector<int> getNext(const string& t) {
    int lt = t.size();
    vector<int> next(lt, 0);
    int j = 0; // j 代表前缀末尾位置，也代表前缀长度
    for (int i = 1; i < lt; i++) { // i 代表后缀末尾位置
        while (j > 0 && t[i] != t[j]) {
            j = next[j - 1]; // 回退
        }
        if (t[i] == t[j]) {
            j++;
        }
        next[i] = j;
    }
    return next;
}

void kmp(const string& s, const string& t) {
    vector<int> next = getNext(t);
    vector<int> ans;
    int ls = s.size(), lt = t.size();
    int j = 0; // j 表示当前匹配到的模式串位置

    for (int i = 0; i < ls; i++) {
        // 当字符不匹配时，根据 next 数组回退 j
        while (j > 0 && s[i] != t[j]) {
            j = next[j - 1];
        }
        // 如果字符匹配，j 向后移动
        if (s[i] == t[j]) {
            j++;
        }
        // 如果 j 达到了模式串长度，说明完全匹配
        if (j == lt) {
            ans.push_back(i - lt + 1); // 记录匹配起始下标
            j = next[j - 1]; // 关键：匹配成功后，j 回退到 next[j-1] 继续寻找下一个匹配
        }
    }

    cout << ans.size() << '\n';
    for (int i = 0; i < ans.size(); i++) {
        cout << ans[i] << ' ';
    }
    cout << endl;
}

int main() {
    string s, t;
    if (cin >> t >> s) {
        kmp(s, t);
    }
    return 0;
}
```