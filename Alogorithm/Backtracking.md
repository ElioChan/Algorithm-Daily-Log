# 简介
回溯是递归的副产物 所以都是通过递归实现回溯  
回溯的本质就是暴力搜索 遍历每一种可能的情况 并找到符合条件的返回。  
在某些情况下可以通过剪枝提高程序的效率,但是本质上依然是暴力搜索 因此效率不高。  
因此能用回溯解决的问题数据规模一定不会太大。  


## 模板
<details>
<summary>Code </summary>
<pre><code class="language-cpp">
void backtracking(参数) {
    if (终止条件) {
        存放结果;
        return;
    }

    for (选择：本层集合中元素（树中节点孩子的数量就是集合的大小）) {
        处理节点;
        backtracking(路径，选择列表); // 递归
        回溯，撤销处理结果
    }
}
</code></pre>
</details>

对于模板的解读：   
for循环里面的内容表示每层元素需要做的事 而backtracking这个递归函数表示从这一层进入下一层怎么进入


## 回溯三步走:  
- 返回值以及参数的设计    
    即每个回溯需要用到什么参数 这个很难决定 一般边写边加 然后是返回值 大部分时候都是void 有些时候用bool 比如八皇后问题  这个我还没搞清楚为什么
- 回溯的终止条件  
    即什么时候终止递归 把整个暴力搜索过程想象成遍历一棵树 考虑叶子节点的状态有什么特点
- 回溯搜索的遍历过程   
    即for循环这一块该怎么写 for里的起止 以及里面的循环体怎么写 怎么去重都在这一块的考虑范围

回溯的难点在于确定参数以及如何去重，同时针对不同的类型的问题会有不同的套路，主要可以分为以下几类问题
1. 组合问题：N个数字按照一定的规则找出k个数的集合
> 这里要和排列区分开，组合与顺序无关 但是排列和顺序有关
2. 切割问题：一个字符串按一定的规则有几种切割方式
> 切割问题和组合问题本质上是一样的，区别在于组合问题是从不同的位置选元素  而切割问题是从不同的位置选择切割线
3. 子集问题：一个N个数的集合里由多少符合条件的子集
> 如果把问题想象成一棵树，那么组合问题和切割问题就是找树的叶子节点 而子集问题就是找树的全部节点
4. 排列问题：N个数按照一定的规则全排列 有几种排列方式
> 排列问题的重点在于去重
5. 棋盘问题：N皇后，解数独等
> 这类问题好像都挺难 目前的经验：判断棋盘是否有效单独写一个函数出来

# LC77 组合
返回[1, n]中所有可能的k个数字的组合
> 由于给定集合是[1, n] 因此没有重复的元素 总的来说就是一个模板题

## 思路
套回溯法的框架 将组合想象成一个数 根节点有n种 选完根节点后 根节点的下一个节点有n-1种选择 所以有n-1个子节点 依次选择下去 直到数的叶子节点  一条路径就是一个组合 同时由于这里组合是无关顺序的 所以我们要求后面选择的数一定比前面的大 防止发生重复
所以终止条件：path.size() == k
回溯过程： path.push_back(i)  backtracking(n, k, i+1), path.pop_back()三步

## 代码
<details>
<summary>Code</summary>
<pre><code class = "language-cpp">
class Solution {
public:
    vector<vector<int>> res; 
    vector<int> path;
    void backtracking(int n, int k, int startIndex) {
        if(path.size() == k) {
            res.push_back(path);
            return;
        }

        for(int i = startIndex; i <= n; i++) {
            path.push_back(i);
            backtracking(n, k, i + 1);
            path.pop_back();
        }
    }
    vector<vector<int>> combine(int n, int k) {
        backtracking(n, k, 1);
        return res;
    }
};
</code></pre>
</details>


## 优化
由于我们要求不重复 所以后面的比前面大 但假设回溯到第i个数字时，后面剩下的数字已经不足以构成一条长为k的path 则可以直接剪枝 即 n - i + 1 < k - path.size()的时候可以剪枝


# LC39 LC40 LC216 组合总和
这三题都是求出组合总和等于给定数值的问题。
- 39：给一个无重复元素的整数数组candiates以及一个目标整数target,找出所有和等于target的不同组合。candiates中同一个元素可以无限重复选取
> 这里的关键是candiates本身无重复元素 但元素可以重复选取 同时要求组合不重复 所以每次递归进入下一层的时候可以选择的元素和这次一样就好了
- 40：给一个可能有重复元素的整数数组candiates以及一个目标整数target,找出所有和等于target的不同组合。candiates中同一个元素只能用一次 但一个组合中可以出现值一样的两个元素
> 这题的关键在于数组可以有重复元素 且每个元素只能用一次 但最后不能有重复的组合 因此需要去重
这里去重去的两个值相同的元素造成的组合相同 所以我们先对数组进行排序使得相同的元素相邻 这样我们只需要判断当前元素和上一个元素相同时 上一个元素是否使用过 若使用过 则表示这个组合里有两个相同值的元素 这是允许的 若上一个元素没有使用过 则选取当前的元素最后得到的组合会和选取上一个元素得到的组合重复 因此去重即可  
观察上面这段分析 我们可以知道我们需要在模板的基础上先对数组进行排序 以及增设vector<bool> used 参数 来表示元素是否使用过  
这里去重也可以使用unordered_set但是空间和时间复杂度都会大很多
- 216：找出所有相加之和为n的k个数的组合且满足：只能使用数字1-9，每个数字最多使用一次
> 这题相比于前面两题会简单一些 和39唯一的区别在于每个数字最多使用一次 所以递归进入下一层的时候下标需要前进1

## 代码

<details>
<summary>Code39</summary>
<pre><code class = "language-cpp">
class Solution {
public:
    vector<vector<int>> res;
    vector<int> path;
    void backtracking(vector<int>& candidates, int target, int sum, int startIndex) {
        if(sum > target) return ;
        if(sum == target) {
            res.push_back(path);
            return ;
        }
        for(int i = startIndex; i < candidates.size(); i++) {
            path.push_back(candidates[i]);
            sum += candidates[i];
            backtracking(candidates, target, sum, i);
            sum -= candidates[i];
            path.pop_back();
        }
    }
    vector<vector<int>> combinationSum(vector<int>& candidates, int target) {
        sort(candidates.begin(), candidates.end());
        backtracking(candidates, target, 0, 0);
        return res;
    }
};
</code></pre>
</details>

<details>
<summary>Code40</summary>
<pre><code class = "language-cpp">
class Solution {
public:
    vector<vector<int>> res;
    vector<int> path;
    void backtracking(vector<int>& candidates, int target, int sum, int startIndex, vector<bool>& used) {
        if(sum > target) return;
        if(sum == target) {
            res.push_back(path);
            return ;
        }
        for(int i = startIndex; i < candidates.size(); i++) {
            if(i && candidates[i] == candidates[i - 1] && used[i - 1] == false) continue;
            path.push_back(candidates[i]);
            sum +=  candidates[i];
            used[i] = true;
            backtracking(candidates, target, sum, i + 1, used);
            used[i] = false;
            sum -= candidates[i];
            path.pop_back();
        }
    }
    vector<vector<int>> combinationSum2(vector<int>& candidates, int target) {
        vector<bool> uesd(candidates.size(), false);
        sort(candidates.begin(), candidates.end());
        backtracking(candidates, target, 0, 0, uesd);
        return res;
    }
};
</code></pre>
</details>



<details>
<summary>Code216</summary>
<pre><code class = "language-cpp">
class Solution {
public:
    vector<vector<int>> res;
    vector<int> path;
    void backtracking(int n, int k, int startIndex, int sum) {
        if(sum > n) return ; // 剪枝 若当前和已经大于目标 则直接返回即可
        if(path.size() == k) {
            if(sum == n) {
                res.push_back(path);
            }
            return ;
        }

        for(int i = startIndex; i <= 9; i++) {
            path.push_back(i);
            sum += i;
            backtracking(n, k, i + 1, sum);
            sum -= i;
            path.pop_back();
        }
    }
    vector<vector<int>> combinationSum3(int k, int n) {
        backtracking(n, k, 1, 0);
        return res;
    }
};
</code></pre>
</details>


# LC131 切割回文串
题意 将一串字符串分割成回文串的字串 返回所有可能的分割结果
## 思路
利用回溯 之前组合的回溯是每次选取一个元素进行组合   
现在分割是每次选取前i个字符串进行分割 不同之处在于之前是选第i个 现在是前i个 同样的需要借助startIndex  
利用模板：
1. 终止条件：找到了回文串的分割方式 即startIndex到了最后面 > s.size()
2. 如何回溯：需要先判断substr(startIndex, i)是否是回文子串 若是 则 path + s.substr() 下次的startIndex设置为startIndex + i 
3. 参数设置：根据前两步分析 参数为s 以及 startIndex

## 代码
<details>
<summary>Code</summary>
<pre><code class = "language-cpp">
class Solution {
public:
    vector<vector<string>> res;
    vector<string> path;
    void backtracking(string& s, int startIndex) {
        if(startIndex >= s.size()) {
            res.push_back(path);
            return ;
        }
        
        for(int i = startIndex; i < s.size(); i++) {
            if(isPalindrome(s, startIndex, i)) {
                path.push_back(s.substr(startIndex, i - startIndex + 1));
            }
            else continue;
            backtracking(s, i + 1);
            path.pop_back();
        }
    }
    bool isPalindrome(string& s, int left, int right) {
        for(int i = left, j = right; i < j; i++,j--) {
            if(s[i] != s[j]) return false;
        }
        return true;
    }
    vector<vector<string>> partition(string s) {
        backtracking(s, 0);
        return res;
    }
};
</code></pre>
</details>

# LC93 复原IP地址
题意：和切割字符串一样 将一串数字切割为IP地址 而IP地址对于每一块是有要求的 
要求不能由前导0 以及 不能大于255
所以我们在切割字符串的基础上判断以上两件事即可
这个判断单独拎一个函数出来写
## 思路
回溯三步走
1. 终止条件：这个我一开始以为和切割字符串一样 但是题目要求IP地址由四个整数组成 所以只能切成四段  怎么判断切成了四段？？？
2. 跳到下一层：这个和startIndex一样 
3. 参数 

## 代码
<details>
<summary>Code</summary>
<pre><code class = "language-cpp">
class Solution {
public:
    vector<string> res;
    string path;
    void backtracking(string& s, int startIndex, int dotnum) {
        if(dotnum == 3) {
            if(isVaild(s, startIndex, s.size() - 1)) {
                res.push_back(s);
            }
            return ;
        }

        for(int i = startIndex; i < s.size(); i++) {
            if(isVaild(s, startIndex, i)) {
                s.insert(s.begin() + i + 1, '.');
                dotnum++;
                backtracking(s, i + 2, dotnum);
                dotnum--;
                s.erase(s.begin() + i + 1);
            }
        }
    }
    bool isVaild(string& s, int begin, int end) {
        if(begin > end) return false;
        if(s[begin] == '0' && begin != end) return false;
        int num = 0;
        for(int i = begin; i <= end; i++) {
            if(s[i] > '9' || s[i] < '0') return false;
            num = num * 10 + s[i] - '0';
            if(num > 255) return false;
        }
        return true;
    }
    vector<string> restoreIpAddresses(string s) {
        backtracking(s, 0, 0);
        return res;
    }
};
</code></pre>
</details>

# LC90 子集
给你一个整数数组 nums ，数组中的元素 互不相同 。返回该数组所有可能的子集（幂集）。
解集 不能 包含重复的子集。你可以按 任意顺序 返回解集。

## 思路
子集问题和组合以及切割问题最大的区别在于需要记录所有的节点 而非只是叶子节点

## 代码
<details>
<summary>Code</summary>
<pre><code class = "language-cpp">
class Solution {
public:
    vector<int> path;
    vector<vector<int>> res;
    void backtracking(vector<int>& nums, int startIndex) {
        res.push_back(path);
        if(startIndex > nums.size()) return ;

        for(int i = startIndex; i < nums.size(); i++) {
            path.push_back(nums[i]);
            backtracking(nums, i + 1);
            path.pop_back();
        }
    }
    vector<vector<int>> subsets(vector<int>& nums) {
        backtracking(nums, 0);
        return res;
    }
};
</code></pre>
</details>


# LC90 子集II
给你一个整数数组 nums ，其中可能包含重复元素，请你返回该数组所有可能的子集（幂集）。
解集 不能 包含重复的子集。返回的解集中，子集可以按 任意顺序 排列。

## 思路
这个和上面的区别在于有重复元素 因此需要去重 去重思想和LC40一致

## 代码
<details>
<summary>Code</summary>
<pre><code class = "language-cpp">
class Solution {
public:
    vector<vector<int>> res;
    vector<int> path;
    void backtracking(vector<int>& nums, int startIndex, vector<bool> used) {
        res.push_back(path);
        if(startIndex > nums.size()) return ;

        for(int i = startIndex; i < nums.size(); i++) {
            if(i && nums[i] == nums[i - 1] && used[i - 1] == false) continue;
            path.push_back(nums[i]);
            used[i] = true;
            backtracking(nums, i + 1, used);
            used[i] = false;
            path.pop_back();
        }
    }
    vector<vector<int>> subsetsWithDup(vector<int>& nums) {
        vector<bool> used(nums.size(), false);
        sort(nums.begin(), nums.end());
        backtracking(nums, 0, used);
        return res;
    }
};
</code></pre>
</details>


# LC491 递增子序列
给你一个整数数组 nums ，找出并返回所有该数组中不同的递增子序列，递增子序列中 至少有两个元素 。你可以按 任意顺序 返回答案。
数组中可能含有重复元素，如出现两个整数相等，也可以视作递增序列的一种特殊情况。

## 思路
和子集问题很像 不同点在于这边的序列无法用之前的去重方法 因为这里要求返回的是递增子序列 若要用之前的去重 需要对原序列进行排序 这样
得到的子序列必定递增 所以要换一种去重的方法 由于需要查找是否之前用过这个元素 因此可以用unordered_set查找速度块

## 代码
<details>
<summary>Code</summary>
<pre><code class = "language-cpp">
class Solution {
public:
    vector<vector<int>> res;
    vector<int> path;
    void backtracking(vector<int>& nums, int startIndex) {
        if(path.size() >= 2) res.push_back(path);
        if(startIndex > nums.size()) return ;
        unordered_set<int> useset;
        for(int i = startIndex; i < nums.size(); i++) {
            if((!path.empty() && nums[i] < path.back()) || useset.find(nums[i]) != useset.end()) continue;
            useset.insert(nums[i]);
            path.push_back(nums[i]);
            backtracking(nums, i + 1);
            path.pop_back();
        }
    }
    vector<vector<int>> findSubsequences(vector<int>& nums) {
        backtracking(nums, 0);
        return res;
    }
};
</code></pre>
</details>

# LC46 LC47 全排列I II
- 46：给定一个不含重复数字的数组 nums ，返回其 所有可能的全排列 。你可以 按任意顺序 返回答案
- 47：给定一个可包含重复数字的序列 nums ，按任意顺序 返回所有不重复的全排列

二者的区别在于是否包含重复数字 
对于47：  
依然是去重的问题，我一开始想的是这个需要对树枝和树层同时去重
因为要求不能由重复的排列 所以需要对树层进行去重  毕竟如果存在两个元素相等 则同一层中后一个元素可以剪枝去重
又因为可包含重复的数字 所以需要对树枝去重 即同一个元素不能使用两次

所以我树枝用了vector<bool> 树层用了unordered_set 用于查找元素 但是这边由于是排列 所以对于path的顺序没有要求 可以直接对原数组进行排序 后利用nums[i] == nums[i-1]进行判断 这样可以节省很大的开销
	
<mark>总结一下：
- 对于顺序没有要求的
	- 树层 先对原数组排序  直接判断nums[i] == nums[i - 1]
	- 树枝 利用vector<bool>
- 对于顺序有要求的
	- 树层 设置unordered_set 但是不用erase 写在循环外面 对应每层一个
	- 树枝 设置unordered_set 或者vector<bool> 都可以 但是建议vector 开销小一点
	
	
所以可以发现47题的树枝树层都可以用同一个vector进行 减少开销

## 代码
<details>
<summary>Code46</summary>
<pre><code class = "language-cpp">
class Solution {
public:
    vector<vector<int>> res;
    vector<int> path;
    void backtracking(vector<int>& nums, vector<bool> used) {
        if(path.size() == nums.size()) {
            res.push_back(path);
            return;
        }

        for(int i = 0; i < nums.size(); i++) {
            if(used[i] == true) continue;

            path.push_back(nums[i]);
            used[i] = true;
            backtracking(nums, used);
            used[i] = false;
            path.pop_back();
        }
    }
    vector<vector<int>> permute(vector<int>& nums) {
        // unordered_set<int> unused;
        vector<bool> used(nums.size(), false);
        backtracking(nums, used);
        return res;
    }
};
</code>></pre>
</details>

<details>
<summary>Code47</summary>
<pre><code class = "language-cpp">
class Solution {
public:
    vector<vector<int>> res;
    vector<int> path;
    void backtracking(vector<int>& nums, vector<bool> dep) {
        if(path.size() == nums.size()) {
            res.push_back(path);
            return ;
        }
         
        unordered_set<int> wide;
        for(int i = 0; i < nums.size(); i++) {
            if(dep[i] == true || wide.find(nums[i]) != wide.end()) continue;

            path.push_back(nums[i]);
            dep[i] = true;
            wide.insert(nums[i]);
            backtracking(nums, dep);
            dep[i] = false;
            path.pop_back();
        }
    }
    vector<vector<int>> permuteUnique(vector<int>& nums) {
        vector<bool> dep(nums.size(), false);
        backtracking(nums, dep);
        return res;
    }
};
</code>></pre>
</details>


# LC51 N皇后
待定

# LC37 解数独

待定
