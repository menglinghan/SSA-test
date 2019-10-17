# SSA-test
孟令涵 DZ1933020
## 作业说明

本次试验为软件分析测试作业，程序由C语言编写，然后使用LLVM框架对其进行SSA转换。其中test.c是程序的源代码，test.ll为LLVM的转换文件。
## 实验过程
首先编写C语言程序，程序代码如下所示
```c
#include<stdio.h>
int main(){
    int x;
    int i=0;
    while(i<10){
        x=10;
        i++;
    }
    return x;
}
```
接下来使用在LLVM的bin文件夹下使用命令：
>clang -S -emit-llvm test.c -o test.ll<br>

可发现在文件夹中自动生成了一个ll文件，即LLVM的转换结果。
## 结果分析
```python
define dso_local i32 @main() #0 {
  %1 = alloca i32, align 4
  %2 = alloca i32, align 4
  %3 = alloca i32, align 4
  store i32 0, i32* %1, align 4
  store i32 0, i32* %3, align 4
  br label %4

4:                                                ; preds = %7, %0
  %5 = load i32, i32* %3, align 4
  %6 = icmp slt i32 %5, 10
  br i1 %6, label %7, label %10

7:                                                ; preds = %4
  store i32 10, i32* %2, align 4
  %8 = load i32, i32* %3, align 4
  %9 = add nsw i32 %8, 1
  store i32 %9, i32* %3, align 4
  br label %4

10:                                               ; preds = %4
  %11 = load i32, i32* %2, align 4
  ret i32 %11
}
```
源码对应的中间表示如上所示，可以发现源码中i对应的%3被赋值了两次：
>store i32 0, i32* %3, align 4<br>
>store i32 %9, i32* %3, align 4<br>

因此，可以看出没有完全被转换为SSA，相应的x对应的%2只有一次赋值，满足SSA转换。
