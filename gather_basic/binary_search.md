# 二分搜索

leetbook 二分查找 - 
https://leetcode.cn/leetbook/read/binary-search/xexoac/

## 常规题

### [binary-search 基础二分查找](https://leetcode.cn/problems/binary-search/)

目标值不一定存在

```go
func search(nums []int, target int) int {
    st := 0
    ed := len(nums) -1
    for st <= ed {
        mid := st + (ed -st)>>1
        if nums[mid] == target {
            return mid
        }
        if nums[mid] > target {
            ed = mid -1
        } else {
            st = mid + 1 
        }
    }
    return -1
}
```

### [374. 猜数字大小 guess-number](https://leetcode.cn/problems/guess-number-higher-or-lower/)

```go
func guessNumber(n int) int {
    st := 0
    ed := n
    for st <= ed {
        mid := st + (ed-st)>>1
        res := guess(mid)
        if res == 0 {
            return mid
        }
        if res == -1 { // higner
            ed = mid - 1
        } else { // lower
            st = mid + 1
        }
    }
    return -1
}
```

## 特定位置

### [278. 第一个错误的版本](https://leetcode.cn/problems/first-bad-version/submissions/)

```go
func firstBadVersion(n int) int {
    st := 0
    ed := n
    for st < ed {
        mid := st + (ed-st)>>1
        if isBadVersion(mid) {
            ed = mid
        } else {
            st = mid + 1
        }
    }
    return st
}
```
和 go 标准库实现一致：
```go
sort.Search()
sort.SearchInts(a []int, x int) // fn函数为 a[i] >= x
```

### [162. 寻找峰值](https://leetcode.cn/problems/find-peak-element/)

```go
func findPeakElement(nums []int) int {
    st := 0
    ed := len(nums) - 1
    for st < ed {
        mid := st + (ed-st)>>1
        if nums[mid] > nums[mid+1] {
            ed = mid
        } else {
            st = mid + 1
        }
    }
    return st
}
```

### [153. 寻找旋转排序数组中的最小值](https://leetcode.cn/problems/find-minimum-in-rotated-sorted-array/)

```go
func findMin(nums []int) int {
    st := 0
    ed := len(nums) - 1
    for st < ed {
        mid := st + (ed - st)>>1
        if nums[mid] > nums[ed] {
            st = mid + 1
        } else {
            ed = mid
        }
    }
    return nums[st]
}
```

### [sqrtx 平方根](https://leetcode.cn/problems/sqrtx/)

最接近的地板 floor

```go
func mySqrt(x int) int {
    st := 0
    ed := x

    for st < ed {
        mid := st + (ed -st+1)>>1 // mid前挪，中右，缩小右边
        res := mid * mid
        if res == x {
            return mid
        }
        if res < x {
            st = mid
        } else {
            ed = mid - 1
        }
    }
    return st
}
```

### [34. 在排序数组中查找元素的第一个和最后一个位置](https://leetcode.cn/problems/find-first-and-last-position-of-element-in-sorted-array/)

第一个+最后一个

```go
func searchRange(nums []int, target int) []int {
    st := 0
    ed := len(nums) - 1
    for st <= ed {
        mid := st + (ed - st)>>1
        if nums[mid] < target {
            st = mid + 1
        } else if nums[mid] > target {
            ed = mid - 1
        } else {
            // 找起点
            hi := ed
            ed = mid
            for st < ed { // 相等时退出
                tmp := st + (ed-st)>>1 // mid中左，找起点
                if nums[tmp] < target { // 小于
                    st = tmp + 1
                } else { // 相等
                    ed = tmp
                }
            }
            lo := st
            // 找终点
            st = mid
            ed = hi
            for st < ed {
                tmp := st + (ed-st+1)>>1 // mid中右，找终点
                if nums[tmp] > target {
                    ed = tmp - 1
                } else {
                    st = tmp
                }
            }
            
            return []int{lo, ed}
        }
    }
    return []int{-1, -1}
}
```

用标准库偷懒
```go
index := sort.SearchInts(nums, target)
if index == len(nums) || nums[index] != target {
    return []int{-1,-1}
}
hi := sort.SearchInts(nums, target+1) - 1
return []int{index, hi}
```

### [658. 找到 K 个最接近的元素](https://leetcode.cn/problems/find-k-closest-elements/submissions/)

```go
func findClosestElements(arr []int, k int, x int) []int {
    st, ed := 0, len(arr) - 1
    for st < ed {
        mid := (ed - st)>>1 + st
        if arr[mid] >= x {
            ed = mid
        } else {
            st = mid + 1
        }
    }
    left, right := st-1, st // 第一个选哪个要判断，左边多扩了一个用于选取
    for i := 0; i < k ; i++ { 
        // 执行k次，[left:right]里面最终会有k+1个数据
        if left < 0 {
            right++
        } else if right >= len(arr) || x-arr[left] <= arr[right]-x { // 优先选小下标
            left--
        } else {
            right++
        }
    }
    return arr[left+1:right] // 左边多扩掉的要去掉
}
```

## 综合

### [33. 搜索旋转排序数组](https://leetcode.cn/problems/search-in-rotated-sorted-array/)

```go
func search(nums []int, target int) int {
    st := 0
    ed := len(nums) - 1
    for st <= ed {
        mid := st + (ed -st)>>1
        if nums[mid] == target {
            return mid
        }
        if nums[mid] >= nums[0] { // mid在左上坡
            // 在单调增的左边检查
            if nums[0] <= target && target < nums[mid] {
                ed = mid -1
            } else {
                st = mid + 1
            }
        } else { // mid在右上坡
            // 在单调增的右边检查
            if nums[mid] < target && target <= nums[len(nums)-1] {
                st = mid + 1
            } else {
                ed = mid - 1
            }
        }
    }
    return -1
}
```
