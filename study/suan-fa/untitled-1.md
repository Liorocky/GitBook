# 297. 二叉树的序列化与反序列化

> 原题：[297. 二叉树的序列化与反序列化](https://leetcode-cn.com/problems/serialize-and-deserialize-binary-tree/) 参考文章：[297. Java bfs层序遍历](https://leetcode-cn.com/problems/serialize-and-deserialize-binary-tree/solution/297-java-bfsceng-xu-bian-li-by-alexliao-lrj/) 参考作者：[Alex](https://leetcode-cn.com/u/alexliao-lrj/)

## 题目

![image-20200616103357842](https://gitee.com/WarMj/image-cdn/raw/master/img/20200616163620.png)

## 思路分析

针对参考文章中的代码进行思路分析、方法总结。

### serialize\(\) 序列化

#### 分析

1. 使用 **前序遍历** 每个节点，如果不为null，则将值放入字符串中，如果为null，则放入"null"
2. 题目有说明不让用类成员、变量，所以不能用递归处理遍历
3. 由于前序遍历正好符合先进先出的原则，所以我们考虑用队列实现前序遍历
4. **将当前节点加入队列，判断节点是否为空，若不为空则当前节点出队列并将值拼接到字符串后，然后将当前节点的左右子节点入队列**
5. **若当前节点为空，则没有子节点，直接拼接"null"字符串** 
6. 重复4、5步骤，直到队列为空
7. 对字符串进行处理后将其返回

#### 代码

```java
public String serialize(TreeNode root) {
        StringBuilder res = new StringBuilder("["); // 拼接的字符串

        Queue<TreeNode> queue = new LinkedList(); // 保存节点队列
        queue.add(root); // 先从根节点开始

        while (!queue.isEmpty()) {
            TreeNode cur = queue.remove(); // 出队首节点
            if (cur == null) {
                /*
                如果节点为空，说明没有子节点，直接拼接null到字符串中
                 */
                res.append("null,");
            } else {
                /*
                节点不为空，则将值加入字符串中
                并且将其左右节点加入队列中
                 */
                res.append(cur.val + ",");
                queue.add(cur.left);
                queue.add(cur.right);
            }
        }

        res.setLength(res.length() - 1); // 此时res中的值最后会多一个逗号，所以需要去掉最后一个字符
        res.append("]");
        return res.toString();
    }
```

### deserialize\(\) 反序列化

#### 分析

1. 将字符串截取并分割，返回字符串数组，数组中的每一项都是一个节点值
2. 写一个方法，传节点值，返回该节点值的节点，如果值为"null"，则返回null
3. 此时我们可以将数组中的每一项看成一个节点
4. 问题变成了如何将数组中的每一个节点按照原先的顺序连接起来
5. 思路依然是用队列存储节点
6. 遍历数组，如果某一个父节点的左右子节点都已找到，那我们就寻找下一个父节点
7. 所以我们的队列应该存储**尚未找到子节点的父节点**
8. 那如何判断**左右子节点都已找到**呢？
   1. 我们可以用一个变量 `isLeft` 来存储是否为左节点
   2. `isLeft`默认为`true`
   3. 让父节点连接左节点，然后对`isLeft`取反，此时`isLeft`为`false`
   4. 然后连接右节点，再将`isLeft`取反，此时`isLeft`为`true`
   5. 如果`isLeft`又变回`true`，说明找到了左右子节点
   6. 此时父节点应该变为队列的队首
9. 当数组遍历完成之后，返回数组第一个节点

#### 代码

```java
public TreeNode deserialize(String data) {
        // 将字符串截取并分割，返回字符串数组，数组中的每一项都是一个节点值
        String[] arr = data.substring(1, data.length() - 1).split(",");

        Queue<TreeNode> queue = new LinkedList<>(); // 存储尚未找到子节点的父节点
        TreeNode root = getNode(arr[0]); // 保存数组第一个节点，最终返回它
        TreeNode parent = root; // 临时的父节点变量
        boolean isLeft = true; // 判断是否已经找到了左右子节点

        for (int i = 1; i < arr.length; i++) {
            TreeNode cur = getNode(arr[i]); // 遍历数组

            if (isLeft) {
                // 说明cur是parent的左节点
                parent.left = cur;
            } else {
                // 说明cur是parent的右节点
                parent.right = cur;
            }

            if (cur != null) {
                // 说明cur是一个父节点，但其左右子节点可能为空
                queue.add(cur);
            }

            isLeft = !isLeft; // 取反

            if (isLeft) {
                /*
                 如果此时为真，说明上面的cur是右节点，左右节点已经找到
                 队首出列，保存为新的父节点
                 */
                parent = queue.poll();
            }
        }

        return root;
    }

    /**
     * 返回以val为值的节点
     * @param val 如果val为"null"，则返回null
     * @return 返回以val为值的节点
     */
    private TreeNode getNode(String val) {
        if (val.equals("null")) {
            return null;
        }

        return new TreeNode(Integer.valueOf(val));
    }
```

