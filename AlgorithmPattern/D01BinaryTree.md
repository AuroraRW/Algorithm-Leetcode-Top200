# Binary Tree
Ref: https://www.youtube.com/watch?v=rm3KTnXSvus

## **1. Binary Tree Traversal**

Preorder--Inorder--Postorder  

### Recursion Template
<details>
  <summary>Solution</summary> 

![title](./images/PD01-00.png)
 
```python
def preorder_rec(root):
    if root is None:
        return
    visit(root)
    preorder_rec(root.left)
    preorder_rec(root.right)
    return

def inorder_rec(root):
    if root is None:
        return
    inorder_rec(root.left)
    visit(root)
    inorder_rec(root.right)
    return

def postorder_rec(root):
    if root is None:
        return
    postorder_rec(root.left)
    postorder_rec(root.right)
    visit(root)
    return
```  
</details>

### Iterative

#### Preorder Traversal 
[Leetcode No144](https://leetcode.com/problems/binary-tree-preorder-traversal/)  
 <details>
  <summary>Solution</summary>

![title](./images/PD01-01.png)

```Python
class Solution:
    def preorderTraversal(self, root: TreeNode) -> List[int]:
        preorder = []
        if root is None:
            return preorder
        
        s = [root]
        while len(s) > 0:
            node = s.pop()
            preorder.append(node.val)
            if node.right is not None:
                s.append(node.right)
            if node.left is not None:
                s.append(node.left)
        
        return preorder
```
</details>

#### Inorder Traversal
[Leetcode No94](https://leetcode.com/problems/binary-tree-inorder-traversal/)  

<details>
  <summary>Solution</summary>

![title](./images/PD01-02.png)
```Python
class Solution:
    def inorderTraversal(self, root: TreeNode) -> List[int]:
        s, inorder = [], []

        node = root
        while len(s) > 0 or node is not None:
            if node is not None:
                s.append(node)
                node = node.left
            else:
                node = s.pop()
                inorder.append(node.val)
                node = node.right
        return inorder
```
</details>

#### Postorder Traversal
[Leetcode No145](https://leetcode.com/problems/binary-tree-postorder-traversal/)  
<details>
  <summary>Solution</summary>

![title](./images/PD01-03.png) 
![title](./images/PD01-04.png)

```Python
class Solution:
    def postorderTraversal(self, root: TreeNode) -> List[int]:
        result=[]
        if root == None: 
            return result
        s=[root]
        
        while len(s) > 0:
            note = s.pop()
            # reverse: insert from the front
            result.insert(0, note.val)
            
            if note.left is not None:
                s.append(note.left)
            if note.right is not None:
                s.append(note.right)
        return result
```
</details>

<details>
  <summary>DFS(Deep-First_Search-form bottom to up)</summary>

```Python
class Solution:
    def preorderTraversal(self, root: TreeNode) -> List[int]:
        
        if root is None:
            return []
        
        left_result = self.preorderTraversal(root.left)
        right_result = self.preorderTraversal(root.right)
        
        return [root.val] + left_result + right_result
```

注意点：

> ???DFS 深度搜索（从上到下） 和分治法区别：前者一般将最终结果通过指针参数传入，后者一般递归返回结果最后合并
</details>

#### BFS-Level Order
[Leetcode No102](https://leetcode.com/problems/binary-tree-level-order-traversal/)
<details>
  <summary>Solution</summary>

```Python
class Solution:
    def levelOrder(self, root: TreeNode) -> List[List[int]]:
        
        levels = []
        if root is None:
            return levels
        
        bfs = collections.deque([root])
        
        while len(bfs) > 0:
            levels.append([])
            
            level_size = len(bfs)
            for _ in range(level_size):
                node = bfs.popleft()
                levels[-1].append(node.val)
                
                if node.left is not None:
                    bfs.append(node.left)
                if node.right is not None:
                    bfs.append(node.right)
        
        return levels
```
</details>

## **2. Divide Conquer**

先分别处理局部，再合并结果

适用场景

- 快速排序
- 归并排序
- 二叉树相关问题

分治法模板

- 递归返回条件
- 分段处理
- 合并结果

常见题目示例

#### maximum-depth-of-binary-tree 
[Leetcode No104](https://leetcode.com/problems/maximum-depth-of-binary-tree/)
<details>
  <summary>Solution</summary>

> 给定一个二叉树，找出其最大深度。

思路 1：分治法

```Python
class Solution:
    def maxDepth(self, root: TreeNode) -> int:
        
        if root is None:
            return 0
        
        return 1 + max(self.maxDepth(root.left), self.maxDepth(root.right))
```

思路 2：层序遍历

```Python
class Solution:
    def maxDepth(self, root: TreeNode) -> List[List[int]]:
        
        depth = 0
        if root is None:
            return depth
        
        bfs = collections.deque([root])
        
        while len(bfs) > 0:
            depth += 1
            level_size = len(bfs)
            for _ in range(level_size):
                node = bfs.popleft()
                if node.left is not None:
                    bfs.append(node.left)
                if node.right is not None:
                    bfs.append(node.right)
        
        return depth
```
</details>

#### balanced-binary-tree 
[Leetcode No110](https://leetcode.com/problems/balanced-binary-tree/)
<details>
  <summary>Solution</summary>

> 给定一个二叉树，判断它是否是高度平衡的二叉树。

思路 1：分治法，左边平衡 && 右边平衡 && 左右两边高度 <= 1，

```Python
class Solution:
    def isBalanced(self, root: TreeNode) -> bool:
 
        def depth(root):
            
            if root is None:
                return 0, True
            
            dl, bl = depth(root.left)
            dr, br = depth(root.right)
            
            return max(dl, dr) + 1, bl and br and abs(dl - dr) < 2
        
        _, out = depth(root)
        
        return out
```

思路 2：使用后序遍历实现分治法的迭代版本

```Python
class Solution:
    def isBalanced(self, root: TreeNode) -> bool:

        s = [[TreeNode(), -1, -1]]
        node, last = root, None
        while len(s) > 1 or node is not None:
            if node is not None:
                s.append([node, -1, -1])
                node = node.left
                if node is None:
                    s[-1][1] = 0
            else:
                peek = s[-1][0]
                if peek.right is not None and last != peek.right:
                    node = peek.right
                else:
                    if peek.right is None:
                        s[-1][2] = 0
                    last, dl, dr = s.pop()
                    if abs(dl - dr) > 1:
                        return False
                    d = max(dl, dr) + 1
                    if s[-1][1] == -1:
                        s[-1][1] = d
                    else:
                        s[-1][2] = d
        
        return True
```
</details>

#### binary-tree-maximum-path-sum 
[Leetcode No124](https://leetcode.com/problems/binary-tree-maximum-path-sum/)
<details>
  <summary>Solution</summary>

> 给定一个**非空**二叉树，返回其最大路径和。

思路：分治法。最大路径的可能情况：左子树的最大路径，右子树的最大路径，或通过根结点的最大路径。其中通过根结点的最大路径值等于以左子树根结点为端点的最大路径值加以右子树根结点为端点的最大路径值再加上根结点值，这里还要考虑有负值的情况即负值路径需要丢弃不取。

```Python
class Solution:
    def maxPathSum(self, root: TreeNode) -> int:
        
        self.maxPath = float('-inf')
        
        def largest_path_ends_at(node):
            if node is None:
                return float('-inf')
            
            e_l = largest_path_ends_at(node.left)
            e_r = largest_path_ends_at(node.right)
            
            self.maxPath = max(self.maxPath, node.val + max(0, e_l) + max(0, e_r), e_l, e_r)
            
            return node.val + max(e_l, e_r, 0)
        
        largest_path_ends_at(root)
        return self.maxPath
```
</details>

#### lowest-common-ancestor-of-a-binary-tree 
[Leetcode No236](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/)
<details>
  <summary>Solution</summary>

> 给定一个二叉树, 找到该树中两个指定节点的最近公共祖先。

思路：分治法，有左子树的公共祖先或者有右子树的公共祖先，就返回子树的祖先，否则返回根节点

```Python
class Solution:
    def lowestCommonAncestor(self, root: 'TreeNode', p: 'TreeNode', q: 'TreeNode') -> 'TreeNode':
        
        if root is None:
            return None
        
        if root == p or root == q:
            return root
        
        left = self.lowestCommonAncestor(root.left, p, q)
        right = self.lowestCommonAncestor(root.right, p, q)
        
        if left is not None and right is not None:
            return root
        elif left is not None:
            return left
        elif right is not None:
            return right
        else:
            return None
```
</details>

## **3. BFS 层次应用**

#### binary-tree-zigzag-level-order-traversal 
[Leetcode No103](https://leetcode.com/problems/binary-tree-zigzag-level-order-traversal/)
<details>
  <summary>Solution</summary>

> 给定一个二叉树，返回其节点值的锯齿形层次遍历。Z 字形遍历

思路：在BFS迭代模板上改用双端队列控制输出顺序

```Python
class Solution:
    def zigzagLevelOrder(self, root: TreeNode) -> List[List[int]]:
        
        levels = []
        if root is None:
            return levels
        
        s = collections.deque([root])

        start_from_left = True
        while len(s) > 0:
            levels.append([])
            level_size = len(s)
            
            if start_from_left:
                for _ in range(level_size):
                    node = s.popleft()
                    levels[-1].append(node.val)
                    if node.left is not None:
                        s.append(node.left)
                    if node.right is not None:
                        s.append(node.right)
            else:
                for _ in range(level_size):
                    node = s.pop()
                    levels[-1].append(node.val)
                    if node.right is not None:
                        s.appendleft(node.right)
                    if node.left is not None:
                        s.appendleft(node.left)
            
            start_from_left = not start_from_left
            
        
        return levels
```
</details>

## **4. 二叉搜索树应用**

#### validate-binary-search-tree 
[Leetcode No98](https://leetcode.com/problems/validate-binary-search-tree/)
<details>
  <summary>Solution</summary>

> 给定一个二叉树，判断其是否是一个有效的二叉搜索树。

思路 1：中序遍历后检查输出是否有序，缺点是如果不平衡无法提前返回结果， 代码略

思路 2：分治法，一个二叉树为合法的二叉搜索树当且仅当左右子树为合法二叉搜索树且根结点值大于右子树最小值小于左子树最大值。缺点是若不用迭代形式实现则无法提前返回，而迭代实现右比较复杂。

```Python
class Solution:
    def isValidBST(self, root: TreeNode) -> bool:
        
        if root is None: return True
        
        def valid_min_max(node):
            
            isValid = True
            if node.left is not None:
                l_isValid, l_min, l_max = valid_min_max(node.left)
                isValid = isValid and node.val > l_max
            else:
                l_isValid, l_min = True, node.val

            if node.right is not None:
                r_isValid, r_min, r_max = valid_min_max(node.right)
                isValid = isValid and node.val < r_min
            else:
                r_isValid, r_max = True, node.val

                
            return l_isValid and r_isValid and isValid, l_min, r_max
        
        return valid_min_max(root)[0]
```

思路 3：利用二叉搜索树的性质，根结点为左子树的右边界，右子树的左边界，使用先序遍历自顶向下更新左右子树的边界并检查是否合法，迭代版本实现简单且可以提前返回结果。

```Python
class Solution:
    def isValidBST(self, root: TreeNode) -> bool:
        
        if root is None:
            return True
        
        s = [(root, float('-inf'), float('inf'))]
        while len(s) > 0:
            node, low, up = s.pop()
            if node.left is not None:
                if node.left.val <= low or node.left.val >= node.val:
                    return False
                s.append((node.left, low, node.val))
            if node.right is not None:
                if node.right.val <= node.val or node.right.val >= up:
                    return False
                s.append((node.right, node.val, up))
        return True
```
</details>

#### insert-into-a-binary-search-tree 
[Leetcode No701](https://leetcode.com/problems/insert-into-a-binary-search-tree/)
<details>
  <summary>Solution</summary>

> 给定二叉搜索树（BST）的根节点和要插入树中的值，将值插入二叉搜索树。 返回插入后二叉搜索树的根节点。

思路：如果只是为了完成任务则找到最后一个叶子节点满足插入条件即可。但此题深挖可以涉及到如何插入并维持平衡二叉搜索树的问题，并不适合初学者。

```Python
class Solution:
    def insertIntoBST(self, root: TreeNode, val: int) -> TreeNode:
        
        if root is None:
            return TreeNode(val)
        
        node = root
        while True:
            if val > node.val:
                if node.right is None:
                    node.right = TreeNode(val)
                    return root
                else:
                    node = node.right
            else:
                if node.left is None:
                    node.left = TreeNode(val)
                    return root
                else:
                    node = node.left
```
</details>

## **5. Summary**

掌握二叉树递归与非递归遍历  
理解 DFS 前序遍历与分治法  
理解 BFS 层次遍历