## 贪心算法

##### LeetCode  . 跳跃问题

给定一个非负整数数组 nums ，你最初位于数组的 第一个下标 。

数组中的每个元素代表你在该位置可以跳跃的最大长度。

判断你是否能够到达最后一个下标。
 

示例 1：

输入：nums = [2,3,1,1,4]
输出：true
解释：可以先跳 1 步，从下标 0 到达下标 1, 然后再从下标 1 跳 3 步到达最后一个下标。
示例 2：

输入：nums = [3,2,1,0,4]
输出：false
解释：无论怎样，总会到达下标为 3 的位置。但该下标的最大跳跃长度是 0 ， 所以永远不可能到达最后一个下标。

```JS
let fn = (nums) => {
	let farthest = nums[0], result = false

	for (let i = 1; i < nums.length - 1; i++) {
		if (farthest >= i) {
			farthest = Math.max(nums[i] + i, farthest)
		}
	}
	return farthest >= nums.length - 1 
}
```
思路就是： 对于每一个位置i的点来说，如果最远可以跳跃x距离，那么在i 至 x 的范围内都是可以到达的。那么对于i 至 x范围内的点，每一个点都根据自己的跳跃距离x以及自己当前的位置i，更新最远到达的位置。如果遍历到最后，最远到达的位置大于等于nums的最后一个位置，就是true。


##### LeetCode 45. 跳跃问题2

给定一个非负整数数组，你最初位于数组的第一个位置。数组中的每个元素代表你在该位置可以跳跃的最大长度。你的目标是使用最少的跳跃次数到达数组的最后一个位置。假设你总是可以到达数组的最后一个位置。


示例 1:

输入: [2,3,1,1,4]
输出: 2
解释: 跳到最后一个位置的最小跳跃数是 2。
     从下标为 0 跳到下标为 1 的位置，跳 1 步，然后跳 3 步到达数组的最后一个位置。
示例 2:

输入: [2,3,0,1,4]
输出: 2


下面是我看答案之后自己的实现，这是错误的。错误的原因是：

```JS

	var fn = (nums) => {
		let farthest = nums[0], jump = 1
		for (let i = 1; i < nums.length - 1; i++) {
			if (farthest >= nums.length - 1) {
				return jump
			}

			if (farthest >= i) {
				// 错误的思路：发现能达到更远的点就跳，但是也许下一个点能跳的比现在这个点更远，这样就多跳了。
				if (farthest < i + nums[i]) {
					farthest = i + nums[i] 
					jump++
				}
			}
		}
		return jump
	}
```

我的思路是，每次发现能跳的更远，我就会跳一次，也就是jump++。但是这个是有问题的，会有多跳的情况。因为对于每一次跳跃之后可以到达的范围我们取[1, nums[i] + 1], 也就是在这个范围之内，**我们找到能跳到最远的那个点再跳**。至于代码的实现就是：记录当前可以跳的边界，在我们到达这个边界之前**不进行跳跃**，只更新最远距离，当我们触碰到这个 边界的时候，才进行跳跃，这样我们就能在这个边界的范围之内，找到最优的跳跃方式。

正确的代码：

```JS
var jump = function(nums) {
    let farthest = 0, jump = 0, end = 0
    for (let i = 0; i < nums.length - 1; i++) {
    		// 每次都更新最远位置
        farthest = Math.max(farthest, i + nums[i])
        // 只有当达到边界的时候，才跳跃
        if (end == i) {
            jump++
            end = farthest
            if (farthest >= nums.length - 1) {
                return jump
            }
        }
    }
    return jump
};
```



