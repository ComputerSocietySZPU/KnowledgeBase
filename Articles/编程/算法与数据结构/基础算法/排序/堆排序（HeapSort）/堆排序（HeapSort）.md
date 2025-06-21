**逻辑**
 **堆**是一种所有**父节点**都**大于等于**（大根堆）或**小于等于**（小根堆）其子节点的**完全二叉树**。**堆排序**（升序）就是一种将数组视为一个**完全二叉树**，将其变为一个**大根堆**后将**堆顶**放到**数组尾**，**重复n次**后数组有序的排列方法，时间复杂度为O(nlogn)。（感觉好像冒泡哦）
**简述**：将数组视为**完全二叉树**，将其变为堆后**将堆顶置底**，**重复n次**。

**代码**
HeapSort
```java
import java.util.Arrays;
 
public class HeapSort {
    public static void main(String[] args) {
        int arr[] = {4, 6, 8, 5, 9 , 7, 1, 3, 2};
        heapSort(arr);
        System.out.println(Arrays.toString(arr));
    }
    public static void heapSort(int arr[]){
        for(int i = arr.length/2-1;i>=0;i--){
            heapAdjust(arr,i,arr.length);
        }
        int temp;
        for(int i = arr.length-1;i>0;i--){
            temp = arr[0];
            arr[0] = arr[i];
            arr[i] = temp;
            heapAdjust(arr,0,i);
        }
    }
    public static void heapAdjust(int arr[], int start, int end){
        int temp = arr[start];
        for(int i = start * 2 + 1;i < end;i = i * 2 + 1){
            if (i+1 < end && arr[i] < arr[i+1]) i++;
            if (temp < arr[i]){
                arr[start] = arr[i];
                start = i;
            }else break;
        }
        arr[start] = temp;
    }
}
```
