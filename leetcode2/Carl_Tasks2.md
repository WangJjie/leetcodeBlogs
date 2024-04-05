# LeetCode 每日任务 —— Days 2

## Leet977——有序数组的平方 —— simple —— 2024.4.5

### 考察点：**双指针**、reverse

本题主要是运用到了双指针的方法，题目中的关键信息在于**非递减**数组，并且从观察样例可以看出其数组可能含有负数，负数的平方可能会大于数组最右端正数的平方，这个是很关键的。

思考点一：我们可以从分界点0开始入手，0平方也是最小的，所以可以很方便直接插入results数组，然后我们分别在左右两边设置左右指针，分别比较其绝对值大小，最后滑动指针即可。但是这种方法我们其一是没法很快的找到0，其二是可能数组中不含0，找到分界点的左右指针会比较耗时；

思考点二：从两边往中间滑动。最理想状态下，该数组全为正数，那么我们直接按顺序平方后`push_back`进results数组即可，如果两边是异号，我们的思路是，先比较绝对值大小，将**大的平方**`push_back`进数组，然后滑动大的那一边指针，直到左右指针重合，然后单独`push_back`进那个重合的值的平方即可。这样得到的results数组我们需要做一下reverse，即可得到我们的非递减的输出。

### 代码实现：

cpp：

```cpp
class Solution {
public:
    vector<int> sortedSquares(vector<int>& nums) {
        int right = nums.size() - 1;
        int left = 0;
        vector<int> results;
        if (nums.size() == 1) {
            results.push_back(nums[left] * nums[left]);
            return results;
        }
        while (left < right) {
            if (abs(nums[left]) <= abs(nums[right])) {
                results.push_back(nums[right] * nums[right]);
                right--;
            } else {
                results.push_back(nums[left] * nums[left]);
                left++;
            }
        }
        results.push_back(nums[left] * nums[left]);
        std::reverse(results.begin(), results.end());
        return results;

    }
};
```

python：

```python
class Solution:
    def sortedSquares(self, nums: List[int]) -> List[int]:
        n = len(nums)
        left, right = 0, n-1
        results = [0] * n
        flag = 0
        while left < right and flag < n:
            if abs(nums[left]) <= abs(nums[right]):
                results[flag] = nums[right] * nums[right]
                right -= 1
            else:
                results[flag] = nums[left] * nums[left]
                left += 1
            flag += 1

        results[n-1] = nums[left] * nums[left]
        results.reverse()

        return results
```

时间复杂度：O(N) 因为数组长度为n，每个元素遍历一遍

空间复杂度：O(1) 只用常数量个values



## Leet209 —— 长度最小的子数组 —— Medium —— 2024.4.5

## 描述：给定一个含有 n 个正整数的数组和一个正整数 s ，找出该数组中满足其和 ≥ s 的长度最小的 连续 子数组，并返回其长度。如果不存在符合条件的子数组，返回 0。

### 考察点：双指针，滑动窗口

思考：本题关键在于找到最小的子数组，很形象我们需要用到两个指针，并且设置两个指针一开始都在数组的头部分别记为：start， end。我们先移动end指针，然后统计start到end间子数组的数值之和（这里设置一个results变量来记录即可），如果results小于target，则继续end++；如果当子数组元素数值之和大于或等于target后，我们将start++（为什么要这样操作，因为我们可能end这个位置突然来了个很大的数，所以我们即使把前面的子数组的数吐出来，依旧满足要求），然后重复这个过程，最终我们需要设置一个指示变量len，每一次满足要求的时候使用`min(len, end - start + 1)`来更新len的值，最终返回len即可；

代码：

```cpp
class Solution {
public:
    int minSubArrayLen(int target, vector<int>& nums) {
        int start = 0;
        int end = 0;
        int len = nums.size() + 2;
        int length = nums.size();
        int results = nums[start];
        while (end < length && start <= end) {
            if (results < target) {
                end++;
                if (end >= length) {
                    break;
                }
                results += nums[end];
            } else {
                len = std::min(len, end - start + 1);
                results -= nums[start++];
            }
        }
        if (len <= length) {
            return len;
        } else {
            return 0;
        }

    }
};
```

时间复杂度：o(N) 因为start和end至多只移动N次；

空间复杂度：o(1) 只使用了常数个变量



## Leet59 —— 螺旋矩阵 II —— Medium —— 2024.4.5

### 思考点：设置边界情况

本题需要我们很谨慎的设置四大边界，然后每次将上下左右边界收缩，然后反复在四个边界上进行操作的过程，非常繁琐，很容易写错。

代码：

```cpp
class Solution {
public:
    vector<vector<int>> generateMatrix(int n) {
        vector<vector<int>> matrix(n, vector<int>(n, 0));
        int upper_bound = 0, lower_bound = n - 1;
        int left_bound = 0, right_bound = n - 1;
        // 需要填入矩阵的数字
        int num = 1;
    
        while (num <= n * n) {
            if (upper_bound <= lower_bound) {
                // 在顶部从左向右遍历
                for (int j = left_bound; j <= right_bound; j++) {
                    matrix[upper_bound][j] = num++;
                }
                // 上边界下移
                upper_bound++;
            }
        
            if (left_bound <= right_bound) {
                // 在右侧从上向下遍历
                for (int i = upper_bound; i <= lower_bound; i++) {
                    matrix[i][right_bound] = num++;
                }
                // 右边界左移
                right_bound--;
            }
        
            if (upper_bound <= lower_bound) {
                // 在底部从右向左遍历
                for (int j = right_bound; j >= left_bound; j--) {
                    matrix[lower_bound][j] = num++;
                }
                // 下边界上移
                lower_bound--;
            }
        
            if (left_bound <= right_bound) {
                // 在左侧从下向上遍历
                for (int i = lower_bound; i >= upper_bound; i--) {
                    matrix[i][left_bound] = num++;
                }
                // 左边界右移
                left_bound++;
            }
        }
        return matrix;
    }
};
```



时间复杂度：O(N2)

空间复杂度：O(1)