# Learning

## 旋转矩阵

对图像的变换有很多种，旋转、剪切、平移等等，这些变换都与矩阵、线性代数有着密不可分的联系**（对矩阵以及线性代数的理解可参考3blue1brown视频）**。

### 二维

二维的旋转主要包括绕原点和绕任意点的旋转，后者更具有普遍性。

#### 绕原点旋转

绕原点旋转的情况十分简单，可以直接由一个二维矩阵来描述其旋转效应：
$$
\begin{pmatrix}
	\cos \theta & -\sin \theta\\
	\sin \theta &  \cos \theta
\end{pmatrix}
$$
表示绕原点逆时针旋转$\theta$角度。

#### 绕任意点旋转

绕任意点旋转可以看成三个步骤：

1. 将旋转点做平移变换**T(-x,-y)**(变换到原点
2. 在原点进行旋转变换**R**
3. 再做平移变换**T(x,y)**移回旋转点到原位置

整个旋转可以表示为：$ T(x,y) R T(-x,-y) $

##### 平移变换

   平移变换不同于旋转和剪切，需要引入一个新维度，下式为二维的平移矩阵，效果为$（dx,dy）$平移：
$$
\begin{pmatrix}
	1 & 0 & dx \\
	0 & 1 & dy \\
	0 & 0 & 1
\end{pmatrix}
$$
综上，我们可以写出绕二维平面任意点旋转的变换:
$$
M=
\begin{pmatrix}
	1 & 0 & dx \\
	0 & 1 & dy \\
	0 & 0 & 1
\end{pmatrix} \cdot 
\begin{pmatrix}
	\cos \theta & -\sin \theta & 0 \\
	\sin \theta &  \cos \theta & 0 \\
	0 & 0 & 1
\end{pmatrix} \cdot
\begin{pmatrix}
	1 & 0 & -dx \\
	0 & 1 & -dy \\
	0 & 0 & 1
\end{pmatrix}
$$
可见为了保证齐次性，我们在旋转矩阵也引入了一个新维度。

使用mma代码验证：

```
RotationTransform[\[Theta], {dx, dy}](*内置函数*)

{{1, 0, dx}, {0, 1, dy}, {0, 0, 1}}.{{Cos[\[Theta]], -Sin[\[Theta]], 
    0}, {Sin[\[Theta]], Cos[\[Theta]], 0}, {0, 0, 1}}.{{1, 
    0, -dx}, {0, 1, -dy}, {0, 0, 1}} // MatrixForm(*手动变换*)

```

<img src="learning.assets/image-20200708185225205.png" alt="image-20200708185225205" style="zoom:50%;" />

### 三维

三维旋转主要是绕某一轴旋转，简单情况是绕坐标轴旋转，普遍情况是绕任意轴旋转。

#### 绕坐标轴旋转

绕坐标轴旋转只需要注意**旋转角度正方向的约定（右手螺旋）**：

<img src="learning.assets/image-20200708191036667.png" alt="image-20200708191036667" style="zoom:33%;" />                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    

可以写出绕三个轴分别的旋转矩阵，考虑到平移变换，我们需要增加一个维度：
$$
\begin{pmatrix}x^`  \\y^`  \\z^`  \\1  \end{pmatrix}
=
\begin{pmatrix}
	\begin{array}{c c c c|c c c c|c c c c}
	1 & 0 & 0 & 0 & \cos \theta & 0 & \sin \theta & 0 & \cos \theta & - \sin \theta & 0 & 0\\
	0 & \cos \theta & - \sin \theta & 0 & 0 & 1 & 0 & 0 & \sin \theta & \cos \theta & 0 & 0\\
	0 & \sin \theta & \cos \theta & 0 & - \sin \theta &0 & \cos \theta & 0 & 0 & 0 & 1 & 0 \\
	0 & 0 & 0 & 1 &    0 & 0 & 0 & 1 &   0 & 0 & 0 & 1 \\
	\end{array}
\end{pmatrix}
\cdot
\begin{pmatrix}  x\\ y \\z \\1  \end{pmatrix}
$$
以上分别是绕**x、y、z**坐标轴旋转的旋转矩阵。

####　绕过原点任意轴旋转	 

对于绕过原点任意轴旋转，我们也可以将其整个过程分解：主要思想是通过两次变换将旋转轴依次变换到坐标平面、坐标轴上，此时相对坐标轴的旋转变换容易求得，此后在反方向依次变换回去 。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  

```                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         
(*可以看到两种方法的结果一样*)
(*内置方法*)
RotationMatrix[1., {1, 2, 3}] // N // MatrixForm

（*按照分解重构方法*）
{a, b, c} = {1, 2, 3}
RotationMatrix[-ArcCos[c/Sqrt[b^2 + c^2]], {1, 0, 0}].
RotationMatrix[ArcSin[a/Sqrt[a^2 + b^2 + c^2]], {0, 1, 0}].
RotationMatrix[1, {0, 0, 1}].
RotationMatrix[-ArcSin[a/Sqrt[a^2 + b^2 + c^2]], {0,1, 0}].
RotationMatrix[ArcCos[c/Sqrt[b^2 + c^2]], {1, 0, 0}] 
// N // MatrixForm
```

#### 绕任意旋转轴旋转

主要分两种情况：平行坐标轴和非平行（后者2包含前者）

##### 平行坐标轴

平行坐标轴仅需要先做平移变换，在做相对坐标轴的旋转变换，在做平移变换回去。

#####　任意坐标轴

先平移到原点，再根据绕原点任意坐标轴旋转进行操作，最后在逆平移变换。

```
%以下变换是基于点｛0,0,1｝处{1, 1, 1}方向轴的旋转变换
%内置函数
RotationTransform[1., {1, 1, 1}, {0, 0, 1}]
%{1.30647, 1.36094, 3.33259}

%做平移分解，再根据绕原点任意坐标轴旋转（根据平移操作特性增加维度）
{{1, 0, 0, 0}, {0, 1, 0, 0}, {0, 0, 1, 1}, {0, 0, 0, 1}}.
((RotationMatrix[1., {1, 1, 1}]~Join~{{0, 0, 0}}) // Transpose[Join[Transpose[#], {{0, 0, 0, 1}}]]&).
{{1, 0, 0, 0}, {0,1, 0, 0}, {0, 0, 1, -1}, {0, 0, 0, 1}}.{1, 2, 3, 1}
%{1.30647, 1.36094, 3.33259, 1.}
```

---

> 参考文献为：[博客园-旋转矩阵]( https://www.cnblogs.com/WangGuiHandsome/p/10094784.html)
>
>  自制动图源文件在桌面，动图展示太大。。。。

+++

+++

## 散度、旋度、斯托克斯公式、高斯公式、曲线曲面积分

### 散度

计算公式：；

矢量场中某一点$p(x,y,z)$的散度用来描述矢量场的**汇聚**和**发散**。