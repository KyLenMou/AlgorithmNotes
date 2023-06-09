# Codeforces Round 866 (Div. 2)

## Problem C

题意：

给定一个数组a，你需要恰好对a进行一次操作：在a中选定一个区间$[l,r]$，把该区间内的数全部变为k，使$MEX(a)$的值增加1

定义$MEX(a)$为在a中数的集合没有出现过的最小的一个自然数，例如$MEX([1,2])=0$,$MEX([0,1,2])=3$,$MEX([0,1,2,99])=3$,$MEX([0,1,2,1,2,1,2,99,99,99,0,0,0])=3$

题解：

首先找到$MEX(a)$的值x，接下来我们要让$MEX(a)$的值为x+1。

依题意，我们接下来要做两件事：

1. 使a中不能出现x+1
2. 使a中必须出现1,2,...,x，即： 
   1. 原本a中没有x，所以修改区间时只能赋值为x
   2. 原本a中1,2,...,x-1一定存在，修改后1,2,...,x-1也必须存在

为了使a中不能出现x+1，我们要把原本a中所有出现的x+1给赋值成x，由于我们是选中一段连续的区间，所以要选择左右两端都为x+1的一段区间，且这段区间以外没有x+1，这样才能使x+1完全消失

为了使修改后1,2,...,x-1仍然存在，我们在对区间$[l,r]$赋值时：

1. 如果$[l,r]$内小于x的值只存在一次，那么赋值后，1到x-1其中的部分会消失，不符合题意
2. 如果$[l,r]$内小于x的值除了在该区间中存在，还在除了该区间外存在，那么在操作后仍会符合题意
3. $[l,r]$内大于x的值不会造成任何影响，不用管

特殊地：

1. 如果a是0-n的全排列，那么必不符合题意
2. 如果$MEX(a)=0$，那么直接把所有的值赋为0，直接符合题意

> [Submission #202598657 - Codeforces](https://codeforces.com/contest/1820/submission/202598657)



