# Leetcode
> Unsolved problems:

https://leetcode.com/problems/remove-k-digits/solution/ <br />
https://leetcode.com/problems/trapping-rain-water/ <br />
https://leetcode.com/submissions/detail/788252836/ <br />
https://leetcode.com/problems/predict-the-winner/ <br />
https://leetcode.com/problems/shortest-unsorted-continuous-subarray/ <br />                                                      https://leetcode.com/problems/regions-cut-by-slashes/ <br />
https://leetcode.com/problems/132-pattern/ <br />

> Link problem : https://leetcode.com/problems/construct-quad-tree/
```
class Solution {
    public Node construct(int[][] grid) {
        int n = grid.length;
        Node root = helper(grid, new int[] {0, 0}, new int[] {n-1, n-1});
        return root;
    }
    public Node helper(int[][] grid, int[] from, int[] to){
        if(from[0] == to[0] && from[1] == to[1]){ //Only one cell
            int row = from[0];
            int col = from[1];
            return new Node(grid[row][col] == 1, true);
        }
        int midRow = (from[0] + to[0] + 1)/2;
        int midCol = (from[1] + to[1] + 1)/2;
        Node topLeft = helper(grid, from, new int[] {midRow-1, midCol-1});
        Node topRight = helper(grid, new int[] {from[0], midCol}, new int[] {midRow-1, to[1]});
        Node bottomLeft = helper(grid, new int[] {midRow, from[1]}, new int[] {to[0], midCol-1});
        Node bottomRight = helper(grid, new int[] {midRow, midCol}, to);
        boolean val = topLeft.val || topRight.val || bottomLeft.val || bottomRight.val;
        boolean allLeaf = topLeft.isLeaf && topRight.isLeaf && bottomLeft.isLeaf && bottomRight.isLeaf;
        boolean sameValue = (topLeft.val == topRight.val) && (topRight.val == bottomLeft.val) && (bottomLeft.val == bottomRight.val);
        if(allLeaf && sameValue){
            return new Node(val, true);
        }
        return new Node(val, false, topLeft, topRight, bottomLeft, bottomRight);
    }
}
```
> Link problem : https://leetcode.com/problems/sliding-window-maximum/
```
class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        Deque<Integer> max = new LinkedList<>();
        int[] result = new int[nums.length - k + 1];
        for(int i = 0; i < nums.length; i++){
            while(max.size() > 0 && nums[i] > max.peekLast()){
                max.pollLast();
            }
            max.add(nums[i]);
            if(i >= k - 1){
                result[i-k+1] = max.peekFirst();
                if(max.peekFirst() == nums[i-k+1]){
                    max.pollFirst();
                }
            }
        }
        return result;
    }
}
```
> Link problem : https://leetcode.com/problems/longest-repeating-character-replacement/
```
class Solution {
    public int[] frequency = new int[26];
    public int characterReplacement(String s, int k) {
        int i = 0;
        int j = 0;
        int n = s.length();
        int result = 0;
        int count = k;
        for(j = 0; j < n; j++){
            char curr = s.charAt(j);
            boolean isFrequent = isMostFrequent(curr);
            if(j != 0){
                if(count > 0 && !isFrequent){
                    count--;
                }else if(count == 0 && (!isFrequent || (isFrequent && j-i-frequency[curr - 65] > k))){
                    frequency[s.charAt(i) - 65]--;
                    i++;
                }
            }
            frequency[curr - 65]++;
        }
        return j - i;
    }
    public boolean isMostFrequent(char a){
        int max = Integer.MIN_VALUE;
        for(int i = 0; i < 26; i++){
            max = Math.max(max, frequency[i]);
        }
        return frequency[a - 65] == max;
    }
}
```
> Link problem : https://leetcode.com/problems/meeting-scheduler/
```
class Solution {
    public List<Integer> minAvailableDuration(int[][] slots1, int[][] slots2, int duration) {
        List<Integer> result = new ArrayList<>();
        Arrays.sort(slots1, (a,b) -> a[0] - b[0]);
        Arrays.sort(slots2, (a,b) -> a[0] - b[0]);
        
        int i = 0;
        int j = 0;
        int length1 = slots1.length;
        int length2 = slots2.length;
        while(i < length1 && j < length2){
            //Start and end time of slot1
            int start1 = slots1[i][0];
            int end1 = slots1[i][1];
            //Start and end time of slot2
            int start2 = slots2[j][0];
            int end2 = slots2[j][1];
            //Find intersect
            int intersect = findIntersect(start1, end1, start2, end2);
            if(intersect >= duration){ //Have availability
                result.add(Math.max(start1, start2));
                result.add(result.get(0) + duration);
                break;
            }
            if(end1 < end2){ //Only iterate slots1 to avoid missing
                i++;
            }else if(end2 < end1){ //Only iterate slots2 to avoid missing
                j++;
            }else{ //Iterate both slots
                i++;
                j++;
            }
        }
        return result;
    }
    public int findIntersect(int start1, int end1, int start2, int end2){
        if(end1 < start2 || end2 < start1){ //No intersection
            return 0;
        }
        return Math.min(end1, end2) - Math.max(start1, start2);
    }
}
```
> Link problem : https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/
```
class Solution {
    public int[] searchRange(int[] nums, int target) {
        int[] result = new int[2];
        Arrays.fill(result, -1);
        if(nums.length == 0){
            return result;
        }
        int left = 0;
        int right = nums.length - 1;
        //Find the first position
        while(left < right){
            int mid = left + (right - left)/2;//Avoid overflow
            if(nums[mid] < target){
                left = mid + 1;
            }else if(nums[mid] > target){
                right = mid - 1;
            }else{
                if(mid == 0 || nums[mid-1] < target){ //Find the first position
                    result[0] = mid;
                    break;
                }else{
                    right = mid - 1;
                }
            }
        }
        if(nums[left] == target){
            result[0] = left;
        }
        if(result[0] == -1){//Cannot find first position
            return result;
        }
        left = 0;
        right = nums.length - 1;
        //Find last position
        while(left < right){
            int mid = left + (right - left)/2;//Avoid overflow
            if(nums[mid] < target){
                left = mid + 1;
            }else if(nums[mid] > target){
                right = mid - 1;
            }else{
                if(mid == 0 || nums[mid+1] > target){ //Find the first position
                    result[1] = mid;
                    break;
                }else{
                    left = mid + 1;
                }
            }
        }
        if(nums[right] == target){
            result[1] = right;
        }
        return result;
        
    }
}
```
> Link problem : https://leetcode.com/problems/3sum-closest/
```
class Solution {
    public int threeSumClosest(int[] nums, int target) {
        int n = nums.length;
        int result = 0;
        int gap = Integer.MAX_VALUE;
        Arrays.sort(nums);
        for(int i = 0; i < n; i++){
            if(i == 0 || nums[i-1] != nums[i]){ //Avoid duplicate
                int low = i+1;
                int high = n-1;
                while(low < high){
                    int sum = nums[i] + nums[low] + nums[high];
                    int temp = Math.abs(sum - target); // The gap between our current sum and the target
                    if(sum < target){
                        if(temp < gap){
                            result = sum;
                            gap = temp;
                        }
                        low++; // Increase the left if our sum is smaller than target
                    }else if(sum > target){
                        if(temp < gap){
                            result = sum;
                            gap = temp;
                        }
                        high--; // Decrease the right if our sum is larger than target
                    }else{
                        result = sum;
                        return result; //Already equal
                    }
                }
            }
        }
        return result;
    }
}
```
