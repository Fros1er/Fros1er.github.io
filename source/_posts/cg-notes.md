---
title: cg-notes
date: 2022-07-30 19:34:32
tags:
mathjax: true
---

有关图形学的一点点笔记。不过因为为一般会用ipad记，所以这里的就当复习用吧....  
不会太详细，毕竟是笔记。

# 数学

## 齐次坐标
<https://blog.csdn.net/zhuiqiuzhuoyue583/article/details/95228010>

## 变换
- 平移
$$T=\begin
{bmatrix}
1&0&0&x \\
0&1&0&y \\
0&0&1&z \\
0&0&0&1 \\
\end{bmatrix}$$

- 缩放
$$S=\begin
{bmatrix}
x&0&0&0\\
0&y&0&0\\
0&0&z&0\\
0&0&0&1\\
\end{bmatrix}$$

- 旋转  
R的角标代表旋转轴
$$R_{(1,0,0)}=\begin
{bmatrix}
1&0&0&0\\
0&\cos\theta&\sin\theta&0\\
0&-\sin\theta&\cos\theta&0\\
0&0&0&1\\
\end{bmatrix}$$
$$R_{(0,1,0)}=\begin
{bmatrix}
\cos\theta&0&-\sin\theta&0\\
0&1&0&0\\
\sin\theta&0&\cos\theta&0\\
0&0&0&1\\
\end{bmatrix}$$
$$R_{(0,0,1)}=\begin
{bmatrix}
\cos\theta&\sin\theta&0&0\\
-\sin\theta&\cos\theta&0&0\\
0&0&1&0\\
0&0&0&1\\
\end{bmatrix}$$

- 绕任意轴(Rodrigues' rotation formula) 
$$
R_{(\vec{n}, \alpha)} = \cos(\alpha)I + (1 - \cos(\alpha))\vec{n}\vec{n}^T + \sin{\alpha} 
\begin{bmatrix}
    0&-n_z&n_y\\
    n_z&0&-n_x\\
    -n_y&n_x&0
\end{bmatrix}
$$
推导过程见<https://www.qiujiawei.com/linear-algebra-14/>

- 法向量变换
$$
\begin{aligned}
\vec{n}^T\vec{v} = 0 \\
\vec{n}^T(M^{-1}M)\vec{v} = 0 \\
\because \vec{v'} = M\vec{v} \\
\therefore \vec{n}^TM^{-1}\vec{v'} = 0 \\
\because \vec{n'}^T\vec{v'} = 0 \\
\therefore \vec{n}^TM^{-1}\vec{v'} = \vec{n'}^T\vec{v'} \\
\vec{n}^TM^{-1}= \vec{n'}^T \\
\vec{n'} = (M^{-1})^T\vec{n} \\
\end{aligned}
$$

## 视图变换
$M = M_{model} * M_{view} * M_{projection}$

### 模型变换 Model
上面提到的几种变换的组合，往世界里摆物体。  
local space -> world space

### 视图变换 View
word space -> view space

**这里用的都是右手系，但opengl是左手系。可能实际会有正负号的区别。**

描述相机：位置$\vec{e}$，朝向(lookat/gaze) $\vec{g}$，上方向$\vec{t}$  
有$\vec{g}\perp\vec{t}$，且两者均为单位向量。

$M_{view}$把相机移到约定俗成的
$$
\begin{aligned}
\vec{e} &= (0, 0, 0)\\
\vec{g} &= (0, 0, -1)\\
\vec{t} &= (0, 1, 0)\\
\end{aligned}
$$

由于相机和物体的相对位置不变，画面不变，所以实际大概是直接规定相机在这然后移所有物体...

移动包括一个平移一个旋转。有$M_{view} = T_{view} + R_{view}$。  
其中，
$$
T_{view} = \begin{bmatrix}
    1&0&0&-\vec{e}_x\\
    0&1&0&-\vec{e}_y\\
    0&0&1&-\vec{e}_z\\
    0&0&0&1\\
\end{bmatrix}
$$  
对于$R_{view}$，先求逆（方便！）：
$$
R_{view}^{-1} = \begin{bmatrix}
    (\vec{g}\times\vec{t})_x&\vec{t}_x&-\vec{g}_x&0\\
    (\vec{g}\times\vec{t})_y&\vec{t}_y&-\vec{g}_y&0\\
    (\vec{g}\times\vec{t})_z&\vec{t}_z&-\vec{g}_z&0\\
    0&0&0&1\\
\end{bmatrix}
$$  
由于这玩意的列向量都是单位向量，还是正交的，所以是正交矩阵，转置一下就是$R_{view}$了。

### 投影变换 Projection
view space -> clip space

把所有可见坐标变换到(-1.0, 1.0)的范围内，然后裁剪掉这部分之外的内容。

#### 正交投影 Orthographic projection
相机离的无限远，可见范围就变成一个立方体了。

![](/cg-notes/proj-1.png)

规定可见的立方体的坐标l(left), r(right), b(bottom), t(top), f(far), n(near)。根据摄像机up=y, lookat=-z来定义。  
然后把这个立方体变成(-1, 1)的标准立方体。

显然先移动再缩放一下就好了。有：
$$
M_{ortho} = \begin{bmatrix}
    \frac{2}{r-l} &0&0&0\\
    0& \frac{2}{t-b} &0&0\\
    0&0& \frac{2}{n-f} &0\\
    0&0&0&1\\
\end{bmatrix}
\begin{bmatrix}
    1&0&0&-\frac{r+l}{2}\\
    0&1&0&-\frac{t+b}{2}\\
    0&0&1&-\frac{n+f}{2}\\
    0&0&0&1\\
\end{bmatrix}
$$

#### 透视投影 Perspective projection
摄像机是某个点，从这里投出一个四棱锥。规定四棱锥平行于底面的两个平面为zNear和zFar，渲染这个Frustum里的东西。
 
![](/cg-notes/proj-2.png)  
把左边的frustum挤压成立方体，然后使用透视投影的方法。
  
![](/cg-notes/proj-3.jpg)  
~~图是拿notability手画的所以非常丑陋~~  
由图，根据相似三角形，有$y'= \frac{n}{z}y$。同理$x'= \frac{n}{z}x$。  
所以有：
$$
M_{persp\rightarrow ortho} \begin{bmatrix}
    x\\
    y\\
    z\\
    1\\
\end{bmatrix} =
\begin{bmatrix}
    nx/z\\
    ny/z\\
    ?\\
    1\\
\end{bmatrix} =
\begin{bmatrix}
    nx\\
    ny\\
    ?\\
    z\\
\end{bmatrix}
$$

$$
M_{persp\rightarrow ortho} = \begin{bmatrix}
    n&0&0&0\\
    0&n&0&0\\
    ?&?&?&?\\
    0&0&1&0\\
\end{bmatrix}
$$

那一行?要用两个特殊值：zNear平面点不动，zFar平面点的z不变来解。往里一代：
$$
M_{persp \rightarrow ortho} = \begin{bmatrix}
    n&0&0&0\\
    0&n&0&0\\
    0&0&n+f&-nf\\
    0&0&1&0\\
\end{bmatrix}
$$

最后$M_{persp}=M_{ortho}M_{persp\rightarrow ortho}$。

还有确定坐标的问题...。zFar(f)和zNear(n)是已知的，另外两个参数是摄像机的fov(一般是fovY)和宽高比aspect。  
那么，
$$
\begin{aligned}
\tan(\frac{fovY}{2})=\frac{t}{n}\\ aspect=\frac{r}{t}
\end{aligned}
$$
根据对称性，$b=-t$，$l=-r$。

由于摄像机是固定的，又有一些奇奇怪怪的对称性，所以可以化简下：
$$
M_{persp} = \begin{bmatrix}
    \frac{1}{a\tan(\frac{fovY}{2})}&0&0&0\\
    0&\frac{1}{\tan(\frac{fovY}{2})}&0&0\\
    0&0&\frac{n+f}{n-f}&-\frac{2nf}{n-f}\\
    0&0&1&0\\
\end{bmatrix}
$$

## 线性插值
$lerp(\Delta x, v_0, v_1) = v0 + \Delta x(v1 - v0)$

双线性插值：  
![](/cg-notes/bilinear.jpg)  
为插出5，先插m和n，再用m和n插出5。多维的同理。

## 重心坐标
对于三角形ABC内的任意点，都可以用$(x,y)=\alpha A+\beta B+\gamma C$表示。其中：
- $\alpha+\beta+\gamma=1$，若不等于1则不在平面内。
- $\alpha, \beta, \gamma>=0$。否则在三角形外。

把点和ABC分别连起来，围成三个小三角形。A正对的三角形面积记为$S_A$，则$\alpha=\frac{S_A}{S_{ABC}}$。$\beta$，$\gamma$ 同理。（算面积用cross product）

## 重心坐标插值
三角形内一点$V_{\alpha, \beta, \gamma} = \alpha V_A + \beta V_B + \gamma V_C$。

## Perlin noise
生成噪声的东西，公式看不懂...总之有。可以和纹理组合起来用。

# 光照

## 一些物理定义
<https://zhuanlan.zhihu.com/p/389616851>  

## 漫反射 Diffuse
$I_d = K_dI_i\max(0, \vec{l}\cdot\vec{n})$

用于Lambert物体（粗糙的那种）。  
Lambert物体符合Lambert cosine law：$color\propto \cos{\theta}$。$\cos{\theta}$就是上面的$\vec{l}\cdot\vec{n}$那一项。

符合各项同性，光从各个方向来没有区别，只和法线夹角有关系。

$K_d$表示物体反射某种颜色光的能力（其实就是颜色）。

虎书里用的代表RGB颜色，规定$K_d$和$I_i$的每个分量都在\[0,1\]之内，这样最终结果也小于1。

公式里的max可以换成绝对值。这么搞叫two-sided lighting，相当于镜像再放置一个光源。

## 环境光 Ambient
$I_a = K_aI_i$

$K_a$据说直觉上是场景内所有颜色的平均值....？

如果要保证$I_d+I_a\leq1$，可以让$K_a+K_d\leq1$。

## 镜面反射或者叫高光 Specular/Phong shading
![](/cg-notes/Phong.png)  
（图片的字母不太一样，e是V，少了一个I的反射R）

$I_s=K_sI_i\max(\vec{R}\cdot\vec{V})^n$  
Blinn Phong的话，是$I_s=K_sI_i\max(\vec{N}\cdot\vec{H})^n$

$K_s$是镜面反射系数（反射某种颜色光的能力，和$K_d$差不多）。用来控制反射光颜色。

n越大反射光越集中。实际上是让$\vec{N}\cdot\vec{H}$缩小的更快一点。

Blinn Phong的效果更柔和一点。

# Shading
## Vertex-Based Diffuse Shading
算顶点的颜色，然后重心坐标插值出三角形的颜色。

## Phong Shading
重心坐标插值出三角形中所有点的法向（本质是对原来光滑曲面的近似），然后用这个法向算颜色。

# Texture
提供一个Texture function（可以是函数或者查表，就是图片），和一个平面上点对texture function的对应关系，就可以贴纹理了（

## 2D纹理
提供一个2D坐标$(u, v)。习惯上u, v\in[0, 1]^2$，在此之外的砍掉整数部分。

需要把物体的xyz坐标map到uv上。好像不用对应的很紧密，只要像那个形状就可以往上贴（不过肯定会失真）。

### 平面投影
$(u, v, *, 1) = M[x, y, z, 1]$  
在平坦的表面上很好用，但和投影方向（一般是法线）垂直的地方就会出现那个面全部被map到texture的一个点上的情况...

### 球坐标
把xyz转到球坐标上，得到$\theta = \arccos(\frac{z}{r})$，$\phi = \arctan2(y, x)$。由于$(\theta, \phi) \in [0, \pi]\times[-\pi,\pi]$，有$u=\frac{\pi + \phi}{2\pi}, v=\frac{\pi-\theta}{\pi}$。

地球仪用的这种。

### 柱坐标
先转到柱坐标再归一化。和球差不多。

### Cubemaps
球坐标在两极会变形，可以用一个立方体代替（不过有在边上不连续的缺点）。

具体是把球上的点投到包着球的正方体上。$(x, y, z)\rightarrow(\frac{x}{z}, \frac{y}{z})$即可。正方体每个面有一个(u,v)。具体的坐标挺讲究。。。查书吧。

## 纹理坐标插值
三角形的每一个顶点存一个(u, v)。内部的某个点的uv通过插值得到。

这里的插值不能在做齐次除法（v/w）后的screen space算（重心坐标会变），需要先在screen space算出重心坐标，然后通过：
$$
\alpha_w = \frac{\alpha/w_0}{\alpha/w_0+\beta/w_1+\gamma/w2}
$$
算出world space（MVP矩阵不会改变重心坐标所以在哪的都一样）的重心坐标。$\beta$和$\gamma$同理。

具体为什么....我猜是因为齐次除法把z搞没了少了一个维度（w在persp后其实是z）。嗯推公式也能推出来，总之不管了。

这玩意叫透视校正插值。

## 取纹理值的方法
插值出的uv不是整数。如果texture function是离散的，得有一个方法取值：
- nearest neighbor：取最靠近的一个
- bilinear：拿周围四个双线性插值
- hermite：把bilinear用的$\Delta x$换成$3\Delta x^2 - 2\Delta x^3$
- bicubic：拿周围十六个点
- ...

## MipMap

解决texture太大的问题（屏幕上的一个像素对应texture的多个像素）。

本质是范围查询。对屏幕上的某个点o，找邻近两点（上和右）a, b。取mipmap level $D = \log_2{\max(|oa|, |ob|)}$。D代表在第几层会取到一个像素。

D不是整数，所以用trilinear interpolation。在floor(D)和ceil(D)双线性插，结果再插一次。

会导致Overblur的问题。原因是每次缩放一半相当于只有宽高比为1的情况。用各项异性过滤（Anisotropic Filtering）可以解决宽高比不为1，但斜着就不行了。

---
*未完待续*