# Latex 表格过大(或过小)的调整方法_wbl901的博客-CSDN博客_latex表格太长放不下
## 1 表格宽过窄

**问题:** 表格宽度小, 与页面大小不匹配, 效果前后如下图所示.  
![](https://img-blog.csdn.net/20160920162319531)

**目标:** 调整表格宽度, 效果为” 按页面宽度调整表格”.  
**命令:** \\setlength{\\tabcolsep}{7mm}{XXXX}  
**实现代码:**

````null
\begin{center}
\textbf{Table 2}~~Improved table.\\
\setlength{\tabcolsep}{7mm}{
\begin{tabular}{cccccc} \toprule
Models  &  $\hat c$  &  $\hat\alpha$  &  $\hat\beta_0$  &  $\hat\beta_1$  &  $\hat\beta_2$  \\ \hline
model  & 30.6302  & 0.4127  & 9.4257  & - & - \\
model  & 12.4089  & 0.5169  & 18.6986  & -6.6157  & - \\
model  & 14.8586  & 0.4991  & 19.5421  & -7.0717  & 0.2183  \\
model  & 3.06302  & 0.41266  & 0.11725  & - & - \\
model  & 1.24089  & 0.51691  & 0.83605  & -0.66157  & - \\
model  & 1.48586  & 0.49906  & 0.95609  & -0.70717  & 0.02183  \\
\bottomrule
\end{tabular}}
\end{center}```

2 表格宽过宽
-------

**问题:** 表格宽度大, 与页面大小不匹配, 效果前后如下图所示.  
![](https://img-blog.csdn.net/20160920163420561)
  
**目标:** 调整表格宽度, 效果为”按内容调整表格”.  
**命令:** \\resizebox{\\textwidth}{15mm}{XXXX}  
**实现代码:**

```null
\begin{center}
\textbf{Table 1}~~Original table.\\
\resizebox{\textwidth}{15mm}{
\begin{tabular}{cccccccccccc} \toprule
Models  &  $\hat c$  &  $\hat\alpha$  &  $\hat\beta_0$  &  $\hat\beta_1$  &  $\hat\beta_2$ & Models  &  $\hat c$  &  $\hat\alpha$  &  $\hat\beta_0$  &  $\hat\beta_1$  &  $\hat\beta_2$  \\ \hline
model  & 30.6302  & 0.4127  & 9.4257  & - & -  & model  & 30.6302  & 0.4127  & 9.4257  & - & -\\
model  & 12.4089  & 0.5169  & 18.6986  & -6.6157  & - & model  & 30.6302  & 0.4127  & 9.4257  & - & - \\
model  & 14.8586  & 0.4991  & 19.5421  & -7.0717  & 0.2183 & model  & 30.6302  & 0.4127  & 9.4257  & - & - \\
model  & 3.06302  & 0.41266  & 0.11725  & - & - & model  & 30.6302  & 0.4127  & 9.4257  & - & - \\
model  & 1.24089  & 0.51691  & 0.83605  & -0.66157  & - & model  & 30.6302  & 0.4127  & 9.4257  & - & - \\
model  & 1.48586  & 0.49906  & 0.95609  & -0.70717  & 0.02183  & model  & 30.6302  & 0.4127  & 9.4257  & - & - \\
\bottomrule
\end{tabular}}
\end{center}``` 
 [https://blog.csdn.net/wbl90/article/details/52597429](https://blog.csdn.net/wbl90/article/details/52597429)
````
