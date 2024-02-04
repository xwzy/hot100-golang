https://leetcode.cn/problems/merge-k-sorted-lists/description/?envType=study-plan-v2&envId=top-100-liked

给你一个链表数组，每个链表都已经按升序排列。

请你将所有链表合并到一个升序链表中，返回合并后的链表。

---

要合并多个已排序的链表为一个单一的排序链表，而不使用最小堆（通常用于优化合并多个链表的过程），我们可以采用分而治之的方法。这种方法涉及到将链表两两合并，直到仅剩下一个链表。这里是如何使用迭代法来合并两个链表，然后应用这个过程来逐步合并整个链表数组。

以下是使用 Go 语言实现的步骤：

1. **定义链表节点结构**：首先，我们需要定义链表的节点结构，如果没有提供，我们可以假设它是这样的：

```go
type ListNode struct {
    Val int
    Next *ListNode
}
```

2. **合并两个链表的函数**：然后，我们编写一个函数来合并两个已排序的链表。这个函数会迭代地比较两个链表的头节点，并将较小的节点接到结果链表上。

```go
func mergeTwoLists(l1 *ListNode, l2 *ListNode) *ListNode {
    dummy := &ListNode{}
    current := dummy

    for l1 != nil && l2 != nil {
        if l1.Val < l2.Val {
            current.Next = l1
            l1 = l1.Next
        } else {
            current.Next = l2
            l2 = l2.Next
        }
        current = current.Next
    }

    if l1 != nil {
        current.Next = l1
    } else if l2 != nil {
        current.Next = l2
    }

    return dummy.Next
}
```

3. **合并整个链表数组**：最后，我们使用这个合并两个链表的函数来逐步合并整个链表数组。我们可以通过递归地将链表数组分成两半，分别合并，然后将这两个结果再合并起来。不过，为了避免使用递归（按照题目要求），我们使用迭代的方式来合并整个链表数组。

```go
func mergeKLists(lists []*ListNode) *ListNode {
    if len(lists) == 0 {
        return nil
    }

    interval := 1
    for interval < len(lists) {
        for i := 0; i + interval < len(lists); i += interval * 2 {
            lists[i] = mergeTwoLists(lists[i], lists[i+interval])
        }
        interval *= 2
    }

    return lists[0]
}
```

这段代码首先检查链表数组是否为空，如果是，则直接返回`nil`。然后，它使用一个外层循环逐步增加合并间隔`interval`，每次将间隔翻倍。内层循环遍历链表数组，每次合并`interval`距离内的两个链表。这个过程一直持续到`interval`大于等于链表数组的长度，这时所有链表都被合并到了第一个链表中。

这种方法有效地将合并 K 个链表的问题转化为了重复合并两个链表的问题，其时间复杂度为 O(NlogK)，其中 N 是所有链表中元素的总数，K 是链表数组的长度。这是因为每次合并操作减少一半的链表，且每次合并都需要遍历所有的元素。
