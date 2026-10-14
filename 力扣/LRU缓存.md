# LUR缓存
[题目链接](https://leetcode.cn/problems/lru-cache/description/?envType=study-plan-v2&envId=top-100-liked)

## 题目描述
请你设计并实现一个满足  LRU (最近最少使用) 缓存 约束的数据结构。
实现 LRUCache 类：

LRUCache(int capacity) 以 正整数 作为容量 capacity 初始化 LRU 缓存

int get(int key) 如果关键字 key 存在于缓存中，则返回关键字的值，否则返回 -1 。

void put(int key, int value) 如果关键字 key 已经存在，则变更其数据值 value ；如果不存在，则向缓存中插入该组 key-value 。如果插入操作导致关键字数量超过 capacity ，则应该 逐出 最久未使用的关键字。

函数 get 和 put 必须以 O(1) 的平均时间复杂度运行。

## 解题思路
看到O(1)的时间复杂度，就立即想到两种可能：1、`vector`; 2、`unordered_map`

但是，如果要**维护一个记录使用时间的变量`time`**，似乎光靠这两个数据结构都不太方便：

`vector`如果想用`pair`来扩展，就必须至少使用两层嵌套才能同时维护`key`,`value`,`time`这三个数据，更和何况如果想要查询`time`，时间复杂度绝对超过O(1)

`key`,`value`其实也是一层暗示，只能使用`unordered_map`了，究竟怎样维护`time`呢？

至此，我们才真正触及到问题的核心：通过**双向链表**来模拟`time`，越靠近`head`说明越新，越靠近`tail`说明越旧。如果需要更新数据，就把对应的指针重新移动到`head`

为什么是**双向链表**？因为哈希表中只存储了一个指针，我们要想移动和删除链表就必须获得它的前驱和后继

## 代码

```c++
struct DLlinkedNode{
    int key, value;
    DLlinkedNode* prev;
    DLlinkedNode* next;
    DLlinkedNode():key(0), value(0), prev(nullptr), next(nullptr){}
    DLlinkedNode(int _key,int _value):key(_key),value(_value),prev(nullptr),next(nullptr){}
};

class LRUCache {
private:
    unordered_map<int ,DLlinkedNode*> cache;
    DLlinkedNode* head;
    DLlinkedNode* tail;
    int size;
    int capacity;
public:
    LRUCache(int _capacity):capacity(_capacity),size(0) {
        head = new DLlinkedNode();
        tail = new DLlinkedNode();
        head->next = tail;
        tail->prev = head;
    }
    
    int get(int key) {
        if(!cache.count(key)){
            return -1;
        }
        DLlinkedNode* node = cache[key];
        moveTohead(node);
        return node->value;
    }
    
    void put(int key, int value) {
        if(!cache.count(key)){
            DLlinkedNode* node = new DLlinkedNode(key, value);
            cache[key] = node;
            addTohead(node);
            ++size;
            if(size>capacity){
                DLlinkedNode* del = Tailnode();
                cache.erase(del->key);
                delete del;
                --size;
            }
        }
        else{
            DLlinkedNode* node = cache[key];
            node->value = value;
            moveTohead(node);
        }
    }

    void addTohead(DLlinkedNode* node){
        node->prev = head;
        node->next = head->next;
        head->next->prev = node;
        head->next = node;
    }

    void removenode(DLlinkedNode* node){
        node->prev->next = node->next;
        node->next->prev = node->prev;
    }

    void moveTohead(DLlinkedNode* node){
        removenode(node);
        addTohead(node);
    }

    DLlinkedNode* Tailnode(){
        DLlinkedNode* node = tail->prev;
        removenode(node);
        return node;
    }
};
```