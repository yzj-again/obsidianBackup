---
tags:
  - 算法
  - 数组
date: 2025-01-10
---
经常刷力扣的读者肯定知道鼎鼎有名的 `twoSum` 问题，不过除了 `twoSum` 问题，力扣上面还有 `3Sum`，`4Sum` 问题，以后如果想出个 `5Sum`，`6Sum` 也不是不可以。

总结来说，这类 `nSum` 问题就是给你输入一个数组 `nums` 和一个目标和 `target`，让你从 `nums` 选择 `n` 个数，使得这些数字之和为 `target`。

那么，对于这种问题有没有什么好办法用套路解决呢？本文就由浅入深，层层推进，用一个函数来解决所有 `nSum` 类型的问题。

提前说一下，对于本篇文章探讨的题目，使用 C++ 编写的代码更简洁易懂些，所以本文给出的都是 C++ 代码，你可以自行翻译成熟悉的语言。

## 一、twoSum 问题

我先来编一道 twoSum 题目：

如果假设输入一个数组 `nums` 和一个目标和 `target`，**请你返回 `nums` 中能够凑出 `target` 的两个元素的值**，比如输入 `nums = [1,3,5,6], target = 9`，那么算法返回两个元素 `[3,6]`。可以假设只有且仅有一对儿元素可以凑出 `target`。

我们可以先对 `nums` 排序，然后利用前文 [[双指针技巧秒杀七道链表题目]] 写过的左右双指针技巧，从两端相向而行就行了：

```cpp
vector<int> twoSum(vector<int>& nums, int target) {
    // 先对数组排序
    sort(nums.begin(), nums.end());
    // 左右指针
    int lo = 0, hi = nums.size() - 1;
    while (lo < hi) {
        int sum = nums[lo] + nums[hi];
        // 根据 sum 和 target 的比较，移动左右指针
        if (sum < target) {
            lo++;
        } else if (sum > target) {
            hi--;
        } else if (sum == target) {
            return {nums[lo], nums[hi]};
        }
    }
    return {};
}
```

这样就可以解决这个问题，力扣第 1 题「[两数之和](https://leetcode.cn/problems/two-sum)」和力扣第 167 题「[两数之和 II - 输入有序数组](https://leetcode.cn/problems/two-sum-ii-input-array-is-sorted)」稍加修改就可以用类似的思路解决，我这里就不写了。

不过我要继续魔改题目，把这个题目变得更泛化，更困难一点：

**`nums` 中可能有多对儿元素之和都等于 `target`，请你的算法返回所有和为 `target` 的元素对儿，其中不能出现重复**。

函数签名如下：

```cpp
vector<vector<int>> twoSumTarget(vector<int>& nums, int target);
```

比如说输入为 `nums = [1,3,1,2,2,3], target = 4`，那么算法返回的结果就是：`[[1,3],[2,2]]`（注意，我要求返回元素，而不是索引）。

对于修改后的问题，关键难点是现在可能有多个和为 `target` 的数对儿，还不能重复，比如上述例子中 `[1,3]` 和 `[3,1]` 就算重复，只能算一次。

首先，基本思路肯定还是排序加双指针：

```cpp
#include <vector>
#include <algorithm>

vector<vector<int>> twoSumTarget(vector<int>& nums, int target) {
    // 先对数组排序
    sort(nums.begin(), nums.end());
    vector<vector<int>> res;
    int lo = 0, hi = nums.size() - 1;
    while (lo < hi) {
        int sum = nums[lo] + nums[hi];
        // 根据 sum 和 target 的比较，移动左右指针
        if (sum < target)
            lo++;
        else if (sum > target)
            hi--;
        else {
            res.push_back({ nums[lo], nums[hi] });
            lo++;
            hi--;
        }
    }
    return res;
}

```

但是，这样实现会造成重复的结果，比如说 `nums = [1,1,1,2,2,3,3], target = 4`，得到的结果中 `[1,3]` 肯定会重复。

出问题的地方在于 `sum == target` 条件的 if 分支，当给 `res` 加入一次结果后，`lo` 和 `hi` 不仅应该相向而行，而且应该跳过所有重复的元素：

![image.png](https://typora-yzj.oss-cn-hangzhou.aliyuncs.com/img/20250111004415.png)

所以，可以对双指针的 while 循环做出如下修改：

```cpp
while (lo < hi) {
    int sum = nums[lo] + nums[hi];
    // 记录索引 lo 和 hi 最初对应的值
    int left = nums[lo], right = nums[hi];
    if (sum < target) {
        lo++;   
    } else if (sum > target) {
        hi--;
    } else {
        res.push_back({left, right});
        // 跳过所有重复的元素
        while (lo < hi && nums[lo] == left) lo++;
        while (lo < hi && nums[hi] == right) hi--;
    }
}
```

这样就可以保证一个答案只被添加一次，重复的结果都会被跳过，可以得到正确的答案。不过，受这个思路的启发，其实前两个 if 分支也是可以做一点效率优化，跳过相同的元素：

```cpp
vector<vector<int>> twoSumTarget(vector<int>& nums, int target) {
    // nums 数组必须有序
    sort(nums.begin(), nums.end());
    int lo = 0, hi = nums.size() - 1;
    vector<vector<int>> res;
    while (lo < hi) {
        int sum = nums[lo] + nums[hi];
        int left = nums[lo], right = nums[hi];
        if (sum < target) {
            // 跳过不符合条件的相同结果
            while (lo < hi && nums[lo] == left) lo++;
        } else if (sum > target) {
            // 跳过不符合条件的相同结果
            while (lo < hi && nums[hi] == right) hi--;
        } else {
            res.push_back({left, right});
            // 跳过符合条件的重复结果
            while (lo < hi && nums[lo] == left) lo++;
            while (lo < hi && nums[hi] == right) hi--;
        }
    }
    return res;
}
```

这样，一个通用化的 `twoSum` 函数就写出来了，请确保你理解了该算法的逻辑，我们后面解决 `3Sum` 和 `4Sum` 的时候会复用这个函数。

这个函数的时间复杂度非常容易看出来，双指针操作的部分虽然有那么多 while 循环，但是时间复杂度还是 $O(N)$，而排序的时间复杂度是 $O(NlogN)$，所以这个函数的时间复杂度是 $O(NlogN)$。

### 附题

#### 两数之和

力扣第 1 题「[两数之和](https://leetcode.cn/problems/two-sum)」，思路见上面，当然比较好的做法是使用哈希表，见 [[两数之和]]

```cpp
vector<int> twoSum(vector<int>& nums, int target) {
        // 创建一个包含原始索引的数组
        vector<pair<int, int>> numsWithIndex;
        for (int i = 0; i < nums.size(); i++) {
            numsWithIndex.push_back({nums[i], i});
        }
        // 按数值排序
        sort(numsWithIndex.begin(), numsWithIndex.end());
        int lo = 0;
        int hi = nums.size() - 1;
        while (lo < hi) {
            int sum = numsWithIndex[lo].first + numsWithIndex[hi].first;
            if (sum < target) {
                lo++;
            } else if (sum > target) {
                hi--;
            } else {
                // 返回原始索引
                return {numsWithIndex[lo].second, numsWithIndex[hi].second};
            }
        }
        return {};
    }
```

---

## 二、3Sum 问题

这是力扣第 15 题「[三数之和](https://leetcode.cn/problems/3sum)」。

题目就是让我们找 `nums` 中和为 0 的三个元素，返回所有可能的三元组（triple），函数签名如下：

```cpp
vector<vector<int>> threeSum(vector<int>& nums);
```

这样，我们再泛化一下题目，不要光和为 0 的三元组了，计算和为 `target` 的三元组吧，同上面的 `twoSum` 一样，也不允许重复的结果：

```cpp
vector<vector<int>> threeSum(vector<int>& nums) {
    return threeSumTarget(nums, 0);
}

vector<vector<int>> threeSumTarget(vector<int>& nums, int target) {
    // 输入数组 nums，返回所有和为 target 的三元组
}
```

这个问题怎么解决呢？**很简单，穷举呗**。现在我们想找和为 `target` 的三个数字，那么对于第一个数字，可能是什么？`nums` 中的每一个元素 `nums[i]` 都有可能！

那么，确定了第一个数字之后，剩下的两个数字可以是什么呢？其实就是和为 `target - nums[i]` 的两个数字呗，那不就是 `twoSum` 函数解决的问题么？

可以直接写代码了，需要把 `twoSum` 函数稍作修改即可复用：

```cpp
class Solution {
public:
    // 计算数组 nums 中所有和为 target 的三元组
    vector<vector<int>> threeSumTarget(vector<int>& nums, int target) {
        // 数组得排个序
        sort(nums.begin(), nums.end());
        int n = nums.size();
        vector<vector<int>> res;
        // 穷举 threeSum 的第一个数
        for (int i = 0; i < n; i++) {
            // 对 target - nums[i] 计算 twoSum
            vector<vector<int>> tuples = twoSumTarget(nums, i + 1, target - nums[i]);
            // 如果存在满足条件的二元组，再加上 nums[i] 就是结果三元组
            for (vector<int>& tuple : tuples) {
                tuple.push_back(nums[i]);
                res.push_back(tuple);
            }
            // 跳过第一个数字重复的情况，否则会出现重复结果
            // n-2 n-1 n n-1是边界
            while (i < n - 1 && nums[i] == nums[i + 1]) i++;
        }
        return res;
    }

private:
    // 从 nums[start] 开始，计算有序数组 nums 中所有和为 target 的二元组
    vector<vector<int>> twoSumTarget(vector<int>& nums, int start, int target) {
        // 左指针改为从 start 开始，其他不变
        int lo = start, hi = nums.size() - 1;
        vector<vector<int>> res;
        while (lo < hi) {
            ...
        }
        return res;

    }
};
```

需要注意的是，类似 `twoSum`，`3Sum` 的结果也可能重复，比如输入是 `nums = [1,1,1,2,3], target = 6`，结果就会重复，可能选出多个 `[1,2,3]`。

**避免重复的关键点在于，不能让第一个数重复，至于后面的两个数，我们复用的 `twoSumTarget` 函数会保证它们不重复**。所以代码中必须用一个 while 循环来保证 `3Sum` 中第一个元素不重复。

至此，`3Sum` 问题就解决了，时间复杂度不难算，排序的复杂度为 $O(NlogN)$，`twoSumTarget` 函数中的双指针操作为 $O(N)$，`threeSumTarget` 函数在 for 循环中调用 `twoSumTarget` 所以总的时间复杂度就是 $O(NlogN+N^2)=O(N^2)$。

### 疑问

为什么需要跳过重复元素？我们只需要确保 **固定的第一个数 `nums[i]` 不重复**，以避免重复的三元组。

- **避免重复结果**：
    
    - 如果数组中有多个相同的元素，直接穷举会导致重复的三元组。
    - 如果 `nums[i]` 和 `nums[i + 1]` 相等，说明当前 `nums[i]` 已经作为第一个数被处理过了。
    - 跳过所有与 `nums[i]` 相同的元素，避免重复处理。
    - 如果 `nums[i]` 和 `nums[i + 1]` 相同，那么 `nums[i + 1]` 作为第一个数时，会得到与 `nums[i]` 相同的结果，因此需要跳过。
- **提高效率**：
    
    - 跳过重复元素可以减少不必要的计算，提高算法效率。

### 三数之和解法二

在外循环，依旧是先固定一个数，因为题目是要求输出顺序和三元组顺序并不重要，那么可以在一次循环内简化为再进行一次两数之和，见下面题解

```cpp
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        // 排序
        sort(nums.begin(), nums.end());
        vector<vector<int>> res;
        int n = nums.size();
        // n-3 n-2 n-1 n index
        //  i   j   k
        for (int i = 0; i < n - 2; i++) {
            int x = nums[i];
            // 对第一个数字x，跳过重复的第一个数
            // 当前x和前一个数字相同，则跳过当前x
            if (i > 0 && x == nums[i - 1]) {
                continue;
            }
            // 优化一，x和最小的两个数和都>0，没有任何机会，直接退出
            if (x + nums[i + 1] + nums[i + 2] > 0) break; 
            // 优化二，x和最大的两个数的和<0，x增大，有机会，但当前x要跳过
            if (x + nums[n - 2] + nums[n - 1] < 0) continue; 
            // 对 target - nums[i] 计算 twoSum
            int j = i + 1;
            int k = n - 1;
            // j和k是这次循环的两数之和边界
            while (j < k) {
                int sum = x + nums[j] + nums[k];
                int left = nums[j], right = nums[k];
                if (sum > 0) {
                    while (j < k && nums[k] == right)
                        k--;
                } else if (sum < 0) {
                    while (j < k && nums[j] == left)
                        j++;
                } else {
                    // 找到目标三元组
                    res.push_back({x, nums[j], nums[k]});
                    // 跳过重复的 nums[j] 和 nums[k]
                    while (j < k && nums[j] == left)
                        j++;
                    while (j < k && nums[k] == right)
                        k--;
                }
            }
        }
        return res;
    }
};
```

用 left 和 right 记录一次循环时两数之和的边界，可以再判断重复时简化，否则，需要再次考虑边界：

```cpp
// 当前数和下一个数进行对比，所以要j、i在while后再次移动一次。
if (sum > 0) {
    while (j < k && nums[k] == nums[k - 1])
        k--;
    k--; // 再次移动右指针
    } else if (sum < 0) {
        while (j < k && nums[j] == nums[j + 1])
            j++;
        j++; // 再次移动左指针
    } else {
        // 找到目标三元组
        res.push_back({x, nums[j], nums[k]});
        // 跳过重复的 nums[j] 和 nums[k]
        while (j < k && nums[j] == nums[j + 1])
            j++;
        while (j < k && nums[k] == nums[k - 1])
            k--;
        j++; // 再次移动左指针
        k--; // 再次移动右指针
}
```

```cpp
// 当前数和上一个数进行对比，所以要j、i在while前先移动一次。
if (sum > 0) {
    k--; // 先移动一次右指针
    while (j < k && nums[k] == nums[k + 1])
        k--;
    } else if (sum < 0) {
        j++; // 先移动一次左指针
        while (j < k && nums[j] == nums[j - 1])
            j++;
    } else {
        // 找到目标三元组
        res.push_back({x, nums[j], nums[k]});
        // 跳过重复的 nums[j] 和 nums[k]
        j++; // 先移动左指针
        k--; // 先移动右指针
        while (j < k && nums[j] == nums[j - 1])
            j++;
        while (j < k && nums[k] == nums[k + 1])
            k--;

}
```

## 三、4Sum 问题

这是力扣第 18 题「[四数之和](https://leetcode.cn/problems/4sum)」：

给你一个由 `n` 个整数组成的数组 `nums` ，和一个目标值 `target` 。请你找出并返回满足下述全部条件且**不重复**的四元组 `[nums[a], nums[b], nums[c], nums[d]]` （若两个四元组元素一一对应，则认为两个四元组重复）：

函数签名如下：

```cpp
vector<vector<int>> fourSum(vector<int>& nums, int target);
```

都到这份上了，`4Sum` 完全就可以用相同的思路：穷举第一个数字，然后调用 `3Sum` 函数计算剩下三个数，最后组合出和为 `target` 的四元组。

```cpp
class Solution {
public:
    vector<vector<int>> fourSum(vector<int>& nums, int target) {
        // 数组需要排序
        sort(nums.begin(), nums.end());
        int n = nums.size();
        vector<vector<int>> res;
        // 穷举 fourSum 的第一个数
        for (int i = 0; i < n; i++) {
            // 对 target - nums[i] 计算 threeSum
            vector<vector<int>> triples = threeSumTarget(nums, i + 1, target - nums[i]);
            // 如果存在满足条件的三元组，再加上 nums[i] 就是结果四元组
            for (vector<int>& triple : triples) {
                triple.push_back(nums[i]);
                res.push_back(triple);
            }
            // fourSum 的第一个数不能重复
            while (i < n - 1 && nums[i] == nums[i + 1]) i++;
        }
        return res;
    }

    // 从 nums[start] 开始，计算有序数组 nums 中所有和为 target 的三元组
    vector<vector<int>> threeSumTarget(vector<int>& nums, int start, long target) {
        int n = nums.size();
        vector<vector<int>> res;
        // i 从 start 开始穷举，其他都不变
        for (int i = start; i < n; i++) {
            ...
        }
        return res;
    }
};
```

这样，按照相同的套路，`4Sum` 问题就解决了，时间复杂度的分析和之前类似，for 循环中调用了 `threeSumTarget` 函数，所以总的时间复杂度就是 $O(N3)$。

注意我们把 `threeSumTarget` 函数签名中的 `target` 变量设置为 `long` 类型，因为本题说了 `nums[i]` 和 `target` 的取值都是 `[-10^9, 10^9]`，`int` 类型的话会造成溢出。

### 四数之和解法二

上面会进行函数调用，现在改成迭代：

```cpp
class Solution {
public:
    vector<vector<int>> fourSum(vector<int>& nums, int target) {
        sort(nums);
        vector<vector<int>> ans;
        int n = nums.size();
        for (int a = 0; a < n - 3; a++) { // 枚举第一个数
            long long x = nums[a];        // 使用 long long 避免溢出
            if (a > 0 && x == nums[a - 1])
                continue; // 跳过重复数字
            if (x + nums[a + 1] + nums[a + 2] + nums[a + 3] > target)
                break; // 优化一
            if (x + nums[n - 3] + nums[n - 2] + nums[n - 1] < target)
                continue;                         // 优化二
            for (int b = a + 1; b < n - 2; b++) { // 枚举第二个数
                long long y = nums[b];
                if (b > a + 1 && y == nums[b - 1])
                    continue; // 跳过重复数字
                if (x + y + nums[b + 1] + nums[b + 2] > target)
                    break; // 优化一
                if (x + y + nums[n - 2] + nums[n - 1] < target)
                    continue; // 优化二
                int c = b + 1, d = n - 1;
                while (c < d) { // 双指针枚举第三个数和第四个数
                    long long s = x + y + nums[c] + nums[d]; // 四数之和
                    if (s > target)
                        d--;
                    else if (s < target)
                        c++;
                    else { // s == target
                        ans.push_back({(int)x, (int)y, nums[c], nums[d]});
                        c++;
                        while (c < d && nums[c] == nums[c - 1])
                            c++; // 跳过重复数字
                        d--;
                        while (c < d && nums[d] == nums[d + 1])
                            d--; // 跳过重复数字
                    }
                }
            }
        }
        return ans;
    }
};
```

> 注意相加时会越界，所以用 `long long` 类型。优化策略和三树之和相同。

## 四、100Sum 问题？

在 LeetCode 上，`4Sum` 就到头了，**但是回想刚才写 `3Sum` 和 `4Sum` 的过程，实际上是遵循相同的模式的**。我相信你只要稍微修改一下 `4Sum` 的函数就可以复用并解决 `5Sum` 问题，然后解决 `6Sum` 问题……

那么，如果我让你求 `100Sum` 问题，怎么办呢？其实我们可以观察上面这些解法，统一出一个 `nSum` 函数：

```cpp
// 注意：调用这个函数之前一定要先给 nums 排序
// n 填写想求的是几数之和，start 从哪个索引开始计算（一般填 0），target 填想凑出的目标和
vector<vector<int>> nSumTarget(vector<int>& nums, int n, int start, long target) {
    int sz = nums.size();
    vector<vector<int>> res;
    // 至少是 2Sum，且数组大小不应该小于 n
    if (n < 2 || sz < n) return res;
    // 2Sum 是 base case
    if (n == 2) {
        // 双指针那一套操作
        int lo = start, hi = sz - 1;
        while (lo < hi) {
            int sum = nums[lo] + nums[hi];
            int left = nums[lo], right = nums[hi];
            if (sum < target) {
                while (lo < hi && nums[lo] == left) lo++;
            } else if (sum > target) {
                while (lo < hi && nums[hi] == right) hi--;
            } else {
                res.push_back({left, right});
                while (lo < hi && nums[lo] == left) lo++;
                while (lo < hi && nums[hi] == right) hi--;
            }
        }
    } else {
        // n > 2 时，递归计算 (n-1)Sum 的结果
        for (int i = start; i < sz; i++) {
            vector<vector<int>> sub = nSumTarget(nums, n - 1, i + 1, target - nums[i]);
            for (vector<int>& arr : sub) {
                // (n-1)Sum 加上 nums[i] 就是 nSum
                arr.push_back(nums[i]);
                res.push_back(arr);
            }
            while (i < sz - 1 && nums[i] == nums[i + 1]) i++;
        }
    }
    return res;
}
```

嗯，看起来很长，实际上就是把之前的题目解法合并起来了，`n == 2` 时是 `twoSum` 的双指针解法，`n > 2` 时就是穷举第一个数字，然后递归调用计算 `(n-1)Sum`，组装答案。

根据之前几道题的时间复杂度可以推算，本函数的时间复杂度应该是 $O(N^(n−1))$，`N` 为数组的长度，`n` 为组成和的数字的个数。

**需要注意的是，调用这个 `nSumTarget` 函数之前一定要先给 `nums` 数组排序**，因为 `nSumTarget` 是一个递归函数，如果在 `nSumTarget` 函数里调用排序函数，那么每次递归都会进行没有必要的排序，效率会非常低。

比如说现在我们写 LeetCode 上的 `4Sum` 问题：

```cpp
class Solution {
public:
    vector<vector<int>> fourSum(vector<int>& nums, int target) {
        sort(nums.begin(), nums.end());
        // n 为 4，从 nums[0] 开始计算和为 target 的四元组
        return nSumTarget(nums, 4, 0, target);
    }

    // 注意：调用这个函数之前一定要先给 nums 排序
    // n 填写想求的是几数之和，start 从哪个索引开始计算（一般填 0），target 填想凑出的目标和
    vector<vector<int>> nSumTarget(vector<int>& nums, int n, int start, long target) {
        // 见上文
    }
};
```

再比如 LeetCode 的 `3Sum` 问题，找 `target == 0` 的三元组：

```cpp
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        sort(nums.begin(), nums.end());
        // n 为 3，从 nums[0] 开始计算和为 0 的三元组
        return nSumTarget(nums, 3, 0, 0);
    }

    // 注意：调用这个函数之前一定要先给 nums 排序
    vector<vector<int>> nSumTarget(vector<int>& nums, int n, int start, long target) {
        // 见上文
    }
};
```