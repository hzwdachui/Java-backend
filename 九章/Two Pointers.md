# Two Pointers
## Fast and slow pointers 
慢指针一次移动一格，快指针一次移动两格（或更多），通过这个倍数来找到某个node（或者环中也可以用来做一个追及）
### Middle of the Linked List
	/**
	 * Definition for ListNode
	 * public class ListNode {
	 *     int val;
	 *     ListNode next;
	 *     ListNode(int x) {
	 *         val = x;
	 *         next = null;
	 *     }
	 * }
	 */
	// java
	public class Solution {
	    /**
	     * @param head: the head of linked list.
	     * @return: a middle node of the linked list
	     */
	    public ListNode middleNode(ListNode head) { 
	        if (head == null) {
	            return null;
	        }
	        if (head.next == null) {
	            return head;
	        }
	        // fast and slow pointers, when fast reached the end 
	        // slow will be at the middle
	        ListNode slow = head;
	        ListNode fast = head.next;
	        while(fast != null && fast.next != null){
	            slow = slow.next;
	            fast = fast.next.next;
	        }
	        return slow;
	    }
	}

## Left and Right Pointers
构造left 和 right两个指针，从两边往中间夹，来寻找想要的。通常需要先**排序**。

- 2sum
- 3sum

### 3sum	
	// java
	public class Solution {
	    /**
	     * @param numbers: Give an array numbers of n integer
	     * @return: Find all unique triplets in the array which gives the sum of zero.
	     */
	    public List<List<Integer>> threeSum(int[] numbers) {
	        if (numbers == null || numbers.length < 3) {
	            return new ArrayList<>();
	        }
	        List<List<Integer>> res = new ArrayList<>();
	        Arrays.sort(numbers);
	        for (int i=0; i<numbers.length - 2; i++) {
	            // skip duplicate
	            if (i>0 && numbers[i] == numbers[i-1]) {
	                continue;
	            } 
	            int[] twoSumArray = Arrays.copyOfRange(numbers, i+1, numbers.length);
	            int target = -numbers[i];
	            twoSum(twoSumArray, target, res);
	        }
	        
	        return res;
	    }
	    
	    public void twoSum(int[] t, int target, List<List<Integer>> res) {
	        int left = 0;
	        int right = t.length - 1;
	        while(left < right) {
	            if(t[left] + t[right] == target){
	                List<Integer> triple = new ArrayList<>(); 
	                triple.add(-target);
	                triple.add(t[left]);
	                triple.add(t[right]);
	                res.add(triple);
	                
	                left++;
	                right--;
	                
	                while (left < right && t[left] == t[left - 1]) {
	                    left++;
	                }
	                // skip duplicate pairs with the same right
	                while (left < right && t[right] == t[right + 1]) {
	                    right--;
	                }
	            } else if (t[left] + t[right] < target) {
	                left++;
	            } else {
	                right--;
	            }
	 
	        }
	    }
	}

## Partition 中的双指针(重要)
- QuickSelect(QuickSort)
- 找pivot
- 交换
>  **quick** sort, **merge** sort, **heap** sort : O(nlogn) algorithm.

QuickSort and Kth largest element

- 都用到partition
- kth element本质是quicksort后找到第k个
- quicksort是对左右两边都递归，kth只递归k所在的那一边，所以是**O(N)**

### kth-largest-element
	public class Solution {
	    /**
	     * @param n: An integer
	     * @param nums: An array
	     * @return: the Kth largest element
	     */
	    public int kthLargestElement(int n, int[] nums) {
	        if(nums == null || nums.length == 0 || n < 1 || n > nums.length) {
	            return -1;
	        }
	
	        return partition(nums, 0, nums.length - 1, nums.length - n);
	    }
	    
	    public int partition(int[] nums, int start, int end, int k) {
	        if (start >= end) {
	            return nums[k];
	        }
	        
	        int left = start;
	        int right = end;
	        int pivot = nums[start + (end - start) / 2];
	        while (left <= right) {
	            while(left <= right && nums[left] < pivot) {
	                left ++; 
	            }
	            
	            while(left <= right && nums[right] > pivot) {
	                right --;
	            }
	            
	            if(left <= right) {
	                swap(nums, left, right);
	                left ++;
	                right --;
	            }
	        }
	        if(k <= right) {
	            return partition(nums, start, right, k);
	        }
	        if(k >= left) {
	            return partition(nums, left, end, k);
	        }
	        return nums[k]; // when k = num.length=> k!=left && k!=right
	        
	    }
	    
	    private void swap(int[] nums, int i, int j) {
	        int tmp = nums[i];
	        nums[i] = nums[j];
	        nums[j] = tmp;
	    }
	}

### QuickSort
	quickSort(A, 0, A.length-1);
	private void quickSort(int[]A, int start, int end){ 
		if(start>=end){ 
			return ; 
		} 
		int left = start, right = end; 
		int pivot = ...; 
	// pivot 的选区不能是首尾，要用value，不能用index int pivot = A[(start+end)/2]; // or use random
	// left<=right not left<right,避免left，right有交集
	// 否则会出现stack overflow，比如[1,2]
	while (left <= right){
	    while (left<=right && A[left] < pivot){
	        // A[left] < pivot 避免出现不均匀的情况
	        // 比如全部为1
	        left++;
	    }
	    while(right...){
	        right--;
	    }
	    if(left<=right){
	        swap(A[left], A[right]);
	        left++;
	        right--;
	    }
	}
	// recursion
	quickSort(A, start, right);
	quickSort(A, left, end);
	}

## 其他
### Move Zeros
- 第一个指针，left，每次往右一个（其实总是指向0）
- 第二个指针，right，指向所有的非0
	
代码： 

	public class Solution {
	    /**
	     * @param nums an integer array
	     * @return nothing, do this in-place
	     */
	    public void moveZeroes(int[] nums) {
	        // Write your code here
	        int left = 0, right = 0;
	        while (right < nums.length) {
	            if (nums[right] != 0) {
	                int temp = nums[left];
	                nums[left] = nums[right];
	                nums[right] = temp;
	                left++;
	            }
	            right++;
	        }
	    }
	}

### remove-duplicate-numbers-in-array
1. 在原数组上操作  
2. 将去除重复之后的元素放在数组的开头  
3. 返回去除重复元素之后的元素个数  
1. O(n)时间复杂度.或者O(nlogn)时间复杂度但没有额外空间

建立一个指针，指针指向目前位置不同的数的末尾，如果当前元素与末尾不同，则加入到末尾

	// O(n) time, O(n) space
	public class Solution {
	    /**
	     * @param nums an array of integers
	     * @return the number of unique integers
	     */
	    public int deduplication(int[] nums) {
	        // Write your code here
	        HashMap<Integer, Boolean> mp = new HashMap<Integer, Boolean>();
	        for (int i = 0; i < nums.length; ++i)
	            mp.put(nums[i], true);
	
	        int result = 0;
	        for (Map.Entry<Integer, Boolean> entry : mp.entrySet())
	            nums[result++] = entry.getKey();
	        return result;
	    }
	}
	
	// O(nlogn) time, O(1) extra space
	// 先排序，再...
	public class Solution {
	    /**
	     * @param nums an array of integers
	     * @return the number of unique integers
	     */
	    public int deduplication(int[] nums) {
	        if (nums.length == 0) {
	            return 0;
	        }
	        
	        Arrays.sort(nums);
	        int len = 0;
	        for (int i = 0; i < nums.length; i++) {
	            if (nums[i] != nums[len]) {
	                nums[++len] = nums[i];
	            }
	        }
	        return len + 1;
	    }
	}