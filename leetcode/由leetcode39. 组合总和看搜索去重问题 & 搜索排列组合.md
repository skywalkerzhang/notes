### 由leetcode[39. 组合总和](https://leetcode-cn.com/problems/combination-sum/)看搜索去重问题 & 搜索排列组合

今天一刷随想录，把组合一起做完了，有一些个人的想法，顺便好像是也思考到了排列，因为还没做排列题等做完排列再来修改这篇文章。

先来看一下lc39

```
题目
给你一个 无重复元素 的整数数组 candidates 和一个目标整数 target ，找出 candidates 中可以使数字和为目标数 target 的 所有不同组合 ，并以列表形式返回。你可以按 任意顺序 返回这些组合。

candidates 中的 同一个 数字可以 无限制重复被选取 。如果至少一个数字的被选数量不同，则两种组合是不同的。 

对于给定的输入，保证和为 target 的不同组合数少于 150 个。
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

联想小时候学数线段的时候，都是从左往右数，因为线段是无方向的，就好像这里的组合一样。因此这道题的正确代码如下

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

按照刚才的逻辑，我希望我的组合里能够拥有一样的元素，因此在**纵向**上我们是可以取重复的，**但是希望没有顺序而产生的重复，因此横向上是不能够有重复的数字的！**

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

