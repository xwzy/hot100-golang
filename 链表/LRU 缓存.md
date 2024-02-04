https://leetcode.cn/problems/lru-cache/description/?envType=study-plan-v2&envId=top-100-liked

请你设计并实现一个满足 LRU (最近最少使用) 缓存 约束的数据结构。
实现 LRUCache 类：
LRUCache(int capacity) 以 正整数 作为容量 capacity 初始化 LRU 缓存
int get(int key) 如果关键字 key 存在于缓存中，则返回关键字的值，否则返回 -1 。
void put(int key, int value) 如果关键字 key 已经存在，则变更其数据值 value ；如果不存在，则向缓存中插入该组 key-value 。如果插入操作导致关键字数量超过 capacity ，则应该 逐出 最久未使用的关键字。
函数 get 和 put 必须以 O(1) 的平均时间复杂度运行。

---

要实现满足 LRU（最近最少使用）缓存约束的数据结构，并保证`get`和`put`操作的平均时间复杂度为 O(1)，我们可以使用哈希表结合双向链表来实现。哈希表提供了快速的查找能力，而双向链表则能够以 O(1)的时间复杂度添加和删除节点，这对于维护访问顺序非常有用。

以下是使用 Go 语言实现 LRU 缓存的代码：

```go
package main

import "fmt"

type LRUCache struct {
    capacity int
    cache    map[int]*Node
    head, tail *Node
}

type Node struct {
    key, value int
    prev, next *Node
}

func Constructor(capacity int) LRUCache {
    head := &Node{}
    tail := &Node{}
    head.next = tail
    tail.prev = head
    return LRUCache{
        capacity: capacity,
        cache:    make(map[int]*Node),
        head:     head,
        tail:     tail,
    }
}

func (this *LRUCache) Get(key int) int {
    if node, exists := this.cache[key]; exists {
        this.moveToHead(node)
        return node.value
    }
    return -1
}

func (this *LRUCache) Put(key int, value int) {
    if node, exists := this.cache[key]; exists {
        node.value = value
        this.moveToHead(node)
    } else {
        newNode := &Node{
            key:   key,
            value: value,
        }
        this.cache[key] = newNode
        this.addToHead(newNode)
        if len(this.cache) > this.capacity {
            removed := this.removeTail()
            delete(this.cache, removed.key)
        }
    }
}

func (this *LRUCache) addToHead(node *Node) {
    node.prev = this.head
    node.next = this.head.next
    this.head.next.prev = node
    this.head.next = node
}

func (this *LRUCache) removeNode(node *Node) {
    prev := node.prev
    next := node.next
    prev.next = next
    next.prev = prev
}

func (this *LRUCache) moveToHead(node *Node) {
    this.removeNode(node)
    this.addToHead(node)
}

func (this *LRUCache) removeTail() *Node {
    node := this.tail.prev
    this.removeNode(node)
    return node
}
```

这段代码首先定义了一个`LRUCache`结构体，其中包含了容量限制`capacity`、一个哈希表`cache`用于存储键和对应节点的映射，以及一个双向链表通过`head`和`tail`哨兵节点来维护所有节点的访问顺序。

-   `Constructor`函数初始化一个新的 LRU 缓存实例。
-   `Get`函数通过键来获取一个值。如果找到了，就把对应的节点移动到双向链表的头部，表示这个节点最近被访问过。
-   `Put`函数插入或更新键值对。如果键已存在，更新其值并将节点移至头部。如果键不存在，创建新节点，添加至头部，如果超出容量，则删除尾部节点（最久未使用的节点）。
-   `addToHead`和`removeNode`辅助函数分别用于在双向链表的头部添加节点和从链表中删除节点。
-   `moveToHead`函数将节点移动到头部，表示这个节点最近被访问。
-   `removeTail`函数删除尾部节点，也就是最久未被访问的节点，并返回被删除的节点，以便从哈希表中删除对应的键值对。

这种实现确保了`get`和`put`操作都能在 O(1)时间复杂度内完成。
