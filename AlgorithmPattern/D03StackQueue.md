# Stack and Queue

## **1. Introduction**

栈的特点是后入先出

![image.png](https://img.fuiboom.com/img/stack.png)

根据这个特点可以临时保存一些数据，之后用到依次再弹出来，常用于 DFS 深度搜索

队列一般常用于 BFS 广度搜索，类似一层一层的搜索

## **2. Stack**

#### min-stack
[Leetcode No155](https://leetcode.com/problems/min-stack/)
<details>
  <summary>Solution</summary> 

> 设计一个支持 push，pop，top 操作，并能在常数时间内检索到最小元素的栈。

思路：用两个栈实现或插入元组实现，保证当前最小值在栈顶即可

```Python
class MinStack:

    def __init__(self):
        self.stack = []

    def push(self, x: int) -> None:
        if len(self.stack) > 0:
            self.stack.append((x, min(x, self.stack[-1][1])))
        else:
            self.stack.append((x, x))

    def pop(self) -> int:
        return self.stack.pop()[0]

    def top(self) -> int:
        return self.stack[-1][0]

    def getMin(self) -> int:
        return self.stack[-1][1]
```
</details>

#### evaluate-reverse-polish-notation
[Leetcode No150](https://leetcode.com/problems/evaluate-reverse-polish-notation/)
<details>
  <summary>Solution</summary> 

> **波兰表达式计算** > **输入:** ["2", "1", "+", "3", "*"] > **输出:** 9
> **解释:** ((2 + 1) \* 3) = 9

思路：通过栈保存原来的元素，遇到表达式弹出运算，再推入结果，重复这个过程

```Python
class Solution:
    def evalRPN(self, tokens: List[str]) -> int:
        
        def comp(or1, op, or2):
            if op == '+':
                return or1 + or2
            
            if op == '-':
                return or1 - or2
            
            if op == '*':
                return or1 * or2
            
            if op == '/':
                abs_result = abs(or1) // abs(or2)
                return abs_result if or1 * or2 > 0 else -abs_result
        
        stack = []
        
        for token in tokens:
            if token in ['+', '-', '*', '/']:
                or2 = stack.pop()
                or1 = stack.pop()
                stack.append(comp(or1, token, or2))
            else:
                stack.append(int(token))
        
        return stack[0]
```
</details>

#### decode-string
[Leetcode No394](https://leetcode.com/problems/decode-string/)

<details>
  <summary>Solution</summary> 

> 给定一个经过编码的字符串，返回它解码后的字符串。
> s = "3[a]2[bc]", 返回 "aaabcbc".
> s = "3[a2[c]]", 返回 "accaccacc".
> s = "2[abc]3[cd]ef", 返回 "abcabccdcdcdef".

思路：通过两个栈进行操作，一个用于存数，另一个用来存字符串

```Python
class Solution:
    def decodeString(self, s: str) -> str:
        
        stack_str = ['']
        stack_num = []
        
        num = 0
        for c in s:
            if c >= '0' and c <= '9':
                num = num * 10 + int(c)
            elif c == '[':
                stack_num.append(num)
                stack_str.append('')
                num = 0
            elif c == ']':
                cur_str = stack_str.pop()
                stack_str[-1] += cur_str * stack_num.pop()
            else:
                stack_str[-1] += c
        
        return stack_str[0]
```

利用栈进行 DFS 迭代搜索模板

```Python
def DFS(vertex):
    visited = set([])
    stack_dfs = [vertex]

    while len(stack_dfs) > 0:
        v = stack_dfs.pop()
        if v is not in visited:
            visit(v)
            visited.add(v)
            for n in v.neighbors:
                stack_dfs.append(n)

    return
```
</details>

#### binary-tree-inorder-traversal
[Leetcode No94](https://leetcode.com/problems/binary-tree-inorder-traversal/)  

<details>
  <summary>Solution</summary> 

> 给定一个二叉树，返回它的*中序*遍历。

- [reference](https://en.wikipedia.org/wiki/Tree_traversal#In-order)

```Python
class Solution:
    def inorderTraversal(self, root: TreeNode) -> List[int]:
        
        stack, inorder = [], []
        node = root
 
        while len(stack) > 0 or node is not None:
            if node is not None: 
                stack.append(node)
                node = node.left
            else:
                node = stack.pop()
                inorder.append(node.val)
                node = node.right
        
        return inorder
```
</details>

#### clone-graph
[Leetcode No133](https://leetcode.com/problems/clone-graph/)
<details>
  <summary>Solution</summary> 

> 给你无向连通图中一个节点的引用，请你返回该图的深拷贝（克隆）。

- BFS

```Python
class Solution:
    def cloneGraph(self, start: 'Node') -> 'Node':
        
        if start is None:
            return None
        
        visited = {start: Node(start.val, [])}
        bfs = collections.deque([start])
        
        while len(bfs) > 0:
            curr = bfs.popleft()
            curr_copy = visited[curr]
            for n in curr.neighbors:
                if n not in visited:
                    visited[n] = Node(n.val, [])
                    bfs.append(n)
                curr_copy.neighbors.append(visited[n])
        
        return visited[start]
```

- DFS iterative

```Python
class Solution:
    def cloneGraph(self, start: 'Node') -> 'Node':
        
        if start is None:
            return None
        
        if not start.neighbors:
            return Node(start.val)
        
        visited = {start: Node(start.val, [])}
        dfs = [start]
        
        while len(dfs) > 0:
            peek = dfs[-1]
            peek_copy = visited[peek]
            if len(peek_copy.neighbors) == 0:
                for n in peek.neighbors:
                    if n not in visited:
                        visited[n] = Node(n.val, [])
                        dfs.append(n)
                    peek_copy.neighbors.append(visited[n])
            else:
                dfs.pop()
        
        return visited[start]
```
</details>

#### number-of-islands
[Leetcode No200](https://leetcode.com/problems/number-of-islands/)
<details>
  <summary>Solution</summary> 

> 给定一个由  '1'（陆地）和 '0'（水）组成的的二维网格，计算岛屿的数量。一个岛被水包围，并且它是通过水平方向或垂直方向上相邻的陆地连接而成的。你可以假设网格的四个边均被水包围。

High-level problem: number of connected component of graph

思路：通过深度搜索遍历可能性（注意标记已访问元素）

```Python
class Solution:
    def numIslands(self, grid: List[List[str]]) -> int:
        
        if not grid or not grid[0]:
            return 0
        
        m, n = len(grid), len(grid[0])

        def dfs_iter(i, j):
            dfs = []
            dfs.append((i, j))
            while len(dfs) > 0:
                i, j = dfs.pop()
                if grid[i][j] == '1':
                    grid[i][j] = '0'
                    if i - 1 >= 0:
                        dfs.append((i - 1, j))
                    if j - 1 >= 0:
                        dfs.append((i, j - 1))
                    if i + 1 < m:
                        dfs.append((i + 1, j))
                    if j + 1 < n:
                        dfs.append((i, j + 1))
            return
        
        num_island = 0
        for i in range(m):
            for j in range(n):
                if grid[i][j] == '1':
                    num_island += 1
                    dfs_iter(i, j)
        
        return num_island
```
</details>

#### largest-rectangle-in-histogram
[Leetcode No84](https://leetcode.com/problems/largest-rectangle-in-histogram/)
<details>
  <summary>Solution</summary> 

> 给定 _n_ 个非负整数，用来表示柱状图中各个柱子的高度。每个柱子彼此相邻，且宽度为 1 。
> 求在该柱状图中，能够勾勒出来的矩形的最大面积。

思路 1：蛮力法，比较每个以 i 开始 j 结束的最大矩形，A(i, j) = (j - i + 1) * min_height(i, j)，时间复杂度 O(n^2) 无法AC

```Python
class Solution:
    def largestRectangleArea(self, heights: List[int]) -> int:
        
        max_area = 0
        
        n = len(heights)
        for i in range(n):
            min_height = heights[i]
            for j in range(i, n):
                min_height = min(min_height, heights[j])
                max_area = max(max_area, min_height * (j - i + 1))
        
        return max_area
```

思路 2: 设 A(i, j) 为区间 [i, j) 内最大矩形的面积，k 为 [i, j) 内最矮 bar 的坐标，则 A(i, j) = max((j - i) * heights[k], A(i, k), A(k+1, j)), 使用分治法进行求解。时间复杂度 O(nlogn)，其中使用简单遍历求最小值无法 AC (最坏情况退化到 O(n^2))，使用线段树优化后勉强AC

```Python
class Solution:
    def largestRectangleArea(self, heights: List[int]) -> int:
        
        n = len(heights)
        
        seg_tree = [None] * n
        seg_tree.extend(list(zip(heights, range(n))))
        for i in range(n - 1, 0, -1):
            seg_tree[i] = min(seg_tree[2 * i], seg_tree[2 * i + 1], key=lambda x: x[0])
        
        def _min(i, j):
            min_ = (heights[i], i)
            i += n
            j += n
            while i < j:
                if i % 2 == 1:
                    min_ = min(min_, seg_tree[i], key=lambda x: x[0])
                    i += 1
                if j % 2 == 1:
                    j -= 1
                    min_ = min(min_, seg_tree[j], key=lambda x: x[0])
                i //= 2
                j //= 2
            
            return min_
        
        def LRA(i, j):
            if i == j:
                return 0
            min_k, k = _min(i, j)
            return max(min_k * (j - i), LRA(k + 1, j), LRA(i, k))
        
        return LRA(0, n)
```

思路 3：包含当前 bar 最大矩形的边界为左边第一个高度小于当前高度的 bar 和右边第一个高度小于当前高度的 bar。

```Python
class Solution:
    def largestRectangleArea(self, heights: List[int]) -> int:
        
        n = len(heights)
        
        stack = [-1]
        max_area = 0
        
        for i in range(n):
            while len(stack) > 1 and heights[stack[-1]] > heights[i]:
                h = stack.pop()
                max_area = max(max_area, heights[h] * (i - stack[-1] - 1))
            stack.append(i)
        
        while len(stack) > 1:
            h = stack.pop()
            max_area = max(max_area, heights[h] * (n - stack[-1] - 1))
        
        return max_area
```
</details>

## **3.Queue 队列**

常用于 BFS 宽度优先搜索

#### implement-queue-using-stacks
[Leetcode No232](https://leetcode.com/problems/implement-queue-using-stacks/)
<details>
  <summary>Solution</summary> 

> 使用栈实现队列

```Python
class MyQueue:

    def __init__(self):
        self.cache = []
        self.out = []

    def push(self, x: int) -> None:
        """
        Push element x to the back of queue.
        """
        self.cache.append(x)

    def pop(self) -> int:
        """
        Removes the element from in front of queue and returns that element.
        """
        if len(self.out) == 0:
            while len(self.cache) > 0:
                self.out.append(self.cache.pop())

        return self.out.pop() 

    def peek(self) -> int:
        """
        Get the front element.
        """
        if len(self.out) > 0:
            return self.out[-1]
        else:
            return self.cache[0]

    def empty(self) -> bool:
        """
        Returns whether the queue is empty.
        """
        return len(self.cache) == 0 and len(self.out) == 0
```
</details>

#### binary-tree-level-order-traversal
[Leetcode No102](https://leetcode.com/problems/binary-tree-level-order-traversal/)
<details>
  <summary>Solution</summary> 

> 二叉树的层序遍历

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

#### 01-matrix
[Leetcode No542](https://leetcode.com/problems/01-matrix/)
<details>
  <summary>Solution</summary> 

> 给定一个由 0 和 1 组成的矩阵，找出每个元素到最近的 0 的距离。
> 两个相邻元素间的距离为 1

思路 1: 从 0 开始 BFS, 遇到距离最小值需要更新的则更新后重新入队更新后续结点

```Python
class Solution:
    def updateMatrix(self, matrix: List[List[int]]) -> List[List[int]]:
        
        if len(matrix) == 0 or len(matrix[0]) == 0:
            return matrix
        
        m, n = len(matrix), len(matrix[0])
        dist = [[float('inf')] * n for _ in range(m)]
        
        bfs = collections.deque([])
        for i in range(m):
            for j in range(n):
                if matrix[i][j] == 0:
                    dist[i][j] = 0
                    bfs.append((i, j))

        neighbors = [(-1, 0), (1, 0), (0, -1), (0, 1)]
        while len(bfs) > 0:
            i, j = bfs.popleft()
            for dn_i, dn_j in neighbors:
                n_i, n_j = i + dn_i, j + dn_j
                if n_i >= 0 and n_i < m and n_j >= 0 and n_j < n:
                    if dist[n_i][n_j] > dist[i][j] + 1:
                        dist[n_i][n_j] = dist[i][j] + 1
                        bfs.append((n_i, n_j))
        
        return dist        
```

思路 2: 2-pass DP，dist(i, j) = max{dist(i - 1, j), dist(i + 1, j), dist(i, j - 1), dist(i, j + 1)} + 1

```Python
class Solution:
    def updateMatrix(self, matrix: List[List[int]]) -> List[List[int]]:
        
        if len(matrix) == 0 or len(matrix[0]) == 0:
            return matrix
        
        m, n = len(matrix), len(matrix[0])
        
        dist = [[float('inf')] * n for _ in range(m)]
        
        for i in range(m):
            for j in range(n):
                if matrix[i][j] == 1:
                    if i - 1 >= 0:
                        dist[i][j] = min(dist[i - 1][j] + 1, dist[i][j])
                    if j - 1 >= 0:
                        dist[i][j] = min(dist[i][j - 1] + 1, dist[i][j])
                else:
                    dist[i][j] = 0
        
        for i in range(-1, -m - 1, -1):
            for j in range(-1, -n - 1, -1):
                if matrix[i][j] == 1:
                    if i + 1 < 0:
                        dist[i][j] = min(dist[i + 1][j] + 1, dist[i][j])
                    if j + 1 < 0:
                        dist[i][j] = min(dist[i][j + 1] + 1, dist[i][j])
        
        return dist
```
</details>

## **4.补充：单调队列**

#### sliding-window-maximum
[Leetcode No239](https://leetcode.com/problems/sliding-window-maximum/)
<details>
  <summary>Solution</summary> 

> 求滑动窗口中的最大元素

```Python
class Solution:
    def maxSlidingWindow(self, nums: List[int], k: int) -> List[int]:
        
        N = len(nums)
        if N * k == 0:
            return []
        
        if k == 1:
            return nums[:]
        
        # define a max queue
        maxQ = collections.deque()
        
        def push(i):
            if maxQ and maxQ[0] == i - k:
                maxQ.popleft()
            
            while maxQ and nums[maxQ[-1]] < nums[i]:
                maxQ.pop()
            
            maxQ.append(i)
            return
        
        result = []
        for i in range(N):
            push(i)
            if i >= k - 1:
                result.append(nums[maxQ[0]])
        
        return result
```
</details>

## **5. Summary**

- 熟悉栈的使用场景
  - 后出先出，保存临时值
  - 利用栈 DFS 深度搜索
- 熟悉队列的使用场景
  - 利用队列 BFS 广度搜索

