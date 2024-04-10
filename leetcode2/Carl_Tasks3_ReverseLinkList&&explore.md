# LeetCode 每日任务 —— Day3 —— 翻转链表相关问题

## Leet206 —— 翻转链表 —— simple —— 2024.4.6

### 给你单链表的头节点 `head` ，请你反转链表，并返回反转后的链表。

### 考察点：双指针，递归，栈模拟

### 思路一：

使用双指针，得先构造一个`prev`节点，然后再构造一个`curr`节点，先设置`prev`节点为`nullptr`，然后使用`tmp`节点记录`curr`的下一个结点，然后更改`curr`节点指向`prev`，再移动`curr`和`prev`，直到滑动到尾部；

```cpp
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        ListNode* temp; // 保存cur的下一个节点
        ListNode* cur = head;
        ListNode* pre = NULL;
        while(cur) {
            temp = cur->next;  // 保存一下 cur的下一个节点，因为接下来要改变cur->next
            cur->next = pre; // 翻转操作
            // 更新pre 和 cur指针
            pre = cur;
            cur = temp;
        }
        return pre;
    }
};
```

### 思路二：

使用递归的思路，感觉用labuladong的示意图一看便明了：

![img](https://labuladong.online/algo/images/%E5%8F%8D%E8%BD%AC%E9%93%BE%E8%A1%A8/2.jpg)

这个 `reverse(head.next)` 执行完成后，整个链表就成了这样：

![img](https://labuladong.online/algo/images/%E5%8F%8D%E8%BD%AC%E9%93%BE%E8%A1%A8/3.jpg)

所以递归到最深处时，应该是这样：

![Untitled](C:\Users\WonJjay\Desktop\Untitled.png)

所以递归的终止条件应该是：

```cpp
if (head == nullptr || head->next == nullptr) {
	return head;
}
```

仔细思考我们的递归过程，我们是要深度优先的，递归到最深处然后再做操作的，所以我们的一系列处理应该是在递归函数的下面写，故下面可以写成：

```cpp
ListNode* reverse(ListNode* head) {
        
        // base case
        if (head == nullptr || head->next == nullptr) {
            return head;
        }

        // execute

        ListNode* Last = reverse(head->next);

```

以我现在倒数第二个状态为例，`head`为`4`节点，`head->next`为`5`节点，`last`为`{6, 5, null}`，故我们需要如下操作：
        

```cpp
ListNode* reverse(ListNode* head) {
    // base case
    if (head == nullptr || head->next == nullptr) {
        return head; // head == nullptr是为了预防原始的linklist为空链表
    }

    // execute

    ListNode* Last = reverse(head->next);
    
    // operation in current node
    head->next->next = head;
    head->next = nullptr;
    return last;
}
```

同样如果我们想在`execute`之前进行操作（这时候其实类似于迭代了），那么我们需要构造一个含有`curr`和`prev`这两个形参的`reverse`函数，然后将双指针的方法放在这个部分即可；

### 思路三：构建stack

现将LinkList存储进一个stack中，由于stack是先进后出，所以第一个出栈的就是我们的LinkList的尾元素，然后这样其实挺方便反转的；

```cpp
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        stack<ListNode*> s;
        ListNode* cur = head;
        while (cur != nullptr) {
            s.push(cur);
            cur = cur->next;
        }
        
        ListNode* newHead = nullptr;
        ListNode* prev = nullptr;
        while (!s.empty()) {
            ListNode* tmp = s.top();
            s.pop();
            
            if (newHead == nullptr) {
                // 这里算是init过程，后续newHead不再更新，作为最终返回的头指针
                newHead = tmp;
                prev = tmp;
            } else {
                prev->next = tmp;
                prev = tmp;
            }
        }
        
        if (prev != nullptr) {
            prev->next = nullptr;
        }
        
        return newHead;
    }
};
```

# 扩展 —— 翻转链表的前N个元素

![img](https://labuladong.online/algo/images/%E5%8F%8D%E8%BD%AC%E9%93%BE%E8%A1%A8/6.jpg)

这时，我们递归的初始条件无法用指针的性质来进行判定了，我们得构建一个额外的函数`reverseN(head， n)`，用`n`的大小来进行初始条件的判定，我们姑且认为初始条件为`n==1`；

有了初始条件后，我们返回的自然是`head`，但是我们是不是得加一个不变的值。思考翻转全部链表，我们每次更改指向为`nullptr`，但这时候我们需要指向的是第四个节点，因此我们需要设置一个全局变量（或者是成员）来记录这第四个节点，即可以设置其为`successor`，使`successor`一直指向`head->next`;

```cpp
LinkList* successor;
ListNode* reverseN(LinkList* head, int n) {
    if (n == 1) {
        successor = head->next;
        return head;
    }
}
```

![18c3dab2445dad90819a23409b4c975](C:\Users\WonJjay\Documents\WeChat Files\wxid_nfkbu7kwarbj12\FileStorage\Temp\18c3dab2445dad90819a23409b4c975.jpg)

同样我们可以判定我们的`operation`应该是基于深度优先，即放在`execute`之后进行；

```cpp
LinkList* successor;
ListNode* reverseN(LinkList* head, int n) {
    // base case
    if (n == 1) {
        successor = head->next;
        return head;
    }

	// execute
    ListNode* Last = reverse(head->next);
    
```

同样以上图最后一个状态为例，`head`为`1`节点，`head->next`为`2`节点，`last`为`{3,2,4, 5, ... }`, `successor`为`4`节点；我们需要让`head->next`指向`head`，而`head->next`指向`successor`，然后`return`我们的`last`即可；

```cpp
LinkList* successor;
ListNode* reverseN(LinkList* head, int n) {
    // base case
    if (n == 1) {
        successor = head->next;
        return head;
    }

	// execute
    ListNode* Last = reverse(head->next);
    head->next->next = head;
    head->next = successor;
    return last;
}
```

# LeetCode92 —— 翻转链表II —— Medium 2024.4.10

## 给你单链表的头指针 `head` 和两个整数 `left` 和 `right` ，其中 `left <= right` 。请你反转从位置 `left` 到位置 `right` 的链表节点，返回 **反转后的链表** 。

![img](https://assets.leetcode.com/uploads/2021/02/19/rev2ex2.jpg)

```cpp
输入：head = [1,2,3,4,5], left = 2, right = 4
输出：[1,4,3,2,5]
```

### 思考点：递归

这道题我们可以先设置两个节点`prev`和`head`，依次滑动`prev`和`head`直到`head`指向第二个节点，然后调用`reverseN(head, right-left+1)`, 将这一部分节点翻转，然后将`prev->next`指向`last`即可；

```cpp
class Solution {
public:
    ListNode* successor;
public:
    ListNode* reverseBetween(ListNode* head, int left, int right) {
        if (left == right) {
            return head;
        }
        if (left == 1) {
            ListNode* last = reverseN(head, right-left+1);
            return last;
        }
        ListNode* prev = nullptr;
        ListNode* curr = head;
        int count = left;
        while (count != 1) {
            prev = curr;
            curr = curr->next;
            count--;
        }
        ListNode* last = reverseN(curr, right-left+1);
        prev->next = last;
        return head;



    }

    ListNode* reverseN(ListNode* head, int n) {
        // base case
        if (n == 1) {
            successor = head->next;
            return head;
        }
        // execute
        ListNode* Last = reverseN(head->next, n-1);
        // operation in this node;
        head->next->next = head;
        head->next = successor;
        return Last;
    }
};
```

