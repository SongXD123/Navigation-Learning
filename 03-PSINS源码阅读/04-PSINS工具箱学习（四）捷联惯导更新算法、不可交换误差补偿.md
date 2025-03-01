## 一、姿态阵微分方程及求解

### 1、姿态阵微分方程

$$
\dot{\boldsymbol{C}}_{b}^{i}=\boldsymbol{C}_{b}^{i}\left({\boldsymbol{\omega}_{i b}^{b}}\times\right)
$$

描述的是姿态的变化和角速率 $\boldsymbol{\omega}_{i b}^{b}$ 的关系，角速率正好可以由陀螺仪测量得到，三个轴装三个陀螺，就有了微分方程，再结合初值，可得到姿态变化阵。

### 2、姿态阵微分方程的毕卡级数解

简记
$$
\dot{\boldsymbol{C}}_{b}^{i}=\boldsymbol{C}_{b}^{i}\left(\boldsymbol{\omega}_{i b}^{b} \times\right) \stackrel{\boldsymbol{\Omega}(t)=\left[\boldsymbol{\omega}_{i b}^{b}(t) \times\right]}{\longrightarrow} \dot{\boldsymbol{C}}(t)=\boldsymbol{C}(t) \boldsymbol{\Omega}(t)
$$
形式上就是一个状态方程。在[连续时间Kalman滤波中](https://blog.csdn.net/daoge2666/article/details/131054453?spm=1001.2014.3001.5501)已经提到过，此方程无初等解，只有毕卡积分级数解： 
$$
\boldsymbol{C}(t)  =\boldsymbol{C}(0)\left[\boldsymbol{I}+\int_{0}^{t} \boldsymbol{\Omega}(\tau) \mathrm{d} \tau+\int_{0}^{t} \int_{0}^{\tau} \boldsymbol{\Omega}\left(\tau_{1}\right) \mathrm{d} \tau_{1} \boldsymbol{\Omega}(\tau) \mathrm{d} \tau+\int_{0}^{t} \int_{0}^{\tau} \int_{0}^{\tau_{1}} \boldsymbol{\Omega}\left(\tau_{2}\right) \mathrm{d} \tau_{2} \boldsymbol{\Omega}\left(\tau_{1}\right) \mathrm{d} \tau_{1} \boldsymbol{\Omega}(\tau) \mathrm{d} \tau+\cdots\right]
$$
一般情况下，上式就是毕卡级数解，不可再继续化简。上式是收敛的，但还不方便得到闭合形式的初等解。只有在**定轴转动**的情况下，若转动角速率满足可交换性条件： 
$$
\boldsymbol{\Omega}(t) \boldsymbol{\Omega}(\tau)=\boldsymbol{\Omega}(\tau) \boldsymbol{\Omega}(t)
$$
递推，可以得到幂指函数形式的闭合解：
$$
\boldsymbol{C}(t)=\boldsymbol{C}(0)\left\{\boldsymbol{I}+\int_{0}^{t} \boldsymbol{\Omega}(\tau) \mathrm{d} \tau+\frac{1}{2 !}\left[\int_{0}^{t} \boldsymbol{\Omega}(\tau) \mathrm{d} \tau\right]^{2}+\frac{1}{3 !}\left[\int_{0}^{t} \boldsymbol{\Omega}(\tau) \mathrm{d} \tau\right]^{3}+\cdots\right\}=\boldsymbol{C}(0) \mathrm{e}^{\int_{0}^{\prime} \Omega(\tau) \mathrm{d} \tau}
$$

即：
$$
\boldsymbol{C}(t)=\boldsymbol{C}(0) \mathrm{e}^{\int_{0}^{\prime} \Omega(\tau) \mathrm{d} \tau}
$$

### 3、定轴转动条件下的姿态更新算法

在 $\boldsymbol{C}(T)=\boldsymbol{C}(0) \mathrm{e}^{\int_{0}^{T} \Omega(\tau) \mathrm{d} \tau}$ 中：
$$
\mathrm{e}^{\int_{0}^{T} \boldsymbol{\Omega}(\tau) \mathrm{d} \tau}  =\mathrm{e}^{\int_{0}^{T}[\boldsymbol{\omega}(\tau) \times] \mathrm{d} \tau}=\mathrm{e}^{[\boldsymbol{\theta}(T) \times]} =\boldsymbol{I}+\frac{\sin \theta(T)}{\theta(T)}[\boldsymbol{\theta}(T) \times]+\frac{1-\cos \theta(T)}{\theta^{2}(T)}[\boldsymbol{\theta}(T) \times]^{2}
$$
将时间区间更改为 $\left[t_{m-1}, t_{m}\right]$，时间的起点都认为是上一时刻的结束时间，则有姿态阵的更新方差：
$$
\boldsymbol{C}_{b(m)}^{i}=\boldsymbol{C}_{b(m-1)}^{i} \boldsymbol{C}_{b(m)}^{b(m-1)}
$$
当前时刻 $m$ 的姿态阵 $\boldsymbol{C}_{b(m)}^{i}$ 等于上一时刻的姿态阵 $\boldsymbol{C}_{b(m)}^{i-1}$ 乘以上一时刻到当前时刻的变化量 $\boldsymbol{C}_{b(m)}^{b(m-1)}$ ：
$$
\boldsymbol{C}_{b(m)}^{b(m-1)}=\boldsymbol{I}+\frac{\sin \Delta \theta_{m}}{\Delta \theta_{m}}\left(\Delta \theta_{m} \times\right)+\frac{1-\cos \Delta \theta_{m}}{\Delta \theta_{m}^{2}}\left(\Delta \theta_{m} \times\right)^{2}
$$
就等于上一时刻到当前时刻角增量 $\Delta \theta_{m}$ 的幂指函数。所谓**角增量**，就是在时间段内角速率的积分 $\Delta \theta_{m}=\int_{t_{m-1}}^{t_{m}^{m}} \omega_{i b}^{b}(t) \mathrm{d} t$ 

## 二、四元数微分方程及求解

$$
\dot{Q}_{b}^{i}=\frac{1}{2} \boldsymbol{Q}_{b}^{i} \circ \boldsymbol{\omega}_{\mathrm{b}}^{b}
$$

写成矩阵形式为：
$$
\dot{Q}(t)=\frac{1}{2} \boldsymbol{M}_{\boldsymbol{\omega}(t)}^{\prime} \boldsymbol{Q}(t)
$$
类似于姿态阵微分方程的求解，只有在 $t, \tau \in[0, T]$ 时间段内满足定轴转动条件 $[\boldsymbol{\omega}(t) \times][\boldsymbol{\omega}(\tau) \times]=[\boldsymbol{\omega}(\tau) \times][\boldsymbol{\omega}(t) \times]$，使：$\boldsymbol{M}_{\omega(t)}^{\prime} \boldsymbol{M}_{\omega(\tau)}^{\prime}=\boldsymbol{M}_{\omega(\tau)}^{\prime} \boldsymbol{M}_{\omega(t)}^{\prime}$，才能求得闭合解：
$$
\boldsymbol{Q}(T)=\mathrm{e}^{\frac{1}{2} \boldsymbol{\theta}(T)} \boldsymbol{Q}(0)
$$
经过一系列推导得：
$$
\boldsymbol{Q}(T)=\boldsymbol{Q}(0) \circ\left[\begin{array}{c}\cos \frac{\theta(T)}{2} \\ \frac{\boldsymbol{\theta}(T)}{\theta(T)} \sin \frac{\theta(T)}{2}\end{array}\right] 
$$
若将研究时间区间从 $[0, T]$ 改为 $\left[t_{m-1}, t_{m}\right]$，可得姿态四元数的递推公式（定轴转动条件下）：
$$
\begin{array}{c}Q_{b(m)}^{i}=Q_{b(m-1)}^{i} \circ Q_{b(m)}^{b(m-1)} \\ Q_{b(m)}^{b(m-1)}=\left[\begin{array}{c}\cos \frac{\Delta \theta_{m}}{2} \\ \frac{\Delta \boldsymbol{\theta}_{m}}{\Delta \theta_{m}} \sin \frac{\Delta \theta_{m}}{2}\end{array}\right]\end{array}
$$
其中，$Q_{b(m-1)}^{i}, Q_{b(m)}^{i}$ 分别表示 $t_{m-1}$ 和 $t_{m}$ 时刻的姿态变换四元数；$Q_{b(m)}^{b(m-1)}$ 是从 $t_{m-1}$ 时刻到 $t_{m}$ 时刻的姿态四元数变化，且有 $\Delta \boldsymbol{\theta}_{m}=\int_{t_{m-1}}^{t_{m}} \boldsymbol{\omega}_{\vec{k}}^{b} \mathrm{~d} t$ 和 $\Delta \theta_{m}=\left|\Delta \boldsymbol{\theta}_{m}\right|$ 。

姿态阵更新方程和四元数更新方程形式非常相似，但四元数算法的计算量和存储量约为姿态阵的一半。

## 三、等效旋转矢量微分方程及求解

### 1、Bortz 方程

由四元数推导可得等效旋转矢量微分方程：
$$
\boldsymbol{\omega}_{\vec{b}}^{b}=\boldsymbol{u}_{\vec{z},}^{b} \dot{\phi}+\dot{\boldsymbol{u}}_{\vec{j}}^{b} \sin \phi-\boldsymbol{u}_{\vec{j}}^{b} \times \dot{\boldsymbol{u}}_{\vec{j},}^{b}(1-\cos \phi)
$$
由一系列的推导，可得更为常用的等效旋转矢量微分方程，也称 Bortz 方程：
$$
\begin{array}{c}\dot{\phi}=\boldsymbol{\omega}+\frac{1}{2} \boldsymbol{\phi} \times \boldsymbol{\omega}+\frac{1}{\phi^{2}}\left(1-\frac{\phi \cdot 2 \sin \frac{\phi}{2} \cos \frac{\phi}{2}}{2 \cdot 2 \sin ^{2} \frac{\phi}{2}}\right)(\boldsymbol{\phi} \times)^{2} \boldsymbol{\omega}= \\ \boldsymbol{\omega}+\frac{1}{2} \boldsymbol{\phi} \times \boldsymbol{\omega}+\frac{1}{\phi^{2}}\left(1-\frac{\phi}{2} \cot \frac{\phi}{2}\right)(\boldsymbol{\phi} \times)^{2} \boldsymbol{\omega}\end{array}
$$

### 2、Bortz 方程的简化

Bortz 方程虽然在理论上是严格成立的，但实际应用时略显繁杂。当等效转动角度 $\phi=|\phi|$ 为小量时，常常将方程右边的余切函数 $\cot (\phi / 2)$ 用泰勒级数展开，进行如下近似：
$$
\dot{\boldsymbol{\phi}} \approx   \boldsymbol{\omega}+\frac{1}{2} \boldsymbol{\phi} \times \boldsymbol{\omega}+\frac{1}{12}(\boldsymbol{\phi} \times)^{2} \boldsymbol{\omega}
$$
忽略二阶项，还可进一步近似为：
$$
\dot{\phi} \approx \omega+\frac{1}{2} \boldsymbol{\phi} \times \omega
$$
在 $\left[t_{m-1}, t\right]$ 时间段，对上式两边积分，并迭代一次，再进行简化得：
$$
\boldsymbol{\phi}(t) \approx \boldsymbol{\phi}\left(t_{m-1}\right)+\Delta \boldsymbol{\theta}\left(t, t_{m-1}\right)+\frac{1}{2} \boldsymbol{\phi}\left(t_{m-1}\right) \times \Delta \boldsymbol{\theta}\left(t, t_{m-1}\right)+\frac{1}{2} \int_{t_{m-1}}^{t} \Delta \boldsymbol{\theta}\left(\tau, t_{m-1}\right) \times \boldsymbol{\omega}(\tau) \mathrm{d} \tau
$$
特别地，若假设 $t_{m-1}=0$ 且等效旋转矢量 $\boldsymbol{\phi}(0)=\mathbf{0}$，则 $\boldsymbol{\phi}(t)$ 可表示从 0 时刻开始的等效旋转矢量“增量”，上式可简化为：
$$
\boldsymbol{\phi}(t)=\Delta \boldsymbol{\theta}(t)+\frac{1}{2} \int_{0}^{t} \Delta \boldsymbol{\theta}(\tau) \times \boldsymbol{\omega}(\tau) \mathrm{d} \tau=\Delta \boldsymbol{\theta}(t)+\color{red}{\boldsymbol{\sigma}(t)}
$$
其中，记：$\Delta \boldsymbol{\theta}(t)=\int_{0}^{t} \boldsymbol{\omega}(\tau) \mathrm{d} \tau \quad$ 和 $\quad \boldsymbol{\sigma}(t)=\frac{1}{2} \int_{0}^{t} \Delta \boldsymbol{\theta}(\tau) \times \boldsymbol{\omega}(\tau) \mathrm{d} \tau$

$\boldsymbol{\sigma}(t)=\boldsymbol{\phi}(t)-\Delta \boldsymbol{\theta}(t)$ 表示等效旋转矢量增量 $\boldsymbol{\phi}(t)$ 与角增量 $\Delta \boldsymbol{\theta}(t)$ 之间的差异, 通常称为**转动不可交换误差的修正量**。对上式两边求得，可得到 Bortz 方程的另一种简化：
$$
\dot{\boldsymbol{\phi}}(t)=\boldsymbol{\omega}(t)+\frac{1}{2} \Delta \boldsymbol{\theta}(t) \times \boldsymbol{\omega}(t)
$$
其优点是右端不再含变量 $\phi(t)$，方便求解。需要特别指出的是，此式成立的前提条件是: $\phi(0)=$ $\Delta \boldsymbol{\theta}(0)=\mathbf{0}$ 且 $\boldsymbol{\phi}(t)$ 始终为小量，$\boldsymbol{\phi}(t)$ 越小近似精度越高。

实际应用时，一般总是以 $t_{m-1}$ 为新的时间起点，令 $\phi\left(t_{m-1}\right)=\mathbf{0}$, ，再根据式 (2.5.17) 计算等效旋 转矢量 $\phi\left(t_{m}\right)$，相当于只进行一步计算，这样有利于保证等效旋转矢量始终为小量，降低公式推导过程中的近似误差。在获得 $\phi\left(t_{m}\right)$ 之后，改等效旋转矢量递推计算为方向余弦阵或四元数递推，完成姿态递推更新，以四元数为例 (姿态阵类似)，等效旋转矢量与四元数相配合的姿态更新算法如下：
$$
\begin{array}{c}Q_{b(m)}^{i}=Q_{b(m-1)}^{i} \circ Q_{b(m)}^{b(m-1)} \\ Q_{b(m)}^{b(m-1)}=\left[\begin{array}{c}\cos \frac{\phi_{m}}{2} \\ \frac{\boldsymbol{\phi_{m}}}{\phi_{m}} \sin \frac{\phi_{m}}{2}\end{array}\right]\end{array}
$$
其中, 将 $\boldsymbol{\phi\left(t_{m}\right)}$ 简记为 $\boldsymbol{\phi_{m}}$，且有 $\phi_{m}=\left|\boldsymbol{\phi_{m}}\right|$，形式与四元数更新方程很相似，但两者有本质区别：

* 四元数更新方程只是简单的用进行变化四元数计算。
* 等效旋转矢量在计算中考虑了转动不可交换误差的补偿，精度更高。

### 3、不可交换误差的理解

不可交换误差是捷联惯导算法中绕不开的概念，**何为不可交换误差**？

我们知道矩阵乘法不满足交换律，$A*B$​ 不等于 $B*A$，但在惯导的姿态解算中，我们就当它们是可以交换的，以得到毕卡级数的收敛数值解。

以姿态阵微分方程为例，想知道可交换性条件的含义，我们可以把它展开成分量形式来看：
$$
\begin{array}{l}
 \boldsymbol{\Omega}(t) \boldsymbol{\Omega}(\tau)=[\boldsymbol{\omega}(t) \times][\boldsymbol{\omega}(\tau) \times]=\left[\begin{array}{ccc}
 -\omega_{1 y} \omega_{2 y}-\omega_{1 z} \omega_{2 z} & \omega_{1 y} \omega_{2 x} & \omega_{1 z} \omega_{2 x} \\
 \omega_{1 x} \omega_{2 y} & -\omega_{1 x} \omega_{2 x}-\omega_{1 z} \omega_{2 z} & \omega_{1 z} \omega_{2 y} \\
 \omega_{1 x} \omega_{2 z} & \omega_{1 y} \omega_{2 z} & -\omega_{1 x} \omega_{2 x}-\omega_{1 y} \omega_{2 y}
 \end{array}\right] \\
 \boldsymbol{\Omega}(\tau) \boldsymbol{\Omega}(t)=[\boldsymbol{\omega}(\tau) \times][\boldsymbol{\omega}(t) \times]=\left[\begin{array}{ccc}
 -\omega_{1 y} \omega_{2 y}-\omega_{1 z} \omega_{2 z} & \omega_{2 y} \omega_{1 x} & \omega_{2 z} \omega_{1 x} \\
 \omega_{2 x} \omega_{1 y} & -\omega_{1 x} \omega_{2 x}-\omega_{1 z} \omega_{2 z} & \omega_{2 z} \omega_{1 y} \\
 \omega_{2 x} \omega_{1 z} & \omega_{2 y} \omega_{1 z} & -\omega_{1 x} \omega_{2 x}-\omega_{1 y} \omega_{2 y}
 \end{array}\right]
 \end{array}
$$
比较各元素，可以发现，对角线元素相等，当两矩阵的上三角元素都相等时矩阵完全相等。
$$
\left\{\begin{array}{l}
 \omega_{1 x} \omega_{2 y}=\omega_{2 x} \omega_{1 y} \\
 \omega_{1 x} \omega_{2 z}=\omega_{2 x} \omega_{1 z} \\
 \omega_{1 y} \omega_{2 z}=\omega_{2 y} \omega_{1 z}
 \end{array} \quad \Longrightarrow \frac{\omega_{1 x}}{\omega_{2 x}}=\frac{\omega_{1 y}}{\omega_{2 y}}=\frac{\omega_{1 z}}{\omega_{2 z}} \quad \begin{array}{c}
 \end{array}\right.
$$
即得：**可交换的条件是载体定轴转动或静止**。

>刚体作绕固定轴线并经过原点的转动，当刚体内所有质元都绕着同一直线做圆周运动，这种运动称为转动，若转轴的位置和方向是固定不动的，此时刚体的转动称为定轴转动。

### 4、Bortz 方程的泰勒级数解

实际应用中，从高精度陀螺中采样获取的往往是一定间隔内的角增量，下文的主要目的是由角增量求解等效旋转矢量。

#### 1. 二子样算法

不妨将时刻 $t_{m-1}$ 重新记为时间起点 0 时刻，陀螺在姿态四元数更新时间段 $[0, T]$ 内进行若干次等间隔角增量采样，暂且假设陀螺角速度输出为线性形式：
$$
\boldsymbol{\omega}(\tau)={\color{blue}\boldsymbol{a}}+2 {\color{blue}\boldsymbol{b}} \tau \quad(0 \leqslant \tau \leqslant T)
$$
则陀螺输出角增量为：
$$
\Delta \boldsymbol{\theta}(\tau)=\int_{0}^{\tau} \boldsymbol{\omega}\left(\tau^{\prime}\right) \mathrm{d} \tau^{\prime}={\color{blue}\boldsymbol{a}} \tau+{\color{blue}\boldsymbol{b}} \tau^{2}
$$
若将 $\boldsymbol{\phi}(t)$ 视为光滑函数且在 $t=0$ 处展开成泰勒级数：
$$
\begin{aligned} \boldsymbol{\phi}(T)= & \boldsymbol{\phi}(0)+T \dot{\boldsymbol{\phi}}(0)+\frac{T^{2}}{2 !} \ddot{\boldsymbol{\phi}}(0)+\frac{T^{3}}{3 !} \dddot{\boldsymbol{\phi}}(0)+\cdots= \\ & \mathbf{0}+T {\color{blue}\boldsymbol{a}}+T^{2} {\color{blue}\boldsymbol{b}}+\frac{T^{3}}{6} {\color{blue}\boldsymbol{a}} \times {\color{blue}\boldsymbol{b}}=T {\color{blue}\boldsymbol{a}}+T^{2} {\color{blue}\boldsymbol{b}}+\frac{T^{3}}{6} {\color{blue}\boldsymbol{a}} \times {\color{blue}\boldsymbol{b}}\end{aligned}
$$
式子中包含了两个未知向量参数 $a$、$b$ ，为了消去他们，求解出 $\boldsymbol{\phi}(t)$ ，需要在采样间隔 $[0, T]$ 内进行两次角增量的采样，记为：
$$
\left.\begin{array}{l}\Delta \boldsymbol{\theta}_{1}=\int_{0}^{T / 2} \boldsymbol{\omega}(\tau) \mathrm{d} \tau={\color{blue}\boldsymbol{a}} \tau+\left.{\color{blue}\boldsymbol{b}}^{2}\right|_{0} ^{T / 2}=\frac{T}{2} {\color{blue}\boldsymbol{a}}+\frac{T^{2}}{4} {\color{blue}\boldsymbol{b}} \\ \Delta \boldsymbol{\theta}_{2}=\int_{T / 2}^{T} \boldsymbol{\omega}(\tau) \mathrm{d} \tau={\color{blue}\boldsymbol{a}} \tau+\left.{\color{blue}\boldsymbol{b}}^{2}\right|_{T / 2} ^{T}=\frac{T}{2} {\color{blue}\boldsymbol{a}}+\frac{3 T^{2}}{4} {\color{blue}\boldsymbol{b}}\end{array}\right\}
$$
由此可求得以角增量表示的常数向量 ${\color{blue}\boldsymbol{a}}=\left(3 \Delta \boldsymbol{\theta}_{1}-\Delta \boldsymbol{\theta}_{2}\right) / T$ 和 ${\color{blue}\boldsymbol{b}}=2\left(\Delta \boldsymbol{\theta}_{2}-\right.$ $\left.\Delta \boldsymbol{\theta}_{1}\right) / T^{2}$，再将其代回原式，便可求得以角增量表示的所谓等效旋转矢量**二子样算法**：
$$
\boldsymbol{\phi}(T)=\left(\Delta \boldsymbol{\theta}_{1}+\Delta \boldsymbol{\theta}_{2}\right)+\color{red}{\frac{2}{3} \Delta \boldsymbol{\theta}_{1} \times \Delta \boldsymbol{\theta}_{2}}
$$

#### 2. 三子样算法

类似二字样算法的推导思路，若设陀螺的角速度输出为如下抛物线形式：
$$
\boldsymbol{\omega}(\tau)={\color{blue}\boldsymbol{a}}+2 {\color{blue}\boldsymbol{b}} \boldsymbol{\tau}+3 {\color{blue}\boldsymbol{c} }\tau^{2} \quad(0 \leqslant \tau \leqslant T)
$$
在更新时间段 $[0, T]$ 内进行三次角增量采样：
$$
\Delta \boldsymbol{\theta}_{1}=\int_{0}^{T / 3} \boldsymbol{\omega}(\tau) \mathrm{d} \tau, \quad \Delta \boldsymbol{\theta}_{2}=\int_{T / 3}^{2 T / 3} \boldsymbol{\omega}(\tau) \mathrm{d} \tau, \quad \Delta \boldsymbol{\theta}_{3}=\int_{2 T / 3}^{T} \boldsymbol{\omega}(\tau) \mathrm{d} \tau
$$
即可求得等效旋转矢量的三子样算法
$$
\boldsymbol{\phi}(T)=\left(\Delta \boldsymbol{\theta}_{1}+\Delta \boldsymbol{\theta}_{2}+\Delta \boldsymbol{\theta}_{3}\right)+\color{red}{\frac{33}{80} \Delta \boldsymbol{\theta}_{1} \times \Delta \boldsymbol{\theta}_{3}+\frac{57}{80}\left(\Delta \boldsymbol{\theta}_{1} \times \Delta \boldsymbol{\theta}_{2}+\Delta \boldsymbol{\theta}_{2} \times \Delta \boldsymbol{\theta}_{3}\right)}
$$

#### 3. 单子样算法

若设陀螺角速度输出为常值，有单子样算法：
$$
\boldsymbol{\phi}(T)=\Delta \boldsymbol{\theta}_{1}=\int_{0}^{T} \boldsymbol{\omega}(\tau) \mathrm{d} \tau
$$

#### 4. 单子样+前一周期

设角速度输出为如下线性形式：
$$
\boldsymbol{\omega}(\tau)={\color{blue}\boldsymbol{a}}+2 {\color{blue}\boldsymbol{b}} \tau \quad(-T \leqslant \tau \leqslant T)
$$
在时间段 $[0, T]$ 内仅进行一次角增量采样，记 $\Delta \boldsymbol{\theta}_{1}=\int_{0}^{T} \boldsymbol{\omega}(\tau) \mathrm{d} \tau$，但该算法在计算等效旋转 矢量 $\boldsymbol{\phi}(T)$ 时还会充分利用前一次的角增量信息，记 $\Delta \boldsymbol{\theta}_{0}=\int_{-T}^{0} \boldsymbol{\omega}(\tau) \mathrm{d} \tau$，通过如下方程组：
$$
\left.\begin{array}{l}\boldsymbol{\phi}(T)=T {\color{blue}\boldsymbol{a}}+T^{2} {\color{blue}\boldsymbol{b}}+\frac{T^{3}}{6} {\color{blue}\boldsymbol{a}} \times {\color{blue}\boldsymbol{b}} \\ \Delta \boldsymbol{\theta}_{1}=\int_{0}^{T} \boldsymbol{\omega}(\tau) \mathrm{d} \tau={\color{blue}\boldsymbol{a}} \tau+\left.{\color{blue}\boldsymbol{b}} \tau^{2}\right|_{0} ^{T}=T {\color{blue}\boldsymbol{a}}+T^{2} {\color{blue}\boldsymbol{b}} \\ \Delta \boldsymbol{\theta}_{0}=\int_{-T}^{0} \boldsymbol{\omega}(\tau) \mathrm{d} \tau={\color{blue}\boldsymbol{a}}{\tau}+\left.{\color{blue}\boldsymbol{b}}{\tau}^{2}\right|_{-T} ^{0}=T {\color{blue}\boldsymbol{a}}-T^{2} {\color{blue}\boldsymbol{b}}\end{array}\right\}
$$
消去 a 和 b，可求得：
$$
\boldsymbol{\phi}(T)=\Delta \boldsymbol{\theta}_{1}+\color{red}{\frac{1}{12} \Delta \boldsymbol{\theta}_{0} \times \Delta \boldsymbol{\theta}_{1}}
$$
相同频率下，单子样+前一周期算法和二子样算法精度量级相当，但提高了姿态的输出频率。

### 5、圆锥运动条件下等效旋转矢量求解

上面几种求解方法都是基于角速度是多项式变化，但是在振动环境下陀螺的输出呈现有规律的反复变化，可以用正弦函数来描述这样的角运动：
$$
\boldsymbol{\omega}(t)=\left[\begin{array}{lll}a \Omega \sin \Omega t & b \Omega \cos \Omega t & c\end{array}\right]^{\mathrm{T}}
$$
通常认为正弦角运动比多项式角运动更激烈，姿态算法如果能适用于正弦角运动环境，也能适用于多项式运动环境。

#### 1. 圆锥运动的描述

**四元数描述**：
$$
Q(t)=\left[\begin{array}{c}\cos (\phi / 2) \\ \sin (\phi / 2) \cos \Omega t \\ \sin (\phi / 2) \sin \Omega t \\ 0\end{array}\right]
$$
**角速度描述**：
$$
\boldsymbol{\omega}(t)=\left[\begin{array}{c}-\Omega \sin \phi \sin \Omega t \\ \Omega \sin \phi \cos \Omega t \\ -2 \Omega \sin ^{2}(\phi / 2)\end{array}\right]=\Omega \sin \phi \cdot\left[\begin{array}{c}-\sin \Omega t \\ \cos \Omega t \\ -\tan (\phi / 2)\end{array}\right]
$$
**等效旋转矢量描述**：
$$
\boldsymbol{\phi(t)}=\phi\left[\begin{array}{c}\cos \Omega t \\ \sin \Omega t \\ 0\end{array}\right]
$$

这说明在任意 $t$ 时刻，动坐标系 $b$ 系绕参考坐标系 $i$ 系 $o x_i y_i$ 平面上的单位转轴 $\boldsymbol{u}(t)=\boldsymbol{\phi}(t) / \phi=$ $\left[\begin{array}{lll}\cos \Omega t & \sin \Omega t & 0\end{array}\right]^{\mathrm{T}}$ 转动了 $\phi$ 角度, 转轴方向时刻在变化而转角恒定不变，动坐标系的 $z_b$ 轴在空间画出一个圆雉面 (半雉角为 $\phi, z$ 轴称为雉轴)，这正是这一角运动称为圆锥运动 (coning motion) 的原因。

#### 2. 圆锥补偿多子样算法

推导得：在时间段 $\left[t_{m-1}, t_m\right]$ 内的等效旋转矢量 (旋转矢量增量) 可近似为：
$$
\boldsymbol{\phi(T)}=\frac{\phi(T)}{\sin \frac{\phi(T)}{2}}\left[\begin{array}{c}
-\sin \phi \sin \frac{\Omega T}{2} \sin \Omega\left(t_m-\frac{T}{2}\right) \\
\sin \phi \sin \frac{\Omega T}{2} \cos \Omega\left(t_m-\frac{T}{2}\right) \\
-\sin ^2 \frac{\phi}{2} \sin \Omega T
\end{array}\right] \approx\left[\begin{array}{c}
-2 \sin \phi \sin \frac{\Omega T}{2} \sin \Omega\left(t_m-\frac{T}{2}\right) \\
2 \sin \phi \sin \frac{\Omega T}{2} \cos \Omega\left(t_m-\frac{T}{2}\right) \\
-2 \sin ^2 \frac{\phi}{2} \sin \Omega T
\end{array}\right]
$$

在等效旋转矢量计算时间段 $\left[t_{m-1}, t_m\right]$ 内的角增量：
$$
\begin{aligned}
\Delta \boldsymbol{\theta}_m= & \int_{t_{m-1}}^{t_m} \boldsymbol{\omega}(t) \mathrm{d} t=
 {\left[\begin{array}{c}
-2 \sin \phi \sin \frac{\Omega T}{2} \sin \Omega\left(t_m-\frac{T}{2}\right) \\
2 \sin \phi \sin \frac{\Omega T}{2} \cos \Omega\left(t_m-\frac{T}{2}\right) \\
-2 \sin ^2 \frac{\phi}{2} \cdot \Omega T
\end{array}\right] }
\end{aligned}
$$

比较上面两式，它们在 $x$ 轴和 $y$ 轴上完全相同，而 $z$ 轴上存在差异，这一差异使得使用角增量代替旋转矢量进行姿态更新时会产生误差，并且误差随时间会不断累积。考虑到半雉角 $\phi$ 为小量，定义如下误差：
$$
\begin{gathered}
\delta \boldsymbol{\phi}(T)=\boldsymbol{\phi}(T)-\Delta \boldsymbol{\theta}_m=\left[\begin{array}{c}
0 \\
0 \\
-2 \sin ^2 \frac{\phi}{2} \sin \Omega T-\left(-2 \sin ^2 \frac{\phi}{2} \cdot \Omega T\right)
\end{array}\right]= 
{\left[\begin{array}{c}
0 \\
2 \sin ^2 \frac{\phi}{2} \cdot(\Omega T-\sin \Omega T)
\end{array}\right]}
\end{gathered}
$$
为了补偿该误差，通常采用多子样补偿算法，在时间段 $\left[t_{m-1}, t_m\right]$ 内进行 $N$ 次采样，采样间隔为 $h=T / N$，可计算得每个采样间隔内的角增量 (称为子样, sub-sample)，记为：
$$
\Delta \boldsymbol{\theta}_{m i}=\int_{t_{m-1}+(i-1) h}^{t_{m-1}+\boldsymbol{z}_h} \boldsymbol{\omega}(t) \mathrm{d} t=\left[\begin{array}{c}
-2 \sin \phi \sin \frac{\lambda}{2} \sin \Omega\left(t_{m-1}+i h-\frac{h}{2}\right) \\
2 \sin \phi \sin \frac{\lambda}{2} \cos \Omega\left(t_{m-1}+i h-\frac{h}{2}\right) \\
-2 \sin ^2 \frac{\phi}{2} \cdot \lambda
\end{array}\right] \quad(i=1,2, \cdots, N)
$$
其中，简记 $\lambda=\Omega h$，显然有时间段 $\left[t_{m-1}, t_m\right]$ 内的总角增量：
$$
\Delta \boldsymbol{\theta}_m=\int_{t_{m-1}}^{t_m} \boldsymbol{\omega}(t) \mathrm{d} t=\sum_{i=1}^N \Delta \boldsymbol{\theta}_{m i}
$$
在圆锥运动条件下，不同子样间的叉乘积在 $z$ 轴 (锥轴) 方向可提供一 定的角增量补偿作用，所以一般使用时间段 $\left[t_{m-1}, t_m\right]$ 内所有子样之间的叉乘积之和来对 $\boldsymbol{\phi}(T)$ 作估计和补偿，记为：
$$
\delta \hat{\boldsymbol{\phi}}(T)=\sum_{j=2}^N \sum_{i=1}^{j-1} k_{i j}^* {\color{red}\Delta \boldsymbol{\theta}_{m i} \times \Delta \boldsymbol{\theta}_{m j}}
$$

由于假设 $\phi$ 和 $\lambda$ 都是小量，有：
$$
{\color{red}\Delta \boldsymbol{\theta}_{m i} \times \Delta \boldsymbol{\theta}_{m j}} \approx\left[\begin{array}{c}
\frac{(i-j)(\phi \lambda)^3}{2} \sin \Omega\left(t_{m-1}+\frac{i+j-1}{2} h\right) \\
-\frac{(i-j)(\phi \lambda)^3}{2} \cos \Omega\left(t_{m-1}+\frac{i+j-1}{2} h\right) \\
-4 \sin ^2 \phi \sin ^2 \frac{\lambda}{2} \sin (i-j) \lambda
\end{array}\right]
$$

注意到，式中 $z$ 轴分量与绝对时间  $t_{m-1}$ 无关, 只与子样数间隔 $(i-j)$ 有关，因此 $\delta \hat{\boldsymbol{\phi}}(T)$ 式中所有子样叉乘积的项数可由 $N(N-1) / 2$ 项降低为 $N-1$ 项，可简化为：
$$
\delta \hat{\boldsymbol{\phi}}(T)=\sum_{i=1}^{N-1} k_{N-i} {\color{red}\Delta \boldsymbol{\theta}_{m i} \times \Delta \boldsymbol{\theta}_{m N}}
$$
一到六子样的系数如下：
$$
\begin{array}{c|c|c|c|c|c|c}
\hline N & k_1 & k_2 & k_3 & k_4 & k_5 & \rho_N \\
\hline 1 & - & & & & & 1 / 12 \\
\hline 2 & 2 / 3 & & & & & 1 / 960 \\
\hline 3 & 27 / 20 & 9 / 20 & & & & 1 / 204120 \\
\hline 4 & 214 / 105 & 92 / 105 & 54 / 105 & & & 1 / 82575360 \\
\hline 5 & 1375 / 504 & 650 / 504 & 525 / 504 & 250 / 504 & & 1 / 54140625000 \\
\hline 6 & 15797 / 4620 & 7834 / 4620 & 7296 / 4620 & 4558 / 4620 & 2315 / 4620 & 1 / 52295018840064 \\
\hline
\end{array}
$$
在表中，误差补偿系数 $k_i(i=1,2, \cdots, N-1)$ 表示间隔为 $i$ 的两子样叉乘的系数，以四子样算法为例，等效旋转矢量计算公式为：
$$
\begin{aligned}
\boldsymbol{\phi}(T)= & \Delta \boldsymbol{\theta}_m+\delta \hat{\boldsymbol{\phi}}(T)= \\
& \left(\Delta \boldsymbol{\theta}_{m 1}+\Delta \boldsymbol{\theta}_{m 2}+\Delta \boldsymbol{\theta}_{m 3}+\Delta \boldsymbol{\theta}_{m 4}\right)+\left(k_3 \Delta \boldsymbol{\theta}_{m 1}+k_2 \Delta \boldsymbol{\theta}_{m 2}+k_1 \Delta \boldsymbol{\theta}_{m 3}\right) \times \Delta \boldsymbol{\theta}_{m 4}= \\
& \left(\Delta \boldsymbol{\theta}_{m 1}+\Delta \boldsymbol{\theta}_{m 2}+\Delta \boldsymbol{\theta}_{m 3}+\Delta \boldsymbol{\theta}_{m 4}\right)+\left(\frac{54}{105} \Delta \boldsymbol{\theta}_{m 1}+\frac{92}{105} \Delta \boldsymbol{\theta}_{m 2}+\frac{214}{105} \Delta \boldsymbol{\theta}_{m 3}\right) \times \Delta \boldsymbol{\theta}_{m 4}
\end{aligned}
$$

> 注意，圆雉误差补偿系数推导过程中进行了如下几点近似：
>
> * 假设 $\phi$ 和 $\Omega T$ 为小量时对理论等效旋转矢量进行近似。
> * 忽略了非圆锥轴振荡对圆雉误差补偿的影响。
> * 当圆雉运动的雉角比较大时, 表中的误差漂移系数可能会变得不准确。

> * 在实际系统中，陀螺仪的测量分辨率或噪声、幅相特性不理想及数据间不同步都会影响到理论上的圆雉误差补偿效果。
> * 实际运载体的剧烈角运动还会激励出陀螺仪的动态误差，动态误差可能远远大于算法引起的误差，致使多子样圆锥误差补偿往往达不到预期的效果，因此实际应用时子样数并非越多 越好，**二子样算法往往能够满足绝大多数的应用**，建议最多不要超过四子样。


### 6、多项式角运动条件下等效旋转矢量精确数值解

传统主流的姿态更新求解方法是，先使用陀螺角增量的多子样采样计算等效旋转矢量，补偿转动不可交换误差，再使用等效旋转矢量计算姿态更新四元数。但是前两节的多子样算法都是在 Bortz 方程二阶近似的基础上进行推导的，从源头上看不可避免地存在原理性误差，使得在大机动环境下选用的子样数越多，精度往往越差。本节将直接利用 Bortz 方程给出基于多项式迭代的等效旋转矢量精确数值解的算法，这样可以有效避免原理性近似误差。

#### 1. 构造角速度

在实际捷联惯导系统中，大多数陀螺采样直接获得的是角增量输出，而本节算法需要用到角速度作为输人，因此，在角运动为多项式形式假设条件下先给出由角增量信息构造角速度的方法。

假设角速度 $\boldsymbol{\omega}(t)$ 为关于时间 $t$  的 $(N-1)$ 次多项式，即：
$$
\begin{gathered}
\boldsymbol{\omega}(t)=\left[\begin{array}{c}
\boldsymbol{\omega}_x(t) \\
\boldsymbol{\omega}_y(t) \\
\boldsymbol{\omega}_z(t)
\end{array}\right]=\left[\begin{array}{cccc}
w_{N-1, x} & w_{N-2, x} & \cdots & w_{0, x} \\
w_{N-1, y} & w_{N-2, y} & \cdots & w_{0, y} \\
w_{N-1, z} & w_{N-2, z} & \cdots & w_{0, z}
\end{array}\right]\left[\begin{array}{c}
t^{N-1} \\
t^{N-2} \\
\vdots \\
1
\end{array}\right]= \\
{\left[\begin{array}{llll}
\boldsymbol{w}_{N-1} & \boldsymbol{w}_{N-2} & \cdots & \boldsymbol{w}_0
\end{array}\right]\left[\begin{array}{c}
t^{N-1} \\
t^{N-2} \\
\vdots \\
1
\end{array}\right]=\left[\begin{array}{c}
\boldsymbol{W}_x \\
\boldsymbol{W}_y \\
\boldsymbol{W}_z
\end{array}\right]\left[\begin{array}{c}
t^{N-1} \\
t^{N-2} \\
\vdots \\
1
\end{array}\right]=\boldsymbol{W}\left[\begin{array}{c}
t^{N-1} \\
\vdots \\
1
\end{array}\right]}
\end{gathered}
$$
假设陀螺仪在时间段 $[0, T]$ 内进行了 $N$ 次角增量采样，分别记为 $\Delta \boldsymbol{\theta}_j(j=1,2, \cdots, N)$，对上式积分可得：
$$
\Delta \boldsymbol{\theta}_j=\int_{t_{j-1}}^{t_j} \boldsymbol{\omega}(t) \mathrm{d} t=\frac{t_j^N-t_{j-1}^N}{N} \boldsymbol{w}_{N-1}+\frac{t_j^{N-1}-t_{j-1}^{N-1}}{N-1} \boldsymbol{w}_{N-2}+\cdots+\left(t_j-t_{j-1}\right) \boldsymbol{w}_0
$$
简记 $t_j=j T / N$ 。将相继 $N$ 次角增量合并在一起写成矩阵形式如下:
$$
\boldsymbol{\Theta}=\boldsymbol{W} \boldsymbol{\Gamma}
$$

$$
\begin{gathered}
\boldsymbol{\Theta}=\left[\begin{array}{cccc}
\Delta \boldsymbol{\theta}_1 & \Delta \boldsymbol{\theta}_2 & \cdots & \Delta \boldsymbol{\theta}_N
\end{array}\right] \\
\boldsymbol{\Gamma}=\left[\begin{array}{cccc}
\left(t_1^N-t_0^N\right) / N & \left(t_2^N-t_1^N\right) / N & \cdots & \left(t_N^N-t_{N-1}^N\right) / N \\
\left(t_1^{N-1}-t_0^{N-1}\right) /(N-1) & \left(t_2^{N-1}-t_1^{N-1}\right) /(N-1) & \cdots & \left(t_N^{N-1}-t_{N-1}^{N-1}\right) /(N-1) \\
\vdots & \vdots & & \vdots \\
t_1-t_0 & t_2-t_1 & \cdots & t_N-t_{N-1}
\end{array}\right]
\end{gathered}
$$

容易求得以角增量表示的多项式系数矩阵：
$$
\boldsymbol{W}=\boldsymbol{\Theta} \boldsymbol{\Gamma}^{-1}
$$
由此可见，根据相继的 $N$ 次角增量采样，总可以构造一个 $(N-1)$ 次的多项式角速度拟合 $\boldsymbol{\omega}(t)$ ，即可由角增量信息构造出角速度。

#### 2. 等效旋转矢量迭代求解

将等效旋转矢量微分方程式重写如下:
$$
\dot{\boldsymbol{\phi}}=\boldsymbol{\omega}+\frac{1}{2} \boldsymbol{\phi} \times \boldsymbol{\omega}+\left(\frac{1}{12}+\frac{\phi^2}{720}+\frac{\phi^4}{30240}+\frac{\phi^6}{1209600}+\cdots\right)(\boldsymbol{\phi} \times)^2 \boldsymbol{\omega}
$$
两边同时在时间区间 $[0, t]$ 上积分，可得：
$$
\boldsymbol{\phi}-\boldsymbol{\phi}(0)=\int_0^t \boldsymbol{\omega}+\frac{1}{2} \boldsymbol{\phi} \times \boldsymbol{\omega}+\left(\frac{1}{12}+\frac{\phi^2}{720}+\frac{\phi^4}{30240}+\frac{\phi^6}{1209600}+\cdots\right)(\boldsymbol{\phi} \times)^2 \boldsymbol{\omega} \mathrm{d} \tau
$$
将式左边 $\phi(0)$ 移到右边, 并改写成迭代的形式，有：
$$
\boldsymbol{\phi}^{(i+1)}=  \boldsymbol{\phi}(0)+\int_0^t \boldsymbol{\omega}+\frac{1}{2} \boldsymbol{\phi}^{(i)} \times \boldsymbol{\omega}+
{\left[\frac{\left(\phi^{(i)}\right)^0}{12}+\frac{\left(\phi^{(i)}\right)^2}{720}+\frac{\left(\phi^{(i)}\right)^4}{30240}+\frac{\left(\phi^{(i)}\right)^6}{1209600}+\cdots\right]\left(\boldsymbol{\phi}^{(i)} \times\right)^2 \boldsymbol{\omega} \mathrm{d} \tau }
$$
其中, $\boldsymbol{\phi}^{(i)}(i=0,1,2, \cdots)$ 表示第 $i$ 次迭代的等效旋转矢量, 且有迭代初值 $\boldsymbol{\phi}^{(0)}=\mathbf{0}$ 。不失一般性, 可令初值 $\phi(0)=0$ 。




$$
\boldsymbol{\Phi}_j^{(i+1)}=\left[\begin{array}{ll}
\boldsymbol{U}_j^{\prime \prime} & 0
\end{array}\right] \odot\left[\begin{array}{lllll}
\cdots & \frac{1}{3} & \frac{1}{2} & 1 & 0
\end{array}\right] \quad(j=x, y, z)
$$
其中，运算符“ $\odot$ ” 表示两个同阶次的多项式系数向量之间的同幂次系数分量相乘运算，显然 有 $\phi_{0, j}^{(i+1)}=0$ 。
至此，实现了从等效旋转矢量 $\phi^{(i)}$ 到 $\phi^{(i+1)}$ 的迭代递推求解，也就完成了从角速度 $\boldsymbol{\omega}$ 和初值 $\boldsymbol{\phi}^{(0)}=\mathbf{0}$ 到 $\boldsymbol{\phi}^{(i+1)}(i \rightarrow \infty)$ 的求解，理论上可获得等效旋转矢量的精确多项式解 $\boldsymbol{\phi}^{(\infty)}=\boldsymbol{\phi}$ 。从上述求解过程中不难看出，如果 $\boldsymbol{\phi}^{(1)} \times \boldsymbol{\omega} \neq \mathbf{0}$，即在非定轴转动情况下，即使角速度 $\boldsymbol{\omega}$ 为有限阶次的多项式，等效旋转矢量 $\boldsymbol{\phi}^{(\infty)}$ 也将是无限阶次的。

在实际利用计算机进行求解时，需对式右端括号内无穷级数取有限阶次处理。一般角速度 $\boldsymbol{\omega}$ 的零次项多项式系数不为 0，则当迭代次数 $i>0$ 时，多项式 $\boldsymbol{\phi}^{(i)}$ 的不为 0 的最低 幂次为 1 。如果存在不可交换误差， $\left(\boldsymbol{\phi}^{(i)} \times\right) \boldsymbol{\omega}$ 的多项式表示系数中不为 0 的最低幂次一般为 $2,\left(\boldsymbol{\phi}^{(i)} \times\right)^2 \boldsymbol{\omega}$ 不为 0 的最低幂次为 $3,\left(\phi^{(i)}\right)^k\left(\boldsymbol{\phi}^{(i)} \times\right)^2 \boldsymbol{\omega}$ 不为 0 的最低幂次为 $k+3(k=0,2,4$, $6, \cdots)$ 。因此，若忽略了式 (2.7.7) 右端被积函数中与模值 $\phi^{(i)}$ 有关的不小于 $k$ 的所有项 , 则将 可能影响被积函数 $k+3$ 次幂的计算准确性, 再经过积分之后等效旋转矢量多项式 $\boldsymbol{\phi}^{(j+1)}$ 的 $k+$ 4 阶精度会受到影响。换句话说, 若忽略 $k$ 及其以上项后, 等效旋转矢量的多项式迭代解



$$
\boldsymbol{\phi}^{(i+1)}=\int_0^t \boldsymbol{\omega}+\frac{1}{2} \boldsymbol{\phi}^{(i)} \times \boldsymbol{\omega}+\frac{1}{12}\left(\boldsymbol{\phi}^{(i)} \times\right)^2 \boldsymbol{\omega} \mathrm{d} \tau
$$





## 四、姿态更新算法

选取 “东-北-天 (E-N-U)” 地理坐标系作为捷联惯导系统的导航参考坐标系, 后面记为 $n$ 系，则以 $n$ 系作为参考系的姿态微分方程为：
$$
\dot{\boldsymbol{C}}_b^n=\boldsymbol{C}_b^n\left(\boldsymbol{\omega}_{n b}^b \times\right)
$$
其中，矩阵 $C_b^n$ 表示载体系 ( $b$ 系) 相对于导航坐标系 ( $n$ 系) 的姿态阵，由于陀螺输出的是 $b$ 系相 对于惯性系 ( $i$ 系) 的角速度 $\boldsymbol{\omega}_{i b}^b$, 而角速度信息 $\boldsymbol{\omega}_{n b}^b$ 不能直接测量获得，需对微分方程式 作如下变换：
$$
\begin{aligned}
\dot{\boldsymbol{C}}_b^n= & \boldsymbol{C}_b^n\left(\boldsymbol{\omega}_{n b}^b \times\right)=\boldsymbol{C}_b^n\left[\left(\boldsymbol{\omega}_{i b}^b-\boldsymbol{\omega}_{i n}^b\right) \times\right]=\boldsymbol{C}_b^n\left(\boldsymbol{\omega}_{i b}^b \times\right)-\boldsymbol{C}_b^n\left(\boldsymbol{\omega}_{i n}^b \times\right)= \\
& \boldsymbol{C}_b^n\left(\boldsymbol{\omega}_{i b}^b \times\right)-\boldsymbol{C}_b^n\left(\boldsymbol{\omega}_{i n}^b \times\right) \boldsymbol{C}_n^b \boldsymbol{C}_b^n=\boldsymbol{C}_b^n\left(\boldsymbol{\omega}_{i b}^b \times\right)-\left({\color{red}\boldsymbol{\omega}_{i n}^n} \times\right) \boldsymbol{C}_b^n
\end{aligned}
$$
其中， $\color{red}\boldsymbol{\omega}_{i n}^n$ 表示 $n$ 系相对于 $i$ 系的旋转，它包含两部分：**地球自转**引起的 $n$ 系旋转，以及惯导系统在地球表面附近移动因**地球表面弯曲**而引起的 $n$ 系旋转，即有 ${\color{red}\boldsymbol{\omega}_{i n}^n}={\color{brown}\boldsymbol{\omega}_{i e}^n}+{\color{pink}\boldsymbol{\omega}_{en}^n}$, 其中
$$
\begin{gathered}
{\color{brown}\boldsymbol{\omega}_{i e}^n}=\left[\begin{array}{lll}
0 & \omega_{i c} \cos L & \omega_{i c} \sin L
\end{array}\right]^{\mathrm{T}} \\
{\color{pink}\boldsymbol{\omega}_{ib}^n}=\left[\begin{array}{lll}
-\frac{v_{\mathrm{N}}}{R_M+h} & \frac{v_{\mathrm{E}}}{R_N+h} & \frac{v_{\mathrm{E}}}{R_N+h} \tan L
\end{array}\right]^{\mathrm{T}}
\end{gathered}
$$
式中， ${\color{brown}\omega_{\text {ie }}}$ 为地球自转角速率； $L$ 和 $h$ 分别为地理纬度和高度。此方程求解麻烦，所以一般先拆解：
$$
C_{b(m)}^{n(m)}={\color{blue}C_i^{n(m)}} {\color{green}C_{b(m)}^i}
$$
根据姿态阵微分方程 $\dot{\boldsymbol{C}}_b^i=\boldsymbol{C}_b^i\left(\boldsymbol{\omega}_{i b}^b \times\right)$ 和 $\dot{\boldsymbol{C}}_i^n=\left(\boldsymbol{\omega}_{n i}^n \times\right) \boldsymbol{C}_i^n=\left(-\boldsymbol{\omega}_{i n}^n \times\right) \boldsymbol{C}_i^n$, 分别可得相对于惯 生系的更新新算法：
$$
\begin{aligned}
{\color{green}\boldsymbol{C}_{b(m)}^i} & ={\color{green}\boldsymbol{C}_{b(m-1)}^i \boldsymbol{C}_{b(m)}^{b(m-1)}} \\
{\color{blue}\boldsymbol{C}_i^{n(m)}} & ={\color{blue}\boldsymbol{C}_{n(m-1)}^{n(m)} \boldsymbol{C}_i^{n(m-1)}}
\end{aligned}
$$
带回原式：
$$
\boldsymbol{C}_{b(m)}^{n(m)}={\color{blue}\boldsymbol{C}_{n(m-1)}^{n(m)} \boldsymbol{C}_i^{n(m-1)}} {\color{green}\boldsymbol{C}_{b(m-1)}^i \boldsymbol{C}_{b(m)}^{b(m-1)}}=\boldsymbol{C}_{n(m-1)}^{n(m)} \boldsymbol{C}_{b(m-1)}^{n(m-1)} \boldsymbol{C}_{b(m)}^{b(m-1)}
$$
其中, $\boldsymbol{C}_{b(m-1)}^{n(m-1)}$ 和 $\boldsymbol{C}_{b(m)}^{n(m)}$ 分别表示 $t_{m-1}$ 和 $t_m$ 时刻的捷联姿态矩阵。若陀螺在时间段 $\left[t_{m-1}, t_m\right]$ 内 $\left(T=t_m-t_{m-1}\right)$ 进行了两次等间隔采样，角增量分别为 $\Delta \boldsymbol{\theta}_{m 1}$ 和 $\Delta \boldsymbol{\theta}_{m 2}$，采用二子样圆雉误差补偿算法，有：
$$
\begin{gathered}
\boldsymbol{C}_{b(m)}^{b(m-1)}=\boldsymbol{M}_{\mathrm{RV}}\left(\boldsymbol{\phi}_{b(m)}^b\right) \\
\boldsymbol{\phi}_{i b(m)}^b=\left(\Delta \boldsymbol{\theta}_{m 1}+\Delta \boldsymbol{\theta}_{m 2}\right)+\frac{2}{3} \Delta \boldsymbol{\theta}_{m 1} \times \Delta \boldsymbol{\theta}_{m 2}
\end{gathered}
$$
通常在导航更新周期 $\left[t_{m-1}, t_m\right]$ 内，可以认为由速度和位置引起的 $\boldsymbol{\omega}_{i n}^n$ 变化很小，即可视 $\boldsymbol{\omega}_{i n}^n$ 为 常值, 记为 $\boldsymbol{\omega}_{i n(m)}^n$，则有：
$$
\boldsymbol{C}_{n(m-1)}^{n(m)}=\left(\boldsymbol{C}_{n(m)}^{n(m-1)}\right)^{\mathrm{T}}=\boldsymbol{M}_{\mathrm{RV}}^{\mathrm{T}}\left(\boldsymbol{\phi}_{i n(m)}^n\right) \approx \boldsymbol{M}_{\mathrm{RV}}^{\mathrm{T}}\left(T \boldsymbol{\omega}_{i(m)}^n\right)
$$

## 五、速度更新算法

#### 1. 比力方程

$$
\dot{\boldsymbol{v}}_{en}^n=C_b^n \boldsymbol{f}_{\mathrm{sf}}^b-\left(2 \boldsymbol{\omega}_{i e}^n+\boldsymbol{\omega}_n^n\right) \times \boldsymbol{v}_{en}^n+\boldsymbol{g}^n
$$

其中，$\boldsymbol{f}_{\mathrm{sf}}^b$ ：为加速度计测量的**比力**；$2 \boldsymbol{\omega}_{i e}^n \times \boldsymbol{v}_{en}^n$ ：为由载体运动和地球自转引起的**哥氏加速度**；$\omega_{e n}^n \times \boldsymbol{v}_{e n}^n$ ：为由载体运动引起的**对地向心加速度**；$\boldsymbol{g}^n$ ：为**重力加速度**；$-\left(2 \omega_{i e}^n+\right.$ $\left.\boldsymbol{\omega}_{en}^n\right) \times \boldsymbol{v}_{en}^n+\boldsymbol{g}^n$ ：统称为**有害加速度**。

只有在加速度计输出中扣除有害加速度后，才能获得运载体在导航系下的几何运动加速度 $\dot{v}_m^n$，对加速度积分一次可得速度，再积分一次可得位置。

#### 2. 速度更新算法





#### 3. 划桨误差







## 六、位置更新算法

将捷联惯导系统的位置微分方程式重写如下：
$$
\dot{L}=\frac{1}{R_M+h} v_N, \quad \dot{\lambda}=\frac{\sec L}{R_N+h} v_{\mathrm{E}}, \quad \dot{h}=v_{\mathrm{U}}
$$
将它们改写成矩阵形式，有：
$$
\dot{p}=\boldsymbol{M}_{p v} v^n
$$
其中，记：
$$
\begin{gathered}
p=\left[\begin{array}{l}
L \\
\lambda \\
h
\end{array}\right], \quad \boldsymbol{M}_{p v}=\left[\begin{array}{ccc}
0 & 1 / R_{M h} & 0 \\
\sec L / R_{N h} & 0 & 0 \\
0 & 0 & 1
\end{array}\right] \\
R_{M h}=R_M+h, \quad R_{N h}=R_N+h \\
R_M=\frac{R_N\left(1-e^2\right)}{\left(1-e^2 \sin ^2 L\right)}, \quad R_N=\frac{R_e}{\left(1-e^2 \sin ^2 L\right)^{1 / 2}}, \quad e=\sqrt{2 f-f^2}
\end{gathered}
$$
与捷联惯导姿态和速度更新算法相比，位置更新算法引起的计算误差一般比较小，可采用比较简单的梯形积分法离散化，得：
$$
\boldsymbol{p}_m-\boldsymbol{p}_{m-1}=\int_{t_{m-1}}^{t_m} \boldsymbol{M}_{p v} \boldsymbol{v}^n \mathrm{~d} t \approx \boldsymbol{M}_{p v}\left(t_{m-1 / 2}\right) \int_{t_{m-1}}^{t_m} \boldsymbol{v}^n \mathrm{~d} t \approx \boldsymbol{M}_{p v(m-1 / 2)}\left(\boldsymbol{v}_{m-1}^{n(m-1)}+\boldsymbol{v}_m^{n(m)}\right) \frac{T}{2}
$$
移项便得**位置更新算法**：
$$
\boldsymbol{p}_m=\boldsymbol{p}_{m-1}+\boldsymbol{M}_{p v(m-1 / 2)}\left(\boldsymbol{v}_{m-1}^{n(m-1)}+\boldsymbol{v}_m^{n(m)}\right) \frac{T}{2}
$$
其中， $\boldsymbol{M}_{p v(m-1 / 2)}$ 可采用速度算法一样的线性外推算法，对矩阵整体 $\boldsymbol{M}_{p v}$ 进行外推；亦可矩阵元素中的位置变量 $L, h$ 外推，再构造矩阵 $\boldsymbol{M}_{p v}$ 。
$$
x_{m-1 / 2}=x_{m-1}+\frac{x_{m-1}-x_{m-2}}{2}=\frac{3 x_{m-1}-x_{m-2}}{2} \quad\left(x=\omega_{i c}^n, \omega_m^n, v^n, g^n\right)
$$

## 七、PSINS 中的不可交换（圆锥/划桨）误差补偿

### 1、多子样不可交换误差补偿 cnscl

有四种方法：

* **优化不可交换误差补偿**：适用圆锥运动条件，每个相同间隔的角增量得到乘积是相等的。

* **一般方法不可交换误差补偿**：适用多项式条件，调用 `conepolyn` 函数进行补偿。
* **扩展形式不可交换补偿**：调用 `coneuncomp` ，
* **高阶误差补偿**：调用 `conehighorder` ，其它方法都只用两两叉乘加系数的补偿，此方法有三阶、四阶的叉乘。



传入参数：

* `imu`：惯导数据，七列，前三列是陀螺角增量、后三列是加速度计的比力增量，最后一列是时间

* `coneoptimal`：优化模式，

  * `0`：**优化不可交换误差补偿**：基于圆锥运动假设，直接算，用 `glv.cs` 中的补偿系数与角增量相乘，得到补偿量。
  * `1`：**一般方法不可交换误差补偿**，基于多项式条件，调用 `conepolyn` 函数，用补偿系数与角增量相乘，得到补偿量。
  * `2`：**扩展形式不可交换补偿**：调用 `coneuncomp`，推导比较麻烦，一二子样系数与多项式条件差不多。
  * `3`：**高阶误差补偿**：调用 `conehighorder` ，系数保存在 `highordercoef.mat` 中，有二三四阶补偿。

  

  



![1689123602793](https://pic-bed-1316053657.cos.ap-nanjing.myqcloud.com/img/1689123602793.png)

#### 1. glv.cs ：圆锥/划桨补偿系数（二到六子样）

$$
\begin{array}{c|c|c|c|c|c|c}
\hline N & k_1 & k_2 & k_3 & k_4 & k_5 & \rho_N \\
\hline 1 & - & & & & & 1 / 12 \\
\hline 2 & 2 / 3 & & & & & 1 / 960 \\
\hline 3 & 27 / 20 & 9 / 20 & & & & 1 / 204120 \\
\hline 4 & 214 / 105 & 92 / 105 & 54 / 105 & & & 1 / 82575360 \\
\hline 5 & 1375 / 504 & 650 / 504 & 525 / 504 & 250 / 504 & & 1 / 54140625000 \\
\hline 6 & 15797 / 4620 & 7834 / 4620 & 7296 / 4620 & 4558 / 4620 & 2315 / 4620 & 1 / 52295018840064 \\
\hline
\end{array}
$$

```matlab
glv.cs = [    % coning & sculling compensation coefficients 圆锥/划桨补偿系数
    [2,    0,    0,    0,    0    ]/3
    [9,    27,   0,    0,    0    ]/20
    [54,   92,   214,  0,    0    ]/105
    [250,  525,  650,  1375, 0    ]/504 
    [2315, 4558, 7296, 7834, 15797]/4620 ];
```




### 2、多项式角运动条件下的精确数值解法

`cnscl` 里的算法都是使用固定的系数，解算的模式固定，

输入多子样角增量采样，先采用 `wm2wtcoef` 函数求取角速度多项式系数，再采用如下方法之一求解精确数值解：

* `qpicard`：四元数毕卡迭代法
* `btzpicard`/`rodpicard`：等效旋转矢量/罗德里格参数迭代法
* `qtaylor`/`dcmtaylor`：四元数/方向余弦阵泰勒级数法

> 暂未实现基于切比雪夫多项式的罗德里格参数迭代算法



### 3、仿真举例

使用 `conesimu` 函数可进行纯圆锥运动仿真；而使用 `highmansimu` 函数可进行多项式角速度大机动仿真。



`demos\test_attitude_update_methods_compare.m` 给出了各种误差补偿算法之间精度比较的例子：











> 严老师注：目前对实际惯导数据作处理，只需使用二子样或“单子样+前一周期”进行不可交换误差补偿即可，其它高子样算法和迭代算法除了理论显摆毫无实用意义。










## 八、PSINS 中的捷联惯导数值更新

### 1、insinit：初始化获得导航结构体 ins






### 2、insupdate：捷联惯导更新：姿态+速度+位置

计算不可交换误差、传感器标定

> 不可交换误差和传感器标定先后顺序都行，先算不可交换误差后标定计算量少一些，比如四子样算法，先对角增量标定要算四次，后标定只要算一个等效旋转矢量的就行。



因为速度更新所需的姿态是前一时刻的姿态，所以先速度更新、再位置更新、最后姿态更新。









#### ethupdate：地球自转角速度和牵连角速度更新







