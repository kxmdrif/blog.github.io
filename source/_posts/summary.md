---
title: 常见算法
date: 2020-08-09 15:03:48
description: 常见算法
categories: Algorithms
tags: Algorithms
---



## 1. 二叉树遍历(非递归)

```java
private List<Integer> dfsPreOrder(TreeNode root) {
	ArrayList<Integer> results = new ArrayList<>();
	Stack<TreeNode> stack = new Stack<>();

	TreeNode cur = root;
	while (cur != null || !stack.empty()) {
		while (cur != null) {
			results.add(cur.val);
			stack.push(cur);
			cur = cur.left;
		}
		cur = stack.pop();
		// 转向
		cur = cur.right;
	}

	return results;
}

private List<Integer> dfsInOrder(TreeNode root) {
    List<Integer> results = new ArrayList<>();
    Stack<TreeNode> stack = new Stack<TreeNode>();
    TreeNode cur = root;
    while (cur != null || !stack.empty()) {
        while (cur != null) {
            stack.push(cur);
            cur = cur.left;
        }
        cur = stack.pop();
        results.add(cur.val);
        cur = cur.right;
    }
    return results;
}

private List<Integer> dfsPostOrder(TreeNode root) {
    List<Integer> results = new ArrayList<>();
    Stack<TreeNode> stack = new Stack<>();
    
    TreeNode cur = root;
    TreeNode last = null;
    while(cur != null || !stack.empty()){
        while (cur != null) {
            stack.push(cur);
            cur = cur.left;
        }
        cur = stack.peek();
        if (cur.right == null || cur.right == last) {
            results.add(cur.val);
            stack.pop();
            // 记录上一个访问的节点
            // 用于判断“访问根节点之前，右子树是否已访问过”
            last = cur;
            // 表示不需要转向，继续弹栈
            cur = null;
        } else {
            cur = cur.right;
        }
    }
    
    return results;
}

```


## 2. 二分查找


```java
int binarySearch(int[] nums, int target) {
    int left = 0; 
    int right = nums.length - 1; // 注意

    while(left <= right) { // 注意
        //int mid = left + (right - left) / 2;防溢出
        //或 int mid = left + ((right - left) >> 1)注意+-优先级高于移位运算
        int mid = (right + left) / 2;
        if(nums[mid] == target)
            return mid; 
        else if (nums[mid] < target)
            left = mid + 1; // 注意
        else if (nums[mid] > target)
            right = mid - 1; // 注意
        }
    return -1;
}

int left_bound(int[] nums, int target) {
    if (nums.length == 0) return -1;
    int left = 0;
    int right = nums.length; // 注意

    while (left < right) { // 注意
        int mid = (left + right) / 2;
        if (nums[mid] == target) {
            right = mid;
        } else if (nums[mid] < target) {
            left = mid + 1;
        } else if (nums[mid] > target) {
            right = mid; // 注意
        }
    }
    // target 比所有数都大
	if (left == nums.length) return -1;
	// 类似之前算法的处理方式
	return nums[left] == target ? left : -1;
}

int right_bound(int[] nums, int target) {
    if (nums.length == 0) return -1;
    int left = 0, right = nums.length;

    while (left < right) {
        int mid = (left + right) / 2;
        if (nums[mid] == target) {
            left = mid + 1; // 注意
        } else if (nums[mid] < target) {
            left = mid + 1;
        } else if (nums[mid] > target) {
            right = mid;
        }
    }
    if (left == 0) return -1;
	  return nums[left-1] == target ? (left-1) : -1;
}

```


## 3. KMP算法

```java
public class KMP {
    public int indexOf(String source, String pattern) {
        int i = 0, j = 0;
        char[] src = source.toCharArray();
        char[] ptn = pattern.toCharArray();
        int sLen = src.length;
        int pLen = ptn.length;
        int[] next = getNext(ptn);
        while (i < sLen && j < pLen) {
            // 如果j = -1,或者当前字符匹配成功(src[i] = ptn[j]),都让i++,j++
            if (j == -1 || src[i] == ptn[j]) {
                i++;
                j++;
            } else {
                // 如果j!=-1且当前字符匹配失败,则令i不变,j=next[j],即让pattern模式串右移j-next[j]个单位
                j = next[j];
            }
        }
        if (j == pLen)
            return i - j;
        return -1;
    }
    public int[] getNext(char[] p) {
        // 已知next[j] = k,利用递归的思想求出next[j+1]的值
        // 如果已知next[j] = k,如何求出next[j+1]呢?具体算法如下:
        // 1. 如果p[j] = p[k], 则next[j+1] = next[k] + 1;
        // 2. 如果p[j] != p[k], 则令k=next[k],如果此时p[j]==p[k],则next[j+1]=k+1,
        // 如果不相等,则继续递归前缀索引,令 k=next[k],继续判断,直至k=-1(即k=next[0])或者p[j]=p[k]为止
        int pLen = p.length;
        int[] next = new int[pLen];
        int k = -1;
        int j = 0;
        next[0] = -1; // next数组中next[0]为-1
        while (j < pLen - 1) {
            if (k == -1 || p[j] == p[k]) {
                k++;
                j++;
                next[j] = k;
            } else {
                k = next[k];
            }
        }
        return next;
    }
}
```

## 4. 快速排序

- Time Complexity: O(nlogn)
- Space Complexity: O(logn)
- 不稳定

```java
public class QuickSort implements IArraySort {

    @Override
    public int[] sort(int[] sourceArray) throws Exception {
        // 对 arr 进行拷贝，不改变参数内容
        int[] arr = Arrays.copyOf(sourceArray, sourceArray.length);

        return quickSort(arr, 0, arr.length - 1);
    }

    private int[] quickSort(int[] arr, int left, int right) {
        if (left < right) {
            int partitionIndex = partition(arr, left, right);
            quickSort(arr, left, partitionIndex - 1);
            quickSort(arr, partitionIndex + 1, right);
        }
        return arr;
    }

    private int partition(int[] arr, int left, int right) {
        // 设定基准值（pivot）
        int pivot = left;
        int index = pivot + 1;
        for (int i = index; i <= right; i++) {
            if (arr[i] < arr[pivot]) {
                swap(arr, i, index);
                index++;
            }
        }
        swap(arr, pivot, index - 1);
        return index - 1;
    }

    private void swap(int[] arr, int i, int j) {
        int temp = arr[i];
        arr[i] = arr[j];
        arr[j] = temp;
    }

}
```

## 5. 堆排序

- Time Complexity: O(nlogn)
- Space Complexity: O(1)
- 不稳定

```java
import java.util.Arrays;

public class HeapSort {
    public static void main(String []args){
        int []arr = {9,8,7,6,5,4,3,2,1};
        sort(arr);
        System.out.println(Arrays.toString(arr));
    }
    public static void sort(int []arr){
        //1.构建大顶堆
        for(int i=arr.length/2-1;i>=0;i--){
            //从第一个非叶子结点从下至上，从右至左调整结构
            adjustHeap(arr,i,arr.length);
        }
        //2.调整堆结构+交换堆顶元素与末尾元素
        for(int j=arr.length-1;j>0;j--){
            swap(arr,0,j);//将堆顶元素与末尾元素进行交换
            adjustHeap(arr,0,j);//重新对堆进行调整
        }

    }

    /**
     * 调整大顶堆（仅是调整过程，建立在大顶堆已构建的基础上）
     * @param arr
     * @param i
     * @param length
     * [i, j)
     */
    public static void adjustHeap(int[] arr, int i, int j){
        int temp = arr[i];//先取出当前元素i
        for(int k = 2 * i + 1; k < j;k = 2 * k + 1){//从i结点的左子结点开始，也就是2i+1处开始
            if(k + 1 < j && arr[k] < arr[k + 1]){//如果左子结点小于右子结点，k指向右子结点
                k++;
            }
            if(arr[k] > temp){//如果子节点大于父节点，将子节点值赋给父节点（不用进行交换）
                arr[i] = arr[k];
                i = k;
            }else{
                break;
            }
        }
        arr[i] = temp;//将temp值放到最终的位置
    }

    /**
     * 交换元素
     * @param arr
     * @param a
     * @param b
     */
    public static void swap(int []arr,int a ,int b){
        int temp=arr[a];
        arr[a] = arr[b];
        arr[b] = temp;
    }
}
```

## 6 归并排序
- Time Complexity: O(nlogn)
- Space Complexity: O(n)
- 稳定

```java
public class Sort {
    public void mergeSort(int[] nums) {
        mergeSort(nums, 0, nums.length - 1);
    }
    public void mergeSortIter(int[] nums) {
        int len = 1;
        while (len <= nums.length) {
            for (int i = 0; i + len <= nums.length; i += 2 * len) {
                int l = i, mid = i + len - 1, r = i + 2 * len - 1;
                if (r >= nums.length)
                    r = nums.length - 1;
                merge(nums, l, mid, r);
            }
            len = 2 * len;
        }
    }
    private void mergeSort(int[] nums, int i, int j) {
        if (i >= j) return;
        int mid = i + ((j - i) >> 1);
        mergeSort(nums, i, mid);
        mergeSort(nums, mid + 1, j);
        merge(nums, i, mid, j);
    }
    private void merge(int[] nums, int i, int mid, int j) {
        int[] tmp = new int[j - i + 1];
        int pi = i, pj = mid + 1, p = 0;
        while (pi <= mid && pj <= j) 
            //稳定排序
            if (nums[pi] <= nums[pj])
                tmp[p++] = nums[pi++];
            else
                tmp[p++] = nums[pj++];
        }
        while (pi <= mid) tmp[p++] = nums[pi++];
        while (pj <= j) tmp[p++] = nums[pj++];
        for (int k = i; k <= j; k++)
            nums[k] = tmp[k - i];
    }
}
```

## 7. 排序总结

| 排序方法 | 时间复杂度(平均) | 时间复杂度(最坏) | 时间复杂度(最好) | 空间复杂度 | 稳定性 |
| -------- | ---------------- | ---------------- | ---------------- | ---------- | ------ |
| 插入排序 | O(n^2)           | O(n^2)           | O(n)             | O(1)       | 稳定   |
| 希尔排序 | O(n^1.3)         | O(n^2)           | O(n)             | O(1)       | 不稳定 |
| 选择排序 | O(n^2)           | O(n^2)           | O(n^2)           | O(1)       | 不稳定 |
| 堆排序   | O(nlogn)         | O(nlogn)         | O(nlogn)         | O(1)       | 不稳定 |
| 冒泡排序 | O(n^2)           | O(n^2)           | O(n)             | O(1)       | 稳定   |
| 快速排序 | O(nlogn)         | O(n^2)           | O(nlogn)         | O(logn)    | 不稳定 |
| 归并排序 | O(nlogn)         | O(nlogn)         | O(nlogn)         | O(n)       | 稳定   |
| 计数排序 | O(n + k)         | O(n + k)         | O(n + k)         | O(n + k)   | 稳定   |
| 桶排序   | O(n + k)         | O(n^2)           | O(n)             | O(n + k)   | 稳定   |
| 基数排序 | O(n * k)         | O(n * k)         | O(n * k)         | O(n + k)   | 稳定   |

## 8. 链表排序
```java
/**1选择排序
非交换节点的值, 而是每次把最小的节点添加在已排好序部分的尾部
*/
public ListNode selectionSortList(ListNode head) {
    ListNode dummy = new ListNode(0);
    dummy.next = head;
    //已排好序部分的尾部
    ListNode tail = dummy;
    while(tail.next != null){
        ListNode min = tail;
        ListNode t = tail;
        while(t.next != null){
            if (t.next.val < min.next.val)
                min = t;
            t = t.next;
        }
        t = min.next;
        min.next = min.next.next;
        t.next = tail.next;
        tail.next = t;
        tail = tail.next;
    }
    return dummy.next;
}

/**插入排序
注意：每遍历一个节点时，前面已经排好序的节点链表和该节点没有相连
所以最开始dummy也不应该与head相连。(相连的话不太好处理)
而且每次也不一定都从头开始查找，可以从上一次找到的插入位置开始查找，
除非上次位置的值大于当前值，此时重置开始查找位置为dummy
*/
public ListNode insertionSortList(ListNode head) {
    ListNode dummy = new ListNode(0);
    ListNode cur = head;
    ListNode target = dummy;
    while(cur != null){
        if (target.val > cur.val)
            target = dummy;
        ListNode next = cur.next;
        while(target.next != null && target.next.val < cur.val)
            target = target.next;
        cur.next = target.next;
        target.next = cur;
        cur = next;
    }
    return dummy.next;
}
/**归并排序
注意尽量分均匀否则会出现无限递归(例如如1->2分成1->2和null, 1->2一直递归)
*/
public ListNode mergeSortList(ListNode head) {
    if (head == null || head.next == null) return head;
    ListNode fast = head, slow = head, prev = null;
    while(fast != null && fast.next != null){
        prev = slow;
        fast = fast.next.next;
        slow = slow.next;
        
    }
    ListNode l1 = head, l2 = prev.next;
    prev.next = null;
    l1 = sortList(l1);
    l2 = sortList(l2);
    return merge(l1, l2);
}
    
private ListNode merge(ListNode l1, ListNode l2){
    ListNode res = new ListNode(0);
    ListNode head = res;
    while(l1 != null && l2 != null){
        if (l1.val < l2.val){
            head.next = l1;
            l1 = l1.next;
        }else{
            head.next = l2;
            l2 = l2.next;
        }
        head = head.next;
    }
    head.next = l1 == null ? l2 : l1;
    return res.next;
}
```

## 9. 最大堆

```java
public class MaxHeap {
    private int[] heap;
    private int size;
    private int capacity;
    public MaxHeap(int capacity) {
        this.size = 0;
        this.capacity = capacity;
        this.heap = new int[capacity];
    }

    public void resize() {
        capacity = 2 * capacity;
        int[] newHeap = new int[capacity];
        for (int i = 0; i < size; i++) {
            newHeap[i] = heap[i];
        }
        heap = newHeap;
    }

    public void push(int val) {
        if (size == capacity) {
            resize();
        }
        heap[size] = val;
        size++;
        shiftUp(size - 1);

    }

    public int size() {
        return size;
    }

    public boolean isEmpty() {
        return size == 0;
    }

    public int pop() {
        if (isEmpty()) throw new RuntimeException("Heap is empty!");
        int val = heap[0];
        heap[0] = heap[size - 1];
        size--;
        shiftDown(0);
        return val;
    }

    private void shiftUp(int i) {
        int tmp = heap[i];
        //note: use condition i > 0 here, if use i >= 0, will cause infinite loop.
        while (i > 0 && tmp > heap[(i - 1) / 2]) {
            heap[i] = heap[(i - 1) / 2];
            i = (i - 1) / 2;
        }
        heap[i] = tmp;
    }

    private void shiftDown(int i) {
        int tmp = heap[i];
        //double pointers, i is the parent of k
        for (int k = 2 * i + 1; k < size; k = 2 * k + 1) {
            if (k + 1 < size && heap[k + 1] > heap[k]) k++;
            if (tmp >= heap[k]) break;
            heap[i] = heap[k];
            i = k;
        }
        heap[i] = tmp;
    }
}

```

