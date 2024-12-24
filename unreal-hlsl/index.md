# Unreal HLSL


# ***HLSL***

## ***数据类型***

***代码基于以下蓝图***

<img title="" src="https://raw.githubusercontent.com/CuteCocoa/MyImage/main/2024/12/22-15-55-26-2024-12-22-15-54-10-1734854045646.png" alt="" data-align="inline">

### ***bool***

```hlsl
bool b = false;
```

### ***int***

*数据范围为 "-2147483648 ~ 2147483647"*

```hlsl
int C = A + B;

return C;
```

### ***half***

*这个也是整形 只不过 **int** 占4byte，**half**占2byte*，*为什么这么设计 因为在shader里面有时候不需要这么大的*

*half 范围在 "-60000~60000"*

```hlsl
return half(1);
```

### ***float***

```hlsl
float f = 1.234;
```

### ***double***

```hlsl
double d = 1.0;
```

### ***uint***

*取值范围是 "0 ~ 4294967295"*

```hlsl
uint ui = 1;
```

### Vector

