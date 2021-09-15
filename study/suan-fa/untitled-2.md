# 剑指 Offer 28. 对称的二叉树

> 参考文章：[面试题28. 对称的二叉树（递归，清晰图解）](https://leetcode-cn.com/problems/dui-cheng-de-er-cha-shu-lcof/solution/mian-shi-ti-28-dui-cheng-de-er-cha-shu-di-gui-qing/) 参考作者：[Krahets](https://leetcode-cn.com/u/jyd/)

## 算法流程

`isSymmetric(root)` ：

* 特例处理： 若根节点 root 为空，则直接返回 true 。
* 返回值： 即 `recur(root.left, root.right)`;

`recur(L, R)`：

* 终止条件：
  * 当 L 和 R 同时越过叶节点： 此树从顶至底的节点都对称，因此返回 true ；
  * 当 L 或 R 中只有一个越过叶节点： 此树不对称，因此返回 false ；
  * 当节点 L 值 不等于节点 R 值： 此树不对称，因此返回 false ；
* 递推工作：
  * 判断两节点 L.left 和 R.right 是否对称，即 `recur(L.left, R.right)` ；
  * 判断两节点 L.right 和 R.left 是否对称，即 `recur(L.right, R.left)` ；
* 返回值： 两对节点都对称时，才是对称树，因此用与逻辑符 && 连接。

## 代码

```java
class Solution {
    public boolean isSymmetric(TreeNode root) {
        return root == null ? true : recur(root.left, root.right);
    }

    private boolean recur(TreeNode l, TreeNode r) {
        if (l == null && r == null) return true;
        if (l == null || r == null || l.val != r.val) return false;
        return recur(l.left, r.right) && recur(l.right, r.left);
    }
}
```

## 总结

在准备使用递归时，要分析递归的**终止条件、递推工作、返回值**，然后根据分析出来的结果再生成代码。

