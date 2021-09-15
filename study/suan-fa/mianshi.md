# 快速排序



```java
//快速排序
void quickSort(int arr[], int l, int r) {
    if (l < r) {
        int i = l, j = r, x = arr[l];
        while (i < j) {
            while (i < j && arr[j] >= x) { // 从右向左找第一个小于x的数
                j--;
            }

            if (i < j) {
                arr[i++] = arr[j];
            }

            while (i < j && arr[i] < x) { // 从左向右找第一个大于等于x的数
                i++;
            }    
            if(i < j) {
                arr[j--] = arr[i];
            }
        }

        arr[i] = x;
        quick_sort(s, l, i - 1); // 递归调用 
        quick_sort(s, i + 1, r);
    }
}
```

