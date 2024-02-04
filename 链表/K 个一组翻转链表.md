https://leetcode.cn/problems/reverse-nodes-in-k-group/description/?envType=study-plan-v2&envId=top-100-liked

给你链表的头节点 head ，每 k 个节点一组进行翻转，请你返回修改后的链表。

k 是一个正整数，它的值小于或等于链表的长度。如果节点总数不是 k 的整数倍，那么请将最后剩余的节点保持原有顺序。

你不能只是单纯的改变节点内部的值，而是需要实际进行节点交换。

---

为了解决这个问题，我们可以使用以下步骤来编写代码：

1. **检查特殊情况**：如果链表为空或`k`为 1，直接返回头节点，因为不需要翻转。
2. **计算链表长度**：遍历整个链表以确定其长度，这将帮助我们了解需要进行多少组翻转。
3. **翻转链表的一部分**：对于链表中的每一组长度为`k`的部分，我们将进行翻转。为了翻转一组，我们可以使用三个指针：`prev`，`current`和`next`。`prev`是当前节点之前的节点，`current`是当前正在处理的节点，而`next`是下一个节点。
4. **处理剩余节点**：如果链表的长度不是`k`的倍数，链表的最后一部分将保持原样。
5. **连接所有部分**：确保翻转的每一部分都正确连接到链表中。

下面是使用 Go 语言实现的代码：

```go
package main

import "fmt"

// ListNode 是链表节点的定义
type ListNode struct {
    Val  int
    Next *ListNode
}

// reverseKGroup 对链表每k个节点进行翻转
func reverseKGroup(head *ListNode, k int) *ListNode {
    if head == nil || k == 1 {
        return head
    }

    // 创建一个哑结点作为新链表的起始点
    dummy := &ListNode{Next: head}
    // 初始化prev为哑结点
    prev := dummy

    // 计算链表长度
    length := 0
    for head != nil {
        length++
        head = head.Next
    }

    // 遍历链表并每k个节点翻转一次
    for length >= k {
        curr := prev.Next
        next := curr.Next
        for i := 1; i < k; i++ {
            curr.Next = next.Next
            next.Next = prev.Next
            prev.Next = next
            next = curr.Next
        }
        prev = curr
        length -= k
    }

    return dummy.Next
}

// printList 打印链表
func printList(l *ListNode) {
    for l != nil {
        fmt.Printf("%d -> ", l.Val)
        l = l.Next
    }
    fmt.Println("nil")
}

func main() {
    // 示例链表：1->2->3->4->5，假设k=2
    head := &ListNode{Val: 1, Next: &ListNode{Val: 2, Next: &ListNode{Val: 3, Next: &ListNode{Val: 4, Next: &ListNode{Val: 5}}}}}
    k := 2

    result := reverseKGroup(head, k)
    printList(result) // 预期输出：2->1->4->3->5
}
```

这个方法首先遍历一遍链表计算出其长度，然后按照长度判断能进行多少次完整的翻转操作。每次翻转都是通过调整节点的`next`指针来完成的，而且需要确保每次翻转后，翻转部分的最后一个节点正确地连接到下一部分的开始。这种方法的时间复杂度是 O(n)，空间复杂度是 O(1)。
