# 03 数组中重复的数字
题意：给定长度为n的数组 所有的元素都在0-(n-1)的范围内 找出其中任意一个重复的数字

## 思路1 哈希
利用一个哈希表存储已经遍历过的数字 若第二次遍历到 则返回

选择用哈希表是因为查找元素快 由于这里的数字范围给定所以也可以直接用一个大小为n的数组存储

时间复杂度O(n) 空间复杂度O(n)

## 思路2 原地修改
由于元素范围给定0 - (n-1) 同时数组长度为n 因此如果没有重复元素 则可以通过不断地交换将数字交换到正确的下标下 若是存在重复 则在交换过程中一定会遇到待交换的数字相同 此时就找到了重复的元素

时间复杂度O(n) 空间复杂度O(1)

<pre><code>
   int findRepeatNumber(vector<int>& nums) {
        int n = nums.size();
        int i = 0;
        while(i < n ){
            if(nums[i] == i) {
                i++;
                continue;
            } 
            if(nums[i] == nums[nums[i]]) return nums[i];
            swap(nums[i], nums[nums[i]]);
        }
        return -1;
    }
</code></pre>

# 04 二维数组中的查找
给定一个二维数组n * m维 每一行都是递增 每一列都是递增 给定一个数 判断二维数组是否包含这个数

## 思路1 二分查询
利用每行都是有序的这一特点 进行二分查询

时间复杂度O(n log m) 空间复杂度O(1)

## 思路2 


# 05 替换空格
给定字符串 将字符串中的空格替换为 %20 

## 思路1 
首先统计字符串中的有多少个空格 然后将字符串扩大到相应的大小  最后逆序遍历字符串遇到空格就替换为 %20

时间复杂度O(n) 空间复杂度O(1)

# 06 从尾到头打印链表
给定链表 逆序打印链表中的元素 以vector的形式返回

## 思路1 递归
递归的将元素加入vector即可

## 思路2 栈
先将元素顺序存入栈中 然后将栈中元素输出到vector中 即可

# 07 重建二叉树
给定二叉树的前序和中序遍历结果 返回树

## 思路1
根据前序 = 根 + 左子树 + 右子树 中序 = 左子树 + 根 + 右子树的特点
逐步确定中序中的根节点的位置 构造左右子树 即可
由于需要找到根节点的位置 因此可以构造哈希表进行快速查询

怎么逐步找？
- 首先确定前序的第一个元素为根 在中序中找到根 可以划分为左右子树 得到左右子树的长度
- 递归的构建左子树 由于第一步中得到了左子树的元素个数 因此类似于第一步可以找到左子树的根节点 然后在中序中找到该节点 进一步划分左右子树 
- 递归的构建右子树 过程类似

<pre><code>
class Solution {
public:
    unordered_map<int, int> index;

    TreeNode* build(vector<int>& preorder, vector<int>& inorder, int pre_left, int pre_right, int in_left, int in_right) {
        if(pre_left > pre_right) return NULL;

        int pre_root = preorder[pre_left];
        int in_root_ind = index[pre_root];
        TreeNode* root = new TreeNode(pre_root);
        int left_sub = in_root_ind - in_left;
        int right_sub = in_right - in_root_ind;
        root->left = build(preorder, inorder, pre_left + 1, pre_left + left_sub, in_left, in_root_ind - 1);
        root->right = build(preorder, inorder, pre_left + left_sub + 1, pre_right, in_root_ind + 1, in_right);
        return root;
    }
    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
        int n = preorder.size();
        for(int i = 0; i < n; i++) index[inorder[i]] = i;
        return build(preorder, inorder, 0, n - 1, 0, n - 1);
    }
};
</code></pre>

## 思路2
<pre><code>
    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
        // 思路2 官解迭代法
        // 中序遍历的第一个元素表示树中左下角的元素
        // 所以我们不断地遍历前序中的元素 直到等于中序的第一个元素 这一段都是构建左子树
        // 直到栈顶元素和中序的第一个元素相等了 我们知道左子树遍历完了 现在开始增加右子树 
        // 如何判断该元素为那个节点的右子树？？？ 由于中序为左根右 前序为根左右 因此pop出栈中的元素 直到和中序的元素不相等时后 就找到了有右节点的父节点 即不相等节点的前一个节点 即为我们要找的节点 把当前元素赋为他的右子树 所所我们需要保存上一个pop出来的元素 
        if(preorder.empty()) return NULL;
        stack<TreeNode*> sta;
        TreeNode* root = new TreeNode(preorder[0]);
        sta.push(root);
        int n = preorder.size();
        int ind = 0;
        for(int i = 1; i < n; i++) {
            int cur = preorder[i];
            auto node = sta.top();
            if(sta.top()->val != inorder[ind]) {
                node = sta.top();
                node->left = new TreeNode(cur);
                sta.push(node->left);
            } else {
                while(!sta.empty() && sta.top()->val == inorder[ind]) {
                    node = sta.top();
                    sta.pop();
                    ind++;
                }
                node->right = new TreeNode(cur);
                sta.push(node->right);
            }
        }
        return root;
    }
</code></pre>


