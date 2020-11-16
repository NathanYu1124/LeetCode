# 链表

```c++
// 链表的劣势: 1.无法高效获取长度O(n) 2.无法根据偏移快速访问元素 
// 链表的要点: 1.双指针(快慢指针/多指针)思想是解决链表问题的最重要思想, 2.注意不能破坏链表结构
// 链表的要点: 3.链表结点的删除注意加哨兵结点处理头结点被删除

// 链表节点
struct ListNode {
		int val;
  	ListNode *next;
  	ListNode(int x) : val(x), next(NULL) {}
};
```

### Easy

#### 1.链表中倒数第k个节点

```c++
// 题目：输入一个链表head，输出该链表中倒数第k个节点的值。例如: 1->3->4->8->7->5, 倒数第二个节点的值为7

边界条件: head为NULL, k <= 0 或者 k > head.len, 统一处理返回NULL
核心思想: 快慢指针(双指针), 快指针先走k步, 然后两个指针同步走, 当快指针走到头时, 慢指针指向倒数第k个节点
时空复杂度:

class Solution {
public:
    ListNode* getKthFromEnd(ListNode *head, int k) {
        if(head == NULL || k <= 0)
            return NULL;    
        ListNode *fast = head, *slow = head;
        while(k > 0) {
          	// k大于链表长度
            if(fast == NULL)
                return NULL;
            fast = fast->next;
            k--;
        }

        while(fast != NULL) {
            fast = fast->next;
            slow = slow->next;
        }
        return slow;
    }
};
```

#### 2.合并两个有序链表

```c++
// 题目: 将两个升序链表合并成一个新的升序链表并返回,新链表由之前的两个链表的节点组成。

边界条件: 至少一个链表为空
核心思想: 归并排序-尾插法, 找出L1和L2的较小头节点并插入新链表,对应链表节点后移一位直至L1和L2都为空
时空复杂度:
  
class Solution {
public:
  ListNode* mergeTwoLists(ListNode *L1, ListNode *L2) 
  {
    ListNode *resHead = new ListNode(-1);
    ListNode *prev = resHead;
    while(L1 != NULL && L2 != NULL) {
      if(L1->val < L2->val) {
        prev->next = L1;
        L1 = L1->next;
      } else {
        prev->next = L2;
        L2 = L2->next;
      }
      prev = prev->next;
    }
    
    prev->next = (L1 == NULL) ? L2 : L1;
   
    return resHead->next;
  }
};
```

#### 3.反转链表

```c++
// 题目: 反转一个单链表, 不借助辅助空间。

边界条件: 链表为空
核心思想: 头插法建立新链表
时空复杂度:

class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        ListNode *resHead = NULL;
        ListNode *curr = head;
        while(curr != NULL) {
            ListNode *tmp = curr->next;
            curr->next = resHead;
            resHead = curr;
            curr = tmp;
        }

        return resHead;
    }
};

// 从链表的第k个位置开始反转链表
```

#### 4.删除排序链表中的重复元素

```c++
// 题目: 给定一个排序链表, 删除所有的重复元素, 使得每个元素只出现一次

边界条件: 链表为空
核心思想: 元素有序的情况下依次比较相邻的两个元素是否相同
时空复杂度:
  
class Solution {
public:
    ListNode* deleteDuplicates(ListNode* head) {
        ListNode *curr = head;
        while(curr != NULL && curr->next != NULL) {
            if(curr->val == curr->next->val) {
              	// 有重复结点时,删除重复的结点后还需依次和后续结点比较
                curr->next = curr->next->next;
            } else {
              	// 无重复结点时, 遍历下一个结点
                curr = curr->next;
            }
        }
        return head;
    }
};
```

#### 5.环形链表

```c++
// 题目: 给定一个链表, 判断链表中是否有环

边界条件: 链表为空或只有一个结点
核心思想: 如果有环则快慢指针必在环内相遇
时空复杂度:

// 快慢指针: 快指针走两步,慢指针走一步, 如果有环则两指针必定能在环中相遇(用数学归纳法证明)
class Solution {
public:
    bool hasCycle(ListNode *head) {
        if(head == NULL || head->next == NULL)
            return false;

        ListNode *fast = head->next, *slow = head;
        while(fast != slow) {
            if(fast == NULL || fast->next == NULL)
                return false;
            fast = fast->next->next;
            slow = slow->next;
        }

        return true;
    }
};
```

#### 6.回文链表 - TODO

```c++
// 题目: 请判断一个链表是否为回文链表。空间复杂度为O(1)

边界条件: 链表为空或链表只有一个结点
核心思想: 快慢指针翻转后半部分链表后再判断
时空复杂度:
  
class Solution {
public:
    bool isPalindrome(ListNode* head) {
        if(head == NULL || head->next == NULL)
            return true;
        ListNode *endOfLHalf = getEndOfLeftHalf(head);
        ListNode *firstOfRHalf = reverseList(endOfLHalf->next);
      	// 拆分成两个链表便于比较
        endOfLHalf->next = NULL;

        ListNode *p1 = head, *p2 = firstOfRHalf;
        while(p1 != NULL && p2 != NULL) {
            if(p1->val != p2->val)
                return false;
            p1 = p1->next;
            p2 = p2->next;
        }

      	// 拼接成原链表
        endOfLHalf->next = reverseList(firstOfRHalf);
        return true;
    }

  	// 反转链表
    ListNode* reverseList(ListNode *head) {
        ListNode *resHead = NULL;
        ListNode *curr = head;
        while(curr != NULL) {
            ListNode *tmp = curr->next;
            curr->next = resHead;
            resHead = curr;
            curr = tmp;
        }
        return resHead;
    }

  	// 找到前半部分的尾结点(奇数个结点的链表的中间结点是前半部分)
    ListNode* getEndOfLeftHalf(ListNode *head) {
        ListNode *fast = head, *slow = head;
        while(fast->next != NULL && fast->next->next != NULL) {
            fast = fast->next->next;
            slow = slow->next;
        }

        return slow;
    }
};
```

#### 7.相交链表

```c++
// 题目: 找到两个相交单链表(共用相交点及以后的结点)的相交起始结点。

边界条件: 有链表为空,或两个链表不相交
核心思想: 双指针巧妙解法
时空复杂度:

class Solution {
public:
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
      	if(headA == NULL || headB == NULL)
          	return NULL;
        ListNode *pa = headA, *pb = headB;
        while(pa != pb) {
           pa = (pa != NULL) ? pa->next : headB;
           pb = (pb != NULL) ? pb->next : headA;
        }

        return pa;
    }
};


```

#### 8.链表的中间结点

```c++
// 题目: 给定一个带有头结点的非空单链表,返回链表的中间结点,若有两个中间结点则返回第二个。

核心思想: 快慢指针
时空复杂度:

class Solution {
public:
    ListNode* middleNode(ListNode* head) {
        ListNode *fast = head, *slow = head;
        while(fast != NULL && fast->next != NULL) {
            fast = fast->next->next;
            slow = slow->next;
        }

        return slow;
    }
};
```

#### 9.删除链表元素

```c++
// 题目: 删除链表中等于给定值val的所有节点

边界条件: 删除链表头结点
核心思想: 添加哨兵结点
时空复杂度:
  
class Solution {
public:
    ListNode* removeElements(ListNode* head, int val) {
      	// 添加哨兵结点
        ListNode *resHead = new ListNode(-1);
        resHead->next = head;
      	
        ListNode *pre = resHead, *curr = head;
        while(curr != NULL) {
            if(curr->val == val) {
                pre->next = curr->next;
                curr = curr->next;
            } else {
                pre = curr;
                curr = curr->next;
            }
        }

        return resHead->next;
    }
};
```

#### 10.从尾到头打印链表

```c++
// 题目: 输入一个链表的头结点, 从尾到头反过来返回每个结点的值(用数组返回)。

边界条件:
核心思想: 反转链表法/辅助栈法
时空复杂度:

class Solution {
public:
    vector<int> reversePrint(ListNode* head) {
        ListNode *backList = reverseList(head);
        ListNode *curr = backList;
        vector<int> ans;
        while(curr != NULL) {
            ans.push_back(curr->val);
            curr = curr->next;
        }

        head = reverseList(backList);
        return ans;
    }
		
  	// 反转链表
    ListNode* reverseList(ListNode *head) {
        ListNode *resHead = NULL;
        ListNode *curr = head;
        while(curr != NULL) {
            ListNode *tmp = curr->next;
            curr->next = resHead;
            resHead = curr;
            curr = tmp;
        }
        return resHead;
    }
};
```

### Medium

#### 1.删除链表的倒数第N个结点

```c++
// 题目: 给定一个链表,删除链表的倒数第n个节点,并且返回链表的头结点。

边界条件: 链表为空,n<=0或n>=链表长度,
核心思想: 添加哨兵结点, 利用快慢指针找倒数第n+1个结点并修改其next指针即可
时空复杂度:

class Solution {
public:
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        if(head == NULL || n <= 0)
            return head;
        
      	// 添加哨兵结点, 方便删除头结点的操作
        ListNode *resHead = new ListNode(-1);
        resHead->next = head;
      
      	// 快慢指针找倒数第n+1个结点(前驱)
        ListNode *fast = resHead, *slow = resHead;
        while(n >= 0) {
            if(fast == NULL)
                return NULL;
            fast = fast->next;
            n--;
        }

        while(fast != NULL) {
            fast = fast->next;
            slow = slow->next;
        }

        slow->next = slow->next->next;
        return resHead->next;
    }
};
```

#### 2.环形链表的入口

```c++
// 题目: 给定一个链表,返回链表开始入环的第一个结点。如果链表无环,则返回null。

边界条件: 链表为空,或链表无环
核心思想: 快慢指针,快慢指针在环中相遇时,快指针指向head且每次走一步,快慢指针再次相遇的地方即为环入口
时空复杂度:

class Solution {
public:
    ListNode *detectCycle(ListNode *head) {
        
        ListNode *fast = head, *slow = head;
        while(true) {
            if(fast == NULL || fast->next == NULL)
                return NULL;
            slow = slow->next;
            fast = fast->next->next;
            if(fast == slow)
                break;
        }

        fast = head;
        while(fast != slow) {
            fast = fast->next;
            slow = slow->next;
        }

        return fast;
    }
};
```

#### 3.反转链表

```c++
// 题目: 反转从位置m到n的链表, 并且只使用一趟扫描即完成反转。

边界条件: 1<=m<=n<=链表长度
核心思想: 链表的指针操作, 画出链表的指针指向
时空复杂度:

class Solution {
public:
    ListNode* reverseBetween(ListNode* head, int m, int n) {
        if(head == NULL || m < 1 || n < m)
            return NULL;

        // 找第m个结点及其前驱
        ListNode *prev = NULL, *curr = head;
        while(m > 1) {
            prev = curr;
            curr = curr->next;
            m--;
            n--;
        }
        // 翻转m到n的链表
        ListNode *bfHead = prev, *tail = curr;
        while(n > 0) {
            ListNode *tmp = curr->next;
            curr->next = prev;
            prev = curr;
            curr = tmp;
            n--;
        }
        // 从头结点开始翻转
        if(bfHead != NULL) {
            bfHead->next = prev;
        } else {
            head = prev;
        }
        tail->next = curr;
        return head;
    }
};
```

#### 4.两数相加 - 小端

```c++
// 题目: 两个非空的链表表示两个非负的整数,其位数按照逆序的方式存储,并且每个结点只存储一位数字,将两个数相加
// 并返回一个新的链表表示它们的和。

边界条件: 两链表不等长
核心思想: 
时空复杂度:

class Solution {
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        ListNode *resHead = new ListNode(0);
        ListNode *p = l1, *q = l2, *curr = resHead;
        int carry = 0;
        while(p != NULL || q != NULL) {
            int x = (p != NULL) ? p->val : 0;
            int y = (q != NULL) ? q->val : 0;
            int sum = x + y + carry;
            curr->next = new ListNode(sum % 10);
            curr = curr->next;
            carry = sum / 10;
            if(p != NULL)
                p = p->next;
            if(q != NULL)
                q = q->next;
        }
        if(carry > 0)
            curr->next = new ListNode(carry);
        
        return resHead->next;
    }
};
```

#### 5.删除排序链表中的重复元素

```c++
// 题目: 给定一个排序链表, 删除所有含有重复数字的结点,只保留原始链表中没有重复出现的数字。

边界条件: 链表为空,链表长度为1,需要删除头结点
核心思想: 双指针
时空复杂度:

class Solution {
public:
    ListNode* deleteDuplicates(ListNode* head) {
        if(head == NULL || head->next == NULL)
            return head;
				
        ListNode *dummyNode = new ListNode(-1);
        dummyNode->next = head;
        ListNode *prev = dummyNode;
        ListNode *curr = head;

        while(curr != NULL && curr->next != NULL) {
            if(prev->next->val != curr->next->val) {
                prev = prev->next;
                curr = curr->next;
            } else {
              	// 一直向后直到找到下一个值不同的结点
                while(curr != NULL && curr->next != NULL 
                      && prev->next->val == curr->next->val) {
                    curr = curr->next;
                }

                prev->next = curr->next;
                curr = curr->next;
            }
        }
        return dummyNode->next;
    }
};
```

#### 6.两两交换链表中的节点

```c++
// 题目: 给定一个链表,两两交换其中相邻的节点,并返回交换后的链表,必须要进行实际的节点交换。

边界条件:
核心思想: 双指针/递归法
时空复杂度: 

class Solution {
public:
    ListNode* swapPairs(ListNode* head) {
        if(head == NULL || head->next == NULL) 
             return head;
        ListNode *dummyNode = new ListNode(-1);
        dummyNode->next = head;
        ListNode *curr = dummyNode;
        while(curr->next != NULL && curr->next->next != NULL) {
            ListNode *node1 = curr->next;
            ListNode *node2 = curr->next->next;

            curr->next = node2;
            node1->next = node2->next;
            node2->next = node1;
            curr = node1;
        }
        
        return dummyNode->next;
    }
};
```

#### 7.旋转链表

```c++
// 题目: 给定一个链表,将链表每个结点向右移动k个位置,其中k是非负数
// 例如: 输入 1->2->3->4->5->NULL, k = 2, 输出  4->5->1->2->3->NULL

边界条件: 链表为空,或链表只有一个结点,或k<0,或k>链表长度
核心思想:	链表成环,从head处找第(len - k % len -1)个结点为新的尾结点
时空复杂度:

class Solution {
public:
    ListNode* rotateRight(ListNode* head, int k) {
        if(head == NULL || k < 0)
            return NULL;
        if(head->next == NULL)
            return head;

        // 找到原链表的尾结点及其长度
        int len = 1;
        ListNode *old_tail = head;
        while(old_tail->next != NULL) {
            old_tail = old_tail->next;
            len++;
        }
        // 拼接成环
        old_tail->next = head;

        // 找到新的头尾结点
        ListNode *new_tail = head;
        for(int i = 1; i <= (len - k % len - 1); i++) {
            new_tail = new_tail->next;
        }
        ListNode *new_head = new_tail->next;
        new_tail->next = NULL;

        return new_head;
    }
};
```

#### 8.两数相加 - 大端

```c++
// 题目: 两个非空的链表表示两个非负的整数,数字最高位在链表开始位置,并且每个结点只存储一位数字,将两个数相加
// 并返回一个新的链表表示它们的和。(进阶: 不翻转链表如何处理)


边界条件:
核心思想:	1.翻转链表后再相加/2.双栈法
时空复杂度:

class Solution {
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
      	// 先翻转链表为小端存储
        ListNode *backL1 = reverseList(l1);
        ListNode *backL2 = reverseList(l2);
	
      	// 小端存储的链表相加
        int carry = 0;
        ListNode *resHead = new ListNode(-1);
        ListNode *curr = resHead;
        while(backL1 != NULL || backL2 != NULL) {
            int v1 = (backL1 != NULL) ? backL1->val : 0;
            int v2 = (backL2 != NULL) ? backL2->val : 0;
            int sum = v1 + v2 + carry;
            carry = sum / 10;
            curr->next = new ListNode(sum % 10);
            curr = curr->next;
            if(backL1 != NULL)
                backL1 = backL1->next;
            if(backL2 != NULL)
                backL2 = backL2->next;
        }
				// 额外的进位
        if(carry > 0)
            curr->next = new ListNode(carry);

      	// 重新将链表翻转过来
        l1 = reverseList(backL1);
        l2 = reverseList(backL2);
        resHead->next = reverseList(resHead->next);
        return resHead->next;
    }

		// 翻转链表
    ListNode *reverseList(ListNode *head) {
        ListNode *resHead = NULL;
        ListNode *curr = head;
        while(curr != NULL) {
            ListNode *tmp = curr->next;
            curr->next = resHead;
            resHead = curr;
            curr = tmp;
        }

        return resHead;
    }
};
```

#### 9.分隔链表

```c++
// 题目: 给定一个链表和一个特定值x,对链表进行分隔,使得所有小于x的节点都在大于或等于x的节点之前, 此外还
// 需保留两侧中每个结点的初始相对位置。

边界条件:
核心思想:	双指针尾插法构建两个链表,最后合并链表
时空复杂度:

class Solution {
public:
    ListNode* partition(ListNode* head, int x) {
      	// 使用哨兵结点构建新链表
        ListNode *before_head = new ListNode(-1);
        ListNode *after_head = new ListNode(-1);
        ListNode *before = before_head;
        ListNode *after = after_head;

        while(head != NULL) {
            if(head->val < x) {
                before->next = head;
                before = before->next;
            } else {
                after->next = head;
                after = after->next;
            }
            head = head->next;
        }

        // 链表尾部置为NULL,避免生成环形链表
        // 原链表倒数第二个结点给after,最后一个结点给before,若不将链表尾部置为NULL,则会生成环形链表
        after->next = NULL;

        before->next = after_head->next;
        return before_head->next;
    }
};
```

#### 10.K分链表

```c++
// 题目: 给定一个链表,将其分隔成k个连续的部分,每部分的长度应尽可能的相等,任意两部分的长度差距不能
// 超过1,有些部分可能为null。将这k个部分按原链表上的顺序输出,前面部分的长度大于或等于后面的长度。

边界条件:
核心思想:
时空复杂度:

class Solution {
public:
    vector<ListNode*> splitListToParts(ListNode* root, int k) {
      	// 先计算链表长度
        ListNode *curr = root;
        int len = 0;
        while(curr != NULL) {
            curr = curr->next;
            len++;
        }

        vector<ListNode*> ans;
        curr = root;
        // k部分,每部分width个结点, 前rem个部分+1个结点
        int width = len / k, rem = len % k;
        for(int i = 0; i < k; i++) {
            ListNode *head = curr;
            int w = (i < rem) ? (width + 1) : width;
          	// 从当前部分的头结点向后找w-1个结点
            for(int j = 0; j < w - 1; j++) {
                if(curr != NULL)
                    curr = curr->next;
            }
          	// 将当前部分的第w个结点进行断链
            if(curr != NULL) {
                ListNode *prev = curr;
                curr = curr->next;
                prev->next = NULL;
            }

            ans.push_back(head);
        }
        return ans;
    }
};
```

#### 11.复杂链表的复制

```c++
/*
	 题目: 在一个复杂链表中,每个结点除了有一个next指针指向下一个结点,还有一个random指针指向链表中的
	 任意结点或者null, 请写一个函数实现对复杂链表的复制(深拷贝)。
*/

边界条件:
核心思想:	1.哈希表映射 2.新建链表结点在原链表结点之后
时空复杂度: 1.时间O(n), 空间O(n)  2.时间O(n), 空间O(1)
  
/* 
class Node {
  public:
  		int val;
  		Node *next;
  		Node *random;
  		Node(int _val) {
        val = _val;
        next = NULL;
        random = NULL;
      }
};
*/

// 1.哈希表法
class Solution {
public:
    Node* copyRandomList(Node* head) {
        
        // 1.哈希表, 用于存储原链表的结点指针与新建链表对应结点的指针的映射关系
        map<Node*, Node*> hMap;
        Node *resHead = new Node(-1);
        Node *curr = head, *resCurr = resHead;
      	// 遍历原链表, 创建结点建立新链表, 同时存储指针的映射关系
        while(curr != NULL) {
            resCurr->next = new Node(curr->val);
            hMap[curr] = resCurr->next;
            curr = curr->next;
            resCurr = resCurr->next;
        }

      	// 再次遍历原链表, 通过哈希表找到原链表结点的random指针在新链表中的对应指针 
        curr = head, resCurr = resHead->next;
        while(curr != NULL) {
            resCurr->random = hMap[curr->random];
            curr = curr->next;
            resCurr = resCurr->next;
        }
				
      	// 返回新链表即可
        return resHead->next;
    }
};

// 2.巧妙指针法
class Solution {
public:
    Node* copyRandomList(Node* head) {
        
        Node *curr = head;
  			// 遍历原链表, 在原链表的每个结点后复制一个相同的结点
        while(curr != NULL) {
            Node *tmp = curr->next;
            curr->next = new Node(curr->val);
            curr->next->next = tmp;
            curr = tmp;
        }

        curr = head;
      	// 复制链表结点的random指针为原链表对应结点的random指针的next值
        while(curr != NULL && curr->next != NULL) {
            curr->next->random = (curr->random == NULL) ? NULL : curr->random->next;
            curr = curr->next->next;
        }
				
      	// 将新链表分成原链表与赋值链表, 奇数结点为原链表结点, 偶数结点为复制链表结点
        Node *resHead = new Node(-1);
        Node *resCurr = resHead;
        Node *prev = head;
        curr = head;
        int cnt = 1;
        while(curr != NULL) {
            if(cnt % 2 == 0) {
                Node *tmp = curr->next;
                resCurr->next = curr;
                resCurr = curr;
                curr = tmp;
                prev->next = curr;
            } else {
                prev = curr;
                curr = curr->next;
            }
            cnt++;
        }
        return resHead->next;
    }
};
```

#### 12.排序链表

```c++
// 题目: 在O(nlogn)时间复杂度和常数级空间复杂度下, 对链表进行升序排序。

边界条件:
核心思想: 自底向上的归并排序
时空复杂度:



```

### Hard

#### 1.k个一组翻转链表

```c++
// 题目:

边界条件:
核心思想:
时空复杂度:


```

























































