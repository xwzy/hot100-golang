https://leetcode.cn/problems/linked-list-cycle-ii/description/?envType=study-plan-v2&envId=top-100-liked

给定一个链表的头节点 head ，返回链表开始入环的第一个节点。 如果链表无环，则返回 null。

如果链表中有某个节点，可以通过连续跟踪 next 指针再次到达，则链表中存在环。 为了表示给定链表中的环，评测系统内部使用整数 pos 来表示链表尾连接到链表中的位置（索引从 0 开始）。如果 pos 是 -1，则在该链表中没有环。注意：pos 不作为参数进行传递，仅仅是为了标识链表的实际情况。

不允许修改 链表。

---

要解决这个问题，我们可以使用 Floyd 的龟兔赛跑算法，也就是快慢指针的方法。这个方法的基本思想是使用两个速度不同的指针遍历链表，一个移动速度是另一个的两倍。如果链表中存在环，那么这两个指针最终会在环内相遇。一旦快慢指针相遇，我们就可以通过一系列步骤找到环的入口。

以下是算法的步骤：

1. 创建两个指针，`slow` 和 `fast`，初始时都指向链表的头节点。
2. 移动这两个指针，其中 `slow` 每次移动一步，`fast` 每次移动两步。
3. 如果链表没有环，`fast` 会先到达链尾（`fast` 或 `fast.Next` 为 `nil`），此时返回 `nil`。
4. 如果链表有环，`slow` 和 `fast` 会在某个点内相遇。当它们相遇时，将 `fast`（或 `slow`）重新指向链表头节点，然后 `slow` 和 `fast` 都以相同的速度（每次一步）移动，当它们再次相遇时，相遇点即为环的入口。

下面是使用 Go 语言实现的代码：

```go
package main

import "fmt"

// ListNode 是链表节点的定义。
type ListNode struct {
    Val  int
    Next *ListNode
}

// detectCycle 返回链表开始入环的第一个节点。如果链表无环，则返回 nil。
func detectCycle(head *ListNode) *ListNode {
    if head == nil || head.Next == nil {
        return nil
    }

    slow, fast := head, head
    for fast != nil && fast.Next != nil {
        slow = slow.Next
        fast = fast.Next.Next
        if slow == fast {
            fast = head
            for slow != fast {
                slow = slow.Next
                fast = fast.Next
            }
            return slow
        }
    }
    return nil
}

// Helper function to create a cycle in the list for demonstration purposes.
func createCycleList() *ListNode {
    head := &ListNode{Val: 3}
    head.Next = &ListNode{Val: 2}
    head.Next.Next = &ListNode{Val: 0}
    head.Next.Next.Next = &ListNode{Val: -4, Next: head.Next} // Creating a cycle here
    return head
}

func main() {
    list := createCycleList()
    cycleNode := detectCycle(list)
    if cycleNode != nil {
        fmt.Println("Cycle detected at node with value:", cycleNode.Val)
    } else {
        fmt.Println("No cycle detected")
    }
}
```

这个解决方案首先使用快慢指针确定链表是否有环。如果有环，通过将一个指针重置到链表头，然后两个指针以相同速度移动，可以找到环的入口节点。这个算法的时间复杂度是 O(n)，空间复杂度是 O(1)，因为它只使用了几个指针而没有使用额外的空间来存储数据。
