# Trees

- [Maximum Depth of Binary Tree](#maximum-depth-of-binary-tree)
- [Symmetric Tree](#symmetric-tree)
- [](#)
- [](#)
- [](#)
- [](#)
- [](#)
- [](#)
- [](#)
- [](#)
- [](#)
- [](#)
- [](#)
- [Construct Binary Tree from Preorder and Inorder Traversal](#construct-binary-tree-from-preorder-and-inorder-traversal)

## Maximum Depth of Binary Tree
https://leetcode.com/problems/maximum-depth-of-binary-tree/
```java
public int maxDepth(TreeNode root) {
    if (root == null) 
      return 0;
    return 1 + Math.max(maxDepth(root.left), maxDepth(root.right));
}
```

## Construct Binary Tree from Preorder and Inorder Traversal
https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/

For example, given
```
preorder = [3,9,20,15,7]
inorder = [9,3,15,20,7]
```
Return the following binary tree:
```
    3
   / \
  9  20
    /  \
   15   7
```   
Algorithm Inorder(tree)
   1. Traverse the left subtree, i.e., call Inorder(left-subtree)
   2. Visit the root.
   3. Traverse the right subtree, i.e., call Inorder(right-subtree)
   
Algorithm Preorder(tree)
   1. Visit the root.
   2. Traverse the left subtree, i.e., call Preorder(left-subtree)
   3. Traverse the right subtree, i.e., call Preorder(right-subtree) 
   
Algorithm Postorder(tree)
   1. Traverse the left subtree, i.e., call Postorder(left-subtree)
   2. Traverse the right subtree, i.e., call Postorder(right-subtree)
   3. Visit the root.

### Решение
Идея: взять корень дерева (`preorder[0]`) и запустить для него рекурсивную процедуру построения дерева. 

Суть её в том, что по индексу текущего корня дерева можно разбить массив `inorder` на левое и правое поддерево.

Далее рекурсивно вызываем эту процедуру для двух поддеревьев.

Все локальные корни дерева берутся из `preorder`, по `inorder` дерево разветвляется на левое и правое.

`currRoot + 1` - индекс левого ребенка в  массиве `preorder`. 

То есть в `preorder` всегда левый ребенок находится справа от родителя на 1 позицию (при условии наличия левого ребенка, а он есть тогда, 
когда левый интервал непустой, т.е. есть левое поддерево)

`currRoot + inIndex - inStart + 1` - индекс правого ребенка в  массиве `preorder`. Такая формула объясняется тем, что все индексы `currRoot < i <= currRoot + inIndex - inStart`
принадлежат левому поддереву, а нам нужен первый элемент правого, так что добавим еще 1.

 ```java
    public int searchElemIdxInArray(int[] array, int elem) {
        int N = array.length;
        for (int i = 0; i < N; i++) {
            if (array[i] == elem)
                return i;
        }
        return -1;
    }
    

    public TreeNode buildTree(int[] preorder, int[] inorder) {
        return helper(0, 0, inorder.length - 1, preorder, inorder);
    }

    public TreeNode helper(int currRoot, int inStart, int inEnd, int[] preorder, int[] inorder) {
        if (currRoot > preorder.length - 1 || inStart > inEnd)
            return null;
        
        TreeNode root = new TreeNode(preorder[currRoot]);
        int inIndex = searchElemIdxInArray(inorder, root.val);
        
        root.left = helper(currRoot + 1, inStart, inIndex - 1, preorder, inorder);
        root.right = helper(currRoot + inIndex - inStart + 1, inIndex + 1, inEnd, preorder, inorder);
        
        return root;
    }
 ```

## Symmetric Tree

https://leetcode.com/problems/symmetric-tree

### Рекурсивное решение

```java
class Solution {
    public boolean compare(TreeNode a, TreeNode b) {
        if (a == null && b == null) return true;
        if (a == null || b == null) return false;
       
        return (a.val == b.val)
            && compare(a.left, b.right)
            && compare(a.right, b.left);
    }
    
    public boolean isSymmetric(TreeNode root) {
        if (root == null)
            return true;
        return compare(root.left, root.right);
    }
}
```

### Итеративное решение

В очередь добавляем элементы дерева, которые будем обходить. В цикле извлекаем 2 элемента очереди - это симметричные узлы дерева. Чтобы очередь имела такую структуру, кладем на каждой итерации новые 4 элемента: a.left, b.right, a.right, b.left

```java
class Solution {
    public boolean isSymmetric(TreeNode root) {
        if (root == null)
            return true;
        
        Queue<TreeNode> q = new LinkedList<>();
        q.add(root);
        q.add(root);
        
        while (!q.isEmpty()) {
            TreeNode a = q.poll();
            TreeNode b = q.poll();
            
            if (a == null && b == null) 
                continue;
            if (a == null || b == null) 
                return false;
            if (a.val != b.val) 
                return false;
            
            q.add(a.left);
            q.add(b.right);
            q.add(a.right);
            q.add(b.left);
        }
        
        return true;   
    }
}
```
