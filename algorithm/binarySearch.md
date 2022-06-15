# 二分查找

<!-- GFM-TOC -->
- [二分查找](#二分查找)
  - [通用模板](#通用模板)
  - [查找最左边相等的](#查找最左边相等的)
  - [查找最右边相等的](#查找最右边相等的)
  - [搜索插入位置](#搜索插入位置)
  - [完整示例](#完整示例)
<!-- GFM-TOC -->

## 通用模板

```
func search(nums []int, target int) int {
	start := 0
	end := len(nums) - 1
	for start+1 < end {
		mid := start + (end-start)/2
		if nums[mid] == target {
			return mid
		} else if nums[mid] > target {
			end = mid
		} else {
			start = mid
		}
	}
	if nums[start] == target {
		return start
	}
	if nums[end] == target {
		return end
	}
	return -1
}
```

## 查找最左边相等的

```
func searchLeft(nums []int, target int) int {
	start := 0
	end := len(nums) - 1
    res := -1
	for start+1 < end {
		mid := start + (end-start)/2
		if nums[mid] >= target {
			end = mid
		} else {
			start = mid
		}
	}
	if nums[end] == target {
		res = end
	}
    if nums[start] == target {
		res = start
	}
	return res
}
```

## 查找最右边相等的

```
func searchRight(nums []int, target int) int {
	start := 0
	end := len(nums) - 1
    res := -1
	for start+1 < end {
		mid := start + (end-start)/2
		if nums[mid] <= target {
			start = mid
		} else {
			end = mid
		}
	}
    if nums[start] == target {
		res = start
	}
    if nums[end] == target {
		res = end
	}
	return res
}
```

## 搜索插入位置

```
func searchInsert(nums []int, target int) int {
	start := 0
	end := len(nums) - 1
	for start+1 < end {
		mid := start + (end-start)/2
		if nums[mid] == target {
			return mid
		} else if nums[mid] > target {
			end = mid
		} else {
			start = mid
		}
	}
	if nums[start] >= target {
		return start
	} else if nums[end] < target {
		return end + 1
	}
	return end
}
```

## 完整示例

```
package main

import (
	"fmt"
)

func main() {
	arr := []int{1, 3, 4, 4, 6, 9}
	for i := 0; i < 10; i++ {
		fmt.Println("查找", i, ":")
		fmt.Println("search:", search(arr, i))
		fmt.Println("searchLeft:", searchLeft(arr, i))
		fmt.Println("searchRight:", searchRight(arr, i))
	}

}

func search(nums []int, target int) int {
	start := 0
	end := len(nums) - 1
	for start+1 < end {
		mid := start + (end-start)/2
		if nums[mid] == target {
			return mid
		} else if nums[mid] < target {
			start = mid
		} else {
			end = mid
		}
	}
	if nums[start] == target {
		return start
	}
	if nums[end] == target {
		return end
	}
	return -1
}

func searchLeft(nums []int, target int) int {
	start := 0
	end := len(nums) - 1
	res := -1
	for start+1 < end {
		mid := start + (end-start)/2
		if nums[mid] >= target {
			end = mid
		} else {
			start = mid
		}
	}
	if nums[end] == target {
		res = end
	}
	if nums[start] == target {
		res = start
	}
	return res
}

func searchRight(nums []int, target int) int {
	start := 0
	end := len(nums) - 1
	res := -1
	for start+1 < end {
		mid := start + (end-start)/2
		if nums[mid] <= target {
			start = mid
		} else {
			end = mid
		}
	}
	if nums[start] == target {
		res = start
	}
	if nums[end] == target {
		res = end
	}
	return res
}
```