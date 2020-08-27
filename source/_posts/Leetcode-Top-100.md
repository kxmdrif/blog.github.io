---
title: Leetcode Top 100
date: 2020-08-26 13:09:15
description: Leetcode Top 100 Liked Questions Summary
categories: Algorithms
tags: Algorithms
---
## 1. Two Sum

**solution**

hashmap

- Time Complexity: O(n)

```java
import java.util.*;
class Solution {
   
    public int[] twoSum(int[] nums, int target) {
        Map<Integer, Integer> m = new HashMap<>();
        for(int i = 0; i < nums.length; i++){
            if (m.containsKey(target - nums[i]))
                return new int[] {m.get(target - nums[i]), i};
            m.put(nums[i], i);
        }
        return null;
    }
}
```
## 2. Add Two Numbers
**solution**

```java
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        ListNode dummy = new ListNode(0);
        ListNode head = dummy;
        int carry = 0;
        while(l1 != null || l2 != null){
            int x = (l1 == null) ? 0 : l1.val;
            int y = (l2 == null) ? 0 : l2.val;
            int sum = carry + x + y;
            carry = sum / 10;
            head.next = new ListNode(sum % 10);
            head = head.next;
            l1 = (l1 == null) ? null : l1.next;
            l2 = (l2 == null) ? null : l2.next;
        }
        if (carry != 0) head.next = new ListNode(carry);
        return dummy.next;
        
    }
}
```

## 3. Longest Substring Without Repeating Characters

**solution**

Time complexity : O(n)
Space complexity (Table): O(m). m is the size of the charset.


int[26] for Letters 'a' - 'z' or 'A' - 'Z'
int[128] for ASCII
int[256] for Extended ASCII

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        int n = s.length();
        int[] index = new int[128];
        int res = 0;
        for(int i = 0, j = 0; j < n; j++){
            //if s[j] has a duplicated in [i, j) with index j1,
            //we let i = j1 + 1, instead of increasing i little by little
            //if s[j] dosen't have a duplicated in [i, j) 
            //(index[s.charAt(j)] = 0 or <= i), then i = i.
            i = Math.max(index[s.charAt(j)], i);
            res = Math.max(res, j - i + 1);
            index[s.charAt(j)] = j + 1;
        }
        return res;
    }
}
```

## 4. Median of Two Sorted Arrays

**solution**
O(log n)

```java
class Solution {
    public double findMedianSortedArrays(int[] nums1, int[] nums2) {
        int m = nums1.length, n = nums2.length;
        //for an array with length n, whether n is odd or even, 
        //the Median is always the average of the (n + 1)/2 th and the (n + 2)/2 th
        int left = (m + n + 1) / 2, right = (m + n + 2) / 2;
        return (findKth(nums1, nums2, 0, 0, left) + 
            findKth(nums1, nums2, 0, 0, right)) / 2.0;
    }
    
    // find kth smallest item in two mergered array
    public int findKth(int[] nums1, int[] nums2, int i, int j, int k){
        if (i >= nums1.length) return nums2[j + k - 1];
        if (j >= nums2.length) return nums1[i + k - 1];
        if (k == 1) return Math.min(nums1[i], nums2[j]);
        
        //if midVal1 is out of range, kth might be in nums1[i, )
        //but kth couldn't be in nums2[i, i + k / 2) 
        //so we use INT MAX to prevent nums1[i, ) from being elimilated
        int midVal1 = (i + k / 2 - 1) < nums1.length
            ? nums1[i + k / 2 - 1] : Integer.MAX_VALUE;
        int midVal2 = (j + k / 2 - 1) < nums2.length
            ? nums2[j + k / 2 - 1] : Integer.MAX_VALUE;
        
        return midVal1 < midVal2 
            ? findKth(nums1, nums2, i + k / 2, j, k - k / 2)
            : findKth(nums1, nums2, i, j + k / 2, k - k / 2);
        //k - k / 2 couldn't be replaced by k / 2!!!
    }
    
    
}
```

## 5. Longest Palindromic Substring

**solution**
- Time complexity: O(n^2)
- Space complexity: O(1)

```java
class Solution {
    public String longestPalindrome(String s) {
        if (s == null || s.isEmpty()) return "";
        int start = 0, end = 0;
        
        for(int i = 0; i < s.length(); i++){
            int len1 = appendCenter(s, i, i);
            int len2 = appendCenter(s, i, i + 1);
            int len = Math.max(len1, len2);
            if (len > end - start){
                start = i - (len - 1) / 2;
                end = i + len / 2;
            }
        }
        return s.substring(start, end + 1);
    }
    
    private int appendCenter(String s, int l, int r){
        while(l >= 0 && r < s.length() && s.charAt(l) == s.charAt(r)){
            l--;
            r++;
        }
        return r - l - 1;
    }
}
```

## 6. ZigZag Conversion
**solution**
- Time Complexity: O(n) where n = len(s)
- Space Complexity: O(n)

```java
class Solution {
    /*
    T = 2 * numRows - 2
    row 0 : indexes: k * T
    row numRows-1: indexes: numRows - 1 + k * T
    row i: indexes: i + k * T and T - i + k * T
    */
    public String convert(String s, int numRows) {
        if (numRows == 1) return s;
        int n = s.length();
        StringBuilder res = new StringBuilder();
        int cycle = 2 * numRows - 2;
        for(int i = 0; i < numRows; i++){
            for(int k = 0; i + k * cycle < n; k++){
                res.append(s.charAt(i + k * cycle));
                if (i != 0 && i != numRows - 1 &&
                    cycle - i + k * cycle < n)
                    res.append(s.charAt(cycle - i + k * cycle));
            }
        }
        return res.toString();
    }
}
```

## 7. Reverse Integer
**slolution**
- Time Complexity: O(log(x)). There are roughly log10(x) digits in x
- Space Complexity: O(1)

```java
class Solution {
    public int reverse(int x) {
        int res = 0;
        while(x != 0){
            int r = x % 10;
            x = x / 10;
            if (res > Integer.MAX_VALUE / 10 || res == Integer.MAX_VALUE && r > 7)
                return 0;
            if (res < Integer.MIN_VALUE / 10 || res == Integer.MIN_VALUE && r < -8)
                return 0;
            res = 10 * res + r;
        }
        return res;
    }
}
```

## 10. Regular Expression Matching

**solution**
Approach DP

- Time complexity: O(SP)  where S, P is the length of s and p;
- Space Complexity: O(SP)

Top-Down Variation
```java
class Solution {
    private int[][] dp;
    public boolean isMatch(String s, String p) {
        int[][] memo = new int[s.length() + 1][p.length() + 1];
        for(int i = 0; i < s.length() + 1; i++)
            for(int j = 0; j < p.length() + 1; j++)
                memo[i][j] = -1;
        return dpMatch(0, 0, s, p, memo);
    }
    private boolean dpMatch(int i, int j, String s, String p, int[][] memo){
        /*
        swap the position of the two lines is ok
        if (j == p.length()) return i == s.length();
        if(memo[i][j] != -1) return memo[i][j] == 1;
        */
        if(memo[i][j] != -1) return memo[i][j] == 1;
        if (j == p.length()) return i == s.length();
        boolean res;
        boolean first_match = i < s.length() && (s.charAt(i) == p.charAt(j) || p.charAt(j) == '.');
        if (j + 1 < p.length() && p.charAt(j + 1) == '*')
            res = (first_match && dpMatch(i + 1, j, s, p, memo))
                || dpMatch(i, j + 2, s, p, memo);
        else
            res = first_match && dpMatch(i + 1, j + 1, s, p, memo);
        memo[i][j] = res ? 1 : 0;
        return res;
    }
    
   
}
```

Bottom-Up Variation
```java
class Solution {
    private int[][] dp;
    public boolean isMatch(String s, String p) {
        int slen = s.length(), plen = p.length();
        //相当于后面加一个空字符
        boolean[][] dp = new boolean[slen + 1][plen + 1];
        //dp[i][plen] = true(i == slen), false(i < slen)
        
        dp[slen][plen] = true;
        //因为p的符号有前后关系所以从尾部开始dp[i][j]代表s[i:]与p[j:]的匹配情况
        for(int i = slen; i >= 0; i--){
            for(int j = plen - 1; j >= 0; j--){
                //i == slen则p[j](j <= plen - 1)一定不可能与s[i](空字符)匹配
                boolean first_match = 
                    i < slen && (s.charAt(i) == p.charAt(j)
                    || p.charAt(j) =='.');
                //j + 1 < plen防止越界
                if (j + 1 < plen && p.charAt(j + 1) == '*')
                //dp[i][j + 2]不需要first_match因为这是与空字符匹配,
                //相当于first_match一定为true。
                //这两种情况对应p[j:j+1]匹配0个或至少一个s[i]
                    dp[i][j] = dp[i][j + 2] || (first_match && dp[i + 1][j]);
                else 
                    dp[i][j] = first_match && dp[i + 1][j + 1];
                
            }
        }
        return dp[0][0];
        
    }
    
   
}
```
## 11. Container With Most Water
**solution**

Initially we consider the area constituting the exterior most lines. Now, to maximize the area, we need to consider the area between the lines of larger lengths. If we try to move the pointer at the longer line inwards, we won't gain any increase in area, since it is limited by the shorter line. But moving the shorter line's pointer could turn out to be beneficial, as per the same argument, despite the reduction in the width. This is done since a relatively longer line obtained by moving the shorter line's pointer might overcome the reduction in area caused by the width reduction.

- Time Complexity: O(n)
- Space Complexity: O(1)

```java
class Solution {
    public int maxArea(int[] height) {
        int res = 0;
        int l = 0, r = height.length - 1;
        while(l < r){
            res = Math.max(res, 
                    Math.min(height[l], height[r]) * (r - l));
            if(height[l] < height[r])
                l++;
            else
                r--;
        }
        return res;
    }
}
```

## 15. 3Sum
**solution**
- Time Complexity: O(n^2)

```java
import java.util.*;
class Solution {
    public List<List<Integer>> threeSum(int[] nums) {
        List<List<Integer>> res = new ArrayList<>();
        if (nums.length <= 2)
            return res;
        Arrays.sort(nums);
        
        for(int i = 0; i < nums.length - 2; i++){
            if (nums[i] > 0) return res;
            if (i > 0 && nums[i] == nums[i - 1]) 
                continue;
            int j = i + 1, k = nums.length - 1;
            int sum = -nums[i];
            while(j < k){
                if (nums[j] + nums[k] == sum){
                    res.add(Arrays.asList(nums[i], nums[j], nums[k]));
                    while(j < k && nums[j] == nums[j + 1]) j++;
                    while(j < k && nums[k] == nums[k - 1]) k--;
                    j++;
                    k--;
                }else if (nums[j] + nums[k] > sum){
                    while(j < k && nums[k] == nums[k - 1]) k--;
                    k--;
                }else{
                    while(j < k && nums[j] == nums[j + 1]) j++;
                    j++;
                }
                    
            }
        }
        return res;
    }
}
```

## 17. Letter Combinations of a Phone Number

**solution**

Approach 1: Backtracking

- Time Complexity: O(3^M * 4 ^ N) where N is the number of digits in the input that maps to 3 letters (e.g. 2, 3, 4, 5, 6, 8) and M is the number of digits in the input that maps to 4 letters (e.g. 7, 9), and N+M is the total number digits in the input.

- Space Complexity: O(3^M * 4 ^ N) since one has to keep 3^N * 4^M solutions.

```java
import java.util.*;
class Solution {
    private List<String> res = new ArrayList<>();
    private int len = 0;
    String[] map = new String[]{"abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"};
    
    public List<String> letterCombinations(String digits) {
        len = digits.length();
        if (len == 0) return res;
        rec(0, new char[len], digits.toCharArray());
        return res;
    }
    private void rec(int i, char[] single, char[] digits){
        if (i == len){
            res.add(new String(single));
            return;
        }
        for(char c : map[digits[i] - '2'].toCharArray()){
            single[i] = c;
            rec(i + 1, single, digits);
        }
    }
    
}
```

Approach 2 : use queue

```java
import java.util.*;
class Solution {
    public List<String> letterCombinations(String digits) {
        String[] map = new String[]{"abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"};
        LinkedList<String> res = new LinkedList<>();
        
        if (digits.isEmpty()) return res;
        res.add("");
        
        while(res.peek().length() != digits.length()){
            String s = res.poll();
            for(char c : 
                map[digits.charAt(s.length()) - '2'].toCharArray())
                
                res.offer(s + c);
            
        }
        return res;
    }
    
}
```

## 19. Remove Nth Node From End of List
**solution**

Approach: Two Pointers

- Time Complexity: O(n)
- Space Complexity: O(1)

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        ListNode dummy = new ListNode(0);
        dummy.next = head;
        ListNode first = dummy, second = dummy;
        for(int i = 0; i < n + 1; i++)
            first = first.next;
        while(first != null){
            first = first.next;
            second = second.next;
        }
        second.next = second.next.next;
        return dummy.next;
        
    }
}
```

## 20. Valid Parentheses

**soultion**

Approach: Stack

- Time Complexity: O(n)
- Space Complexity: O(1)

```java
class Solution {
    public boolean isValid(String s) {
        Stack<Character> stack = new Stack<>();
        for(char c : s.toCharArray()){
            if (c == '(')
                stack.push(')');
            else if (c == '[')
                stack.push(']');
            else if (c == '{')
                stack.push('}');
            else if(stack.isEmpty() || stack.pop() != c)
                return false;
        }
        return stack.isEmpty();
    }
}
```

## 21. Merge Two Sorted Lists

**solution**
Approach1 : Iteration

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        ListNode dummy = new ListNode(0);
        ListNode head = dummy;
        while(l1 != null && l2 != null){
            if (l1.val < l2.val){
                head.next = l1;
                l1 = l1.next;
            }
            else{
                head.next = l2;
                l2 = l2.next;
            }
            head = head.next;
        }
        head.next = l1 == null ? l2 : l1;
        return dummy.next;
    }
}
```
Approach 2: Recursion
```java
class Solution {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        if (l1 == null) return l2;
        if (l2 == null) return l1;
        if (l1.val < l2.val){
            l1.next = mergeTwoLists(l1.next, l2);
            return l1;
        }
        else{
            l2.next = mergeTwoLists(l1, l2.next);
            return l2;
        }
    }
}
```

## 22. Generate Parentheses
**solution**
Approach: Backtracking

- Time Complexity: O(4^n / sqrt(n))
- Space Complexity: O(n)

```java
class Solution {
    
    public List<String> generateParenthesis(int n) {
        List<String> res = new ArrayList<>();
        dfs(n, 0, 0, "", res);
        return res;
    }
    
    public void dfs(int n, int left, int right, String comb, List<String> res){
        //left is the number of '(', right is the number of ')'
        //if left = n and right = n, add to result list
        if (left == n && right == n){
            res.add(comb);
            return;
        }
        //the following condition can ensure that the combination is well-formed
        
        //if left < n , we can add '('
        if (left < n)
            dfs(n, left + 1, right, comb + '(', res);
        //if right < left, we can add ')'
        if (right < left)
            dfs(n, left, right + 1, comb + ')', res);
    }
}
```

## 23. Merge k Sorted Lists
**solution**

Approach 1: Merge lists one by one

- Time Complexity: O(kN) where k is the number of linked lists, n is the total number of nodes in two lists
- Space Complexity: O(1)

```java
class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        if (lists.length == 0) return null;
        ListNode res = lists[0];
        for(int i = 1; i < lists.length; i++)
            res = mergeTwoLists(res, lists[i]);
        return res;
    }
    
    private ListNode mergeTwoLists(ListNode l1, ListNode l2){
        if (l1 == null) return l2;
        if (l2 == null) return l1;
        if (l1.val < l2.val){
            l1.next = mergeTwoLists(l1.next, l2);
            return l1;
        }else{
            l2.next = mergeTwoLists(l1, l2.next);
            return l2;
        }
    }
}
```

Approach 2: Merge with Divide And Conquer

- Time Complexity: O(N log k) (merge log k times which is the level of recursion tree)
- Space Complexity: O(1)

```java
class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        return merge(lists, 0, lists.length - 1);
    }
    
    private ListNode merge(ListNode[] lists, int start, int end){
        if(start > end) return null;
        if (start == end) return lists[start];
        int mid = start + (end - start) / 2;
        ListNode l1 = merge(lists, start, mid);
        ListNode l2 = merge(lists, mid + 1, end);
        ListNode dummy = new ListNode(0);
        ListNode cur = dummy;
        while(l1 != null && l2 != null){
            if (l1.val < l2.val){
                cur.next = l1;
                l1 = l1.next;
            }else{
                cur.next = l2;
                l2 = l2.next;
            }
            cur = cur.next;
        }
        cur.next = l1 == null ? l2 : l1;
        return dummy.next;
    }
}
```

## 32. Longest Valid Parentheses
**solution**

Approach 1: Using Stack

- Time Complexity: O(n)
- Space Complexity: O(n)

```java
class Solution {
    public int longestValidParentheses(String s) {
        Stack<Integer> stack = new Stack<>();
        stack.push(-1);
        int maxlen = 0;
        for(int i = 0; i <s.length(); i++){
            if (s.charAt(i) == '(')
                stack.push(i);
            else{
                stack.pop();
                if (stack.isEmpty())
                    stack.push(i);
                maxlen = Math.max(maxlen, i - stack.peek());
                
            }
        }
        return maxlen;
    }
}
```
Approach 2: Without extra space

- Time Complexity: O(n)
- Space Complexity: O(1)

```java
public class Solution {
    public int longestValidParentheses(String s) {
        int left = 0, right = 0, maxlength = 0;
        for (int i = 0; i < s.length(); i++) {
            if (s.charAt(i) == '(') {
                left++;
            } else {
                right++;
            }
            if (left == right) {
                maxlength = Math.max(maxlength, 2 * right);
            } else if (right >= left) {
                left = right = 0;
            }
        }
        left = right = 0;
        for (int i = s.length() - 1; i >= 0; i--) {
            if (s.charAt(i) == '(') {
                left++;
            } else {
                right++;
            }
            if (left == right) {
                maxlength = Math.max(maxlength, 2 * left);
            } else if (left >= right) {
                left = right = 0;
            }
        }
        return maxlength;
    }
}
```

## 33. Search in Rotated Sorted Array
**solution**
Approach: Binary search

- Time complexity: O(log n)
- Space complexity: O(1)

```java
class Solution {
    public int search(int[] nums, int target) {
        return binarySearch(nums, 0, nums.length - 1, target);
    }
    private int binarySearch(int[] nums, int start, int end,
                             int target){
        while(start <= end){
            int mid = start + (end - start) / 2;
            if (nums[mid] == target)
                return mid;
            if (nums[mid] >= nums[start]){
                if (target >= nums[start] && target < nums[mid])
                    end = mid - 1;
                else
                    start = mid + 1;
            }else{
                if (target > nums[mid] && target <= nums[end])
                    start = mid + 1;
                else
                    end = mid - 1;
            }
        }
        return -1;
    }
}
```

## 34. Find First and Last Position of Element in Sorted Array 

**solution**

```java
class Solution {
    public int[] searchRange(int[] nums, int target) {
        int[] res = {-1, -1};
        int left = binarySearch(nums, target, true);
        int right = binarySearch(nums, target, false);
        res[0] = (left == nums.length || nums[left] != target) 
            ? -1 : left;
        res[1] = (right == 0 || nums[right - 1] != target)
            ? -1 : right - 1;
        return res;
    }
    //attention for nums is empty!
    private int binarySearch(int[] nums, int target, boolean isleft){
        int l = 0, r = nums.length;
        while(l < r){
            int mid = l + ((r - l) >> 1);
            if (target < nums[mid] || (isleft && nums[mid] == target))
                r = mid;
            else
                l = mid + 1;
        }
        return l;
    }
    
}
```

## 39. Combination Sum
**solution**
Approach: Backtracking

```java
class Solution {
    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        List<List<Integer>> res = new ArrayList<>();
        backtrack(res, new ArrayList<>(), target, 0, candidates);
        return res;
    }
    /*
    1 use begin to avoid duplicates
    2 copy the combination list at last instead of every loop to
    decrease the cost of copying a list
    */
    private void backtrack(List<List<Integer>> res, List<Integer> comb, int target, int begin, int[] candidates){
        if (target < 0) return;
        if (target == 0){
            res.add(new ArrayList<>(comb));
            return;
        }
        for(int i = begin; i < candidates.length; i++){
            comb.add(candidates[i]);
            backtrack(res, comb, target - candidates[i], i, candidates);
            comb.remove(comb.size() - 1);
        }
    }
}
```

## 41. First Missing Positive
**solution**
- Time complexity: O(n)
- Space Complexity: O(1)

缺失的数字一定在[1,n+1]的范围中，n为数组长度+1遍历数组时，对于每一位要while循环至该位置的数字(1～n)放到正确的位置为止, 若(1~n)有重复则重复的只有一个在正确位置。

```java
class Solution {
    public int firstMissingPositive(int[] nums) {
        int n = nums.length;
        /*
        while里面最后一个条件不能写: nums[i] = i + 1(检测i位置是否放置了该放的值)
        而应该写nums[nums[i] - 1] != nums[i](检测nums[i]应该放置的位置，
        即nums[i] - 1位置是否放置了该放的值)
        否则对于nums[i] = nums[nums[i]]的情况，如[1, 1], [2, 2]会造成死循环
        或者可以写成这样
        while(nums[i] >= 1 && nums[i] <= n 
                  && nums[i] - 1 != i + 1){
                if (nums[nums[i] - 1] == nums[i])
                    break;
                swap(nums, i, nums[i] - 1);
            }
        */
        for(int i = 0; i < n; i++){
            while(nums[i] >= 1 && nums[i] <= n 
                  && nums[nums[i] - 1] != nums[i])
                swap(nums, i, nums[i] - 1);
        }
        for(int i = 0; i < n; i++)
            if(nums[i] != i + 1)
                return i + 1;
        return n + 1;
    }
    
    private void swap(int[] nums, int i, int j){
        int tmp = nums[i];
        nums[i] = nums[j];
        nums[j] = tmp;
    }
}
```

## 42. Trapping Rain Water

**solution**
Approach 1: Dynamic Programming

- Time complexity: O(n)
- Space complexity: O(n)

```java
class Solution {
    public int trap(int[] height) {
        
        int res = 0;
        int n = height.length;
        if (n < 3) return 0;
        
        int[] leftMax = new int[n];
        int[] rightMax = new int[n];
        
        leftMax[0] = height[0];
        for (int i= 1; i < n; i++){
            leftMax[i] = Math.max(leftMax[i - 1], height[i]);
        }
        
        rightMax[n - 1] = height[n - 1];
        for(int i = n - 2; i >= 0; i--){
            rightMax[i] = Math.max(rightMax[i + 1], height[i]);
        }
        
        for(int i = 1; i < n - 1; i++)
            res = res + Math.min(rightMax[i], leftMax[i]) - height[i];
        
        return res;
    }
}
```
Approach 2: Using 2 pointers:

- Time complexity: O(n)
- Space complexity: O(1)

```java
class Solution {
    public int trap(int[] height) {
        int leftmax = 0, rightmax = 0;
        int l = 0, r = height.length - 1;
        int res = 0;
        /*注意是l <= r而不是l < r否则某个位置没有计算
        比如r - l = 1时只计算了其中一个位置之后就结束循环，另一个位置没计算
        */
        while(l <= r){
            if (leftmax < rightmax){
                res += Math.max(0, leftmax - height[l]);
                leftmax = Math.max(leftmax, height[l++]);
            }else{
                res += Math.max(0, rightmax - height[r]);
                rightmax = Math.max(rightmax, height[r--]);
            }
        }
        return res;
    }
}
```

## 45. Jump Game II
**solution**

- Time complexity: O(n)
- Space complexity: O(1)

About i < nums.length - 1:
The for loop logic checks whether we need to jump back (to a certain previous position) in order to jump further when we are at position i . We don't need to check whether we need to jump further when we already at the last position A.Length-1.

```java
class Solution {
    public int jump(int[] nums) {
        //end is the farthest pos that curr pos can reach, maxPos is the farthest pos that [curr, end] can reach
        int minJump = 0, end = 0, maxPos = 0;
        //注意i < nums.length - 1: 以[2, 3, 1, 1, 4]为例
        for(int i = 0; i < nums.length - 1; i++){
            maxPos = Math.max(maxPos, i + nums[i]);
            if (i == end){
                minJump++;
                end = maxPos;
            }
        }
        return minJump;
        
    }
}
```

## 46. Permutations
**solution**
Approach: Backtracking

```java
class Solution {
    public List<List<Integer>> permute(int[] nums) {
        List<List<Integer>> res = new ArrayList<>();
        int[] used = new int[nums.length];
        rec(res, new ArrayList<>(), used, nums);
        return res;
    }
    private void rec(List<List<Integer>> res,
                     List<Integer> curr, int[] used, int[] nums){
        if (curr.size() == nums.length){
            res.add(new ArrayList<>(curr));
            return;
        }
        for(int i = 0; i < nums.length; i++){
            if(used[i] == 0){
                used[i] = 1;
                curr.add(nums[i]);
                rec(res, curr, used, nums);
                curr.remove(curr.size() - 1);
                used[i] = 0;
            }
        }
    }
}
```

## 48. Rotate Image
**solution**

```java
class Solution {
    public void rotate(int[][] matrix) {
        /*
        A  B         C  A
                ->            
        C  D         D  B
          |
          |
          |          A  C         C  A
          |------>          ->  
                     B  D         D  B
        */
        int m = matrix.length;
        if (m == 0) return;
        int n = matrix[0].length;
        for(int i = 0; i < m; i++){
            for(int j = 0; j < n; j++){
                if (i < j){
                    int tmp = matrix[i][j];
                    matrix[i][j] = matrix[j][i];
                    matrix[j][i] = tmp;
                }
            }
        }
        for(int i = 0; i < m; i++){
            for(int j = 0; j < n / 2; j++){
                int tmp = matrix[i][j];
                matrix[i][j] = matrix[i][n - 1 - j];
                matrix[i][n - 1 - j] = tmp;
            }
        }
    }
}
```

## 49. Group Anagrams
**solution**

- Time complexity: O(NK), where N is the length of strs, and K is the maximum length of a string in strs
- Space complexity: O(NK)

```java

//Categorize by Count
class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {
        Map<String, List<String>> map = new HashMap<>();
        for(String str : strs){
            char[] arr = new char[26];
            for(char c : str.toCharArray())
                arr[c - 'a']++;
            
            String key = String.valueOf(arr);
            
            List<String> value = map.getOrDefault(key,
                                        new ArrayList<>());
            value.add(str);
            map.put(key, value);
            
        }
        return new ArrayList<>(map.values());
        
    }
}
```

## 53. Maximum Subarray
**solution**

- Time complexity: O(n)
- Space complexity: O(1)

```java
class Solution {
    public int maxSubArray(int[] nums) {
    //maxSum must be one of the possible subarray sum(consider [-1])
        int thisSum = 0, maxSum = nums[0];
        for(int num : nums){
            thisSum += num;
            maxSum = Math.max(maxSum, thisSum);
            if (thisSum < 0)
                thisSum = 0;
        }
        return maxSum;
    }
}
```
## 55. Jump Game

**soultion**
Approach 1 : (Dynamic Programming Top-down)      O(n^2)

```java
class Solution {
    int[] mem;
    public boolean canJump(int[] nums) {
        mem = new int[nums.length];
        for(int i = 0; i < nums.length; i++) mem[i] = -1;
        return canJumpHelper(nums, 0);
    }
    public boolean canJumpHelper(int[] nums, int beginIndex){
        if(mem[beginIndex] == 0) return false;
        if(mem[beginIndex] == 1) return true;
        
        if (beginIndex >= nums.length - 1) return true;
        boolean res = false;
        for(int i = 1; i <= nums[beginIndex]; i++){
            int nextIndex = beginIndex + i;
            res = canJumpHelper(nums, nextIndex);
            if (res) return true;
        }
        mem[beginIndex] = res ? 1 : 0;
        return res;
    }
}
```



Approach 2:   greedy O(n)

```java
class Solution {
    public boolean canJump(int[] nums) {
        int pos = nums.length - 1;
        for(int i = nums.length - 1; i >= 0; i--)
            if (nums[i] + i >= pos)
                pos = i;
        return pos == 0;
    }
}
```


## 56. Merge Intervals
**solution**
Approach : Sorting

- Time complexity:  O(nlogn)
- Space complexity: O(1) or O(n)(depends on the sort algorithm)

```java
class Solution {
    public int[][] merge(int[][] intervals) {
        if (intervals.length == 0) return new int[0][0];
        Arrays.sort(intervals, (o1, o2) -> (o1[0] - o2[0]));
        int min = intervals[0][0];
        int max = intervals[0][1];
        List<int[]> reslist = new ArrayList<>();
        for(int i = 1; i < intervals.length; i++){
            if (intervals[i][0] <= max){
                max = Math.max(max, intervals[i][1]);
            }else{
                reslist.add(new int[]{min, max});
                min = intervals[i][0];
                max = intervals[i][1];
            }
        }
        //remember to add the last interval!!
        reslist.add(new int[]{min, max});
        return reslist.toArray(new int[0][0]);
    }
}
```

## 62. Unique Paths
**solution**
Approach 1: DP

- Time complexity: O(mn)

```java
class Solution {
    public int uniquePaths(int m, int n) {
        int[][] dp = new int[m][n];
        for(int i = 0; i < m; i++)
            dp[i][0] = 1;
        for(int j = 0; j < n; j++)
            dp[0][j] = 1;
        for(int i = 1; i < m; i++)
            for(int j = 1; j < n; j++)
                dp[i][j] = dp[i - 1][j] + dp[i][j - 1];
        return dp[m - 1][n - 1];
    }
    
}
```

Approach 2: Math    
result is (m + n - 2) C(n - 1)

```java
class Solution {
    public int uniquePaths(int m, int n) {
        return combine(m + n - 2, n - 1);
    }
    
    private int combine(int n, int m){
        if (m == 0) return 1;
        //avoid overflow
        m = Math.min(m , n - m);
        long a = 1, b = 1;
        for(int i = 0; i < m; i++){
            a *= (n - i);
            b *= (i + 1);
        }
        return (int) (a / b);
    }
    
   
}
```

## 70. Climbing Stairs
**solution**
Approach: DP

- Time complexity: O(n)
- Space complexity: O(1)

```java
class Solution {
    public int climbStairs(int n) {
    //f[0] = 1!!
    //pre1 is 1 step before, pre2 is 2 steps before
        int pre1 = 1, pre2 = 1;
        int res = 1;
        for(int i = 2; i < n + 1; i++){
            res = pre1 + pre2;
            pre2 = pre1;
            pre1 = res;
        }
        return res;
        
    }
    
}
```



## 64. Minimum Path Sum
**solution**
Approach: DP

```java
class Solution {
    public int minPathSum(int[][] grid) {
        int m = grid.length;
        if (m == 0) return 0;
        int n = grid[0].length;
        int[][] dp = new int[m][n];
        dp[0][0] = grid[0][0];
        for(int i = 1; i < m; i++)
            dp[i][0] = dp[i - 1][0] + grid[i][0];
        for(int j = 1; j < n; j++)
            dp[0][j] = dp[0][j - 1] + grid[0][j];
        for(int i = 1; i < m; i++)
            for(int j = 1; j < n; j++)
                dp[i][j] = Math.min(dp[i][j - 1], dp[i - 1][j]) + grid[i][j];
        return dp[m - 1][n - 1];
    }
    
}
```

## 72. Edit Distance
**solution** 
Approach: DP

- Time complexity: O(mn), where m, n is length of word1, word2
- Space complexity: O(mn)

```java
class Solution {
    public int minDistance(String word1, String word2) {
        int l1 = word1.length(), l2 = word2.length();
        //add an empty char in the begin
        int[][] dp = new int[l1 + 1][l2 + 1];
        for(int i = 0; i < l1 + 1; i++)
            dp[i][0] = i;
        for(int j = 0; j < l2 + 1; j++)
            dp[0][j] = j;
        for(int i = 1; i < l1 + 1; i++){
            for(int j = 1; j < l2 + 1; j++){
                if (word1.charAt(i - 1) == word2.charAt(j - 1))
                //we can prove in this case, dp[i - 1][j - 1] <= dp[i][j - 1] + 1
                //and dp[i - 1][j - 1] <= dp[i - 1][j] + 1
                    dp[i][j] = dp[i - 1][j - 1];
                else
                    dp[i][j] = Math.min(dp[i][j - 1],
                        Math.min(dp[i - 1][j], dp[i - 1][j - 1])) + 1;
            }
        }
        return dp[l1][l2];
    }
}
```

- Time complexity: O(mn)
- Space complexity: O(n)

```java
class Solution {
    public int minDistance(String word1, String word2) {
        int l1 = word1.length(), l2 = word2.length();
        //add an empty char in the begin
        int[] pre = new int[l2 + 1];
        int[] cur = new int[l2 + 1];
        for(int j = 0; j < l2 + 1; j++)
            pre[j] = j;
        for(int i = 1; i < l1 + 1; i++){
            cur[0] = i;
            for(int j = 1; j < l2 + 1; j++){
                if (word1.charAt(i - 1) == word2.charAt(j - 1))
               
                    cur[j] = pre[j - 1];
                else
                    cur[j] = Math.min(cur[j - 1],
                            Math.min(pre[j], pre[j - 1])) + 1;
            }
            int[] tmp = cur;
            cur = pre;
            pre = tmp;
                                      
        }
        return pre[l2];
    }
}
```

## 75. Sort Colors

**solution**
use double pointers

```java
class Solution {
    public void sortColors(int[] nums) {
        //double pointers
        //left points to the next position of the right bound of '0'
        //right points to the previous position of the left bound of '2'
        int left = 0, right = nums.length - 1;
        for(int i = 0; i <= right; i++){
            if (nums[i] == 0)
                swap(nums, left++, i);
            else if (nums[i] == 2)
                swap(nums, i--, right--);
        }
    }
    public void swap(int[] nums, int i, int j){
        int tmp = nums[i];
        nums[i] = nums[j];
        nums[j] = tmp;
    }
}
```


## 76.  Minimum Window Substring

```java
class Solution {
    public String minWindow(String s, String t) {
        
        if (s.isEmpty() || t.isEmpty()) return "";
        
        char[] chs = s.toCharArray();
        char[] cht = t.toCharArray();
        
        int[] dictT = new int[128];
        int[] window = new int[128];
        for(char c : cht)
            dictT[c]++;
        
        //start, end初始值为0以及最后结果[start, end)
        //应对start, end在循环中从未更新的情况如s = "a", t = "aa"
        //min也不可少否则只有start和end两者差为0无法更新最小值
        int l = 0, r = 0, start = 0, end = 0;
        int min = Integer.MAX_VALUE;
        int required = cht.length;
        //formed 代表窗口里面所有在T中出现的字符数量
        //(每个字符数量小于等于T中数量,若超过则按T中数量算)的总和
        int formed = 0;
        
        while(r < chs.length){
            if (window[chs[r]]++ < dictT[chs[r]])
                formed++;
            while(l <= r && formed == required){
                if (r - l + 1 < min){
                    start = l;
                    end = r + 1;
                    min = r - l + 1;
                }
                if (window[chs[l]]-- <= dictT[chs[l]])
                    formed--;
                l++;
            }
            r++;
        }
        
        return s.substring(start, end);
        
    }
}
```
## 78. Subsets
**solution**
Approach 1: Lexicographic (Binary Sorted) Subsets

- Time complexity: O(n * 2^n)
- Space complexity: O(n * 2 ^ n)

```java
class Solution {
    public List<List<Integer>> subsets(int[] nums) {
        
        List<List<Integer>> res = new ArrayList<>();
        int n = nums.length;
        
        for(int i = (int) Math.pow(2, n); i < (int) Math.pow(2, n + 1); i++){
            String bitmask = Integer.toBinaryString(i).substring(1);
            List<Integer> subset = new ArrayList<>();
            for(int j = 0; j < n; j++)
                if (bitmask.charAt(j) == '1')
                    subset.add(nums[j]);
            res.add(subset);
        }
        return res;
    }
}
```

Approach 2 : Backtracking

- Time complexity: O(n * 2^n)
- Space complexity: O(n * 2 ^ n)

```java
class Solution {
    public List<List<Integer>> subsets(int[] nums) {
        
        List<List<Integer>> res = new ArrayList<>();
        backtrack(res, new ArrayList<>(), 0, nums);
        return res;
    }
    
    private void backtrack(List<List<Integer>> res,
                List<Integer> subset, int begin, int[] nums){
        res.add(new ArrayList<>(subset));
        for(int i = begin; i < nums.length; i++){
            subset.add(nums[i]);
            backtrack(res, subset, i + 1, nums);
            subset.remove(subset.size() - 1);
        }
    }
}
```

## 79. Word Search

**solution**
DFS

```java
class Solution {
   
    public boolean exist(char[][] board, String word) {
        
        for(int i = 0; i < board.length; i++){
            for(int j = 0; j < board[0].length; j++){
                boolean tmp = dfs(board, word, 0, i, j);
                if (tmp) return true;
            }
        }
        return false;
    }
    
    private boolean dfs(char[][] board, String word, int begin,
                         int i, int j){
        //begin == word.length() must be in front of 
        //the next 'if' statement, consider baord = [['a']]
        //and word = "a"
        if (begin == word.length()) return true;
        if (i < 0 || i >= board.length
           || j < 0 || j >= board[0].length
           || word.charAt(begin) != board[i][j]) return false;
        
        char tmp = board[i][j];
        //use board itself to mark used
        //we mark board[i][j] instead of the next position
        //or the result will be always false
        board[i][j] = ' ';
        boolean res = dfs(board, word, begin + 1, i - 1, j)
            || dfs(board, word, begin + 1, i + 1, j)
            || dfs(board, word, begin + 1, i, j - 1)
            || dfs(board, word, begin + 1, i, j + 1);
        board[i][j] = tmp;
        return res;
    }
 
}
```

## 84. Largest Rectangle in Histogram
**solution**
Approach 1
use extra array

```java
class Solution {
    public int largestRectangleArea(int[] heights) {
         if (heights == null || heights.length == 0) 
             return 0;
        
        //first index of the bar the left less than the current bar
        int[] lessFromLeft = new int[heights.length];
        //similarily
        int[] lessFromRight = new int[heights.length];
        lessFromLeft[0] = -1;
        lessFromRight[heights.length - 1] = heights.length;
        for(int i = 1; i < heights.length; i++){
            int p = i - 1;
            while(p >= 0 && heights[p] >= heights[i])
                p = lessFromLeft[p];
            lessFromLeft[i] = p;
        }
        for(int i = heights.length - 2; i >= 0; i--){
            int p = i + 1;
            while(p < heights.length && heights[p] >= heights[i])
                p = lessFromRight[p];
            lessFromRight[i] = p;
        }
        int maxArea = 0;
        for(int i = 0; i < heights.length; i++)
            maxArea = Math.max(maxArea, heights[i] * (lessFromRight[i] - lessFromLeft[i] - 1));
        return maxArea;
    }
}
```

Approach 2
use increasing stack

```java
class Solution {
    public int largestRectangleArea(int[] heights) {
        Stack<Integer> stack = new Stack<>();
        int res = 0;
        // i <= heights.length !!!
        for(int i = 0; i <= heights.length; i++){
            int h = (i == heights.length ? 0 : heights[i]);
            if (stack.isEmpty() || h >= heights[stack.peek()]){
                stack.push(i);
            }else{
                int tp = stack.pop();
                res = Math.max(res, 
                    heights[tp] * (stack.isEmpty() ? i : i - stack.peek() - 1));
                i--;
            }
        }
        return res;
    }
}
```

## 85. Maximal Rectangle
**solution**
Approach: Based on problem 84

```java
class Solution {
    public int maximalRectangle(char[][] matrix) {
        // regard as m 'Largest Rectangle in Histogram' problem
        int m = matrix.length;
        if (m == 0) return 0;
        int n = matrix[0].length;
        if (n == 0) return 0;
        int[] heights = new int[n];
        int res = 0;
        for(int i = 0; i < m; i++){
            for(int j = 0; j < n; j++)
                heights[j] = matrix[i][j] == '0' ? 0 : heights[j] + 1;
            res = Math.max(res, largestRectangleArea(heights));
        }
        return res;
    }
    private int largestRectangleArea(int[] heights) {
         if (heights == null || heights.length == 0) 
             return 0;
        
        //first index of the bar the left less than the current bar
        int[] lessFromLeft = new int[heights.length];
        //similarily
        int[] lessFromRight = new int[heights.length];
        lessFromLeft[0] = -1;
        lessFromRight[heights.length - 1] = heights.length;
        for(int i = 1; i < heights.length; i++){
            int p = i - 1;
            while(p >= 0 && heights[p] >= heights[i])
                p = lessFromLeft[p];
            lessFromLeft[i] = p;
        }
        for(int i = heights.length - 2; i >= 0; i--){
            int p = i + 1;
            while(p < heights.length && heights[p] >= heights[i])
                p = lessFromRight[p];
            lessFromRight[i] = p;
        }
        int maxArea = 0;
        for(int i = 0; i < heights.length; i++)
            maxArea = Math.max(maxArea, heights[i] * (lessFromRight[i] - lessFromLeft[i] - 1));
        return maxArea;
    }
}
```

## 94. Binary Tree Inorder Traversal

Approach 1: Recursive Approach

- Time complexity: O(n)
- Space complexity: worst : O(n), average: O(logn)

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        traversal(res, root);
        return res;
    }
    
    private void traversal(List<Integer> res, TreeNode root){
        if (root == null) return;
        traversal(res, root.left);
        res.add(root.val);
        traversal(res, root.right);
    }
}
```

Approach 2: Iterating method using Stack

- Time complexity: O(n)
- Space complexity: worst : O(n), average: O(logn)

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        Stack<TreeNode> stack = new Stack<>();
        TreeNode cur = root;
        while(cur != null || !stack.isEmpty()){
            while(cur != null){
                stack.push(cur);
                cur = cur.left;
            }
            cur = stack.pop();
            res.add(cur.val);
            cur = cur.right;
        }
        return res;
    }
    
    
}
```

## 96. Unique Binary Search Trees

```java
class Solution {
    
    //f(n) = f(0)f(n - 1) + f(1)f(n - 2) + ... + f(n - 2)f(1) + f(n - 1)f(0)
    //f(0) = f(1)= 1, f(k)f(n - k - 1) --> k + 1 is root
    public int numTrees(int n) {
        int[] dp = new int[n + 1];
        dp[0] = dp[1] = 1;
        for(int i = 2; i < n + 1; i++)
            for(int j = 0; j < i; j++)
                dp[i] += dp[j] * dp[i - j - 1];
        return dp[n];
    }
   
}
```

## 98. Validate Binary Search Tree

**solution**
Approach 1: Recursion

- Time Complexity : O(n)
- Space Complexity: O(n)

```java
class Solution {
    private boolean res;
    public boolean isValidBST(TreeNode root) {
        return helper(root, null, null);
    }
    //use integer instead of int to avoid root.val = INT_MAX OR INT_MIN
    private boolean helper(TreeNode root, Integer lower, Integer upper){
        if (root == null) return true;
        if (lower != null && root.val <= lower) return false;
        if (upper != null && root.val >= upper) return false;
        
        return helper(root.left, lower, root.val)
            && helper(root.right, root.val, upper);
    }
    
    
}
```

Approach 2: Inorder traversal

- Time Complexity : O(n)
- Space Complexity: O(n)

```java
class Solution {
    private boolean res;
    public boolean isValidBST(TreeNode root) {
        Stack<TreeNode> stack = new Stack<>();
        TreeNode cur = root;
        //use long instead of int to avoid root.val = INT_MAX OR INT_MIN
        long pre = Long.MIN_VALUE;
        while(cur != null || !stack.isEmpty()){
            while(cur != null){
                stack.push(cur);
                cur = cur.left;
            }
            cur = stack.pop();
            if (cur.val <= pre) return false;
            pre = cur.val;
            cur = cur.right;
            
        }
        return true;
        
    }
    
    
}
```



## 101. Symmetric Tree

**solution**

```java
class Solution {
    //Symmetric Tree  ==>  the tree is the mirror ofitself
    public boolean isSymmetric(TreeNode root) {
        return isMirror(root, root);
    }
    
    private boolean isMirror(TreeNode t1, TreeNode t2){
        if (t1 == null && t2 == null) return true;
        if (t1 == null || t2 == null) return false;
        
        return t1.val == t2.val && isMirror(t1.left, t2.right)
            && isMirror(t1.right, t2.left);
    }
}
```
## 102. Binary Tree Level Order Traversal

**solution**

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
import java.util.*;
class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> res = new ArrayList<>();
        if (root == null) return res; 
        Queue<TreeNode> queue = new LinkedList<>();
        queue.add(root);
        
        while(!queue.isEmpty()){
            int size = queue.size();
            List<Integer> level = new ArrayList<>();
            for(int i = 0; i < size; i++){
                TreeNode node = queue.poll();
                level.add(node.val);
                if (node.left != null) queue.offer(node.left);
                if (node.right != null) queue.offer(node.right);
            }
            res.add(level);
        }
        return res;
    }
}
```

## 104. Maximum Depth of Binary Tree

**solution**
Approach1 : Recursion

```java
import java.util.*;

class Solution {
    public int maxDepth(TreeNode root) {
        if (root == null) return 0;
        return 1 + Math.max(maxDepth(root.left), maxDepth(root.right));
    }
}
```
Apoproach2: Iteration

```java
import java.util.*;

class Solution {
    public int maxDepth(TreeNode root) {
        if (root == null) return 0;
        Queue<TreeNode> queue = new LinkedList<TreeNode>();
        queue.offer(root);
        int depth = 0;
        while(!queue.isEmpty()){
            int size = queue.size();
            for(int i = 0; i < size; i++){
                TreeNode node = queue.poll();
                if (node.left != null)
                    queue.offer(node.left);
                if (node.right != null)
                    queue.offer(node.right);
            }
            depth++;
        }
        return depth;
        
    }
}
```
## 105. Construct Binary Tree from Preorder and Inorder Traversal
**solution**

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public TreeNode buildTree(int[] preorder, int[] inorder) {
        Map<Integer, Integer> inMap = new HashMap<>();
        for(int i = 0; i < inorder.length; i++)
            inMap.put(inorder[i], i);
        return helper(inMap, preorder, inorder, 0, 0, preorder.length);
    }
    
    private TreeNode helper(Map<Integer, Integer> inMap, 
                            int[] preorder, int[] inorder,
                int preStart, int inStart, int length){
        if (length == 0) return null;
        int rootval = preorder[preStart];
        TreeNode root = new TreeNode(rootval);
        //use map instaed of scan to find the position of root
        int idx = inMap.get(rootval);
        
        root.left = helper(inMap, preorder, inorder,
                           preStart + 1, inStart, idx - inStart);
        root.right = helper(inMap, preorder, inorder,
                           preStart + idx - inStart + 1,
                        idx + 1, length - idx + inStart - 1);
        return root;
    }
    
}
```

## 114. Flatten Binary Tree to Linked List
**solution**
Approach 1

- Time Complexity: O(n^2)

```java
class Solution {
    public void flatten(TreeNode root) {
        if(root == null) return;
        TreeNode left = root.left;
        TreeNode right = root.right;
        flatten(left);
        flatten(right);
        root.right = left;
        root.left = null;
        TreeNode ptr = root;
        while(ptr.right != null)
            ptr = ptr.right;
        ptr.right = right;
        
    }
    
}
```

Approach 2

- Time Complexity: O(n)

```
    1
   / \
  2   5
 / \   \
3   4   6
-----------        
pre = 5
cur = 4

    1
   / \
  2   \
 / \  |
3   4 |
     \|
      5
       \
        6
-----------        
pre = 4
cur = 3

    1
   / \
  2  |
 /|  | 
3 |  |
 \|  |
  4  |
   \ |
    5
     \
      6
-----------        
cur = 2
pre = 3

    1
   / \
  2   \
   \   \
    3   \
     \  |
      4 |
       \|
        5
         \
          6
-----------        
cur = 1
pre = 2

1
 \
  2
   \
    3
     \
      4
       \
        5
         \
          6
```

```java
class Solution {
    private TreeNode prev = null;
    public void flatten(TreeNode root) {
        if (root == null) return;
        flatten(root.right);
        flatten(root.left);
        root.right = prev;
        root.left = null;
        prev = root;
    }
    
}
```
## 121. Best Time to Buy and Sell Stock


**solution**

```java
class Solution {
    public int maxProfit(int[] prices) {
        int res = 0;
        int p = 0;
        for(int i = 0; i < prices.length; i++){
            res = Math.max(res, prices[i] - prices[p]);
            if (prices[i] < prices[p])
                p = i;
        }
        return res;
    }
}

----------or---------------------

class Solution {
    public int maxProfit(int[] prices) {
        int res = 0;
        int min = Integer.MAX_VALUE;
        for(int i = 0; i < prices.length; i++){
            res = Math.max(res, prices[i] - min);
            if (prices[i] < min)
                min = prices[i];
        }
        return res;
    }
}
```

## 124. Binary Tree Maximum Path Sum

**solution**

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    private int max;
    public int maxPathSum(TreeNode root) {
        max = Integer.MIN_VALUE;
        travel(root);
        return max;
    }
    private int travel(TreeNode root){
        if (root == null) return 0;
        int leftVal = travel(root.left);
        int rightVal = travel(root.right);
        int maxRoot = Math.max(root.val, root.val + leftVal);
        maxRoot = Math.max(maxRoot, root.val + rightVal);
        maxRoot = Math.max(maxRoot, root.val + rightVal + leftVal);
        
        if (maxRoot > max)
            max = maxRoot;
        return Math.max(root.val, Math.max(root.val + leftVal, root.val + rightVal));
    }
}

-----------------or------------------------------
class Solution {
    private int max;
    public int maxPathSum(TreeNode root) {
        max = Integer.MIN_VALUE;
        travel(root);
        return max;
    }
    private int travel(TreeNode root){
        if (root == null) return 0;
        int left = Math.max(0, travel(root.left));
        int right = Math.max(0, travel(root.right));
        max = Math.max(max, root.val + left + right);
        return root.val + Math.max(left, right);
        
        
    }
}
```

## 128. Longest Consecutive Sequence
**solution**
Approach:  HashSet

- Time Complexity : O(n)
- Space Complexity: O(n)

```java
class Solution {
    public int longestConsecutive(int[] nums) {
        Set<Integer> set = new HashSet<>();
        for(int num : nums)
            set.add(num);
        int res = 0;
        for(int num : nums){
            if (!set.contains(num - 1)){
                int len = 0;
                int cur = num;
                while(set.contains(cur)){
                    len++;
                    cur++;
                }
                res = Math.max(res, len);    
            }
            
        }
        return res;
    }
}
```
## 136. Single Number
**solution**
Approach: XOR

```java
class Solution {
    public int singleNumber(int[] nums) {
        int res = 0;
        for(int num : nums)
            res ^= num;
        return res;
    }
}
```

## 138. Copy List with Random Pointer
**solution**

Approach : 
insert copy node into the gap of two adjacent node,
then set the random pointer of the copy node, 
then get the result and recover the original list.

- Time complexity: O(n)
- Space complexity: O(1)

```java
/*
// Definition for a Node.
class Node {
    int val;
    Node next;
    Node random;

    public Node(int val) {
        this.val = val;
        this.next = null;
        this.random = null;
    }
}
*/
import java.util.*;
class Solution {
    public Node copyRandomList(Node head) {
        Node dummy = new Node(0);
        Node headptr = head;
        while(headptr != null){
            Node next = headptr.next;
            headptr.next = new Node(headptr.val);
            headptr.next.next = next;
            headptr = next;
        }
        headptr = head;
        while(headptr != null){
            Node random = headptr.random;
            headptr.next.random = random == null ? null : random.next;
            headptr = headptr.next.next;
        }
        headptr = head;
        Node resptr = dummy;
        while(headptr != null){
            
            resptr.next = headptr.next;
            headptr.next = headptr.next.next;
            headptr = headptr.next;
            resptr = resptr.next;
        }
        return dummy.next;
        
    }
}
```

## 139. Word Break

**solution**

Approach:  Top-down DP


```java
class Solution {
    
    public boolean wordBreak(String s, List<String> wordDict) {
        int[] memo = new int[s.length()];
        Arrays.fill(memo, -1);
        return helper(s, 0, wordDict, memo);
    }
    
    private boolean helper(String s, int begin,
                           List<String> wordDict,
                           int[] memo){
        if (begin == s.length()) return true;
        if (memo[begin] != -1) return memo[begin] == 1;
        
        boolean res = false;
        for(int i = 0; i < wordDict.size(); i++){
            if (s.startsWith(wordDict.get(i), begin)){
                res = res || 
                helper(s, begin + wordDict.get(i).length(),
                       wordDict, memo);
                if (res) break;
            }
            
        }
        memo[begin] = res ? 1 : 0;
        return res;
}
    
   
}
```

## 141. Linked List Cycle

Approach: fast and slow pointer

```java
/**
 * Definition for singly-linked list.
 * class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) {
 *         val = x;
 *         next = null;
 *     }
 * }
 */
public class Solution {
    public boolean hasCycle(ListNode head) {
        ListNode fast = head,slow = head;
        do{
            if (fast == null || fast.next == null)
                return false;
            fast = fast.next.next;
            slow = slow.next;
        }while(fast != slow);
        return true;
    }
}
```

## 146. LRU Cache
**solution**
Approach: HashMap + Double LinkedList
(regard map and linkedlist as independent structure)

```java
class LRUCache {
    class Node{
        int key;
        int value;
        Node next;
        Node pre;
        Node(int key, int value){
            this.key = key;
            this.value = value;
        }
        Node(){
            this(0, 0);
        }
    }
    private int capacity;
    private int size;
    //dummy node, not in map
    //head end is the newest
    private Node head;
    private Node tail;
    private Map<Integer, Node> map;
    public LRUCache(int capacity) {
        head = new Node();
        tail = new Node();
        head.next = tail;
        tail.pre = head;
        this.capacity = capacity;
        this.size = 0;
        map = new HashMap<>();
    }
    
    public int get(int key) {
        Node node = map.get(key);
        if (node == null)
            return -1;
        
        update(node);
        return node.value;
    }
    
    public void put(int key, int value) {
        Node node = map.get(key);
        if (node != null){
            node.value = value;
            update(node);
        }
        else{
            node = new Node(key, value);
            map.put(key, node);
            add(node);
            this.size++;
        }
        //First add, then decide to remove or not;
        if (this.size > capacity){
            Node toDel = tail.pre;
            //attention: tail.pre will be changed 
            //after remove,
            //so remove(tail.pre) then map.remove(tail.pre.key)
            //is wrong!
            remove(toDel);
            map.remove(toDel.key);
            this.size--;
        }
    }
    
    private void update(Node node){
        remove(node);
        add(node);
    }
    
    private void add(Node node){
        Node tmp = head.next;
        node.pre = head;
        node.next = tmp;
        head.next = node;
        tmp.pre = node;
    }
    private void remove(Node node){
        node.pre.next = node.next;
        node.next.pre = node.pre;
        node.pre = null;
        node.next = null;
    }
}

/**
 * Your LRUCache object will be instantiated and called as such:
 * LRUCache obj = new LRUCache(capacity);
 * int param_1 = obj.get(key);
 * obj.put(key,value);
 */
```

## 148. Sort List
**solution**
Approach: merge sort

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public ListNode sortList(ListNode head) {
        //divide the list as average as possible
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
}

-------or-------------------
class Solution {
    public ListNode sortList(ListNode head) {
        //head.next == null: avoid infinite loop
        //example list is: [1]
        if (head == null || head.next == null) return head;
        //fast = head.next(not head): avoid infinite loop
        //example list is: [1, 2]
        //two part should have same number of nodes if the length is even
        ListNode fast = head.next, slow = head;
        while(fast != null && fast.next != null){
            fast = fast.next.next;
            slow = slow.next;
        }
        ListNode l1 = head, l2 = slow.next;
        slow.next = null;
        l1 = sortList(l1);
        l2 = sortList(l2);
        return merge(l1, l2);
    }
}
```

## 152. Maximum Product Subarray
**solution**
Approach: DP
max[i] = max(nums[i], nums[i] * max[i - 1], nums[i] * min[i - 1])
min[i] = min(nums[i], nums[i] * max[i - 1], nums[i] * min[i - 1])
max[i] represents the max product of the array which ends with nums[i]
same for min[i]
res = max(max[i]) for i >= 0 and i < n

```java
class Solution {
    public int maxProduct(int[] nums) {
        int res = Integer.MIN_VALUE;
        int maxPro = 1, minPro = 1;
        for(int num : nums){
            int maxProSave = maxPro;
            maxPro = Math.max(num, 
                              Math.max(num * maxPro, num * minPro));
            minPro = Math.min(num, 
                              Math.min(num * maxProSave, num * minPro));
            res = Math.max(res, maxPro);
        }
        return res;
    }
}
```
## 155. Min Stack

```java
class MinStack {

    /** initialize your data structure here. */
    class Node{
        int val;
        int min;
        Node(int val, int min){
            this.val = val;
            this.min = min;
        }
    }
    private Stack<Node> stack;
    public MinStack() {
        stack = new Stack<>();
    }
    
    public void push(int x) {
        
        if (stack.isEmpty())
            stack.push(new Node(x, x));
        else
            stack.push(new Node(x, Math.min(x, stack.peek().min)));
            
    }
    
    public void pop() {
        stack.pop();
    }
    
    public int top() {
        return stack.peek().val;
    }
    
    public int getMin() {
        return stack.peek().min;
    }
}

/**
 * Your MinStack object will be instantiated and called as such:
 * MinStack obj = new MinStack();
 * obj.push(x);
 * obj.pop();
 * int param_3 = obj.top();
 * int param_4 = obj.getMin();
 */
```

## 160. Intersection of Two Linked Lists
**solution**
Approach: Two Pointers

- Time complexity: O(m + n)
- Space complexity: O(1)

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) {
 *         val = x;
 *         next = null;
 *     }
 * }
 */
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        ListNode pA = headA, pB = headB;
        while(pA != pB){
            pA = pA == null ? headB : pA.next;
            pB = pB == null ? headA : pB.next;
        }
        return pA;
    }
}
```
## 169. Majority Element
**solution**

Approach: Boyer-Moore Voting Algorithm

- Time complexity: O(n)
- Space complexity: O(1)

```java
class Solution {
    public int majorityElement(int[] nums) {
        //any initial value is ok
        //because it will be always set to the first element
        //after the first loop
        int candidate = 0;
        int count = 0;
        for(int num : nums){
            if (count == 0)
                candidate = num;
            count += (candidate == num) ? 1 : -1;
        }
        return candidate;
    }
}
```
## 198. House Robber
**solution**
Approach: Top-down DP

- Time complexity: O(n)
- Space complexity: O(n)

```java
class Solution {
    public int rob(int[] nums) {
        int memo[] = new int[nums.length];
        Arrays.fill(memo, -1);
        return dp(0, nums, memo);
    }
    private int dp(int begin, int[] nums, int[] memo){
        if (begin >= nums.length) return 0;
        if (memo[begin] != -1) return memo[begin];
        
        int res = Math.max(nums[begin] + dp(begin + 2, nums, memo),
                          dp(begin + 1, nums, memo));
        memo[begin] = res;
        return res;
        
    }
}
```

## 200. Number of Islands
**solution**
Approach: DFS

```java
class Solution {
    public int numIslands(char[][] grid) {
        int res = 0; 
        if (grid.length == 0) return res;
        for(int i = 0; i < grid.length; i++){
            for(int j = 0; j < grid[0].length; j++){
                if (grid[i][j] == '1'){
                    ++res;
                    dfs(grid, i, j);
                }
            }
        }
        return res;
    }
    
    private void dfs(char[][] grid, int i, int j){
        if (i < 0 || i >= grid.length 
           || j < 0 || j >= grid[0].length
           || grid[i][j] != '1')
            return;
        grid[i][j] = '0';
        dfs(grid, i - 1, j);
        dfs(grid, i + 1, j);
        dfs(grid, i, j - 1);
        dfs(grid, i, j + 1);
    }
}
```

## 206. Reverse Linked List
**solution**

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode reverseList(ListNode head) {
        ListNode prev = null, curr = head;
        while(curr != null){
            ListNode next = curr.next;
            curr.next = prev;
            prev = curr;
            curr = next;
        }
        return prev;
    }
}
```

## 207. Course Schedule
**solution**
Approach: Topological Sort

```java
import java.util.*;
class Solution {
    public boolean canFinish(int numCourses, int[][] prerequisites) {
        Map<Integer, List<Integer>> edge = new HashMap<>();
        int[] inDegree = new int[numCourses];
        for(int i = 0; i < numCourses; i++)
                edge.put(i, new ArrayList<>());
                
        for(int[] pre : prerequisites){
            edge.get(pre[1]).add(pre[0])
            inDegree[pre[0]]++;
        }
        
        return topologySort(edge, inDegree);
    }
    
    private boolean topologySort(Map<Integer, List<Integer>> edge, int[] inDegree){
        int n = inDegree.length;
        Queue<Integer> queue = new LinkedList<>();
        int count = 0;
        for(int i = 0; i < n; i++)
            if(inDegree[i] == 0)
                queue.offer(i);
        while(!queue.isEmpty()){
            int node = queue.poll();
            count++;
            for(Integer adj : edge.get(node)){
                inDegree[adj]--;
                if(inDegree[adj] == 0)
                    queue.add(adj);
            }
        }
        return count == n;
    }
}

//------------------------------or-------------------------------------

class Solution {
    public boolean canFinish(int numCourses, int[][] prerequisites) {
        //use array instead of map. Attention:
        //List<Integer>[] adjs = new ArrayList<Integer>[numCourses];
        //or = new ArrayList<>[numCourses] is wrong(generic array creation).
        List<Integer>[] adjs = new ArrayList[numCourses];
        int[] inDegree = new int[numCourses];
        for(int i = 0; i < numCourses; i++)
            adjs[i] = new ArrayList<>();
        for(int[] prereq : prerequisites){
            inDegree[prereq[0]]++;
            adjs[prereq[1]].add(prereq[0]);
        }
        return topologicalSort(adjs, inDegree);
            
    }
    
    private boolean topologicalSort(List<Integer>[] adjs,
                                    int[] inDegree){
        int n = inDegree.length;
        Queue<Integer> queue = new LinkedList<>();
        for(int i = 0; i < n; i++)
            if (inDegree[i] == 0)
                queue.offer(i);
        int count = 0;
        while(!queue.isEmpty()){
            int node = queue.poll();
            count++;
            for(int adj : adjs[node]){
                inDegree[adj]--;
                if (inDegree[adj] == 0)
                    queue.offer(adj);
            }
        }
        return count == n;
    }
}
```

## 208. Implement Trie (Prefix Tree)
**solution**

```java
class Trie {

    class TrieNode{
        public boolean isWord;
        public TrieNode[] children;
        public TrieNode(){
            children = new TrieNode[26];
        }
    }
    /** Initialize your data structure here. */
    private TrieNode root;
    public Trie() {
        root = new TrieNode();
    }
    
    /** Inserts a word into the trie. */
    public void insert(String word) {
        TrieNode cur = root;
        for(char ch : word.toCharArray()){
            if (cur.children[ch - 'a'] == null)
                cur.children[ch - 'a'] = new TrieNode();
            cur = cur.children[ch - 'a'];
        }
        cur.isWord = true;
    }
    
    /** Returns if the word is in the trie. */
    public boolean search(String word) {
        TrieNode cur = root;
        for(char ch : word.toCharArray()){
            if (cur.children[ch - 'a'] == null)
                return false;
            cur = cur.children[ch - 'a'];
        }
        return cur.isWord;
    }
    
    /** Returns if there is any word in the trie that starts with the given prefix. */
    public boolean startsWith(String prefix) {
        TrieNode cur = root;
        for(char ch : prefix.toCharArray()){
            if (cur.children[ch - 'a'] == null)
                return false;
            cur = cur.children[ch - 'a'];
        }
        return true;
    }
}

/**
 * Your Trie object will be instantiated and called as such:
 * Trie obj = new Trie();
 * obj.insert(word);
 * boolean param_2 = obj.search(word);
 * boolean param_3 = obj.startsWith(prefix);
 */
```

## 212. Word Search II
**solution**
Approach: Trie (a brute force way is invoking word Search I for every word)

```java
class Solution {
    
    public List<String> findWords(char[][] board, String[] words) {
        List<String> res = new ArrayList<>();
        TrieNode root = buildTrie(words);
        for(int i = 0; i < board.length; i++)
            for(int j = 0; j < board[0].length; j++)
                dfs(board, i, j, root, res);
        return res;
    }
    //we can regard trie as a special string(compare to word search I)
    private void dfs(char[][] board, int i, int j,
                     TrieNode root, List<String> res){
        if (root.word != null){
            res.add(root.word);
            //avoid duplicate
            root.word = null;
        }
        if (i < 0 || i >= board.length
            || j < 0 || j >= board[0].length 
            || board[i][j] == ' ' 
            || root.next[board[i][j] - 'a'] == null)
            return;
        char ch = board[i][j];
        board[i][j] = ' ';
        dfs(board, i - 1, j, root.next[ch - 'a'], res);
        dfs(board, i + 1, j, root.next[ch - 'a'], res);
        dfs(board, i, j - 1, root.next[ch - 'a'], res);
        dfs(board, i, j + 1, root.next[ch - 'a'], res);
        board[i][j] = ch;
        
            
    }
    
    class TrieNode{
        TrieNode[] next;
        String word;
        public TrieNode(){
            next = new TrieNode[26];
        }
    }
    private TrieNode buildTrie(String[] words){
        TrieNode root = new TrieNode();
        for(String word : words){
            TrieNode cur = root;
            for(char ch : word.toCharArray()){
                if (cur.next[ch - 'a'] == null)
                    cur.next[ch - 'a'] = new TrieNode();
                cur = cur.next[ch - 'a'];
            }
            cur.word = word;
        }
        return root;
    }
        
        
}
```
## 215. Kth Largest Element in an Array
Approach 1: Sort

- Time complexity: O(nlogn)
- Space complexity: O(1)
```java
class Solution {
    public int findKthLargest(int[] nums, int k) {
        Arrays.sort(nums);
        return nums[nums.length - k];
    }
}
```

Approach 2: Heap Sort

- Time complexity: O(nlogk)
- Space complexity: O(k)
```java
class Solution {
    public int findKthLargest(int[] nums, int k) {
        PriorityQueue<Integer> pq = new PriorityQueue<>();
        for(int num : nums){
            pq.offer(num);
            if (pq.size() > k)
                pq.poll();
        }
        return pq.peek();
    }
}
```


Approach 3: Quick select

- Time complexity: Best O(n), Worst O(n^2)
- Space complexity: O(1)

```java
class Solution {
    public int findKthLargest(int[] nums, int k) {
        return quickSelect(nums, nums.length - k + 1, 0,
                           nums.length - 1);
    }
    
    //find kth smallest
    private int quickSelect(int[] nums, int k, int l, int r){
        int index = partation(nums, l, r);
        if (l + k - 1 == index)
            return nums[index];
        else if (l + k - 1 > index)
            return quickSelect(nums, k + l - index - 1, index + 1, r);
        else 
            return quickSelect(nums, k, l, index - 1);
    }
    
    private int partation(int[] nums, int l, int r){
        int pivot = l;
        int index = pivot + 1;
        for(int i = index; i <= r; i++){
            if (nums[i] < nums[pivot]){
                swap(nums, i, index);
                index++;
            }
        }
        swap(nums, l, index - 1);
        return index - 1;
    }
    
    private void swap(int[] nums, int i, int j){
        int tmp = nums[i];
        nums[i] = nums[j];
        nums[j] = tmp;
    }
}
```
## 221. Maximal Square
**solution**
Approach: DP

```java
class Solution {
    public int maximalSquare(char[][] matrix) {
        int m = matrix.length;
        if (m == 0) return 0;
        int n = matrix[0].length;
        int[][] dp = new int[m + 1][n + 1];
        int maxlen = 0;
        for(int i = 1; i < m + 1; i++){
            for(int j = 1; j < n + 1; j++){
                if (matrix[i - 1][j - 1] == '1'){
                    dp[i][j] = Math.min(dp[i - 1][j - 1],
                                     Math.min(dp[i - 1][j], dp[i][j - 1])) + 1;
                maxlen = Math.max(maxlen, dp[i][j]);
                }
            }
        }
        return maxlen * maxlen;
    }
}
```

## 226. Invert Binary Tree
**solution**
Approach1 : Recursive

- Time complexity: O(n)
- Space complexity: O(h), h is the height of the tree

```java
class Solution {
    public TreeNode invertTree(TreeNode root) {
        if (root == null) return root;
        invertTree(root.left);
        invertTree(root.right);
        TreeNode tmp = root.left;
        root.left = root.right;
        root.right = tmp;
        return root;
    }
}
```
Approach2 : Iterative

- Time complexity: O(n)
- Space complexity: O(n)

```java
class Solution {
    public TreeNode invertTree(TreeNode root) {
        if (root == null) return null;
        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);
        while(!queue.isEmpty()){
            TreeNode node = queue.poll();
            TreeNode tmp = node.left;
            node.left = node.right;
            node.right = tmp;
            if (node.left != null) queue.offer(node.left);
            if (node.right != null) queue.offer(node.right);
        }
        return root;
    }
}
```
## 230. Kth Smallest Element in a BST
**solution**
Approach 1: Recursion
```java
class Solution {
    private int res;
    private int count;//用Integer也可
    public int kthSmallest(TreeNode root, int k) {
        this.res = 0;
        this.count = k;
        inOrder(root);
        return res;
    }
    private void inOrder(TreeNode root){
        if (root == null) return;
        inOrder(root.left);
        count = count - 1;
        if (count == 0){
            res = root.val;
            return;
        }
        inOrder(root.right);
    }
}

/*
class Solution {
    private int res;
    public int kthSmallest(TreeNode root, int k) {
        res = 0;
        inOrder(root, k);
        return res;
    }
    不能这样写因为Integer对象k自增/自减或者k = k - 1这种
    执行后引用k会指向新的位置,原来的对象没有修改.违背了每次递归
    都修改同一个对象的想法。
    private void inOrder(TreeNode root, Integer k){
        if (root == null) return;
        inOrder(root.left, k);
        k = k - 1;
        if (k == 0) res = root.val;
        inOrder(root.right, k);
    }
}
*/
```
Approach 2: Iteration
```java
class Solution {
    public int kthSmallest(TreeNode root, int k) {
        Stack<TreeNode> stack = new Stack<>();
        TreeNode cur = root;
        while(cur != null || !stack.isEmpty()){
            while(cur != null){
                stack.push(cur);
                cur = cur.left;
            }
            cur = stack.pop();
            k--;
            if (k == 0) return cur.val;
            cur = cur.right;
        }
        return -1;
    }
    
}
```

## 234. Palindrome Linked List
**solution**

- Time complexity: O(n)
- Space complexity: O(1)

```java
class Solution {
    public boolean isPalindrome(ListNode head) {
        ListNode fast = head, slow = head;
        while(fast != null && fast.next != null){
            slow = slow.next;
            fast = fast.next.next;
        }
        slow = reverse(slow);
        fast = head;
        while(slow != null && fast != null){
            if (slow.val != fast.val)
                return false;
            slow = slow.next;
            fast = fast.next;
        }
        return true;
    }
    private ListNode reverse(ListNode head){
        ListNode pre = null;
        while(head != null){
            ListNode next = head.next;
            head.next = pre;
            pre = head;
            head = next;
        }
        return pre;
    }
}
```
## 236. Lowest Common Ancestor of a Binary Tree
**solution**

- Time complexity: O(n)
- Space complexity: O(n)
```java
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if (root == null || root == p || root == q)
            return root;
        TreeNode left = lowestCommonAncestor(root.left, p, q);
        TreeNode right = lowestCommonAncestor(root.right, p, q);
        return left == null ? right : right == null ? left : root;
    }
}
```

## 238. Product of Array Except Self
**solution**
Approach: O(1) space approach

- Time complexity: O(n)
- Space complexity: O(1) (The input and output array does not count as extra space for the purpose of space complexity analysis.)

```java
class Solution {
    public int[] productExceptSelf(int[] nums) {
        int[] res = new int[nums.length];
        res[0] = 1;
        for(int i = 1; i < nums.length; i++)
            res[i] = res[i - 1] * nums[i - 1];
        int R = 1;
        for(int i = nums.length - 1; i >= 0; i--){
            res[i] = R * res[i];
            R = R * nums[i];
        }
        return res;
    }
}
```

## 239. Sliding Window Maximum
**solution**
Approach: Deque

- Time complexity: O(n)
- Space complexity: O(n)

```java
class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        int[] res = new int[nums.length - k + 1];
        Deque<Integer> deque = new ArrayDeque<>();
        for(int i = 0; i < nums.length; i++){
            //remove the item which is out of range of the current k-range window
            while(!deque.isEmpty() && deque.peek() < i - k + 1)
                deque.poll();
            //remove smaller numbers in k range as they are useless
            while(!deque.isEmpty() &&
                  nums[i] > nums[deque.peekLast()])
                deque.pollLast();
            deque.offer(i);
            //the head of the deque is the max value of current window
            if (i >= k - 1)
                res[i - k + 1] = nums[deque.peek()];
            
        }
        return res;
    }
}
```
## 240. Search a 2D Matrix II
Write an efficient algorithm that searches for a value in an m x n matrix. This matrix has the following properties:

- Integers in each row are sorted in ascending from left to right.
- Integers in each column are sorted in ascending from top to bottom.
example: 
[
  [1,   4,  7, 11, 15],
  [2,   5,  8, 12, 19],
  [3,   6,  9, 16, 22],
  [10, 13, 14, 17, 24],
  [18, 21, 23, 26, 30]
]
**solution**
- Time Complexity : O(m + n)
- Space Complexity: O(1)

```java
class Solution {
    public boolean searchMatrix(int[][] matrix, int target) {
        int m = matrix.length;
        if (m == 0) return false;
        int n = matrix[0].length;
        if (n == 0) return false;
        int r = 0, c = n - 1;
        while(r < m && c >= 0){
            if (matrix[r][c] == target) return true;
            if (matrix[r][c] > target) c--;
            else r++;
        }
        return false;
    }
}
```

## 279. Perfect Squares
**solution**
Approach1 : DP

- Time Complexity: O(n * sqrt(n))(?)
- Space Complexity: O(n)
```java
class Solution {
    public int numSquares(int n) {
        //dp[n] = max{dp[n - i * i] + 1}(i * i <= n);
        int[] dp = new int[n + 1];
        dp[0] = 0;
        for(int i = 1; i < n + 1; i++){
            int min = Integer.MAX_VALUE;
            for(int j = 1; i - j * j >= 0; j++)
                min = Math.min(min, dp[i - j * j] + 1);
            dp[i] = min;
        }
        return dp[n];
        
    }
}
```
Approach2: Math


- Time Complexity: O(sqrt(n))
- Space Complexity: O(1)

四平方和定理: 任何数都可以由4个平方数组成，即 n = a^2 + b^2 + c^2 + d^2，所以这题的答案已经限定在了 [1,4] 之间

三平方和定理: n = a^2 + b^2 + c^2 当且仅当n 无法写成(4^k) * (8m + 7)(k,m为非负整数)

除了满足以上这个公式的数以外的任何数都可以由3个平方数组成

```java
class Solution {
    public int numSquares(int n) {
        int sqrt = (int) Math.sqrt(n);
        if (sqrt * sqrt == n) return 1;
        //if k == 2^i, then n % k = n & (k - 1)
        while((n & 3) == 0) n >>= 2;
        if ((n & 7) == 7) return 4;
        for(int i = 1; i <= sqrt; i++){
            int rest = n - i * i;
            int rs = (int) Math.sqrt(rest);
            if (rs * rs == rest) return 2;
        }
        return 3;
    }
}
```

## 283. Move Zeroes
**solution**


- Time Complexity: O(n)
- Space Complexity: O(1)

```java
class Solution {
    public void moveZeroes(int[] nums) {
        /*
         note that we can't use left bount of zero and decrease it,
         or we can't maintain the relative order of the non-zero elements
         right bound of non-zero.
         example: [0,1,0,3,12]
         both ptr = len - 1, i--(i from len - 1) and ptr = len - 1, i++(i from 0)
         are wrong
        */
        int ptr = 0;
        for(int i = 0; i < nums.length; i++){
            if (nums[i] != 0)
                swap(nums, i, ptr++);
        }
    }
    private void swap(int[] nums, int i, int j){
        int tmp = nums[i];
        nums[i] = nums[j];
        nums[j] = tmp;
    }
}
```

## 287. Find the Duplicate Number

Note:
You must not modify the array (assume the array is read only).
You must use only constant, O(1) extra space.
Your runtime complexity should be less than O(n2).
There is only one duplicate number in the array, but it could be repeated more than once.

**solution**

Approach: fast and slow pointer

- Time Complexity: O(n)
- Space Complexity: O(1)

```java
class Solution {
    public int findDuplicate(int[] nums) {
        int slow = nums[0];
        int fast = nums[0];
        do{
            slow = nums[slow];
            fast = nums[nums[fast]];
        }while(slow != fast);
        fast = nums[0];
        while(slow != fast){
            slow = nums[slow];
            fast = nums[fast];
        }
        return fast;
    }
}
```

## 295. Find Median from Data Stream
**solution**
Approach: Two heaps

- Time Complexity: O(log n)
- Space Complexity: O(n)

```java
class MedianFinder {

    /** initialize your data structure here. */
    private PriorityQueue<Integer> max;
    private PriorityQueue<Integer> min;
    public MedianFinder() {
        max = new PriorityQueue<>((i1, i2) -> (i2 - i1));
        min = new PriorityQueue<>();
    }
    
    public void addNum(int num) {
        max.offer(num);
        min.offer(max.poll());
        if (max.size() < min.size())
            max.offer(min.poll());
    }
    
    public double findMedian() {
        if (max.size() == min.size())
            return (max.peek() + min.peek()) / 2.0;
        else
            return max.peek();
    }
}

```

## 297. Serialize and Deserialize Binary Tree
**solution**

Approach 1: DFS

- Time Complexity: O(n)
- Space Complexity: O(n)

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
public class Codec {
    private static final String SPLITTER = ",";
    private static final String NN = "#";

    // Encodes a tree to a single string.
    public String serialize(TreeNode root) {
        StringBuilder sb = new StringBuilder();
        buildString(root, sb);
        return sb.toString();
    }
    
    private void buildString(TreeNode root, StringBuilder sb){
        if (root == null){
            sb.append(NN).append(SPLITTER);
            return;
        }
        sb.append(root.val).append(SPLITTER);
        buildString(root.left, sb);
        buildString(root.right, sb);
    }

    // Decodes your encoded data to tree.
    public TreeNode deserialize(String data) {
        //note: for example
        //"a,".split(",") ==> len = 1
        //",a".split(",") ==> len = 2
        Queue<String> queue = new LinkedList<>();
        queue.addAll(Arrays.asList(data.split(",")));
        return buildTree(queue);
    }
    
    private TreeNode buildTree(Queue<String> queue){
        String node = queue.poll();
        if (node.equals(NN)){
            return null;
        }
        TreeNode root = new TreeNode(Integer.parseInt(node));
        root.left = buildTree(queue);
        root.right = buildTree(queue);
        return root;
    }
}

// Your Codec object will be instantiated and called as such:
// Codec codec = new Codec();
// codec.deserialize(codec.serialize(root));
```

Approach 2: BFS

- Time Complexity: O(n)
- Space Complexity: O(n)

```java
public class Codec {
    private static final String SPLITTER = ",";
    private static final String NN = "#";
    // Encodes a tree to a single string.
    public String serialize(TreeNode root) {
        if (root == null) return "";
        StringBuilder sb = new StringBuilder();
        //note: linkedlist can add null to the queue
        //but ArrayDeque can't
        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);
        while(!queue.isEmpty()){
            TreeNode node = queue.poll();
            if (node == null){
                sb.append(NN).append(SPLITTER);
                continue;
            }
            sb.append(node.val).append(SPLITTER);
            queue.offer(node.left);
            queue.offer(node.right);
        }
        return sb.toString();
    }

    // Decodes your encoded data to tree.
    public TreeNode deserialize(String data) {
        if (data.isEmpty()) return null;
        Queue<TreeNode> queue = new LinkedList<>();
        String[] items = data.split(SPLITTER);
        int ptr = 0;
        TreeNode root = new TreeNode(Integer.parseInt(items[ptr++]));
        queue.offer(root);
        while(!queue.isEmpty()){
            TreeNode node = queue.poll();
            String item = items[ptr++];
            if (!item.equals(NN)){
                node.left = new TreeNode(Integer.parseInt(item));
                queue.offer(node.left);
            }
            item = items[ptr++];
            if (!item.equals(NN)){
                node.right = new TreeNode(Integer.parseInt(item));
                queue.offer(node.right);
            }
        }
        return root;
        /* This way is also ok
        while(!queue.isEmpty()){
            TreeNode node = queue.poll();
            if (node == null) continue;
            String item = items[ptr++];
            if (!item.equals(NN)){
                node.left = new TreeNode(Integer.parseInt(item));
                
            }
            queue.offer(node.left);
            item = items[ptr++];
            if (!item.equals(NN)){
                node.right = new TreeNode(Integer.parseInt(item));
                
            }
            queue.offer(node.right);
        }
        */
        
    }
}
```

## 300. Longest Increasing Subsequence
**solution**

Approach1: DP

- Time Complexity: O(n^2)
- Space Complexity: O(n)

```java
class Solution {
    public int lengthOfLIS(int[] nums) {
        //dp[i]=max(dp[j])+1,∀0≤j<i and dp[i] >dp[j]
        int n = nums.length;
        if (n == 0) return 0;
        int[] dp = new int[n];
        dp[0] = 1;
        int maxlen = 1;//for example: [0]
        for(int i = 1; i < n; i++){
            int maxval = 0;
            for(int j = 0; j < i; j++)
                if (nums[i] > nums[j])
                    maxval = Math.max(maxval, dp[j]);
            dp[i] = maxval + 1;
            maxlen = Math.max(dp[i], maxlen);            
        }
        return maxlen;
        
    }
    
}
```

Approach 2 : Binary Search


- Time Complexity: O(nlogn)
- Space Complexity: O(n)

tails is an array storing the smallest tail of all increasing subsequences with length i+1 in tails[i].
For example, say we have nums = [4,5,6,3], then all the available increasing subsequences are:

len = 1   :      [4], [5], [6], [3]   => tails[0] = 3
len = 2   :      [4, 5], [5, 6]       => tails[1] = 5
len = 3   :      [4, 5, 6]            => tails[2] = 6
We can easily prove that tails is a increasing array. Therefore it is possible to do a binary search in tails array to find the one needs update.

Each time we only do one of the two:

(1) if x is larger than all tails, append it, increase the size by 1
(2) if tails[i-1] < x <= tails[i], update tails[i]

```java
class Solution {
    public int lengthOfLIS(int[] nums) {
        int[] tails = new int[nums.length];
        int size = 0;
        for(int num : nums){
            int l = 0, r = size;
            while(l < r){
                int mid = l + ((r - l) >> 1);
                if (tails[mid] < num)
                    l = mid + 1;
                else
                    r = mid;
            }
            //if num exists l is the pos of num in tails
            //else l is the pos where tails[pos] is larger than num firstly
            tails[l] = num;
            if (l == size) ++size;
        }
        return size;
    }
}
```

Example: For [1,3,5,2,8,4,6]
For this list, we can have LIS with different length.
For length = 1, [1], [3], [5], [2], [8], [4], [6], we pick the one with smallest tail element as the representation of length=1, which is [1]
For length = 2, [1,2] [1,3] [3,5] [2,8], ...., we pick [1,2] as the representation of length=2.
Similarly, we can derive the sequence for length=3 and length=4
The result sequence would be:
len=1: [1]
len=2: [1,2]
len=3: [1,3,4]
len=4: [1,3,5,6]

According to the logic in the post,we can conclude that:
(1) If there comes another element, 9
We iterate all the sequences, found 9 is even greater than the tail of len=4 sequence, we then copy len=4 sequence to be a new sequece, and append 9 to the new sequence, which is len=5: [1,3,5,6,9]
The result is:
len=1: [1]
len=2: [1,2]
len=3: [1,3,4]
len=4: [1,3,5,6]
len=5: [1,3,5,6,9]

(2) If there comes another 3,
We found len=3 [1,3,4], whose tailer is just greater than 3, we update the len=3 sequence tobe [1,3,3]. The result is:
len=1: [1]
len=2: [1,2]
len=3: [1,3,3]
len=4: [1,3,5,6]

(3) If there comes another 0,
0 is smaller than the tail in len=1 sequence, so we update the len=1 sequence. The result is:
len=1: [0]
len=2: [1,2]
len=3: [1,3,3]
len=4: [1,3,5,6]

## 301. Remove Invalid Parentheses
**solution**

```java
class Solution {
    public List<String> removeInvalidParentheses(String s) {
        List<String> res = new ArrayList<>();
        remove(s, res, 0, 0, ')');
        return res;
    }
    
    private void remove(String s, List<String> res,
                        int iStart, int jStart, char ch){
        //each level remove one parentheses
        for(int count = 0, i = iStart; i < s.length(); i++){
            if (s.charAt(i) == '(' || s.charAt(i) == ')')
                count += (s.charAt(i) == ch ? -1 : 1);
            if (count >= 0) continue;
            // We have an extra closed paren we need to remove
            for(int j = jStart; j <= i; j++){
                // Try removing one at each position, skipping duplicates
                //example: (())) only remove index == 2
                if (s.charAt(j) == ch &&
                    (j == jStart || s.charAt(j - 1) != ch))
                    //After remove the char at pos j, the sbustring before iStart = i
                    //is valid, so we start at iStart on the next level of recursion.
                    //-------------------------------------------------------------
                    //jStart = j prevents duplicates(for example: 
                    //remove idx=a then remove idx=b and remove idx=b then remove
                    //idx = a will produce duplicate for a < b if we don't 
                    //start at the last removal positation
                    remove(s.substring(0, j) + s.substring(j + 1), res, i, j, ch);
            }
            return;// Stop here. The recursive calls handle the rest of the string.
        }
        
        // No invalid closed parenthesis detected.
        //Now check opposite direction, or reverse back to original direction.
        String reversed = new StringBuilder(s).reverse().toString();
        if (ch == ')')
            remove(reversed, res, 0, 0, '(');
        else//reverse two times => recover the original relative sequence
            res.add(reversed);
    }
}
```

## 309. Best Time to Buy and Sell Stock with Cooldown
**solution**
Approach DP

state   --action-->  next state(state: [hold, sold, rest]. action: [buy, sell, rest])
hold   --sell-->        sold
hold   --rest-->       hold
sold   --rest-->        rest
rest    --rest-->        rest
rest    --buy-->        hold

sold[i] = hold[i - 1] + price[i]; (max profit when i th day ends and in sold state, 0 <=i < n) 
hold[i] = max(rest[i - 1] - price[i], hold[i - 1])
rest[i] = max(sold[i - 1], rest[i - 1])

```java
class Solution {
    public int maxProfit(int[] prices) {
        int n = prices.length;
        int[] hold = new int[n + 1];
        int[] sold = new int[n + 1];
        int[] rest = new int[n + 1];
        //initial state we don't have stock, this state is impossible, set as INT_MIN
        hold[0] = Integer.MIN_VALUE;
        for(int i = 0; i < n; i++){
            hold[i + 1] = Math.max(hold[i], rest[i] - prices[i]);
            sold[i + 1] = hold[i] + prices[i];
            rest[i + 1] = Math.max(rest[i], sold[i]);
        }
        return Math.max(hold[n], Math.max(rest[n], sold[n]));
    }
}
//-----------------------or-----------------------------------
class Solution {
    public int maxProfit(int[] prices) {
        int sold = 0, rest = 0, hold = Integer.MIN_VALUE;
        for(int price : prices){
            int soldPre = sold;
            sold = hold + price;
            hold = Math.max(rest - price, hold);
            rest = Math.max(soldPre, rest);
        }
        return Math.max(sold, Math.max(hold, rest));
    }
}
```

## 322. Coin Change
**solution**

Approach 1 (Dynamic programming - Top down)


- Time Complexity: O(S * n), where S is the amount, n is denomination count
- Space Complexity: O(S)

```java
class Solution {
    public int coinChange(int[] coins, int amount) {
        //dp[i] = min(dp[i - coins[k]]) + 1 (dp[i - coins[k]] >= 0)
        //if i - coins[k] < 0 then dp[i - coins[j]] = -1
        int[] memo = new int[amount + 1];
        Arrays.fill(memo, -2);
        return helper(coins, amount, memo);
    }
    private int helper(int[] coins, int amount, int[] memo){
        if (amount < 0) return -1;
        if (amount == 0) return 0;
        if(memo[amount] != -2) return memo[amount];
        int res = Integer.MAX_VALUE;
        for(int i = 0; i < coins.length; i++){
            int submin = helper(coins, amount - coins[i], memo);
            if (submin >= 0)
                res = Math.min(res, submin + 1);
        }
        res = (res == Integer.MAX_VALUE) ? -1 : res;
        memo[amount] = res;
        return res;
    }
}
```

Approach 2 (Dynamic programming - Bottom up)

- Time Complexity: O(S * n), where S is the amount, n is denomination count
- Space Complexity: O(S)

```java
class Solution {
    public int coinChange(int[] coins, int amount) {
        int[] dp = new int[amount + 1];
        for(int i = 1; i < amount + 1; i++){
            int min = Integer.MAX_VALUE;
            for(int j = 0; j < coins.length; j++){
                int pre = i - coins[j] < 0
                    ? -1 : dp[i - coins[j]];
                if (pre >= 0)
                    min = Math.min(min, 1 + pre);
                }
            min = (min == Integer.MAX_VALUE) ? -1 : min;
            dp[i] = min;
        }
        return dp[amount];
    }
}
```

## 337. House Robber III
**solution**

- Time Complexity: O(n)
- Space Complexity: O(logn)


```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public int rob(TreeNode root) {
        int[] res = robSub(root);
        return Math.max(res[0], res[1]);
    }
    //note: we return both res[0] and res[1] instead of the max of them only
    //because the parent node need them both to calculate 
    private int[] robSub(TreeNode root){
        //res[0] -> max value without robbing current node
        //res[1] -> max value with robbing current node
        int[] res =  new int[]{0, 0};
        if (root == null) return res;
        int[] leftMax = robSub(root.left);
        int[] rightMax = robSub(root.right);
        //if we don't rob current node, then we can choose to rob
        //the sub-node or not.
        res[0] = Math.max(leftMax[0], leftMax[1]) + 
            Math.max(rightMax[0], rightMax[1]);
        //if we rob the currentNode, then we can onlty choose to
        //not rob the sub-node
        res[1] = root.val + leftMax[0] + rightMax[0];
        return res;
    }
}
```

## 338. Counting Bits
**solution**

- Time Complexity: O(n)
- Space Complexity: O(n)

```java
/*
Explaination.
Take number X for example, 10011001.
Divide it in 2 parts:
<1>the last digit ( 1 or 0, which is " i&1 ", equivalent to " i%2 " )
<2>the other digits ( the number of 1, which is " f[i >> 1] ", equivalent to " f[i/2] " )
*/
class Solution {
    public int[] countBits(int num) {
        int[] res = new int[num + 1];
        res[0] = 0;
        for(int i = 1; i < num + 1; i++)
            res[i] = res[i >> 1] + (i & 1);
        return res;
    }
}
```

## 347. Top K Frequent Elements
**solution**

Approach 1 :  Heap

- Time Complexity: O(nlogk)
- Space Complexity: O(n + k) (store the hash map with not more N elements and a heap with k elements)

```java
class Solution {
    public int[] topKFrequent(int[] nums, int k) {
        Map<Integer, Integer> map = new HashMap<>();
        for(int num : nums)
            map.put(num, map.getOrDefault(num, 0) + 1);
        PriorityQueue<Integer> pq = new PriorityQueue<>((n1, n2) -> 
                                    (map.get(n1) - map.get(n2)));
        for(int n : map.keySet()){
            pq.offer(n);
            if (pq.size() > k) pq.poll();
        }
        int[] res = new int[k];
        for(int i = 0; i < k; i++)
            res[i] = pq.poll();
        return res;
        
    }
}
```

Approach 2 :  Quick Select

- Time Complexity: O(n)(average), O(n^2)(worst)
- Space Complexity: O(n) (store hash map and array of unique element)

```java
class Solution {
    private Map<Integer, Integer> count;
    public int[] topKFrequent(int[] nums, int k) {
        count = new HashMap<>();
        for(int num : nums)
        count.put(num, count.getOrDefault(num, 0) + 1);
        int[] unique = new int[count.keySet().size()];
        int i = 0;
        for(int num : count.keySet())
            unique[i++] = num;
        int n = unique.length;
        quickSelect(unique, 0, n - 1, n - k + 1);
        return Arrays.copyOfRange(unique, n - k, n);
        
        
    }
    
    //note: we we find the kth, the part before the kth is smaller than it
    //and the part after the kth is larger than it.
    private void quickSelect(int[] nums, int l, int r, int k){
        int idx = partition(nums, l, r);
        if (l + k - 1 == idx)
            return;
        else if (l + k - 1 > idx)
            quickSelect(nums, idx + 1, r, l + k - idx - 1);
        else
            quickSelect(nums, l, idx - 1, k);
    }
    private int partition(int[] nums, int l, int r){
        int pivot = l;
        int idx = l + 1;
        for(int i = l + 1; i <= r; i++){
            if (count.get(nums[i]) < count.get(nums[pivot]))
                swap(nums, i, idx++);
        }
        idx = idx - 1;
        swap(nums, pivot, idx);
        return idx;
    }
    private void swap(int[] nums, int i, int j){
        int tmp = nums[i];
        nums[i] = nums[j];
        nums[j] = tmp;
    }
}
```

Approach 3 :  Bucket Select

- Time Complexity: O(n)
- Space Complexity: O(n)

```java
class Solution {
    public int[] topKFrequent(int[] nums, int k) {
        Map<Integer, Integer> count = new HashMap<>();
        for(int num : nums)
            count.put(num, count.getOrDefault(num, 0) + 1);
        //new List<>[nums.length + 1] and new List<Integer>[nums.length + 1]
        //is wrong!!
        List<Integer>[] bucket = new List[nums.length + 1];
        for(int num : count.keySet()){
            int freq = count.get(num);
            if (bucket[freq] == null)
                bucket[freq] = new ArrayList<>();
            bucket[freq].add(num);
        }
        int[] res = new int[k];
        int p = 0;
        for(int i = nums.length; i >= 0; i--){
            if (bucket[i] == null) continue;
            for(int num : bucket[i]){
                res[p++] = num;
                if (p == k) return res;
            }
        }
        return res;
    }
    
}
```
## 394. Decode String
**solution**

```java
class Solution {
    public String decodeString(String s) {
        StringBuilder res = new StringBuilder();
        Stack<Integer> nums = new Stack<>();
        Stack<StringBuilder> strs = new Stack<>();
        int count = 0;
        //res stores the decode result until the current char
        for(char c : s.toCharArray()){
            if (Character.isDigit(c)){
                count = 10 * count + c - '0';
            }
            
            else if (c == '['){
                nums.push(count);
                count = 0;
                strs.push(res);
                res = new StringBuilder();
            }else if (c == ']'){
                int cnt = nums.pop();
                StringBuilder tmp = res;
                res = strs.pop();
                for(int i = 0; i < cnt; i++)
                    res.append(tmp);
            }else{
                res.append(c);
            }
            
        }
        return res.toString();
    }
}
```

## 406. Queue Reconstruction by Height
**solution**

Approach: 

1. Pick out tallest group of people and sort them in a subarray (S). Since there's no other groups of people taller than them, therefore each guy's index will be just as same as his k value.
2. For 2nd tallest group (and the rest), insert each one of them into (S) by k value. So on and so forth.
E.g.
input: [[7,0], [4,4], [7,1], [5,0], [6,1], [5,2]]
subarray after step 1: [[7,0], [7,1]]
subarray after step 2: [[7,0], [6,1], [7,1]]

- Time Complexity: O(n^2)
- Space Complexity: O(n)

```java
class Solution {
    public int[][] reconstructQueue(int[][] people) {
        List<int[]> res = new ArrayList<>();
        Arrays.sort(people, (o1, o2) -> 
                    o1[0] != o2[0] ? o2[0] - o1[0] : o1[1] - o2[1]);
        for(int[] p : people)
            res.add(p[1], p);
        return res.toArray(new int[0][0]);
    }
}
```

## 416. Partition Equal Subset Sum
**solution**

- Time Complexity: O(n * sum)
- Space Complexity: O(sum)

```java
class Solution {
    public boolean canPartition(int[] nums) {
        //f(n, s) -> in [0, n - 1], if there exists
        // a subset whose sum is s;
        //f(n, s) = f(n - 1, s) || f(n - 1, s - nums[n - 1]);
        int sum = 0;
        for(int num : nums)
            sum += num;
        if (sum % 2 != 0) return false;
        int s = sum / 2;
        boolean[] dp = new boolean[s + 1];
        dp[0] = true;
        for(int num : nums){
            //decrese j, if dp[j] is changed
            //then the value of dp[k](k < j) doesn't don't 
            //require the original value in dp[j].
            //but if we increse j, the value of dp[k](k > j)
            //require the original value in dp[j], but dp[j]
            //is changed, we will get wrong answer.
            //if we want to increase j, use an extra array to save the current result
            for(int j = s; j >= 0; j--){
                if (j - num >= 0)
                    dp[j] = dp[j] || dp[j - num];
            }
            
        }
        return dp[s];
        
    }
}
```
## 437. Path Sum III
**solution**

Approach 1: Use HashMap

- Time Complexity: O(n)
- Space Complexity: O(logn)


Each recursion returns the total count of valid paths in the subtree rooted at the current node. And this sum can be divided into three parts:
- the total number of valid paths in the subtree rooted at the current node's left child
- the total number of valid paths in the subtree rooted at the current node's right child
- the number of valid paths ended by the current node

Similar to `560. Subarray sum equals K problem`

```java
class Solution {
    public int pathSum(TreeNode root, int sum) {
        Map<Integer, Integer> preSum = new HashMap<>();
        //Default sum = 0 has one count
        preSum.put(0, 1);
        return helper(root, 0, sum, preSum);
    }
    public int helper(TreeNode cur, int sum, int target,
                     Map<Integer, Integer> preSum){
        if (cur == null) return 0;
        // update the prefix sum by adding the current val
        sum += cur.val;
        // get the number of valid path, ended by the current node
        //example: 1->2->3->4, cur = 4, target = 7, sum = 10
        //sum - target = 3 exists in {0:1,1:1,3:1,6:1}
        //so 3->4 is a valid path
        int res = preSum.getOrDefault(sum - target, 0);
        // update the map with the current sum, 
        //so the map is good to be passed to the next recursion
        preSum.put(sum, preSum.getOrDefault(sum, 0) + 1);

        res += helper(cur.left, sum, target, preSum)
            + helper(cur.right, sum, target, preSum);
        // restore the map, as the recursion goes from the bottom to the top
        //Remove the current node so it wont affect other path
        preSum.put(sum, preSum.getOrDefault(sum, 0)  - 1);
        return res;
        
    }
}
```

Approach 2: DFS

- Time Complexity: O(n^2) in worst case (no branching); O(nlogn) in best case (balanced tree).
- Space Complexity: O(n) due to recursion

```java
class Solution {
    public int pathSum(TreeNode root, int sum) {
        // pathSum(root, sum) returns number of paths in the subtree rooted at root
        // s.t. the sum of values on the path equals `sum`. 
        // The path might not include root.
        if (root == null) return 0;
        return dfs(root, sum) + pathSum(root.left, sum)
            + pathSum(root.right, sum);
    }
    public int dfs(TreeNode root, int sum){
        // pathSumFrom(root, sum) returns number of paths INCLUDING
        //(not necessary starting from)
        // root whose sum of values equals `sum`.
        if (root == null) return 0;
        return (sum == root.val ? 1 : 0)
            + dfs(root.left, sum - root.val)
            + dfs(root.right, sum - root.val);
    }
}
```

## 438. Find All Anagrams in a String
**solution**

Approach: Similar To 76.  Minimum Window Substring

```java
class Solution {
    public List<Integer> findAnagrams(String s, String p) {
        char[] chs = s.toCharArray();
        char[] chp = p.toCharArray();
        
        int[] dictP = new int[128];
        int[] window = new int[128];
        for(char c : chp)
            dictP[c]++;
        
        int l = 0, r = 0;
        int formed = 0, required = chp.length;
        List<Integer> res = new ArrayList<>();
        
        while(r < chs.length){
            if (window[chs[r]]++ < dictP[chs[r]])
                formed++;
            while(l <= r && formed == required){
                //if this condition(r - l + 1 == chp.length) is true
                //next time, the loop will end.
                //because in this case, window and dictP is same;
                if (r - l + 1 == chp.length)
                    res.add(l);
                if (window[chs[l]]-- <= dictP[chs[l]])
                    formed--;
                l++;
            }
            r++;
        }
        return res;
    }
}
```

## 448. Find All Numbers Disappeared in an Array
**soultion**
Approach1: Similar to 41. First Missing Positive

- Time Complexity: O(n)
(each cell will be touched at most 2 times after that for loop is finished execution.)
- Space Complexity: O(1) (assume the returned list does not count as extra space.)

```java
class Solution {
    public List<Integer> findDisappearedNumbers(int[] nums) {
        List<Integer> res = new ArrayList<>();
        for(int i = 0; i < nums.length; i++){
            while(nums[nums[i] - 1] != nums[i])
                swap(nums, i, nums[i] - 1);
        }
        for(int i = 0; i < nums.length; i++){
            if (nums[i] != i + 1)
                res.add(i + 1);
        }
        return res;
    }
    private void swap(int[] nums, int i, int j){
        int tmp = nums[i];
        nums[i] = nums[j];
        nums[j] = tmp;
    }
}
```

Approach 2: set value of pos i to negative to mark the existence of the number i + 1

- Time Complexity: O(n)
- Space Complexity: O(1) (assume the returned list does not count as extra space.)

```java
class Solution {
    public List<Integer> findDisappearedNumbers(int[] nums) {
        List<Integer> res = new ArrayList<>();
        for(int i = 0; i < nums.length; i++){
            int val = Math.abs(nums[i]);
            nums[val - 1] = - Math.abs(nums[val - 1]);
            
        }
        for(int i = 0; i < nums.length; i++)
            if (nums[i] > 0)
                res.add(i + 1);
        return res;
    }
}
```

## 494. Target Sum
**solution**
Approach: DP
dp[i][j] = dp[i-1][j+nums[i]] + dp[i-1][j-nums[i]]
dp[i][j] ==> ways of subarray [0, i] that assign symbols to make sum in the subarray equal to j;

- Time Complexity: O(n * sum)
- Space Complexity: O(sum)


```java
class Solution {
    public int findTargetSumWays(int[] nums, int S) {
        int sum = 0;
        for(int num : nums)
            sum += num;
        if (S < -sum || S > sum) return 0;
        int[] dp = new int[2 * sum + 1];
        //0 ~ 2 * sum <====> -sum ~ sum
        //empty subarray in the initial state
        dp[sum] = 1;
        for(int i = 0; i < nums.length; i++){
            int[] tmp = new int[2 * sum + 1];
            for(int j = 0; j < 2 * sum + 1; j++){
                //dp[j] > 0 to avoid index out of range
                if (dp[j] > 0){
                    tmp[j - nums[i]] += dp[j];
                    tmp[j + nums[i]] += dp[j];
                }
            }
            dp = tmp;
        }
        return dp[S + sum];
    }
}
```

## 543. Diameter of Binary Tree

**solution**

Approach: Similar to 124. Binary Tree Maximum Path Sum, here we regard the value of each node as 1

- Time Complexity: O(n)
- Space Complexity: O(n)(worst case. totally not balanced)


```java
class Solution {
    private int max;
    public int diameterOfBinaryTree(TreeNode root) {
        //if we don't add this, root = null will return -1
        if (root == null) return 0;
        max = Integer.MIN_VALUE;
        travel(root);
        //length of path equals to pathsum - 1(regard the value of each node as 1)
        return max - 1;
    }
    
    public int travel(TreeNode root){
        if (root == null) return 0;
        int left = travel(root.left);
        int right = travel(root.right);
        max = Math.max(max, left + right + 1);
        return Math.max(left + 1, right + 1);
    }
}
```

## 560. Subarray Sum Equals K
**solution**
Approach : Using Hashmap

- Time Complexity: O(n)
- Space Complexity: O(n)

```java
class Solution {
    public int subarraySum(int[] nums, int k) {
        Map<Integer, Integer> preSum = new HashMap<>();
        preSum.put(0, 1);
        int sum = 0, res = 0;
        for(int num : nums){
            sum += num;
            if (preSum.containsKey(sum - k))
                res += preSum.get(sum - k);
            preSum.put(sum, preSum.getOrDefault(sum , 0) + 1);
        }
        return res;
    }
}
```

## 581. Shortest Unsorted Continuous Subarray
**solution**

- Time Complexity: O(n)
- Space Complexity: O(1)

```java
class Solution {
    public int findUnsortedSubarray(int[] nums) {
        int max = Integer.MIN_VALUE, min = Integer.MAX_VALUE;
        //find the min and max in the unsorted part.
        //we only need to find them in the descending pairs
        for(int i = 0; i < nums.length - 1; i++){
            if (nums[i] > nums[i + 1]){
                max = Math.max(max, nums[i]);
                min = Math.min(min, nums[i + 1]);
            }
        }
        //find the first pos whose value is bigger than min from left to right
        int l = 0, r = nums.length - 1;
        while(l < nums.length && nums[l] <= min)
            ++l;
        //find the first pos whose value is bigger than min from right to left
        while(r >= 0 && nums[r] >= max)
            --r;
        return r - l < 0 ? 0 : r - l + 1;
    }
}
```

## 617. Merge Two Binary Trees

**solution**

- Time Complexity: O(m)(m represents the minimum number of nodes from the two given trees)
- Space Complexity: O(m)(worst, average is O(logm))

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public TreeNode mergeTrees(TreeNode t1, TreeNode t2) {
        if (t1 == null) return t2;
        if (t2 == null) return t1;
        TreeNode root = new TreeNode(t1.val + t2.val);
        root.left = mergeTrees(t1.left, t2.left);
        root.right = mergeTrees(t1.right, t2.right);
        return root;
    }
}
/*----------------------or----------------------------------*/
class Solution {
    public TreeNode mergeTrees(TreeNode t1, TreeNode t2) {
        if (t1 == null) return t2;
        if (t2 == null) return t1;
        //resue t1
        t1.val += t2.val
        t1.left = mergeTrees(t1.left, t2.left);
        t1.right = mergeTrees(t1.right, t2.right);
        return t1;
    }
}

```

## 621. Task Scheduler
**solution**
- Time Complexity: O(n)
- Space Complexity: O(26)


```java
class Solution {
    public int leastInterval(char[] tasks, int n) {
        int[] count = new int[26];
        for(char c : tasks)
            count[c - 'A']++;
        Arrays.sort(count);
        int idx = 25;
        while(idx >= 0 && count[idx] == count[25])
              idx--;
        return Math.max(tasks.length,
                        25 - idx + (n + 1) * (count[25] - 1));
        
    }
}
```

First consider the most frequent characters, we can determine their relative positions first and use them as a frame to insert the remaining less frequent characters. Here is a proof by construction:

Let F be the set of most frequent chars with frequency k.
We can create k chunks, each chunk is identical and is a string consists of chars in F in a specific fixed order.
Let the heads of these chunks to be H_i; then H_2 should be at least n chars away from H_1, and so on so forth; then we insert the less frequent chars into the gaps between these chunks sequentially one by one ordered by frequency in a decreasing order and try to fill the k-1 gaps as full or evenly as possible each time you insert a character. In summary, append the less frequent characters to the end of each chunk of the first k-1 chunks sequentially and round and round, then join the chunks and keep their heads' relative distance from each other to be at least n.

Examples:

AAAABBBEEFFGG 3

here X represents a space gap:

>Frame: "AXXXAXXXAXXXA"
>insert 'B': "ABXXABXXABXXA" <--- 'B' has higher frequency than the other characters, insert it first.
>insert 'E': "ABEXABEXABXXA"
>insert 'F': "ABEFABEXABFXA" <--- each time try to fill the k-1 gaps as full or evenly as possible.
>insert 'G': "ABEFABEGABFGA"


AACCCBEEE 2

>3 identical chunks "CE", "CE CE CE" <-- this is a frame
>insert 'A' among the gaps of chunks since it has higher frequency than 'B' ---> "CEACEACE"
>insert 'B' ---> "CEABCEACE" <----- result is tasks.length;


AACCCDDEEE 3

>3 identical chunks "CE", "CE CE CE" <--- this is a frame.
>Begin to insert 'A'->"CEA CEA CE"
>Begin to insert 'B'->"CEABCEABCE" <---- result is tasks.length;


ACCCEEE 2

>3 identical chunks "CE", "CE CE CE" <-- this is a frame
>Begin to insert 'A' --> "CEACE CE" <-- result is (c[25] - 1) * (n + 1) + 25 -i = 2 * 3 + 2 = 8


## 647. Palindromic Substrings
**solution**

- Time Complexity: O(n^2)
- Space Complexity: O(1)

```java
class Solution {
    public int countSubstrings(String s) {
        int res = 0;
        for(int i = 0; i < s.length(); i++){
            res += appendCenter(s, i, i);
            res += appendCenter(s, i, i + 1);
        }
        return res;
    }
    
    private int appendCenter(String s, int l, int r){
        int count = 0;
        while(l >= 0 && r < s.length()
              && s.charAt(l) == s.charAt(r)){
            count++;
            l--;
            r++;
        }
        return count;
    }
}
```

## 739. Daily Temperatures
**solution**  
Approach: decreasing stack

- Time Complexity: O(n)
- Space Complexity: O(n)

```java
class Solution {
    public int[] dailyTemperatures(int[] T) {
        int[] res = new int[T.length];
        Stack<Integer> stack = new Stack<>();
        for(int i = T.length - 1; i >= 0; i--){
            while(!stack.isEmpty() && T[i] >= T[stack.peek()])
                stack.pop();
            res[i] = stack.isEmpty() ? 0 : stack.peek() - i;
            stack.push(i);
        }
        return res;
    }
}
```