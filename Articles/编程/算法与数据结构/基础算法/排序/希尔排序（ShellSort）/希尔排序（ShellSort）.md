**逻辑**
 ** 插入排序**在顺序时时间复杂度为o(n)，逆序时为o(n²)，当被排序的对象越接近排序结果时，插入排序的效率**越高**，后由**希尔**提出了一种对插入排序的优化，基本思路是先选定一个整数作为**增量**（一般为length/2 或 length/2 +1），把**相距增量距离的数**为一组（如增量为2时，1 2 3 4 5分为1 3 5与2 4两组），对每一组进行排序，然后将增量缩小（一般为 增量/2 或 增量/3 + 1），继续分组排序，重复直到增量缩小为1，故又称为**缩小增量排序**。
**简述**：使增量 **gap = arr.length/2**，每隔**gap**为一组进行排序后使 **gap/=2**，重复到 **gap=1**。
​
**代码**
ShellSort
```java
public class ShellSort {
    public static void main(String[] args) {
        int[] arr = { 8, 9, 1, 7, 2, 3, 5, 4, 6, 0 , -1};
        shellSort(arr);
        for (int i: arr) {
            System.out.print(i + " ");
        }
    }

    public static void shellSort(int[] arr) {
        int temp = 0;
        for (int gap = arr.length / 2; gap > 0; gap /= 2){
            for (int i = gap; i < arr.length; i++) {
                int j = i;
                temp = arr[i];
                while (j - gap >= 0 && temp < arr[j - gap]) {
                    arr[j] = arr[j - gap];
                    j -= gap;
                }
                arr[j] = temp;
            }
        }
    }
}
```
