#链表总结
#### 个人心得：链表主要考察指针的操作（针对C++），算法方面并不难。
## 1.两数相加
给你两个 非空 的链表，表示两个非负的整数。它们每位数字都是按照 逆序 的方式存储的，并且每个节点只能存储一位 数字。请你将两个数相加，并以相同形式返回一个表示和的链表。
```
class Solution {
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        ListNode* dummy = new ListNode(-1);
        ListNode* cur = dummy;
        int carry = 0;
        while(l1 || l2){
            int x = l1? l1->val:0;
            int y = l2? l2->val:0;
            int s = x+y+carry;
            carry = s/10;
            cur->next = new ListNode(s%10);
            cur = cur->next;
            if(l1) l1 = l1->next;
            if(l2) l2 = l2->next;
        }
        if(carry) cur->next = new ListNode(carry);
        return dummy->next;
    }
};
```
## 2.删除链表倒数第N个节点
```
// 双指针的应用
class Solution {
public:
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        ListNode* p = new ListNode(-1);
        p->next = head;
        ListNode* fast = p;
        ListNode* slow = p;
        for (int i=0;i<=n;i++) fast = fast->next;
        while(fast){
            fast=fast->next;
            slow=slow->next;
        }
        slow->next = slow->next->next;
        return p->next;
    }
};
```
## 3.环形链表
判断单链表是否有环
```
// 双指针应用
class Solution {
public:
bool hasCycle(ListNode* head)
{
	//两个运动员位于同意起点head
	ListNode* faster{ head };  //快的运动员
	ListNode* slower{ head };  //慢的运动员

	if (head == NULL)  //输入链表为空，必然不是循环链表
		return false;

	while (faster != NULL && faster->next != NULL)
	{
		faster = faster->next->next;  //快的运动员每次跑两步
		slower = slower->next;  //慢的运动员每次跑一步
		if (faster == slower)  //他们在比赛中相遇了
			return true;  //可以断定是环形道，直道不可能相遇
	}
	return false;  //快的运动员到终点了，那就是直道，绕圈跑不会有终点
}
};
```
## 3.环形链表Ⅱ
给定一个链表，返回链表开始入环的第一个节点。 如果链表无环，则返回 null。
```
// 双指针应用
class Solution {
public:
    ListNode *detectCycle(ListNode *head) {
    ListNode *slow, *fast;
    slow = head;
    fast = head;
    while(fast)
    {
        if(!fast->next || !fast->next->next)
            return NULL;
        else
        {
            slow = slow->next;
            fast = fast->next->next;
            if(slow == fast)
                break;
        }
    }
    fast = head;
    while(fast != slow)
    {
        fast = fast->next;
        slow = slow->next;
    }
    return fast;
    }
};
```
## 4.相交链表
找到两个单链表相交的起始节点
```
// 双指针应用
class Solution {
public:
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        ListNode* p1 = headA;
        ListNode*p2 = headB;
        while(p1!=p2){
            p1=p1==NULL? headB:p1->next;
            p2=p2==NULL? headA:p2->next;
        }
        return p1;
    }
};
```
## 5.合并两个有序链表
```
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        ListNode* dummy = new ListNode(-1);
        ListNode* cur = dummy;
        while(l1 && l2){
            if(l1->val < l2->val){
                cur->next = new ListNode(l1->val);
                cur = cur->next;
                l1 = l1->next;
            }
            else{
                cur->next = new ListNode(l2->val);
                cur = cur->next;
                l2 = l2->next;
            }
        }
        if(l1) cur->next = l1;
        if(l2) cur->next = l2;
        return dummy->next;
    }
};
```
## 6.合并K个有序链表
```
//可以利用“5”，这里用堆排序解决
class Solution {
public:
    struct cmp{
       bool operator()(ListNode *a,ListNode *b){
          return a->val > b->val;
       }
    };
    ListNode* mergeKLists(vector<ListNode*>& lists) {
        priority_queue<ListNode*, vector<ListNode*>, cmp> pri_queue;
        for(auto elem :lists){
            if (elem) pri_queue.push(elem);
        }
        ListNode* p = new ListNode(-1);
        ListNode* q = p;
        while(!pri_queue.empty()){
            ListNode*top = pri_queue.top();
            pri_queue.pop();
            q->next = top;
            q = top;
            if (top->next) pri_queue.push(top->next);
        }
        return p->next;
    }
};
```
## 7.反转链表
```
//非递归
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        if(!head || !head->next) return head;
        ListNode* pre = NULL;
        ListNode* cur = head;
        ListNode* nxt;
        while(cur!=NULL){
            nxt = cur->next;
            cur->next = pre;
            pre = cur;
            cur = nxt;
        }
        return pre;
    }
};

//递归
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        if(!head) return head;
        if(!head->next) return head;
        ListNode* newhead = reverseList(head->next);
        head->next->next = head;
        head->next = NULL;
        return newhead;
    }
};
```
## 8.反转链表Ⅱ
反转从位置 m 到 n 的链表
```
class Solution {
public:
    ListNode* reverseBetween(ListNode* head, int m, int n) {
        ListNode *dummy=new ListNode(-1);
        dummy->next=head;
        ListNode* pre;
        ListNode* newhead=dummy;
        for(int i = 1;i<m;i++) newhead=newhead->next;
        pre = newhead->next;
        ListNode *p = pre;
        for(int i = m;i<n;i++){
            ListNode* q=p->next;
            p->next = q->next;
            q->next = newhead->next;
            newhead->next = q;
        }
        return dummy->next;
    }
```
## 9.K个一组反转链表
```
class Solution {
public:
    ListNode* reverse(ListNode* a, ListNode* b){
        ListNode* pre = NULL;
        ListNode* cur = a;
        ListNode* nxt;
        while(cur!=b){
            nxt = cur->next;
            cur->next = pre;
            pre = cur;
            cur = nxt;
        }
        return pre;
    }
    ListNode* reverseKGroup(ListNode* head, int k) {
        ListNode* a = head;
        ListNode* b = head;
        for(int i = 0; i < k; i++){
            if(!b) return head;
            b = b->next;
        }
        ListNode* newHead = reverse(a,b);
        a->next = reverseKGroup(b,k);
        return newHead;
    }
};

```
## 10.旋转链表
给定一个链表，旋转链表，将链表每个节点向右移动 k 个位置，其中 k 是非负数。
```
class Solution {
public:
    ListNode* rotateRight(ListNode* head, int k) {
        if(head==NULL || head->next==NULL || k==0) return head;
        ListNode* pre = NULL;
        ListNode* cur=head;
        ListNode* tail=head;
        int len = 1;
        while(tail->next!=NULL){
            len++;
            tail=tail->next;
        }
        if(k == len || k%len == 0) return head;
        int tmp = len-k%len;
        for(int i = 0; i<tmp; i++){
            pre = cur;
            cur=cur->next;
        }
        pre->next = NULL;
        ListNode* newhead = cur;
        while(cur->next) cur=cur->next;
        cur->next = head;
        return newhead;
    }
};

```
## 11.分隔链表
给你一个链表的头节点 head 和一个特定值 x ，请你对链表进行分隔，使得所有 小于 x 的节点都出现在 大于或等于 x 的节点之前。你应当 保留 两个分区中每个节点的初始相对位置。
```
class Solution {
public:
    ListNode* partition(ListNode* head, int x) {
        ListNode *h1 = new ListNode(-1); // 记录比x小的节点组成的链表
        ListNode *h2 = new ListNode(-1); // 记录比x大的节点组成的链表
        ListNode *p1 = h1, *p2 = h2;
        while(head!=NULL){
            if(head->val<x){
                p1->next = head;
                p1 = p1->next;
            }
            else{
                p2->next = head;
                p2 = p2->next;
            }
            head = head->next;
        }
        p2->next = NULL;
        p1->next = h2->next;
        return h1->next;
    }
};
```
## 12.重排链表
给定一个单链表 L：L0→L1→…→Ln-1→Ln ，
将其重新排列后变为： L0→Ln→L1→Ln-1→L2→Ln-2→…
你不能只是单纯的改变节点内部的值，而是需要实际的进行节点交换。
```
class Solution {
public:
    ListNode* findSecond(ListNode* fast, ListNode* slow){
        while(fast && fast->next && fast->next->next){
            slow = slow->next;
            fast = fast->next->next;
        }
        ListNode* newHead = slow->next;
        slow->next = NULL;
        return newHead;
    }
    ListNode* reverse(ListNode* head){
        ListNode* pre = NULL;
        ListNode* cur = head;
        while(cur){
            ListNode* nxt = cur->next;
            cur->next = pre;
            pre = cur;
            cur = nxt;
        }
        return pre;
    }
    void reorderList(ListNode* head) {
        if(!head || !head->next) return;
        ListNode *fast = head, *slow = head;
        ListNode *secondHalf = findSecond(fast, slow); // 找到链表的中间结点
        ListNode *secondHalfReversed = reverse(secondHalf); // 翻转后半部分链表
        ListNode* p1 = head;
        while(secondHalfReversed){
            ListNode* p2 = secondHalfReversed;
            ListNode* q1 = p1->next;
            ListNode* q2 = p2->next;
            p1->next = p2;
            p2->next = q1;
            secondHalfReversed = q2;
            p1 = q1;
        }
    }
};
```
## 13.删除链表中等于给定值 val 的所有节点。
```
class Solution {
public:
    ListNode* removeElements(ListNode* head, int val) {
        if(head==NULL) return head;
        ListNode*pre=head;
        ListNode*cur=head->next;
        while(cur)
        {
            if(cur->val==val)
            {
                pre->next=cur->next;
            }
            else
                pre=pre->next;
            cur=cur->next;
        }
        return head->val==val? head->next:head;
    }
};
```
## 14.删除排序链表的重复元素
```
class Solution {
public:
    ListNode* deleteDuplicates(ListNode* head) {
        ListNode * p=head;
        ListNode * q;
        while(p!=NULL)
        {
            q=p->next;
            if(q!=NULL&&p->val == q->val)
            {
                p->next=q->next;
                delete q;
                q=NULL;
            }
            else
                p=p->next;
        }
        return head;
    }
};
```
## 15.删除排序链表的重复元素Ⅱ
给定一个排序链表，删除所有含有重复数字的节点，只保留原始链表中 没有重复出现 的数字。
```
class Solution {
public:
    ListNode* deleteDuplicates(ListNode* head) {
        unordered_map <int,int>mp;
        ListNode* p = head;
        while(p!=NULL){
            mp[p->val]++;
            p = p->next;
        }
        ListNode* dummy  = new ListNode(-1);
        dummy->next = head;
        ListNode* pre = dummy , *cur = dummy->next;
        while(cur){
            if(mp[cur->val]>1){
                int num = cur->val;
                while(cur->next!=NULL && cur->next->val == num){
                    cur=cur->next;
                }
                pre->next = cur->next;
            }
            else{
                pre = pre->next;
            }
            cur = cur->next;
        }
        return dummy->next;
    }
};
```
## 16.回文链表
```
class Solution {
public:
    bool isPalindrome(ListNode* head) {
        if (!head) return true;
        vector<int> a;
        ListNode* p = head;
        while(p!=NULL){
            a.push_back(p->val);
            p=p->next;
        }
        int left=0;
        int right=a.size()-1;
        while(left<right){
            if(a[left]!=a[right]) return false;
            else{
                left++;
                right--;
            }
        }
        return true;
    }
};

//此题的另外一种解法：用快慢指针
```
## 17.排序链表
```
class Solution {
public:
    ListNode* cut(ListNode* head,int n)
    {
        ListNode* p=head;
        while(--n && p)
            p=p->next;

        if(!p)
            return NULL;

        ListNode* suc=p->next;
        p->next = NULL;
        return suc;
    }

    ListNode* merge(ListNode* l1,ListNode* l2)
    {
        ListNode* dummy=new ListNode(0);
        ListNode* p=dummy;
        while(l1 && l2)
        {
            if(l1->val < l2->val)
            {
                p->next=l1;
                l1=l1->next;
                p=p->next;
            }
            else
            {
                p->next=l2;
                l2=l2->next;
                p=p->next;
            }
        }
        p->next=l1 ? l1 : l2;
        return dummy->next;
    }

    ListNode* sortList(ListNode* head) {
        ListNode dummyHead(0);
        dummyHead.next = head;
        auto p = head;
        int length = 0;
        while (p) {
            ++length;
            p = p->next;
        }
        for (int size = 1; size < length; size <<= 1) {
            auto cur = dummyHead.next;
            auto tail = &dummyHead;
            while (cur) {
                auto left = cur;
                auto right = cut(left, size); // left->@->@ right->@->@->@...
                cur = cut(right, size); // left->@->@ right->@->@  cur->@->...
                tail->next = merge(left, right);
                while (tail->next) {
                    tail = tail->next;
                }
            }
        }
        return dummyHead.next;
    }
};
```
## 18.奇偶链表
给定一个单链表，把所有的奇数节点和偶数节点分别排在一起。请注意，这里的奇数节点和偶数节点指的是节点编号的奇偶性，而不是节点的值的奇偶性。
```
class Solution {
public:
    ListNode* oddEvenList(ListNode* head) {
        if(!head|| !head->next) return head;
        ListNode* oddhead =  head; //奇数链表
        ListNode* evenhead = head->next; //偶数链表
        ListNode* odd = oddhead;
        ListNode* even = evenhead;
        while(even && even->next){
            odd->next = odd->next->next;
            even->next = even->next->next;
            odd = odd->next;
            even = even->next;
        }
        odd->next = evenhead;
        return oddhead;
    }
};
```
## 19.有序链表转换二叉搜索树
```
// 如果不使用额外数组保存节点的值，要通过双指针找mid值来生成当前根节点
class Solution {
public:
    TreeNode* BST(vector<int>& nodes, int start, int end){
        if(start > end) return NULL;
        if(start == end)  return new TreeNode(nodes[start]);
        int mid = (start + end)/2;
        TreeNode* root = new TreeNode(nodes[mid]);
        root->left = BST(nodes, start, mid-1);
        root->right = BST(nodes, mid+1, end);
        return root;
    }
    TreeNode* sortedListToBST(ListNode* head) {
        if(!head) return NULL;
        vector<int> nodes;
        while(head){
            nodes.push_back(head->val);
            head = head->next;
        }
        return BST(nodes,0,nodes.size()-1);
    }
};
```