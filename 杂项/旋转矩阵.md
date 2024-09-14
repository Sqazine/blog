# 旋转矩阵


矩阵有时候需要旋转,例如顺时针旋转90度,逆时针旋转90度之类的



假设有这么个矩阵:

$$
\begin{pmatrix} 
1 & 2 & 3 & 4 \\
5 & 6 & 7 & 8 \\
9 & 10 & 11 & 12 \\
13 & 14 & 15 & 16 \\
\end{pmatrix}
$$

# 顺时针旋转90度:

$$
\begin{pmatrix} 
13 & 9 & 5 & 1 \\
14 & 10 & 6 & 2 \\
15 & 11 & 7 & 3 \\
16 & 12 & 8 & 4 \\
\end{pmatrix}
$$

可以看见位置变化:

$$
\begin{matrix} 
[0,0]\rightarrow[0,3] \\
[1,3]\rightarrow[3,2]
\end{matrix}
$$

根据规律得到

```cpp
 for (int i = 0; i < n; i++)
        for (int j = 0; j < n; j++)
            temp[n-1 - j][n-1 - i] = arr[i][n-1 - j];
```

同理,逆时针旋转90度:

```cpp
for (int i = 0; i < n; i++)
        for (int j = 0; j < n; j++)
            temp[j][i] = arr[i][n - 1 - j];
```

# 顺时针旋转180度

$$
\begin{pmatrix} 
16 & 15 & 14 & 13 \\
12 & 11 & 10 & 9 \\
8 & 7 & 6 & 5 \\
4 & 3 & 2 & 1 \\
\end{pmatrix}
$$

可以看见位置变化:

$$
\begin{matrix} 
[0,0]\rightarrow[3,3] \\
[1,3]\rightarrow[2,0]
\end{matrix}
$$

根据规律得到

```cpp
 for (int i = 0; i < n; i++)
        for (int j = 0; j < n; j++)
            temp[n - 1 - i][j] = arr[i][n - 1 -j];
```