# leetcode 刷题之路（链表）

## 数组篇

### leetcode707 设计链表

```
思路：需要注意，初始化时，head指针被置为nullptr，链表长度为0。需要考虑各种边界条件
```

```c++
class MyLinkedList {
    struct ListNode {
        int val;
        ListNode* next;
        ListNode(int x):val(x), next(nullptr){}
    };
    int m_size;
    ListNode* m_head;
public:
    /** Initialize your data structure here. */
    MyLinkedList() {
        m_head = nullptr;
        m_size = 0;
    }

    /** Get the value of the index-th node in the linked list. If the index is invalid, return -1. */
    int get(int index) {
        if (index < 0 || index > m_size || m_size == 0) {
            return -1;
        }
        ListNode* tmp = m_head;
        for (int i = 0; i < index; i++) {
            tmp = tmp->next;
        }
        if(tmp) {
            return tmp->val;
        } else {
            return -1;
        }

    }

    /** Add a node of value val before the first element of the linked list. After the insertion, the new node will be the first node of the linked list. */
    void addAtHead(int val) {
        ListNode* newHead = new ListNode(val);
        newHead->next = m_head;
        m_head = newHead;
        m_size++;
    }

    /** Append a node of value val to the last element of the linked list. */
    void addAtTail(int val) {
        if (m_size == 0) {
            addAtHead(val);
            return;
        }
        ListNode* tmp = m_head;
        while (tmp->next) {
            tmp = tmp->next;
        }
        if (tmp) {
            tmp->next = new ListNode(val);
            m_size++;
        }
    }

    /** Add a node of value val before the index-th node in the linked list. If index equals to the length of linked list, the node will be appended to the end of linked list. If index is greater than the length, the node will not be inserted. */
    void addAtIndex(int index, int val) {
        if (index > m_size) {
            return;
        } else if (index == m_size) {
            addAtTail(val);
            return;
        } else if (index <= 0) {
            addAtHead(val);
            return;
        } else {
            ListNode* tmp = m_head;
            ListNode* newNode = new ListNode(val);
            for (int i = 0; i < index - 1; i++) { // find the element which is ahead of the insert position
                tmp = tmp->next;
            }
            newNode->next = tmp->next;
            tmp->next = newNode;
            m_size++;
        }
    }

    /** Delete the index-th node in the linked list, if the index is valid. */
    void deleteAtIndex(int index) {
        if (index < 0 || index > m_size || m_size == 0) {
            return;
        }
        ListNode* tmp = m_head;
        if (index == 0) {
            m_head = m_head->next;
            m_size--;
            delete tmp;
            return;
        }
        for (int i = 0; i < index - 1; i++) { // find the element which is ahead of the delete position
            tmp = tmp->next;
        }
        if (!tmp) {
            return;
        }
        if (tmp->next) {
            ListNode* del = tmp->next;
            tmp->next = del->next;
            delete del;
            m_size--;
        }
    }
};
```

### leetcode141 环形链表

```
思路：快慢指针法，注意：
1.fast和slow的初始化都为head
2.跳出while循环的条件是判读fast和fast->next是否为空，不能判断slow，因为slow慢，判断无意义
3.需考虑head为空的特殊情况
```

```c++
bool hasCycle(ListNode *head) {
        if (!head) {
            return false;
        }
        ListNode* fast = head;
        ListNode* slow = head;
        while (fast && fast->next) {
            slow = slow->next;
            fast = fast->next->next;
            if (slow == fast) {
                return true;
            }
        }
        return false;
    }
```

### leetcode142 环形链表2

```
思路：假设非环形长度为a，环形长度为b，fast一次走2步，slow一次走1步，第一次相遇时，fast指针走了a+x，slow走了a+y，且有(a+x)=2(a+y)，且x-y=nb,即两者相遇，则fast比slow多走了n圈，故有两者相遇时，fast走了2nb，slow走了nb。需要求a的长度，则此时，slow再走a步即可，如何让slow再走a步就停下？可以发现，入环节点的位置，等于a+nb，则若第一次相遇后，从head开始有一个跟slow相同速度的节点走，则走a步之后，slow刚好也走了a+nb步，则两者在入环点相遇，相遇位置即为所要求的输出
```

```c++
bool hasCycle(ListNode *head) {
        if (!head) {
            return false;
        }
        ListNode* fast = head;
        ListNode* slow = head;
        while (fast && fast->next) {
            slow = slow->next;
            fast = fast->next->next;
            if (slow == fast) {
                return true;
            }
        }
        return false;
    }

    ListNode *detectCycle(ListNode *head) {
        if (!hasCycle(head)) {
            return nullptr;
        }
        ListNode* ans;
        ListNode* fast = head;
        ListNode* slow = head;
        while (fast && fast->next) {
            slow = slow->next;
            fast = fast->next->next;
            if (slow == fast) {
                break;
            }
        }
        fast = head;
        while (fast != slow) {
            fast = fast->next;
            slow = slow->next;
        }
        return fast;
    }
```

### leetcode160 相交链表

```
思路：构建哈希表，将A的所有点存入，然后遍历B，在其中找相同的指针，如果能找到则返回找到的指针，否则返回nullptr
```

```c++
#include<unordered_set>
ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        if(!headA || !headB) {
            return nullptr;
        }
        ListNode* iterA = headA;
        ListNode* iterB = headB;
        unordered_set<ListNode*> set;
        while(iterA) {
            set.insert(iterA);
            iterA = iterA->next;
        }
        while(iterB) {
            if(set.find(iterB) != set.end()) {
                return iterB;
            }
            iterB = iterB->next;
        }
        return nullptr;
    }
```

