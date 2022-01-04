### 由leetcode[39. 组合总和](https://leetcode-cn.com/problems/combination-sum/)看搜索去重问题 & 搜索排列组合

今天一刷随想录，把组合一起做完了，有一些个人的想法，顺便好像是也思考到了排列，因为还没做排列题等做完排列再来修改这篇文章。

先来看一下lc39

```
题目
给你一个 无重复元素 的整数数组 candidates 和一个目标整数 target ，找出 candidates 中可以使数字和为目标数 target 的 所有不同组合 ，并以列表形式返回。你可以按 任意顺序 返回这些组合。

candidates 中的 同一个 数字可以 无限制重复被选取 。如果至少一个数字的被选数量不同，则两种组合是不同的。 

对于给定的输入，保证和为 target 的不同组合数少于 150 个。
示例 1：

输入：candidates = [2,3,6,7], target = 7
输出：[[2,2,3],[7]]
解释：
2 和 3 可以形成一组候选，2 + 2 + 3 = 7 。注意 2 可以使用多次。
7 也是一个候选， 7 = 7 。
仅有这两种组合。

来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/combination-sum
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。
```

开始我的**错误**想法，既然元素都可以重复，那么在横向的时候只要每次都从数组的最前面开始就好了（即没有index——把相同数组视为不同数组）

展示下错误代码

```java
void backtracking(int[] candidates, int sum){
    if(sum < 0) return;
    if(sum == 0){
        res.add(new ArrayList(path));
        return;
    }
    for(int i = 0; i < candidates.length; i ++){
        // 剪枝
        if(candidates[i] > sum) continue;
        path.add(candidates[i]);
        backtracking(candidates, sum - candidates[i]);
        path.remove(path.size() - 1);
    }
}
```

得到结果如下

![image-20211229172034168](C:\Users\rina\AppData\Roaming\Typora\typora-user-images\image-20211229172034168.png)

我意识到这是**排列**，而不是**组合**，可是为什么横向使用重复数字不能达到题目的效果呢？

首先画出我的搜索树



![image-20211229173437831](C:\Users\rina\AppData\Roaming\Typora\typora-user-images\image-20211229173437831.png)

已经可以看出[2,5]和[5,2]已经重复了。对比卡哥的树

![39.组合总和](https://img-blog.csdnimg.cn/20201223170730367.png)

可以看出卡哥的树是有序的，横向时是从左向右的，这时可以去除掉排列产生的重复。

联想小时候学数线段的时候，都是从左往右数，因为线段是无方向的，就好像这里的组合一样。如果是排列问题就是数有向线段，那么对于每条线段，不光需要从左往右数，也要从右往左数，然后再纵向上去掉重复数过的线段。

如图

![image-20211231123812488](C:\Users\rina\AppData\Roaming\Typora\typora-user-images\image-20211231123812488.png)

对于下面这种情况，第二个顶点搜了第三个顶点没错，但是第三个顶点要重新搜索第二个顶点。也就是说每次横向都需要重新遍历数组（除了他自己，因为自己搜索自己没有意义）。这种去重是纵向维度的，等到后面排列的时候我们再讲。

![46.全排列](https://code-thinking-1253855093.file.myqcloud.com/pics/20211027181706.png)

因此这道题的正确代码如下

```java
class Solution {
    List<List<Integer>> res = new ArrayList<>();
    List<Integer> path = new ArrayList<>();
    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        backtracking(candidates, target, 0);
        return res;
    }
    // 用减法省一个参数
    void backtracking(int[] candidates, int sum, int idx){
        if(sum < 0) return;
        if(sum == 0){
            res.add(new ArrayList(path));
            return;
        }
        for(int i = idx; i < candidates.length; i ++){
            // 剪枝
            if(candidates[i] > sum) continue;
            path.add(candidates[i]);
            backtracking(candidates, sum - candidates[i], i); //是回溯的时候放i
            path.remove(path.size() - 1);
        }
    }
}
```

即**纵向重复**，这样就可以保证去除顺序不一导致的重复同时也可以拿到重复元素来求和。

再来看一题，[40. 组合总和 II](https://leetcode-cn.com/problems/combination-sum-ii/)

```java
题目
给定一个数组 candidates 和一个目标数 target ，找出 candidates 中所有可以使数字和为 target 的组合。

candidates 中的每个数字在每个组合中只能使用一次。

注意：解集不能包含重复的组合。 

输入: candidates = [10,1,2,7,6,1,5], target = 8,
输出:
[
[1,1,6],
[1,2,5],
[1,7],
[2,6]
]
```

按照刚才的逻辑，我希望我的组合里能够拥有一样的元素，因此在**纵向**上我们是可以取重复的，**但是希望没有因相同数字顺序不同而产生的重复，因此横向上是不能够有重复的数字的！**

按照卡哥的思路，先排序，用used数组记录状态，如果前后两个数相等（表示已知有重复数字了）的话。当used[i-1]为false恰恰代表前面的数字**在上层没有用过**。因为我们一定是**优先选择前面的数字操作的**，证明回溯完了。**当used[i-1]=true的时候，证明上层已经用过这个数字了，相同数字不在同一层。**

![40.组合总和II1](https://img-blog.csdnimg.cn/20201123202817973.png)

```java
class Solution {
    List<List<Integer>> res = new ArrayList<>();
    List<Integer> path = new ArrayList<>();
    boolean[] used;
    public List<List<Integer>> combinationSum2(int[] candidates, int target) {
        used = new boolean[candidates.length];
        Arrays.sort(candidates);
        backtracking(candidates, target, 0);
        return res;
    }
    void backtracking(int[] candidates, int sum, int idx){
        if(sum < 0) return;
        if(sum == 0){
            res.add(new ArrayList(path));
            return;
        }
        for(int i = idx; i < candidates.length; i ++){
            // 剪枝
            if(candidates[i] > sum) continue;
            // 如果上个操作过了，就当他没有，不存在了
            // 当used[i-1]为false恰恰代表前面的数字**在上层没有用过**。因为我们一定是**优先选择前面的数字操作的**，证明回溯完了。**当used[i-1]=true的时候，证明上层已经用过这个数字了，相同数字不在同一层。**
            if(i > 0 && candidates[i] == candidates[i - 1] && !used[i - 1]) continue; 
            used[i] = true;
            path.add(candidates[i]);
            backtracking(candidates, sum - candidates[i], i + 1);
            path.remove(path.size() - 1);
            used[i] = false;
        }
    }
}          
```

#### 排列问题

#### [46. 全排列](https://leetcode-cn.com/problems/permutations/)

```
给定一个不含重复数字的数组 nums ，返回其 所有可能的全排列 。你可以 按任意顺序 返回答案。

 

示例 1：

输入：nums = [1,2,3]
输出：[[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]
示例 2：

输入：nums = [0,1]
输出：[[0,1],[1,0]]
示例 3：

输入：nums = [1]
输出：[[1]]
```



之前有讲过，for i 从idx开始，代表横向是搜索有序的（因为组合是不计顺序的，所以只要按照一种顺序搜下去就行）

但是排列是有序的。因此横向应该是每个数组都要用的，而每个元素不能重复取，所以纵向是不能重复（不放回）的。用used[i]表示nums[i]有没有用过

```java
class Solution {
    List<List<Integer>> res = new ArrayList<>();
    List<Integer> path = new ArrayList<>();
    boolean[] used;
    public List<List<Integer>> permute(int[] nums) {
        used = new boolean[nums.length];
        backtracking(nums);
        return res;
    }
    void backtracking(int[] nums){
        if(path.size() == nums.length) {
            res.add(new ArrayList<>(path));
            return;
        }
        for(int i = 0; i < nums.length; i ++){
            // 控制列不要重复
            if(used[i] == true) continue;
            used[i] = true;
            path.add(nums[i]);
            backtracking(nums);
            path.remove(path.size() - 1);
            used[i] = false;
        }
    }
}
```

这里的used[i]是true就不能再搜索了，保证了纵向是不重复的（或者是不放回）。

#### 消除横向重复和纵向重复的总结

正如上文提到，在排序问题中是要考虑如何去除**纵向重复**的，和横向重复一样都是使用used数组（也可以set之类），但是判定结果却不一样。

控制列不重复是true。就是说之前上一层列用过了就不能用了。和之前控制每层的false恰好相反。

**对于消除每层的重复来说，卡哥教了两种写法，一种是定义全局变量（要先排序），一种是每次递归时建立新的visit数组。**

* 定义全局变量是因为排序了，所以重复的数字必然相邻，这时看**上层**是否用了这个数字，因为一定是优先使用上层数字的，所以上层用了就可以放心用，如果上层没用过证明已经回溯了，就是完全用过了，所以一定不能再用
* 而局部变量就不需要清空（[491. 递增子序列](https://leetcode-cn.com/problems/increasing-subsequences/)）**它只记录本层元素是否重复使用，新的一层都会重新定义（清空）**，要着眼于纵向的递归，是单条的，不涉及回溯哦。（之前因为是全局变量重复使用才回溯）

**对于消除每列的重复而言，上层用过了这一层肯定不能用，然后全局变量，用完需要回溯**



根据这一准则，去看[47. 全排列 II](https://leetcode-cn.com/problems/permutations-ii/)

```
给定一个可包含重复数字的序列 nums ，按任意顺序 返回所有不重复的全排列。

 

示例 1：

输入：nums = [1,1,2]
输出：
[[1,1,2],
 [1,2,1],
 [2,1,1]]
示例 2：

输入：nums = [1,2,3]
输出：[[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]
```



你会发现，首先需要消除相同数字的重复，所以横向要去重，再者纵向搜索要保证搜索时元素不放回。很容易写出代码

```java
class Solution {
    List<List<Integer>> res = new ArrayList<>();
    List<Integer> path = new ArrayList<>();
    boolean[] used;
    public List<List<Integer>> permuteUnique(int[] nums) {
        used = new boolean[nums.length];
        Arrays.sort(nums);
        backtracking(nums);
        return res;
    }
    void backtracking(int[] nums){
        if(path.size() == nums.length) {
            res.add(new ArrayList<>(path));
            return;
        }
        for(int i = 0; i < nums.length; i ++){
            // 控制行不要重复
            if(i > 0 && nums[i - 1] == nums[i] && used[i - 1] == false) continue;
            // 控制列不重复
            if(used[i] == false){
                used[i] = true;
                path.add(nums[i]);
                backtracking(nums);
                path.remove(path.size() - 1);
                used[i] = false;
            }
        }
    }
}
```

