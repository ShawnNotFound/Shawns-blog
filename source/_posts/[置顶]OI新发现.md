# OI新发现

## 高速查找二进制中1的个数
```c++
int a;
cin >> a;
cout << __builtin_popcount(a) << endl;
```
+ 无需任何头文件，OI可用
+ 时间复杂度$O(\log{\log{n}})$或$O(1)$，绝对高于手写
+ 香的，嘶溜嘶溜~
