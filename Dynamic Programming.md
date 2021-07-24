# 动态规划


### 一、0 1背包问题

思路：对于第i件物品来说，存在两种情况：

假设现在的背包容量为C

1. 取当前物品：这种情况的价值就是“前i-1件物品的最大价值”，并且现在的背包容量为“C - 当前物品的重量”
2. 不取当前物品：这种情况的价值就是(“前i-1件物品的最大价值” + 当前物品的价值)， 并且现在的背包容量为C

这样就将大问题 归结于小的问题，就是 “是否取当前的物品，取决于之前的物品的价值以及背包的容量”

得到方程：i----第i件物品 j----背包容量 v[i]----物品价值。w[i]----物品重量

有dp二维数组：

dp[i][j] = Math.max(dp[i-1, j], dp[i-1, j-w[i]] + v[i])

```JS
const KnapSack = (values, weights, C) => {
	let dp = [[]]
	// 因为下面每一行依赖于上一行的数据，所以要初始化第一行
	for (let i = 0; i <= weights.length; i++) {
		if (weights[i] < C) {
			dp[0][i] = 0
		} else {
			dp[0][i] = values[i]
		}
	}
	for (let i = 0; i <= C; i++) {
		for (let j = 1; j <= weights.length; j++) {
			if (!dp[j]) {
				dp[j] = []
			}
			if (i < weights[j]) {
				dp[j][i] = dp[j-1][i]
			} else {
				dp[j][i] = Math.max(dp[j-1][i], dp[j-1][i - weights[j]] + values[j])
			}
		}
	}
	return dp[weight.length][C]
}
```

### 二、零钱问题

##### LeetCode 322. 零钱兑换

题目描述：给定不同面额的硬币 coins 和一个总金额amount。编写一个函数来计算可以凑成总金额所需的最少的硬币个数。如果没有任何一种硬币组合能组成总金额，返回 -1。

你可以认为每种硬币的数量是无限的。

```JS
输入：coins = [1, 2, 5], amount = 11
输出：3 
解释：11 = 5 + 5 + 1
```
状态转换方程
```JS
	// i 是金额
	dp[i] = Math.min(dp[i], dp[i - coin] + 1)
```	
实现：
```JS
  var fn = (coins, amount) => {

  	let dp = new Array(amount + 1)
  	dp.fill(amount + 1)
  	dp[0] = 0
  	for (let i = 1; i <= amount; i++) {
  		for (let j = 0; j < coins.length; j++) {
  			if (i >= coins[j]) {
  				dp[i] = Math.min(dp[i], dp[i - coins[j]] + 1)
  			}
  		}
  	}
  	console.log(dp)
  	return dp[amount]
  }
```

思路总结： 
	
对于示例来说，凑齐金额i的最优解其实就是 `Math.min(F(i - 1), F(i - 2), F(i - 5)) + 1`
由于我们需要遍历所有可取的面额，所以如何在循环中对所有面额的情况取最小呢？

**俩俩取最小，然后赋值给当前的dp[i]，然后每次都用dp[i]与下一个再取最小**

### 三、股票问题

> 股票问题神文：
> [Leetcode股票问题通解（上)](https://zhuanlan.zhihu.com/p/105760677)
> [秒杀面试中的股票问题-看这一篇就够了](https://medium.com/@USTCLink/%E7%A7%92%E6%9D%80%E9%9D%A2%E8%AF%95%E4%B8%AD%E7%9A%84%E8%82%A1%E7%A5%A8%E9%97%AE%E9%A2%98-%E7%9C%8B%E8%BF%99%E4%B8%80%E7%AF%87%E5%B0%B1%E5%A4%9F%E4%BA%86-fa730ae4681d)
```JS
// k 是交易次数
dp[i][0] = Math.max(dp[0][k][i-1], dp[1][k][i-1] + prices[i])
dp[i][1] = Math.max(dp[1][k][i-1], dp[0][k][i-1] - prices[i])

```

##### LeetCode 121 买卖股票的最佳时机 

只能交易一次

```JS
// 测试用例 [7,1,5,3,6,4]
const highestRevenue = (prices) => {
	let n = prices.length, dp = [[0, 0 - prices[0]]]

	for (let i = 1; i < prices.length; i++) {
		if (!dp[i]) {
			dp[i] = []
		}
		dp[i][0] = Math.max(dp[i-1][0], dp[i-1][1] + prices[i])
		dp[i][1] = Math.max(dp[i-1][1], 0-prices[i])
	}
	return dp[n-1][0]
}
```
我们现在使用二维数组存储dp表，但是其实当天的最大利润只与前一天的最大利润有关系，所以可以将二维数组去掉，直接保存前一天的最大利润即可

优化如下
**TODO 优化空间复杂度**

设a为当天没有股票的最大收益，b为当天有股票的最大收益，这样每次遍历计算a的时候，使用的b就是前一天有股票的情况
```JS
const highestRevenue = (prices) => {
	//  初始化 a为前一天没有股票的最大值，b为前一天有股票的最大利润
	let n = prices.length, a = 0, b = 0 - prices[0]

	for (let i = 1; i < n; i++) {
		// 当天的利润 取决于前一天的利润
		a = Math.max(a, b + prices[i])
		b = Math.max(b, 0 - prices[i])
	}
	return a
}

```

##### LeetCode 122 买卖股票的最佳时机 

可以交易多次

依然套用状态转移方程：

`dp[i][0] = Math.max(dp[i-1][0], dp[i-1][1] + price[i])`
`dp[i][1] = Math.max(dp[i-1][1], dp[i-1][0] - price[i])`

实现如下：

```JS
const fn = (prices) => {
	let length = prices.length, dp = [[0, 0-prices[0]]]
	for (let i = 1; i < prices.length; i++) {
		if (!dp[i]) {
			dp[i] = []
		}
		dp[i][0] = Math.max(dp[i-1][0], dp[i-1][1] + prices[i])
		dp[i][1] = Math.max(dp[i-1][1], dp[i-1][0] - prices[i])
	}
	return dp[prices.length - 1][0]
}
```

##### LeetCode 309 最佳买卖股票时机含冷冻期

含有冷冻期的意思就是，第i天卖出股票之后，第i+1天就不能买入了

```JS
	const fn = (prices) => {
    // dp[i][0] 持有股票
    // dp[i][1] 不持有股票且不在冷冻期
    // dp[i][2] 不持有股票且在冷冻期
    // 注：上面的状态指的是当天结束时候临界点的状态，会影响i+1天。这个临界点的状态只会收到当天是否卖出的影响
    // 这个临界点只会影响i+1天是否能买卖，而不会受到i-1天临界点的影响。
    let dp = [[0 - prices[0], 0, 0]]
    for (let i = 0; i < prices; i++) {
        if (!dp[i]) {
            dp[i] = []
        }
        dp[i][0] = Math.max(dp[i-1][0], dp[i-1][1] - prices[i])
        // dp[i][1] = dp[i-1][1]
        dp[i][1] = Math.max(dp[i-1][1], dp[i-1][2])
        dp[i][2] = dp[i-1][0] + prices[i]
    }
    return Math.max(dp[prices.length - 1][1], dp[prices.length - 1][2])
	}
```


##### LeetCode 714 买卖股票的最佳时机含手续费

题目要点

1. 每次交易都需要支付手续费/fee(所谓的每次交易就是指卖出的时候交手续费)
2. 可以进行无限次交易

对于这个题目依然可以套用之前的状态转移方程

```JS
	const fn = (prices, fee) => {
		let length = prices.length, dp = [[0, 0 - prices[0]]]
		for (let i = 1; i < prices.length; i++) {
			if (!dp[i]) {
				dp[i] = []
			}
			dp[i][0] = Math.max(dp[i-1][0], dp[i-1][1] + prices[i] - fee)
			dp[i][1] = Math.max(dp[i-1][1], dp[i-1][0] - prices[i])
		}

		return dp[length -1][0]
	}
```

### 四、盛水问题

##### LeetCode 11 盛最多水的容器

给你 n 个非负整数 a1，a2，...，an，每个数代表坐标中的一个点 (i, ai) 。在坐标内画 n 条垂直线，垂直线 i 的两个端点分别为 (i, ai) 和 (i, 0) 。找出其中的两条线，使得它们与 x 轴共同构成的容器可以容纳最多的水。

输出为最大的盛水量

```JS
	var max_area = (height) => {
		let i = 0, j = height.length -1, result = 0
		while (i != j && height.length) {
			result = Math.max(Math.min(height[i], height[j]) * (j - i), result)
			i < j ? (i++) : (j--)
		}
		return result
	}
```

思路：

设置头尾指针，盛水多少由短边决定，所以每次计算出一个result之后，向中间移动较短的那个height， 目的是尝试让短边移动到更长一点的新的位置。之所以设置头尾指针，我觉得是因为至少让"底盘"最大化，然后让短的边尝试寻找更长的，如果下一个边盛水更多，那么就更新result




### 五、三数之和

**给定一个包含 n 个整数的数组 nums，判断 nums 中是否存在三个元素 a，b，c ，使得 a + b + c = 0 ？找出所有满足条件且不重复的三元组。**

例如： 

```
例如, 给定数组 nums = [-1, 0, 1, 2, -1, -4]，

满足要求的三元组集合为：
[
  [-1, 0, 1],
  [-1, -1, 2]
]
```

```JS
var fn = (nums) => {
	nums = nums.sort((a, b) => a - b)
	let result = []
	for (let i = 0; i < nums.length; i++) {
		let j = i + 1, k = nums.length - 1
		if (nums[i] == nums[i-1]) {
			continue
		}
		while (j < k) {
			if (nums[i] + nums[j] + nums[k] === 0) {
				result.push([nums[i], nums[j], nums[k]])
				j++
				k--
			} else if (nums[i] + nums[j] + nums[k] > 0) {
				k--
			} else {
				j++
			}
		}
	}
	return result
}
```
思路 ：

使用三个指针，将三个指针对应的三个数相加，如果结果已经是0，则将这个三个数组成的数组加入结果中，并且将j,k向中间移动（因为对于当前的最大的k和最小的j已经等于0了，那么就将k减小，j增大）。如果当前小于0，那么就将j向后移动。反之将k向左移动


### 六、字符串问题

##### LeetCode 14.最长公共前缀

题目描述： 

**编写一个函数来查找字符串数组中的最长公共前缀。
如果不存在公共前缀，返回空字符串 ""。**

示例： 

```
输入: ["flower","flow","flight"]
输出: "fl"
```


以下是我自己的实现

有一个细节是我之前一直没有意识到的，for循环遍历之后，指针会**等于**length
```JS
	var fn = (strs) => {
		let i = 0, len = strs[0].length, result = ''
		while (i < len) {
			let j = 1
			for (; j < strs.length; j++) {
				if (strs[j][i] !== strs[0][i]) {
					break
				}
			}
			// 之前写成了 j === strs.length - 1
			if (j === strs.length) {
					result+=strs[j - 1][i]
					i++
			} else {
					break
			}
		}
		return result
	}
```


##### LeetCode-3、无重复字符的最长子串

给定一个字符串，请你找出其中不含有重复字符的最长字串

示例：

输入： “abcabcbb”
输出： 3 （abc）

```JS
var fn = (str) => {
	let result = 0, j = 1, sub = [str[0]]
	for (let i = 0; i < str.length - 1; i++) {
		while (j < str.length) {
			if (sub.includes(str[j])) {
				result = Math.max(result, sub.length)
				break
			}
			sub.push(str[j])
			console.log(sub)
			j++
		}
	}
	return result
}
```
优化

```JS
	var fn = (strs) => {
		let i = 0, j = 1, result = []
		while (j < strs.length) {
			if (result.includes(str)) {

			}
		}
	}
```

### 子集问题

##### LeetCode 416. 分割等和子集

给定一个只包含正整数的非空数组。是否可以将这个数组分割成两个子集，使得两个子集的元素和相等。

注意:

	每个数组中的元素不会超过 100
	数组的大小不会超过 200

```
输入: [1, 5, 11, 5]
输出: true
解释: 数组可以分割成 [1, 5, 5] 和 [11].
```
方法抽象：

**能否找到两个子集和相等，就是有没有一个子集的和等于sum/2, 如果有一个子集的和是sum/2，那么剩下的元素组成的子集自然也等与num/2。也就是数组中有n个元素，能不能相加等于target，也就转化成为硬币问题了**


```JS

var canPartition = function (nums) {
  let sum = nums.reduce((sum, item) => sum + item, 0)

	if (sum % 2) {
		return false
	}
	sum = sum / 2

	let dp = Array.from({length: nums.length + 1}).map((item) => {
		return Array.from({length: sum + 1}).fill(false)
	})
	for (let i = 0; i <= nums.length; i++) {
		dp[i][0] = true
	}

	for (let i = 1; i <= nums.length; i++) {
		for (let j = 0; j <= dp[0].length; j++) {
			if (j >= nums[i - 1]) {
				// 这里的【取当前的数字】之所以是dp[i-1], 下面有思路讲解
				dp[i][j] = dp[i-1][j] || dp[i-1][j - nums[i-1]]
			} else {
				dp[i][j] = dp[i-1][j]				
			}
		}
	}
	return dp[nums.length][sum]
}
```

这个问题与零钱问题不一样的点在于，这里的数字只能取一次，而零钱问题的硬币可以无限次取。所以对与零钱问题，考虑【取当前硬币】的情况是`dp[i][j-coins[i]]`，这是因为我们要考虑的是：不管之前有没有取过当前的这个面额的硬币，不影响我们要组成`target - coins[i]`的金额。就是说我们组成`target - coins[i]`的时候，用了当前面额的硬币也没关系

而这里的数字每个数字只能用一次，那么我们需要考虑的是，在此之前没有使用当前的数字的是否可以组成target，所以是`dp[i-1][j - nums[i-1]]`


### 打家劫舍

##### LeetCode 193. 打家劫舍1

你是一个专业的小偷，计划偷窃沿街的房屋。每间房内都藏有一定的现金，影响你偷窃的唯一制约因素就是相邻的房屋装有相互连通的防盗系统，如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警。

给定一个代表每个房屋存放金额的非负整数数组，计算你 不触动警报装置的情况下 ，一夜之内能够偷窃到的最高金额。

示例：

输入：[1,2,3,1]
输出：4
解释：偷窃 1 号房屋 (金额 = 1) ，然后偷窃 3 号房屋 (金额 = 3)。
     偷窃到的最高金额 = 1 + 3 = 4 。


```JS
var rob = function(nums) {
    let dp = [[0, nums[0]]]
	for (let i = 1; i < nums.length; i++) {
		if (!dp[i]) {
			dp[i] = []
		}
		dp[i][0] = Math.max(dp[i-1][1], dp[i-1][0])
		dp[i][1] = dp[i-1][0] + nums[i]
	}
	return Math.max(dp[nums.length - 1][0], dp[nums.length - 1][1])
};

```
使用最擅长的二维数组解决其实很简单。但是尝试优化一下


```JS
var rob = function(nums) {
		// 使用一位数组就只考虑当前这间屋子的最大价值.对于第一间屋子就是自己的价值。
		// 对于第二间：1⃣️第一间没偷，则取第二间的价值；第一间偷了，则取第一间的价值。
    let dp = [nums[0], Math.max(nums[0], nums[1])]

    for (let i = 2; i < nums.length; i++) {
    	// 偷窃第i间屋子，那么最大价值就是i-2的最大价值加上i的最大价值；不偷i间，则最大价值就是i-1的最大价值
    	dp[i] = Math.max(dp[i - 2] + nums[i], dp[i-1])
    }	
    return dp[nums.length - 1]
};
```
继续优化。因为当前房屋的最大价值，仅与前一天和前两天的价值有关系。所以可以不使用数组：

```JS
var rob = function(nums) {
		if (nums.length == 1) {
			return nums[0]
		}
    let first = nums[0], second = Math.max(nums[0], nums[1])
    for (let i = 2; i < nums.length; i++) {
    	let temp = second
    	second = Math.max(first + nums[i], second)
    	first = temp
    }
    return second
};

```



##### LeetCode 213. 打家劫舍2

你是一个专业的小偷，计划偷窃沿街的房屋，每间房内都藏有一定的现金。这个地方所有的房屋都 围成一圈 ，这意味着第一个房屋和最后一个房屋是紧挨着的。同时，相邻的房屋装有相互连通的防盗系统，如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警 。

给定一个代表每个房屋存放金额的非负整数数组，计算你 在不触动警报装置的情况下 ，今晚能够偷窃到的最高金额。


示例 1：

输入：nums = [2,3,2]
输出：3
解释：你不能先偷窃 1 号房屋（金额 = 2），然后偷窃 3 号房屋（金额 = 2）, 因为他们是相邻的。

 思路与打家劫舍1相同，但是不同的点在于最后一间房屋的最大金额不仅仅与它之前的最大金额有关系，还与第一间房屋的最大金额有关系。因为如果要是偷窃了第一间房屋，那么最后一间房屋也不能偷窃。这点在我做的时候不知道怎么处理。看过答案之后，发现可以分为两种情况来考虑。

 1. 不考虑最后一间房屋，也就是从[0, n-1]房屋偷窃，这样就完全与打家劫舍1相同的情况了。
 2. 考虑最后一间房屋，但是不考虑第一间。也就是从[1, n]的范围中偷窃。这样最后一间房屋的状态就只与他的前一间房屋相关了/


```JS
var rob = function(nums) {
    let _fn = (start, end) => {
		let dp = []
        dp[start] = [0, nums[start]]
		for (let i = start + 1; i < end; i++) {
            if (!dp[i]) {
                dp[i] = []
            }
            dp[i][0] = Math.max(dp[i-1][0], dp[i-1][1])
            dp[i][1] = dp[i-1][0] + nums[i]
		}
		return Math.max(dp[end-1][0], dp[end-1][1])
	}
	if (nums.length === 1) {
        return nums[0]
    }
	return Math.max(_fn(0, nums.length - 1), _fn(1, nums.length))
};
```

##### LeetCode 740. 删除并获得点数

给你一个整数数组 nums ，你可以对它进行一些操作。

每次操作中，选择任意一个 nums[i] ，删除它并获得 nums[i] 的点数。之后，你必须删除 所有 等于 nums[i] - 1 和 nums[i] + 1 的元素。

开始你拥有 0 个点数。返回你能通过这些操作获得的最大点数。

 

示例 1：

```
输入：nums = [3,4,2]
输出：6
解释：
删除 4 获得 4 个点数，因此 3 也被删除。
之后，删除 2 获得 2 个点数。总共获得 6 个点数。

```
```JS
var deleteAndEarn = function(nums) {
    nums = nums.sort((a, b) => a - b), size = nums.length
	let temp = Array.from({ length: nums[size-1] + 1}).fill(0)
	for (let val of nums) {
		temp[val]+=val
	}
	let dp = [[0, temp[0]]]
	for (let i = 1; i < temp.length; i++) {
		if (!dp[i]) {
			dp[i] = []
		}
		dp[i][0] = Math.max(dp[i-1][1], dp[i-1][0])
		dp[i][1] = dp[i-1][0] + temp[i]
	}
	return Math.max(dp[temp.length - 1][1], dp[temp.length - 1][0])
};
```
这道题主要缺乏的是 **将新问题转化为已经做过的问题的思路**

题目描述是 如果选择了`x`，那么`x + 1`和`x - 1`就都不能再选了. 那如果将nums里面的各个值作为一个 _数组A_ 的下标，这个问题不就转化为：选了第i个元素，那么i-1和i+1个元素就不能选了。就转化为“打家劫舍”这个问题了。并且对于数组中相同的值，只要选择一个，那么其他的相同的值也就一定可以选择，所以对于数组A来说，每个位置的“价值”就是nums里面相对于这个位置的值的总和。例如：

nums = [1,2,3,3,3,3,5]

则构成的新数组就是：

A = [0, 1, 2, 12, 0 , 5]

则这道题就完全转化为“打家劫舍”的问题了


##### LeetCode 45. 跳跃游戏 II


给定一个非负整数数组，你最初位于数组的第一个位置。数组中的每个元素代表你在该位置可以跳跃的最大长度。你的目标是使用最少的跳跃次数到达数组的最后一个位置。假设你总是可以到达数组的最后一个位置。

 
```JS
示例 1:

输入: [2,3,1,1,4]
输出: 2
解释: 跳到最后一个位置的最小跳跃数是 2。
     从下标为 0 跳到下标为 1 的位置，跳 1 步，然后跳 3 步到达数组的最后一个位置。

```

> 思路参考视频：[视频](https://leetcode-cn.com/problems/jump-game-ii/solution/dong-tai-gui-hua-jie-fa-by-alchemist-5r/)

大体思路就是：每一个位置的最优解都是通过这个位置之前的**每一个点的跳跃次数来优化的**
使用动态规划的时候，**需要遍历到最有一个点**, 因为动态规划与贪心算法的思路不同，动态规划是 找到每一个点的最优解，而最后一个点是我们要到达的位置，所以最后一个点的最优解才是我们的结果

```JS
	var fn = (nums) => {
		let dp = Array.from({ length: nums.length }).fill(nums.length)
		dp[0] = 0
		for (let i = 1; i < nums.length; i++) {
			for (let j = 0; j < i; j++) {
				if (j + nums[j] >= i) {
					dp[i] = Math.min(dp[j] + 1, dp[i])
				}
			}
		}
		return dp[nums.length - 1]
	}	

```

##### LeetCode 139. 单词拆分

给定一个非空字符串 s 和一个包含非空单词的列表 wordDict，判定 s 是否可以被空格拆分为一个或多个在字典中出现的单词。

说明：

拆分时可以重复使用字典中的单词。
你可以假设字典中没有重复的单词。
示例 1：

输入: s = "leetcode", wordDict = ["leet", "code"]
输出: true
解释: 返回 true 因为 "leetcode" 可以被拆分成 "leet code"。

思路：实际是应用了dp的记忆化来优化遍历的流程。比如 考察“lee”的时候实际需要考察：

第一步：考察“” 和 “lee”是否都合法
第二部：考察“l” 和 “ee”是否都合法
第三步：考察“le” 和 “e”是否都合法

如果上面其中一个合法了，那就证明 lee是可以被拆成字典中的组合的，那么当前的i就记为true，这个值在接下来的遍历中还可以被用使用，避免了重复计算。

**特别注意的一个基础知识**

```JS
	String.prototype.substr 和 String.prototype.substring的区别
	
	substr(start [,length]) 也就是第一个参数是开始索引，第二个参数是需要截取的长度
	
	substring(start [, end]) 也就是第一个参数是开始索引，第二个参数结束索引，但是结束索引指向的字符不会被包含在结果中。

```


```JS
	let fn = (s, wordDict) => {
		let _wordDict = new Set(wordDict)
		dp = [true]
		for (let i = 1; i < s.length; i++) {
			for (let j = 0; j < i; j++) {
				if (dp[j] && _wordDict.has(s.substr(j, i - j))) {
					dp[i] = true
					break
				}
			}
		}
		return dp[s.length-1]
	}
```


##### LeetCode 413. 等差数列划分

如果一个数列至少有三个元素，并且任意两个相邻元素之差相同，则称该数列为等差数列。

例如，以下数列为等差数列:

1, 3, 5, 7, 9
7, 7, 7, 7
3, -1, -5, -9
以下数列不是等差数列。

1, 1, 2, 5, 7
 

数组 A 包含 N 个数，且索引从0开始。数组 A 的一个子数组划分为数组 (P, Q)，P 与 Q 是整数且满足 0<=P<Q<N 。

如果满足以下条件，则称子数组(P, Q)为等差数组：

元素 A[P], A[p + 1], ..., A[Q - 1], A[Q] 是等差的。并且 P + 1 < Q 。

函数要返回数组 A 中所有为等差数组的子数组个数

```JS
示例：

A = [1, 2, 3, 4]

返回: 3, A 中有三个子等差数组: [1, 2, 3], [2, 3, 4] 以及自身 [1, 2, 3, 4]。
```

下面是我自己的暴力解法，出乎意料的竟然击败了80%的时间和80%的空间
思路是： 由于需要考察**连续**的子数组是否是等差数组所以对于如下情况：`[1,3,5,9,15]`中的3, 9 ,15 就不会算在结果之中了。所以可以从头开始遍历这个数组，i 是当前遍历的值，j是i的下一个值，k是j的下一个值。这里不需要三层循环，因为不需要考虑[i i+2 ...]这种情况，因为这种情况不是连续的子数组。所以j保持每次遍历都在i的下一个，但是如果找到一个符合条件的子数组，需要将j加一，因为k会加一，所以必须要保证j和k是相邻的两个元素，才能正确的考察 加上下一个元素之后的子数组是否还是等差数组。例如：现在知道[1,2,3]是等差数组，但是加上4之后，要让j指向3，才能正确考察。

关于剪枝：如果[i,j,k]组成的数组已经不是等差数组了，那么就不需要考虑[i,j,k+1], 因为他不是连续的。

**暴力解法**

```JS
	let fn = (nums) => {
		let count = 0
		for (let i = 0; i < nums.length - 2; i++) {
			let j = i + 1;
			let diff = nums[j] - nums[i]
			for (let k = j + 1; k < nums.length; k++) {
				if (nums[k] - nums[j] == diff) {
					count++
					j++
				} else {
					break
				}
			}
		}
		return count
	}
```


##### LeetCode 931. 下降路径最小和

给你一个 n x n 的 方形 整数数组 matrix ，请你找出并返回通过 matrix 的下降路径 的 最小和 。

下降路径 可以从第一行中的任何元素开始，并从每一行中选择一个元素。在下一行选择的元素和当前行所选元素最多相隔一列（即位于正下方或者沿对角线向左或者向右的第一个元素）。具体来说，位置 (row, col) 的下一个元素应当是 (row + 1, col - 1)、(row + 1, col) 或者 (row + 1, col + 1) 。

 

示例 1：

输入：matrix = [[2,1,3],[6,5,4],[7,8,9]]
输出：13
解释：下面是两条和最小的下降路径，用加粗标注：
[[2,1,3],      [[2,1,3],
 [6,5,4],       [6,5,4],
 [7,8,9]]       [7,8,9]]


简单的动态规划的思路，只是时间复杂度达到了O(N^2).


```JS
	var fn = (matrix) => {
		let maxIndex = matrix[0].length
		let dp = [matrix[0]]
		for (let i = 1; i < maxIndex; i++) {
			if (!dp[i]) {
				dp[i] = []
			}
			for (let j = 0; j < maxIndex; j++) {
				dp[i][j] = Math.min(dp[i-1][j], dp[i-1][j+1] || Infinity, dp[i-1][j-1] || Infinity) + matrix[i][j]
			}
		}
		return Math.min.apply(Math, dp[maxIndex - 1])
	}
```


##### LeetCode 62. 不同路径

一个机器人位于一个 m x n 网格的左上角 （起始点在下图中标记为 “Start” ）。

机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角（在下图中标记为 “Finish” ）。

问总共有多少条不同的路径？


输入：m = 3, n = 2
输出：3
解释：
从左上角开始，总共有 3 条路径可以到达右下角。
1. 向右 -> 向下 -> 向下
2. 向下 -> 向下 -> 向右
3. 向下 -> 向右 -> 向下

```JS
var fn = (m, n) => {
	let dp = Array.from({ length: n }).fill(1)
	for (let i = 1; i < m; i++) {
		let temp = []
		for (let j = 0; j < n; j++) {
			temp[j] = (dp[j] || 0) + (temp[j-1] || 0)
		}
		dp = temp
	}
	return dp[n-1]
}

```



##### LeetCode 63. 不同路径 II

一个机器人位于一个 m x n 网格的左上角 （起始点在下图中标记为“Start” ）。

机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角（在下图中标记为“Finish”）。

现在考虑网格中有障碍物。那么从左上角到右下角将会有多少条不同的路径？

```JS
例子：

输入：obstacleGrid = [[0,0,0],[0,1,0],[0,0,0]]
输出：2
解释：
3x3 网格的正中间有一个障碍物。
从左上角到右下角一共有 2 条不同的路径：
1. 向右 -> 向右 -> 向下 -> 向下
2. 向下 -> 向下 -> 向右 -> 向右
```


```JS
	var fn = (obstacleGrid) => {
		if (obstacleGrid[0][0] == 1) {
			return 0
		}
		let m = obstacleGrid.length, n = obstacleGrid[0].length, dp = [[1]]
		for (let i = 1; i < n; i++) {
			if (obstacleGrid[0][i] == 1) {
				break
			}
			dp[0].push(1)
		}
		for (let i = 1; i < m; i++) {
			if (!dp[i]) {
				dp[i] = []
			}
			for (let j = 0; j < n; j++) {
				if (obstacleGrid[i][j] === 1) {
					dp[i][j] = 0
					continue
				}
				dp[i][j] = (dp[i-1][j] || 0) + (dp[i][j-1] || 0)
			}
		}
		return dp[m-1][n-1] || 0
	}
```


##### LeetCode 64. 最小路径和

给定一个包含非负整数的 m x n 网格 grid ，请找出一条从左上角到右下角的路径，使得路径上的数字总和为最小。

说明：每次只能向下或者向右移动一步。

示例： 

输入：grid = [[1,3,1],[1,5,1],[4,2,1]]
输出：7
解释：因为路径 1→3→1→1→1 的总和最小。


思路：

`dp[i][j] = Math.min(dp[i-1][j], dp[i][j-1]) + grid[i][j]`

```JS
	let fn = (grid) => {
		let dp = [[grid[0][0]]], m = grid.length, n = grid[0].length
		for (let i = 1; i < m; i++) {
			dp[0][i] = dp[0][i-1] + grid[0][i]
		}
		for (let i = 1; i < m; i++) {
			if (!dp[i]) {
				dp[i] = []
			}
			for (let j = 0; j < n; j++) {
				dp[i][j] = Math.min(dp[i][j-1] || Infinity, dp[i-1][j] || Infinity) + grid[i][j]
			}
		}
		return dp[m-1][n-1]
	}
	
```






