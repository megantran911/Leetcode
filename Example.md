```
public class DoordashOA {
    public static int getMaximumRewardPoints(int n, int[] r1, int[] r2, int k){
        List<Integer> lost = new ArrayList<Integer>();
        int result = 0;
        for(int i = 0; i < n; i++){
            result += r1[i];
            lost.add(r2[i] - r1[i]);
        }
        Collections.sort(lost, new Comparator<Integer>() {
            @Override
            public int compare(Integer o1, Integer o2) {
                return o2 - o1;
            }
        });

        for(int i = 0; i < n - k; i++){
            result += lost.get(i);
        }
        return result;
    }
    public static int getMinOperations(int n, int[] source, int[] target){
        int[] diff = new int[n];
        for(int i = 0; i < n; i++){
            diff[i] = target[i] - source[i];
            //System.out.println(diff[i]);
            if(diff[i] < 0){// Source is larger than target
                return -1;
            }
        }
        //Have a peak that is not i == 0 or i == n-1
        for(int i = 0; i < n; i++){
            if(i > 0 && i < n - 1 && diff[i-1] < diff[i] && diff[i] > diff[i+1]){
                return -1;
            }
        }
        //Not set yet
        int left = -1;
        int right = -1;
        for(int i = 0; i < n; i++){
            if(i == 0 || diff[i] < diff[i-1]){
                left = i;
                right = left;
            }
            if(left != -1 && diff[left] == diff[i]){
                right = i;
            }
        }
        int result = diff[left]; //Increase the foundation
        int level = diff[left];
        //Check how many times we have to increase the left
        for(int i = left - 1; i >= 0; i--){
            result += diff[i] - level;
            level = diff[i];
        }
        //Check how many times we have to increase the right
        level = diff[right];
        for(int i = right+1; i < n; i++){
            result += diff[i] - level;
            level = diff[i];
        }
        return result;
    }
    public static void main(String[] args){
        int n = 5;
        int k = 3;
        int[] r1 = {5,4,3,2,1};
        int[] r2 = {1,2,3,4,5};
        System.out.println(getMaximumRewardPoints(n, r1, r2, k));
        int[] source = {1};
        int[] target = {2};
        System.out.println(getMinOperations(source.length, source, target));
    }
}
```
