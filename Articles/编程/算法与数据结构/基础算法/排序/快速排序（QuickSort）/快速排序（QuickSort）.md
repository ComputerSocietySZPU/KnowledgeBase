**逻辑**
选择数组中的一个数作为**中心（pivot）**，将比[pivot](https://so.csdn.net/so/search?q=pivot&spm=1001.2101.3001.7020)大的和比pivot小的**分别归类**分为**两组**，每组分别**递归**重复上述步骤直到仅剩一个数。
**简述**：已经很简了！！
**代码**
QuickSort
```java
public class QuickSort {
    public static void main(String[] args) {
        int[] arr = { 5, 3, 6, 2, 1, 9, 4, 7, 8, -1, 0};
        quickSort(arr, 0, arr.length - 1);
        for (int i : arr) {
            System.out.print(i + " ");
        }
    }
 
    public static void quickSort(int[] arr, int low, int high) {
        if (low < high) {
            int pivot= partition(arr, low, high);
            quickSort(arr, low, pivot - 1);
            quickSort(arr, pivot + 1, high);
        }
    }
    public static int partition(int[] arr, int low, int high) {
        int pivot = arr[low];
        while (low < high){
            while(low < high && arr[high] >= pivot){
                high--;
            }
            arr[low] = arr[high];
            while (low < high && arr[low] <= pivot){
                low++;
            }
            arr[high] = arr[low];
        }
        arr[low] = pivot;
        return low;
    }
}
```
