# 26. 删除排序数组中的重复项

> 原题：[26. 删除排序数组中的重复项](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-array/)

![26. &#x5220;&#x9664;&#x6392;&#x5E8F;&#x6570;&#x7EC4;&#x4E2D;&#x7684;&#x91CD;&#x590D;&#x9879;](https://gitee.com/WarMj/image-cdn/raw/master/img/20200617180448.png)

## 分析

因为数组中可能有重复元素，而我们需要跳过这些重复元素，就需要**一个指针**向后移动；如果找到了不重复的元素，就得将这个元素赋值到前面的新数组中，所以还需要**一个指针**。

所以我们采用**快慢双指针**来解决题目。

很明显，向后跳过重复元素的指针是快指针，我们用`cur`来表示。 那慢指针我们可以用遍历旧数组的`i`来表示。

我们来看这一个测试用例：

> 0 0 1 1 1 2 2 3 3 4

从索引1开始，判断是否与前一个元素重复，如果重复，则`i`不动，`cur`后移寻找第一个不重复的元素： 1. 此时i指向索引1，cur指向索引2，发现对应位置的值不同 2. 执行赋值：`nums[i] = nums[cur++]` 3. 完成后cur后移，然后进入下一个循环，i同样后移

如果`cur`直到数组末尾依然没有找到不重复的元素，那么直接返回`i`，表示新数组的大小。

如下用例

> 1 2 3

## 代码

```java
class Solution {
    public int removeDuplicates(int[] nums) {
        int cur = 1; // 快指针
        int i = 1; // 慢指针，表示新数组的大小

        for (; i < nums.length; i++) {
            if (cur == nums.length) {
                return i; // 当快指针遍历完成之后，直接返回i
            }

            /*
            快指针与慢指针-1的元素判断是否重复，如果重复，快指针后移
            如果快指针移动到了数组末尾，说明新数组后面的元素都是重复的，则直接返回i即可
             */
            while (nums[cur] == nums[i - 1]) {
                if (++cur == nums.length) {
                    return i;
                }
            }

            // 说明cur是重复元素后的第一个不重复元素，添加到新数组中
            nums[i] = nums[cur++];
        }

        return i;
    }
}
```

