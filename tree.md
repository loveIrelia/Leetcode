#二叉树总结
#### 个人心得：二叉树的题型比较单一，不少题型带有DFS的思想，可以通过二叉树的前序遍历或者后序遍历（递归方法）为基础来解题，除此之外可以用层序遍历为基础解题。
## 1.二叉树的三种非递归遍历
####前序遍历
```
class Solution {
public:
    vector<int> preorderTraversal(TreeNode* root) {
        if (root == nullptr) {
            return {};
        }
        TreeNode* cur = root;
        vector<int> ans;
        stack<TreeNode*> s;
        while(cur != nullptr || !s.empty()) {
            while(cur != nullptr) {
                // 第一次访问就输出（即入栈时就输出）
                s.push(cur);
                ans.push_back(cur->val);
                cur = cur->left;
            }
            cur = s.top();
            s.pop();
            cur = cur->right;
        }
        return ans;
    }
};
```
####中序遍历
```
class Solution {
public:
    vector<int> inorderTraversal(TreeNode* root) {
        // 迭代方式
        if (root == nullptr) {
            return {};
        }
        TreeNode* cur = root;
        vector<int> ans;
        stack<TreeNode*> s;
        while(cur != nullptr || !s.empty()) {
            while(cur != nullptr) {
                // 第一次访问（入栈时）不输出
                s.push(cur);
                cur = cur->left;
            }
            // 第二次访问（出栈时）输出
            cur = s.top();
            ans.push_back(cur->val);
            s.pop();
            cur = cur->right;
        }
        return ans;
    }
};
```
####后序遍历
```
class Solution {
public:
    // 教科书的写法，添加标志位
    // flag=1：第二次访问（即第一次出栈）
    // flag=2：第三次访问（即第二次出栈，因为第二次访问后会重新入栈）
    struct PostTreeNode {
        TreeNode* TNode;
        int flag;
    };

    vector<int> postorderTraversal(TreeNode* root) {
        if (root == nullptr) {
            return {};
        }
        TreeNode* cur = root;
        vector<int> ans;
        stack<PostTreeNode*> s;
        PostTreeNode* p;
        while(cur != nullptr || !s.empty()) {
            if (cur != nullptr) {
                p = new PostTreeNode;
                // 第一次访问（入栈时）不输出
                p->TNode = cur;
                p->flag = 1;
                s.push(p);
                cur = cur->left;
            } else {
                p = s.top();
                s.pop();
                if (p->flag == 1) {
                    // 是第二次访问，即其右子树还没有访问
                    s.push(p);
                    p->flag = 2;
                    cur = p->TNode->right;
                } else {
                    // 是第三次访问，即其右子树都访问过了
                    ans.push_back(p->TNode->val);
                }
            }
        }
        return ans;
    }
};
```
注：非递归后序遍历二叉树还有一种简单的方法：按照根-右-左的顺序进行前序遍历，完成后逆序输出，在此不贴代码。
## 2.二叉树的层序遍历
```
class Solution {
public:
    vector<vector<int>> levelOrder(TreeNode* root) {
        vector<vector<int>> v;
        queue<TreeNode*> q;   //队列
        if (root==NULL) return {};
        q.push(root);
        while(!q.empty())
        {
            int len = q.size();
            vector<int> level;
            while(len)
            {
                TreeNode* temp = q.front();
                q.pop();
                level.push_back(temp->val);
                len--;
                if (temp->left != NULL) q.push(temp->left);
                if (temp->right != NULL) q.push(temp->right);
            }
            v.push_back(level);
        }
        return v; //返回二维数组，每一组是某一层的所有节点
    }
};
```
## 2.1二叉树的锯齿形层序遍历
```
// 与 "2"的遍历略微不同，上一层正序，下一层倒序...
class Solution {
public:
    vector<vector<int>> zigzagLevelOrder(TreeNode* root) {
        vector<vector<int>> result;
        if (!root) return result;
        TreeNode *cur = root;
        queue<TreeNode *> q;
        q.push(cur);
        int idx = 1;
        while(!q.empty()){
            int len = q.size();
            vector<int> v;
            for(int i = 0;i<len;i++){
                TreeNode* t = q.front();
                q.pop();
                v.push_back(t->val);
                if(t->left) q.push(t->left);
                if(t->right) q.push(t->right);
            }
            if (idx%2==0) reverse(v.begin(),v.end());
            result.push_back(v);
            idx++;
        }
        return result;
    }
};
```
## 3.二叉树的最大高度
```
// 后序遍历的思路
class Solution {
public:
    int maxDepth(TreeNode* root) {
        if(!root) return 0;
        int left = maxDepth(root->left);
        int right = maxDepth(root->right);
        return left>right ? left+1:right+1;
    }
};
```
## 3.1 二叉树的最小高度
最小深度是从根节点到最近叶子节点的最短路径上的节点数量。
```
class Solution {
public:
    int minDepth(TreeNode* root) {
        if(!root) return 0;
        if(!root->left&&!root->right) return 1;
        int left = minDepth(root->left);
        int right = minDepth(root->right);
		//这里其中一个节点为空，说明left和right有一个必然为0，所以可以返回m1 + m2 + 1;
        if(!root->left || !root->right) return left+right+1;
        return min(left,right)+1;
    }
};
```
## 4.验证二叉排序树
```
//利用递归，需要树及其子树作为根时，左孩子的值<根的值<右孩子的值
class Solution {
public:
    bool isValidBST(TreeNode* root) {
        TreeNode* min = NULL;
        TreeNode* max = NULL;
        return helper(root, min ,max);
    }
    bool helper(TreeNode* root, TreeNode* min, TreeNode* max){
        if (!root) return true;
        if (min && root->val<=min->val) return false;
        if (max && root->val>=max->val) return false;
        return helper(root->left,min,root) && helper(root->right,root,max);
    }
};
```
## 5.验证两颗树是否相同
```
// 可以利用前序遍历的思想，只要两棵树的节点一开始就不一样，则直接返回False
// 否则递归检查两棵树的左子节点和右子节点是否相同
class Solution {
public:
    bool isSameTree(TreeNode* p, TreeNode* q) {
        if(!p && !q) return true;
        if(!p && q) return false;
        if(p && !q) return false;
        if(p->val!=q->val) return false;
        return isSameTree(p->left,q->left) && isSameTree(p->right,q->right);
    }
};
```
## 6.对称二叉树
验证该树自身是否镜像对称
```
// 思路和“验证两颗树是否相同”有点类似，利用前序遍历的思想进行检查，开始递归，
// 因为是判断镜像，所以是左子树和右子树比较，即递归为(p->left,q->right)和(p->right,q->left)
class Solution {
public:
   bool isSymmetric(TreeNode* root) {
        return isMirror(root,root);
   }
   bool isMirror(TreeNode *p, TreeNode *q) {
        if(p==NULL && q==NULL) return true;
        if(p==NULL || q==NULL) return false;
        if(p->val!=q->val) return false;
        else
            return isMirror(p->left,q->right) && isMirror(p->right,q->left);
   }
};
```
## 7.从前序遍历和中序遍历来构造二叉树
```
// 思路比较直白，创建了四个数组来区分左子树和右子树的节点（不止一种解法）。
// 理论就是：preorder[0]一定是当前的根节点，通过这个去inorder找当前的根节点，
// inorder数组中，当前根节点的索引的前面部分是当前根节点的左子树，后面部分是当前根节点的右子树,
// 最终递归生成
class Solution {
public:
    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
        int m = preorder.size();
        if (m==0) return NULL;
        TreeNode* root = new TreeNode(preorder[0]);
        vector<int> preorder_left, inorder_left, preorder_right, inorder_right;
        int i ;
        for(i=0;i<m;i++){
            if (inorder[i]==preorder[0]) break;
            else  inorder_left.push_back(inorder[i]);
        }
        for(i=i+1;i<m;i++) inorder_right.push_back(inorder[i]);
        for(int j =1;j<m;j++){
            if (j<=inorder_left.size()) preorder_left.push_back(preorder[j]);
            else  preorder_right.push_back(preorder[j]);
        }
        root->left =  buildTree(preorder_left, inorder_left);
        root->right = buildTree(preorder_right, inorder_right);
        return root;
    }
};

```
## 8.从中序与后序遍历序列构造二叉树
```
// 思路和“7”相同，区别为postorder[n-1]是根节点，preorder[0]是根节点
class Solution {
public:
    TreeNode* buildTree(vector<int>& inorder, vector<int>& postorder) {
        int m = postorder.size();
        if (m==0) return NULL;
        TreeNode* root = new TreeNode(postorder[m-1]);
        vector<int> inorder_left, postorder_left, inorder_right, postorder_right;
        int i ;
        for(i=0;i<m;i++){
            if (inorder[i]==postorder[m-1]) break;
            else  inorder_left.push_back(inorder[i]);
        }
        for(i=i+1;i<m;i++) inorder_right.push_back(inorder[i]);
        for(int j =0;j<m-1;j++){
            if (j<inorder_left.size()) postorder_left.push_back(postorder[j]);
            else  postorder_right.push_back(postorder[j]);
        }
        root->left =  buildTree(inorder_left, postorder_left);
        root->right = buildTree(inorder_right, postorder_right);
        return root;
    }
};
```
## 9.验证平衡二叉树
```
// 平衡二叉树定义：所有当前根节点的左右子树的高度差的绝对值≤1
// 思路：利用后序遍历的思路计算当前根节点的左右子树的高度差，
// 该值的绝对值如果大于1，返回-1，否则返回当前根节点的高度。
class Solution {
public:
    bool isBalanced(TreeNode* root) {
        return height(root)!=-1;
    }
    int height(TreeNode* root){
        if (!root) return 0;
        int left = height(root->left);
        int right = height(root->right);
        if (abs(left-right)>1 || left==-1 || right==-1){
            return -1;
        }
        else return 1+max(left,right);
    }
};
```
## 10. 路径总和
![](https://kuangstudy.oss-cn-beijing.aliyuncs.com/bbs/2021/02/07/kuangstudyba5cf054-ca53-47c1-9af9-ffffc3859df3.jpg)
```
// 深度优先搜索
class Solution {
public:
    bool hasPathSum(TreeNode* root, int sum) {
        if(!root) return false;
        if(!root->left&&!root->right&&sum-root->val==0) return true;
        return hasPathSum(root->left, sum-root->val)|| hasPathSum(root->right, sum-root->val);
    }
};
```
## 10.1 路径总和Ⅱ
![](https://kuangstudy.oss-cn-beijing.aliyuncs.com/bbs/2021/02/07/kuangstudy5a0289da-d875-4197-9816-f18bd91cb2c3.jpg)
```
// 深度优先搜索
class Solution {
public:
    void DFS(vector<vector<int>>& res, vector<int>& path, TreeNode* root, int sum){
        if(!root) return;
        path.push_back(root->val);
        sum-=root->val;
        if(!root->left&&!root->right && sum==0){
            res.push_back(path);
        }
        DFS(res,path,root->left,sum);
        DFS(res,path,root->right,sum);
        path.pop_back();
    }
    vector<vector<int>> pathSum(TreeNode* root, int sum) {
        if (!root) return {};
        vector<vector<int>> res;
        vector<int> path;
        DFS(res, path, root, sum);
        return res;
    }
};
```
## 10.2 路径总和Ⅲ
![](https://kuangstudy.oss-cn-beijing.aliyuncs.com/bbs/2021/02/07/kuangstudy30dd469c-e9a3-4fbe-8e59-9b486b85c66f.jpg)
```
// 因为不要求从根节点开始到叶子节点结束，
// 所以要进行双深度优先搜索
class Solution {
public:
    // int ans = 0;
    int DFS(TreeNode* root, int sum, int ans){
        if (!root) return 0 ;
        sum-=root->val;
        if(!sum) ans++;
        ans+=DFS(root->left,sum, ans);
        ans+=DFS(root->right,sum, ans);
        return ans;
    }
    int pathSum(TreeNode* root, int sum) {
        if(!root) return 0;
        int ans = 0;
        int res = DFS(root,sum, ans);
        pathSum(root->left, sum);
        pathSum(root->right, sum);
        return res;
    }
};
```
## 11.二叉树展开为链表
![](https://kuangstudy.oss-cn-beijing.aliyuncs.com/bbs/2021/02/07/kuangstudyea24f128-4da4-4e9f-8501-3df59701f39f.jpg)
```
// 思路：从当前根节点开始找它左子树的最右节点，
// 该最右节点连接当前根节点的右子树，
// 当前根节点的左指针为NULL，右指针指向左子树
class Solution {
public:
    void flatten(TreeNode* root) {
        while(root){
            // if(root->left==nullptr) root=root->right;
            if(root->left!=nullptr){
                auto tmp = root->left;
                while(tmp->right) tmp = tmp->right;
                tmp->right = root->right;
                root->right = root->left;
                root->left = nullptr;
            }
            root=root->right;
        }
        return;
    }
};
```
## 12.二叉树最大路径和
![](https://kuangstudy.oss-cn-beijing.aliyuncs.com/bbs/2021/02/07/kuangstudy69fe710a-f992-49e1-8d33-dab5c7135fb1.jpg)
```
// 利用后序遍历的思想，对当前根节点来说，它的最大路径和,
// 一定是当前值+左子树、右子树的最大路径和（和计算高度类似，计算高度可以看作节点值为1），
// 所以在递归的过程中会进行 path>max_sum 比较
class Solution {
public:
    int max_sum=INT_MIN;
    int maxGain(TreeNode* root){
        if (!root) return 0;
        int left = max(maxGain(root->left),0);
        int right = max(maxGain(root->right),0);
        int path = root->val+left+right;
        if (path>max_sum) max_sum=path;
        return root->val+max(left,right);
    }
    int maxPathSum(TreeNode* root) {
        maxGain(root);
        return max_sum;
    }
};
```
## 13.求根到叶子节点数字之和
```
// 通过DFS直接计算所有路径值，累加即可
class Solution {
public:
    int sumNumbers(TreeNode* root) {
        int s = 0;
        vector<int> res;
        DFS(s,res,root);
        int ans = 0;
        for(int i = 0;i<res.size();i++) ans+=res[i];
        return ans;
    }
    void DFS(int& s, vector<int>& res,TreeNode* root){
        if(root==NULL) return;
        s = s*10+root->val;
        if(root->left==NULL && root->right == NULL) res.push_back(s);
        DFS(s,res,root->left);
        DFS(s,res,root->right);
        s = (s-root->val)/10;
    }
};
```
## 14.二叉树的右视图
```
// 取树的每一层的从左到右数最后一个节点，层序遍历
class Solution {
public:
    vector<int> rightSideView(TreeNode* root) {
        vector<int> vec;
        if (!root) return vec;
        queue<TreeNode*> Q;
        Q.push(root);
        while(!Q.empty()){
            int count = Q.size();
            for(int i = 0;i<count;i++){
                TreeNode* temp = Q.front();
                if (i==count-1) vec.push_back(temp->val);
                if (temp->left) Q.push(temp->left);
                if (temp->right) Q.push(temp->right);
                Q.pop();
            }
        }
        return vec;

    }
};
```
## 15.完全二叉树的节点个数
```
// 网上有个二分查找+位运算的解法，其实DFS和BFS就可以解，这里就贴个简单的BFS解法。
class Solution {
public:
    int countNodes(TreeNode* root) {
        if(!root) return 0;
        queue<TreeNode* >q;
        q.push(root);
        int res=0;
        while(!q.empty()){
            TreeNode* temp = q.front();
            q.pop();
            res++;
            if(temp->left) q.push(temp->left);
            if(temp->right) q.push(temp->right);
        }
        return res;
    }
};
```
## 16.翻转二叉树
![](https://kuangstudy.oss-cn-beijing.aliyuncs.com/bbs/2021/02/07/kuangstudyd46afb27-7728-4e73-bd6a-c809945f068f.jpg)
```
// 自下而上翻转，运用后序遍历的思想
class Solution {
public:
    TreeNode* invertTree(TreeNode* root) {
        if(!root) return NULL;
        TreeNode* left = invertTree(root->left);
        TreeNode* right = invertTree(root->right);
        root->right = left;
        root->left = right;
        return root;
    }
};
```
## 17.二叉排序树的第k小元素
```
// 解法不止一种。因为二叉排序树的中序遍历是有序的，
// 所以遍历到第K次就找到了
class Solution {
public:
    int kthSmallest(TreeNode* root, int k) {
        int res = 0, n = 0;
        DFS(root,k,res,n);
        return res;
    }
    void DFS(TreeNode* root, int k, int& res, int& n){
        if(!root) return;
        DFS(root->left,k,res,n);
        n++;
        if(n==k) res = root->val;
        DFS(root->right,k,res,n);
    }
};
```
## 18. 二叉树的最近公共祖先
![](https://kuangstudy.oss-cn-beijing.aliyuncs.com/bbs/2021/02/07/kuangstudy387c4b48-8c2f-44fe-8103-be971172d49c.jpg)
```
class Solution {
// 最近公共祖先只会存在一个，所以不断的寻找当前节点的左孩子或者右孩子是否是P或者Q，
// 如果是就可以直接返回了，再对代码中的left，或者right判断，
// 因为最近公共祖先只会存在一个，所以两个其中有一个为空，返回left或right，
// 如果两个都不是空，返回root，
//如果两个都空，只能返回NULL
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if(root == NULL)
            return NULL; //如果当前root是NULL，直接返回
        if(root == p || root == q)
            return root; // 如果当前root就是P或者Q，直接返回
        //以上是两个递归出口
		//采用后序遍历的思想
        TreeNode* left =  lowestCommonAncestor(root->left, p, q);
        TreeNode* right = lowestCommonAncestor(root->right, p, q);
        if(left == NULL)
            return right;
        if(right == NULL)
            return left;
        if(left && right) // p和q在两侧
            return root;
        return NULL; // 必须有返回值
    }
};
```
## 19.二叉排序树的最近公共祖先
```
class Solution {
// 除了按照“18”的做法，可利用二叉排序树的性质
// 如果当前节点的值大于P,Q的值，说明 P,Q应该在当前节点的左子树，因此将当前节点移动到它的左子节点,
// 如果当前节点的值小于P,Q的值，说明P,Q应该在当前节点的右子树，因此将当前节点移动到它的右子节点,
// 如果当前节点的值不满足上述两条要求，那么说明当前节点就是「分岔点」。此时P,Q要么在当前节点的不同的子树中，要么其中一个就是当前节点。
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        int temp = root->val;
        if(p->val>temp && q->val>temp) return lowestCommonAncestor(root->right, p,  q);
        else if (p->val<temp && q->val<temp)  return lowestCommonAncestor(root->left, p,  q);
        else return root ;
    }
};
```
## 20.二叉树的序列化和反序列化
```
// 采用Python写了，因为Python处理字符串相对简单一点
class Codec:

    def serialize(self, root):
        """Encodes a tree to a single string.
        :type root: TreeNode
        :rtype: str
        """
        res = []
        def DFS(node):
            if node:
                res.append(str(node.val))
                DFS(node.left)
                DFS(node.right)
            else:
                res.append("#")
        DFS(root)
        return ",".join(res)


    def deserialize(self, data):
        """Decodes your encoded data to tree.
        :type data: str
        :rtype: TreeNode
        """
        def DFS():
            val = next(it)
            if val=='#' :
                return None
            node = TreeNode(val)
            node.left= DFS()
            node.right = DFS()
            return node
        it = iter(data.split(','))
        return DFS()
```
## 21.二叉树的左叶子之和
```
// 可以DFS也可以BFS，这里使用DFS
class Solution {
public:
    int sumOfLeftLeaves(TreeNode* root) {
        int sum = 0;
        if(!root) return 0;
        if(root->left){
            if(!root->left->left && ! root->left->right){
                sum = root->left->val;
            }
            else sum = sumOfLeftLeaves(root->left);
        }
        sum += sumOfLeftLeaves(root->right);
        return sum;
    }
};
```
## 22.二叉树的直径
给定一棵二叉树，你需要计算它的直径长度。一棵二叉树的直径长度是任意两个结点路径长度中的最大值。这条路径可能穿过也可能不穿过根结点。
```
class Solution {
// 此题与二叉树最大路径和 相同
public:
    int length = 0;
    int DFS(TreeNode* root){
        if(!root) return 0;
        int left = DFS(root->left);
        int right = DFS(root->right);
        length = max(length,left+right+1);
        return max(left,right)+1;
    }
    int diameterOfBinaryTree(TreeNode* root) {
        if (!root) return 0;
        DFS(root);
        return length-1;
    }
};

//二叉树最大路径和
class Solution {
public:
    int max_sum=INT_MIN;
    int maxGain(TreeNode* root){
        if (!root) return 0;
        int left = max(maxGain(root->left),0);
        int right = max(maxGain(root->right),0);
        int path = root->val+left+right;
        if (path>max_sum) max_sum=path;
        return root->val+max(left,right);
    }
    int maxPathSum(TreeNode* root) {
        maxGain(root);
        return max_sum;
    }
};
```
## 23.另一个树的子树
```
class Solution {
// 思路：前序遍历，以原树的每一个节点为当前根节点，去匹配
public:
    bool DFS(TreeNode* s, TreeNode* t){
        if (!s && !t) return true;
        if((!s && t)||(s && !t)||(s->val!=t->val)) return false;
        return DFS(s->left, t->left) && DFS(s->right, t->right);
    }
    bool isSubtree(TreeNode* s, TreeNode* t) {
        if(!s) return false;
        return DFS(s,t) || isSubtree(s->left,t) || isSubtree(s->right,t);
    }
};
```
## 24.合并二叉树
```
class Solution {
// 思路：前序遍历
public:
    TreeNode* mergeTrees(TreeNode* t1, TreeNode* t2) {
        if (t1==NULL) return t2;
        if(t2==NULL) return t1;
        t1->val+=t2->val;
        t1->left = mergeTrees(t1->left, t2->left);
        t1->right = mergeTrees(t1->right, t2->right);
        return t1;
    }
};
```
## 25.二叉树的完全性检验
```
//完全二叉树的层序遍历不会出现形如：[p0,null,p1,p2]，所以使用pre作为前驱判断
class Solution {
public:
    bool isCompleteTree(TreeNode* root) {
        if(!root) return true;
        queue<TreeNode* > q;
        q.push(root);
        TreeNode* prev = root;
        while(!q.empty()){
            TreeNode* temp = q.front();
            q.pop();
            if(!prev && temp) return false;
            if(temp){
                q.push(temp->left);
                q.push(temp->right);
            }
            prev = temp;
        }
        return true;
    }
};
```