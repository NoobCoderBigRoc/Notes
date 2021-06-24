# 二叉树
---
树 是一种经常用到的数据结构，用来模拟具有树状结构性质的数据集合。

树里的每一个节点有一个值和一个包含所有子节点的列表。从图的观点来看，树也可视为一个拥有N 个节点和N-1 条边的一个有向无环图。

二叉树是一种更为典型的树状结构。如它名字所描述的那样，二叉树是每个节点最多有两个子树的树结构，通常子树被称作“左子树”和“右子树”。
## 各语言定义一颗二叉树Node
Python3
```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right
```
 JAVA
```Java
public class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;
    TreeNode() {}
    TreeNode(int val) { this.val = val; }
    TreeNode(int val, TreeNode left, TreeNode right) {
        this.val = val;
        this.left = left;
        this.right = right;
    }
}
```
C++
```C++
struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode() : val(0), left(nullptr), right(nullptr) {}
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
    TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
};
```
C
```C
struct TreeNode {
    int val;
    struct TreeNode *left;
    struct TreeNode *right;
};
```

Javascript
```Javascript
function TreeNode(val, left, right) {
    this.val = (val===undefined ? 0 : val)
    this.left = (left===undefined ? null : left)
    this.right = (right===undefined ? null : right)
} 
```

Golang
```Go
type TreeNode struct {
    Val int
    Left *TreeNode
    right *TreeNode
}
```
## 树的遍历
1. **前序遍历**
前序遍历首先访问根节点，然后遍历左子树，最后遍历右子树

### 方法一：递归
**思路**
1. 按照访问根节点——左子树——右子树的方式遍历这棵树
2. 访问左子树或者右子树的时候，我们按照同样的方式遍历，直到遍历完整棵


**算法**
1. 定义 preorder(root) 表示当前遍历到 root 节点的答案
2. 首先将 root 节点的值加入答案
3. 递归调用 preorder(root.left) 来遍历 root 节点的左子树
4. 最后递归调用 preorder(root.right) 来遍历 root 节点的右子树
**递归终止的条件为碰到空节点**

**复杂度分析**
- 时间复杂度
  时间复杂度：$O(n)$，其中 $n$ 是二叉树的节点数。每一个节点恰好被遍历一次。
- 空间复杂度
  $O(n)$，为递归过程中栈的开销，平均情况下为 $O(\log n)$，最坏情况下树呈现链状，为 $O(n)$。


Python实现
```Python
class Solution: 
    def preorderTraversal(self, root:TreeNode) -> List[int]:
        def preorder(root:TreeNode): 
            if not root:
                return
            res.append(root.val)
            preorder(root.left)
            preorder(root.right)

        res = list()
        preorder(root)
        return res
```

Java实现
```Java
class Solution{
    public List<int> preorderTraversal(TreeNode root){
        List<Integer> res = new ArrayList<Integer>();
        preorder(root, res);
        return res;
    }

    public void preorder(TreeNode root, List<Integer> res){
        if(root.val == null){
            return;
        }
        res.add(root.val);
        res.add(root.left, res);
        res.add(root.right, res);
    }
}
```
Golang实现
```Go
func preorderTraversal(root *TreeNode) (vals []int){
    var preorder func(*TreeNode)
    preorder = func(node *TreeNode){
        if node == nil{
            return
        }
        vals = append(vals, node.Val)
        preorder(node.left)
        preorder(node.right)
    }
    preorder(root)
    return
}
```
### 方法二：迭代
**思路算法**
与递归等价
**区别**
递归的时候隐式地维护了一个栈，而我们在迭代的时候需要显式地将这个栈模拟出来

**复杂度分析**
- 时间复杂度
  时间复杂度：$O(n)$，其中 $n$ 是二叉树的节点数。每一个节点恰好被遍历一次。
- 空间复杂度
  $O(n)$，为递归过程中栈的开销为迭代过程中显式栈的开销，平均情况下为 $O(\log n)$，最坏情况下树呈现链状，为 $O(n)$。

Python3实现
```Python
class Solution:
    def preorderTraversal(self, root:TreeNode) -> List[int];
        res = list()
        if not root:
            return res;
        
        stack = []
        node = root
        while stack or node:
            while node:
                res.append(node.val)
                stack.append(node)
                node = node.left
            node = stack.pop()
            node = node.right
        return res
```

Java实现
```Java
class Solution{
    public List<Integer> preorderTraversal(TreeNode root){
        List<Integer> res = new ArrayList();
        if(root == null){
            return res;
        }

        Deque<TreeNode> stack = new LinkedList<TreeNode>();
        TreeNode node = root;
        while (!stack.isEmpty() || node != null){
            while (node != null){
                res.add(node.val)
                stack.push(node);
                node = node.left;
            }
            node = stack.pop();
            node = node.right;
        }
        return res;
    }
}
```

Golang实现
```Go
func preorderTraversal (root *TreeNode) (vals []int){
    stack := []*TreeNode{}
    node := root
    for node != nil || len(stack) > 0 {
        for node != nil {
            vals = append(vals, node.Val)
            stack = append(stack, node)
            node = node.left
        }
        node = stack[len(stack)-1].Right
        stack = stack[:len(stack)-1]
    }
    return
}
```

### 方法三：Morris遍历
**思路算法**
Morris 遍历的核心思想是利用树的大量空闲指针，实现空间开销的极限缩减
  1. 新建临时节点，令该节点为 root；
  2. 如果当前节点的左子节点为空，将当前节点加入答案，并遍历当前节点的右子节点；
  3. 如果当前节点的左子节点不为空，在当前节点的左子树中找到当前节点在中序遍历下的前驱节点：
   - 如果前驱节点的右子节点为空，将前驱节点的右子节点设置为当前节点。然后将当前节点加入答案，并将前驱节点的右子节点更新为当前节点。当前节点更新为当前节点的左子节点。
   - 如果前驱节点的右子节点为当前节点，将它的右子节点重新设为空。当前节点更新为当前节点的右子节点。
  4. 重复步骤 2 和步骤 3，直到遍历结束。

**复杂度分析**
- 时间复杂度
  时间复杂度：$O(n)$，其中 $n$ 是二叉树的节点数。每一个节点恰好被遍历一次。没有左子树的节点只被访问一次，有左子树的节点被访问两次。
- 空间复杂度
  $O(1)$。只操作已经存在的指针（树的空闲指针），因此只需要常数的额外空间。

Python3实现
```Python
class Solution:
    def preorderTraversal(self, root:TreeNode) -> List[int]:
        res = list()
        if not root:
            return res
        
        p1 = root
        while p1:
            p2 = p1.left
            if p2:
                while p2.right and p2.right != p1:
                    p2 = p2.right
                if not p2.right:
                    res.append(p1.val)
                    p2.right = p1
                    p1 = p1.left
                    continue
                else:
                    p2.right = None
            else:
                res.append(p1.val)
            p1 = p1.right

        return res
```

Java实现
```Java
class Solution{
    public List<Integer> preorderTraversal(TreeNode root){
        List<Integer> res = new ArrayList<Integer>();
        if (root == null) {
            return res;
        }
    }

    TreeNode p1 = root, p2 = null;

    while (p1 != null) {
        p2 = p1.left;
        if (p2 != null) {
            while (p1.right != null && p2.right != p1) {
                p2 = p2.right;
            }
            if (p2.right == null) {
                res.add(p1.val);
                p2.right = p1;
                p1 = p1.left;
                continue;
            } else {
                p2.right = null;
            }
        } else {
            res.add(p1.val);
        }
        p1 = p1.right;
        return res;
    }
}
```
