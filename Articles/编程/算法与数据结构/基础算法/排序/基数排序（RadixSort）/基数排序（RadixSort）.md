**逻辑**
 **基数排序**（radix sort）属于“** 分配式排序** ”（distribution sort），又称“ **桶子法** ”（bucket sort），是由 **赫尔曼·何乐礼** 提出的对 **桶排序** 的**拓展**。从 **最低位** 开始逐位进行排序，一直到**最高位**排序完成以后, 数列变为有序。
​
 **注**：为了防止溢出，需要一个大小为 **10*arr.length** 的二维数组（负数为 **19*arr.length** ）。
 **简述**：**从低位到高位** 进行 **桶排序** 。
**代码**
RadixSort
```java
import java.util.Arrays;
 
public class RadixSort {
    public static void main(String[] args) {
        int[] arr = { 53, 3, 542, 748, 14, 214 , 0, 100};
        radixSort(arr);
        System.out.println(Arrays.toString(arr));
    }
 
    public static void radixSort(int[] arr) {
        int max = arr[0];
        for (int i = 1; i < arr.length; i++) if (arr[i] > max) max = arr[i];
        int maxlength = (max + "").length();
        int count[] = new int[10];
        int[][] bucket = new int[10][arr.length];
        for(int i = 0, n = 1;i<maxlength;i++,n*=10){
            for(int j = 0;j<10;j++)count[j] = 0;
            for(int j = 0;j<arr.length;j++){
                int dight = (arr[j]/n)%10;
                bucket[dight][count[dight]++] = arr[j];
            }
            int index = 0;
            for(int j = 0;j<10;j++)
                for(int o = 0;o<count[j];o++){
                    arr[index++]=bucket[j][o];
                }
        }
    }
}
```
