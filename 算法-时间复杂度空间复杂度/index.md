# 时间 / 空间复杂度


# 时间复杂度 & 空间复杂度

# ***时间复杂度***

1. ***O(1) 常数复杂度***
2. ***O(log n) 对数复杂度***
3. ***O(n) 线性时间复杂度***
4. ***O(nlogn) 线性对数时间复杂度***
5. ***O(n ^ 2) 平方***
6. ***O(n ^ 3) 立方***
7. ***O(2 ^ n) 指数***
8. ***O(n!) 阶乘***
9. ***O(1) < O(logn) < O(n) < O(nlogn) < O(n ^ 2) < O(n ^ 3) < O(2 ^ n) < O(n!)***

## ***O(1)***

```go
n := 1000
fmt.Println(n)
```

## ***O(logn)***

1. ***时间复杂度 O(logn) —对数阶，当数据增大n倍时，耗时增大logn倍（这里的log是以2为底的，比如，当数据增大256倍时，耗时只增大8倍，是比线性还要低的时间复杂度）。二分查找就是O(logn)的算法，每找一次排除一半的可能，256个数据中查找只要找8次就可以找到目标***

2. ***代码***
   
   ```go
   for i := 1; i < n; i = i * 2 {
     fmt.Println(i)
   }
   ```

## ***O(n)***

```go
for i := 0; i < n; i++ {
  fmt.Println(i)
}
```

## ***O(n + m)***

```go
for i := 0; i < n; i++ {
  fmt.Println(i)
}
for j := 0; j < m; j++ {

} // O(n + m)
```

## ***O(nlogn)***

线性对数阶O(nlogn)其实非常容易理解，将对数阶O(logn)的代码循环n遍的话，那么它的时间复杂度就是 n * O(logn)，也就是了O(nlogn)，归并排序的复杂度就是O(nlogn)

```c
for (int m = 1; m <= n; m++) {
    int i = 1;
    while (i < n) {
        i = i * 2;
    }
}
```

## ***O(n ^ 2)***

```go
for i := 0; i < n; i++ {
  for j := 0; j < n; j++ {
    fmt.Println(i)
    fmt.Println(j)
  }
}
```

## ***O(nm)***

```go
for i := 0; i < n; i++ {
  for j := 0; j < m; j++ {
    fmt.Println(i)
    fmt.Println(j)
  }
}
```

## ***O(k ^ n)***

```go
func fib(n int) int {
  if n <= 0 {
    return 1
  }
  return n * fib(n - 1)
}
```

## ***时间复杂度减少***

```go
计算: 1 + 2+ 3 + 4 + 5 + .. + n

方法1: 
y = 0
for i = 1; i <= n; i++ {
  y += i
}

方法2 求和公式:
y = n * (n+1)/2
```

## ***图示总结***

[![image-20231024215432797](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/202310242154273.png)](https://raw.githubusercontent.com/vlicecream/cloudImage/main/data/202310242154273.png)

# ***空间复杂度***

## ***leetcode 常争论的一个点***

```go
func test(n int) []int {
  res := make([]int, 0)
  res = append(res, n)
  return res
}
```

## ***O(1)***

```c
int i;
```

## ***O(n)***

```c
int[] arr;
```

## ***O(n ^ 2)***

```c
int[][] arr;
```

