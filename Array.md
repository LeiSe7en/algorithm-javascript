# 数组相关的算法题

### 数组相关的算法主要是锻炼思维能力，因为其实对于数组的处理很简单。


#### LeetCode 88. 合并两个有序数组
给你两个有序整数数组 nums1 和 nums2，请你将 nums2 合并到 nums1 中，使 nums1 成为一个有序数组。

初始化 nums1 和 nums2 的元素数量分别为 m 和 n 。你可以假设 nums1 的空间大小等于 m + n，这样它就有足够的空间保存来自 nums2 的元素。

 

示例 1：

输入：nums1 = [1,2,3,0,0,0], m = 3, nums2 = [2,5,6], n = 3
输出：[1,2,2,3,5,6]

思路： 双指针。由于原数组就是有序的，所以从屁股开始比较，把两个数组中的较大的屁股放在nums1的最后。第一个指针指向nums1的尾部，第二个指针指向nums2的尾部，p则是m+n用于填充数据。找到更大的一个，则填充到p的位置，然后p--，较大的那个指针同时减1。**这道题最麻烦的是要判断边界情况，也就是如果nums1或者nums2为空的时候，怎么才能用一套算法处理**

```JS
var merge = function(nums1, m, nums2, n) {
    let p1 = m - 1, p2 = n - 1, p = m + n -1
    while (p >= 0) {
        if ((nums1[p1] ?? -Infinity) < nums2[p2]) {
            nums1[p--] = nums2[p2--]
        } else {
            nums1[p--] = nums1[p1--]
        }
    }
};

```


#### LeetCode. 350 两个数组的交集2

```JS
给定两个数组，编写一个函数来计算它们的交集。

示例 1：

输入：nums1 = [1,2,2,1], nums2 = [2,2]
输出：[2,2]
示例 2:

输入：nums1 = [4,9,5], nums2 = [9,4,9,8,4]
输出：[4,9]
 

说明：

输出结果中每个元素出现的次数，应与元素在两个数组中出现次数的最小值一致。
我们可以不考虑输出结果的顺序。

```

这里主要需要考虑出现次数的问题，因为要与最小出现次数保持一致，那么就需要考虑每个元素出现的次数，在哈希表中就要记录每个值出现的次数，并且匹配到之后就-1.

方法1 就是说我上面说的hash表

```JS
var intersect = function(nums1, nums2) {
    let map = {}, result = []
    if (nums1.length < nums2.length) {
        let temp = nums1
        nums1 = nums2
        nums2 = temp
    }
    for (let num of nums1) {
        map[num] = (map[num] || 0) + 1
    }    
    for (let num of nums2) {
        if (map[num]) {
            map[num]--
            result.push(num)
        }
    }
    return result
};
	
```
方法2 是排序加双指针

```JS
var intersect = function(nums1, nums2) {
   nums1 = nums1.sort((a,b) => a - b)
   nums2 = nums2.sort((a,b) => a - b)
  let p1 = 0, p2 = 0, result = []
  while (p1 < nums1.length && p2 < nums2.length) {
      if (nums1[p1] == nums2[p2]) {
          result.push(nums1[p1])
          p1++
          p2++
      }else if (nums1[p1] < nums2[p2]) {
          p1++
      }else {
          p2++
      }
  }
  return result
};
```


