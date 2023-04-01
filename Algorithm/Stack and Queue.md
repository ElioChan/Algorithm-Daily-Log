# LC 232 用栈实现队列
## 思路 
双栈实现队列:
输入栈 和 输出栈 
- 输入数据时 往输入栈中push数据即可
- 输出数据时 先判断输出栈是否为空 不为空直接pop 若为空 则需要先将输入栈的数据全部移动到输出栈中
## 代码
<details>
<summmary>Code</summary>
<pre><code class="language-cpp">
class MyQueue {
public:
    stack<int> sta_in;
    stack<int> sta_out;
    MyQueue() {

    }
    
    void push(int x) {
        sta_in.push(x);
    }
    
    int pop() {
        if(sta_out.empty()) {
            while(!sta_in.empty()) {
                int temp = sta_in.top();
                sta_in.pop();
                sta_out.push(temp);
            }
        }
        int res = sta_out.top();
        sta_out.pop();
        return res;
    }
    
    int peek() {
        if(sta_out.empty()) {
            while(!sta_in.empty()) {
                int temp = sta_in.top();
                sta_in.pop();
                sta_out.push(temp);
            }
        }
        return sta_out.top();
    }
    
    bool empty() {
        return sta_in.empty() && sta_out.empty();
    }
};

/**
 * Your MyQueue object will be instantiated and called as such:
 * MyQueue* obj = new MyQueue();
 * obj->push(x);
 * int param_2 = obj->pop();
 * int param_3 = obj->peek();
 * bool param_4 = obj->empty();
 */
</code></pre>
</details>


# LC225 用队列实现栈
## 思路
这里和用栈实现队列不太一样 可以用一个队列实现栈  
入栈 即入队列, 出栈 由于队列先进先出 而栈要求后进先出 所以需要将队列尾部的元素出栈  
**具体做法** : 将队首的元素pop出来后 添加到队尾 直到原来的队尾元素即栈顶元素到达队首
## 代码

<details>
<summary>Code</summary>
<pre><code class="language-cpp">
class MyStack {
public:
    queue<int> que;
    MyStack() {

    }
    
    void push(int x) {
        que.push(x);
    }
    
    int pop() {
        int len = que.size();
        while(len > 1) {
            int temp = que.front();
            que.pop();
            que.push(temp);
            len--;
        }
        int temp = que.front();
        que.pop();
        return temp;
    }
    
    int top() {

        return que.back();       
    }
    
    bool empty() {
        return que.empty();
    }
};

/**
 * Your MyStack object will be instantiated and called as such:
 * MyStack* obj = new MyStack();
 * obj->push(x);
 * int param_2 = obj->pop();
 * int param_3 = obj->top();
 * bool param_4 = obj->empty();
 */
</code></pre>
</details>

# LC20 有效的括号 LC1047 删除字符串中的所有相邻重复项 LC150 逆波兰表达式求值
两题都是匹配字符串的问题 经典的利用栈后进先出的特性的问题  
需要注意的是：**判断条件的先后顺序也很重要**  
这题需要将不等于栈顶元素的char压入栈中 需要判断栈是否为空  
写成
if(s[i] != sta.top() || sta.empty()) 就会报错   
原因在于 当栈为空时 前一个条件会出错  
但是如果把empty放在前面 由于||只要有一个为真 会自动跳过后面的判断 因此不会报错  

C++的string提供栈的接口.....救敏 才知道 所以可以直接用string存就完事了

## 代码
<details>
<summary>Code</summary>
<pre><code class = "language-cpp">
class Solution {
public:
    string removeDuplicates(string s) {
        string stk;
        for (char ch : s) {
            if (!stk.empty() && stk.back() == ch) {
                stk.pop_back();
            } else {
                stk.push_back(ch);
            }
        }
        return stk;
    }
};
</pre></code>
</details>


# LC239 滑动窗口最小值
<mark>单调队列</mark>




# LC347 前K个高频元素
<mark>优先队列</mark>
题目:给定一个数组，返回出现次数前k个高的元素
## 思路
这题思路不算难 我一开始想的是利用hash表统计次数 然后排序 最后弄到vector里  但是排序这块没想好 所以想用set自动排序 但是用set应该就超时了吧

看了题解后 大致思路一致 不过这里的排序用的是优先队列也叫大顶堆
因为我们只需要前k高的元素 所以不需要对所有的元素进行排序 维护一个大小为k的优先队列即可 

现在难点来到了优先队列上 因为之前没有接触过 所以完全不知道该怎么写 还好评论区有大神帮我总结，我摘抄一遍：https://leetcode.cn/problems/top-k-frequent-elements/solution/c-xiao-bai-you-hao-you-xian-dui-lie-de-j-53ay/

## 声明语句：
```
priority_queue<Type, Container, Function>;
```
Type是需要存放的数据类型 在这题里面由于需要对频次排序 但最后返回的是元素 所以使用pair
Container是实现底层堆的容器 例如vector, deque默认为vector
Function是比较方式 默认的是大顶堆 由于我们这边存储的数据类型是pair所以需要自己定义比较方式
 ## 自定义比较方式
 先po一下看到过的官解和代码随想录里的定义方法：
 官解：官解相当于定义了 一个函数  所以直接作为参数传入priority_que的定义声明即可
 这里的decltype和auto 都是自动声明类型 但是auto要求变量一定要初始化 decltype不要求
 <details>
 <summary>Code</summary>
 <pre><code class = "langauge-cpp">
 static bool cmp(pair<int, int>& m, pair<int, int>& n) {
	 return m.second > n.second;
 }
 
 priority_queue<pair<int, int>, vector<pair<int, int>>, decltype(&cmp)> q(cmp);
 </pre></code>
 </details>

 代码随想录：代码随想录定义了一个类
 <details>
 <summary>Code</summary>
 <pre><code class = "language-cpp">
 class mycompar{
 	 public:
		 bool operator()(const pair<int, int>& l, const pair<int, int>& r) {
		 return l.second > r.second;
		}
	};
	
	priority_queue<pair<int, int>, vector<pair<int, int>>, mycompar> q;
 </pre></code>
 </details>
 可以看出两者之间的比较都是第一个参数是否大于第二个参数 这个是小顶堆 即堆定元素最小 这样我们每次将最小的元素pop掉 到最后剩下的k个元素是前k大啦
 
 > 存疑：为什么是大于号是小顶堆 堆的内部是怎么实现的？？？


题解里面的定义方式，定义成结构体：
<details>
<summary>Code</summary>
<pre><code class = "language-cpp">
//大顶堆
struct myComparison
{
	bool operator () (fruit f1,fruit f2)
	{
		return f1.price < f2.price;
	}
};
//此时优先队列的定义应该如下
priority_queue<fruit,vector<fruit>,myComparison> q;
</pre></code>
</details>

 ##  常用函数
 top()    
 pop()  
 push()  
 emplace()  
 empty()  
 size()  
 

# LC496 下一个更大元素
<mark>单调栈 next greater element</mark>
