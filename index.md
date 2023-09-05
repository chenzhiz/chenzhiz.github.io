### ![image.png](https://cdn.nlark.com/yuque/0/2023/png/33613431/1693893823952-51fbd7fb-5e08-4859-8526-3d56e732408b.png#averageHue=%23fdfbfa&clientId=ucb3a8ad2-84c4-4&from=paste&height=415&id=u329f46f6&originHeight=622&originWidth=1781&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=508681&status=done&style=none&taskId=ub847dad3-2d15-4566-b295-475d84bc6c1&title=&width=1187.3333333333333)
### 论文摘要
图形是描述实体之间关系的通用表示形式。通常情况下，它们被可视化为节点-链接图。**但是具有大量节点和边的复杂图往往会导致过多的视觉混乱。**
缓解这个问题的一般方法是使用交互。

1. **平移和缩放（Pan-and-Zoom）**

允许显示更多细节，但是会失去全局上下文，就是放大以后有一部分图就看不见了
所以 Pan-and-Zoom 通overview+detail）视图技术一起使用，即提供一个全局概览的图。常配合全局+详细（**但是这种方式用分离的两个视图作者说可能会打破有趣的结构**，比如长路径，聚类或者一些中等规模的结构（空间连续性问题）。

2. **鱼眼技术**

一种替代的技术是焦点+上下文（focus + context）方法，在单个视图中显示焦点细节和全局上下文。通常就是通过**鱼眼图**来实现，即**放大感兴趣的局部区域，同时压缩其他比较不重要的区域。**
但是传统鱼眼技术存在缺陷，图形鱼眼（graphical fisheye）、双曲线显示（hyperbolic browser），在缩放时会忽略图的结构造成图像畸变。拓扑鱼眼（Topological fisheyes）通过基于层次化的粗化图保留图的结构来解决这个问题，但这个方法并不通用。大多数现有方法在放大过程中并不试图改善图布局的可读性（如避免节点重叠）但这种方法不支持缩放。一些将链接渲染成曲线的方法（如iSphere、双曲线方法）则会阻碍图探索。
因此文章推出了一套框架，可以产生结构相关的鱼眼（Structure-aware Fisheye），其可视化设计基于Cohe等人的工作：

1. 最小化图表结构的失真
2. 提高感兴趣节点的可读性
3. 在交互过程中保持一致的布局

文章的关键方法是将鱼眼缩放问题建模为一个优化问题，**目标是通过保持图布局中的边方向来实现交互式、平滑且结构相关的缩放。**如此，文章提出的方法就可以最小化局部结构的扭曲从而提升空间连续性。其他改进包括引入美学限制（比如最小化节点重合），以及允许用户交互式的（通过画圈，或者画一个层次结构）在鱼眼交互的时候来指定保留特定的结构。
另一方面，文章设计了不同的任务导向的鱼眼镜头：

1. 多焦点镜头，用于突出显示多个焦点周围的结构
2. 聚类镜头，用于保持子结构
3. 路径镜头，用于聚焦和保留用户定义的路径

并且文章作者开发了高效的基于GPU的实现，能够支持最多15K的节点数量。
### 相关工作
#### 图探索技术
##### 平移和缩放（pan-and-zoom）
![image.png](https://cdn.nlark.com/yuque/0/2023/png/33613431/1693830356742-edd98070-56e8-47ce-b7c3-b1cb541d4ea7.png#averageHue=%23fefdfc&clientId=uc346ed92-d840-4&from=paste&height=256&id=w0enI&originHeight=517&originWidth=489&originalType=binary&ratio=1.25&rotation=0&showTitle=false&size=51951&status=done&style=none&taskId=u297656f4-a748-4277-938c-423cc20856a&title=&width=242.60000610351562)![image.png](https://cdn.nlark.com/yuque/0/2023/png/33613431/1693830385458-6014d553-8adf-4d3d-a82a-be7a23d8609d.png#averageHue=%23fefdfc&clientId=uc346ed92-d840-4&from=paste&height=255&id=u08230bd5&originHeight=526&originWidth=483&originalType=binary&ratio=1.25&rotation=0&showTitle=false&size=54645&status=done&style=none&taskId=u7a47c75c-3487-42c6-98ff-b7c0d8c503c&title=&width=234)
平移和缩放是最常用的交互技术，它允许用户选择并放大感兴趣的区域。在现有的可缩放界面中，有几个被用于图形探索。然而，这种交互方式存在一些固有的限制，比如导航模式不足和用户迷失方向感。因此，可缩放界面通常会与一个额外的全局视图一起使用，以展示整个数据。在大多数情况下，节点-链接图在两个视图中都使用相同的布局技术显示，因此两个视图中的图结构的可读性几乎相同。
##### 鱼眼视图（fisheye views）
![image.png](https://cdn.nlark.com/yuque/0/2023/png/33613431/1693830451599-3dfc257f-4137-4bc8-b2ba-8060251a5098.png#averageHue=%23fefdfc&clientId=uc346ed92-d840-4&from=paste&height=255&id=u3954256e&originHeight=507&originWidth=983&originalType=binary&ratio=1.25&rotation=0&showTitle=false&size=113073&status=done&style=none&taskId=u6fcb35f7-e424-44aa-85c9-83e7fcece92&title=&width=495.2083740234375)
鱼眼视图是著名的焦点+上下文（focus + context）技术的例子，通过空间扭曲将详细的焦点区域无缝地融入到全局上下文中。已经提出了几种针对图形的鱼眼技术，例如图形鱼眼（graphical fisheye）、双曲线显示（hyperbolic display）和iSphere。
**图形鱼眼直接转换图的二维布局**，而**后两者将二维布局映射到非欧几里德几何空间（双曲面或黎曼球面），然后再投影回原始的二维空间**。尽管这些方法通过不同的几何变换扭曲了空间，**但都仅仅基于节点与焦点之间的距离来扭曲节点**。这样做的结果是，**由图的边界定义的结构完全被忽略**，因此可能会对图的形状产生较大的扭曲。为了简单起见，文章将这些方法称为几何鱼眼。
为了保持给定的结构，拓扑鱼眼（Topological fisheyes）技术使用预计算的粗化图（coarsened graphs）层次结构来指导扭曲。然而，**对于一般的图形来说，构建这样的层次结构是非常复杂的**，需要保持结构的操作。相比之下，本文的方法基于输入布局的边缘方向提供了一种高效的公式，以保持图形结构；通过这种新的方法，能够比以前的方法更好地保持结构的质量和性能。值得注意的是，**Ti等人也通过边缘方向的约束来限制几何放大。然而，该方法在映射中单独处理水平和垂直轴，**文章中的方法可以直接处理一般图中的任意方向，并将优化问题公式化为一个线性系统。
##### 语义缩放（semantic zooming）
语义缩放常用于探索分层图，其中输入布局按层次聚类到不同的抽象级别。默认情况下，它显示最粗糙的级别；然后用户可以自由缩放并探索下一级别。换句话说，导航受到图形显示大小的限制。为了解决这个问题，拓扑鱼眼视图利用混合图，将原始图的详细区域与较粗的表示中的其他区域合并，然后应用几何鱼眼视图。**文章的方法可以看作是语义缩放和几何鱼眼的结合，其中保留了结构但扭曲基于几何变换。**
##### 动态布局（dynamic layout）
![image.png](https://cdn.nlark.com/yuque/0/2023/png/33613431/1693831488565-ee2f46de-ce8b-4f9d-8a96-f0a5cc67085d.png#averageHue=%23f1f1f1&clientId=uc346ed92-d840-4&from=paste&height=716&id=u42702262&originHeight=644&originWidth=1303&originalType=binary&ratio=1.25&rotation=0&showTitle=false&size=339919&status=done&style=none&taskId=uc4532131-b779-4b4c-bf84-06ee8820b88&title=&width=1447.7778161307922)
动态布局旨在通过优化图的布局来提高感兴趣区域的可读性。EdgeLens 通过将边缘与焦点节点远离来揭示焦点节点周围的区域。LocalEdge lens和BringNeighbors lens 是EdgeLens的变体，它们过滤焦点区域内节点之间的边缘，并将相邻节点靠近感兴趣的节点。MoleView lens引入了连续捆绑，以展示不同级别的感兴趣边缘的结构。**文章的方法是通过优化来交互地提高图的可读性，美学约束也可以高效地集成进来。**
#### 高质量图分布技术
针对图形可视化，已经开发了各种自动图形布局算法，其中基于压力（stress-based）的方法非常受欢迎。这些方法通过调整节点位置来优化节点之间的距离，从而产生良好的布局。然而，**仅优化距离约束并不能避免边缘重叠或保留结构等效果**。为了解决这些问题，约束图形布局方法将几何约束纳入优化中。
早期的尝试集中在方向约束上，然而相应的优化问题通常是NP-Hard的。因此，现有的启发式求解过程可能无法产生令人满意的布局，特别是对于大型图形而言。Dwyer等人提出了Dig-CoLa，通过将约束集成到压力主导优化中，高效地解决了约束布局的优化问题。随后，他们将压力主导扩展到满足分离约束和非线性约束。最近，Wang等人以向量形式重新定义了压力主导，以便可以通过边缘方向定义各种约束类型并用于优化。文章沿着这一研究方向，但提出了一个新的优化模型，**结合结构、可读性和时间约束来生成鱼眼视图，并以二次时间复杂度求解这些约束。**
### 背景
基础符号：
图：{V, E} 、节点：V 、边：E
点位置： $x_i \,{\in R}^2$
所有点的位置：$X = \left\{ x_1,\cdots ,x_n \right\}$
![image.png](https://cdn.nlark.com/yuque/0/2023/png/33613431/1693834201008-e4132631-0543-439c-b244-e0aa423a1542.png#averageHue=%23f8f8f8&clientId=uaba88efd-2597-4&from=paste&height=380&id=uca51e80c&originHeight=380&originWidth=656&originalType=binary&ratio=1&rotation=0&showTitle=false&size=47190&status=done&style=none&taskId=u50d51855-615d-4e73-beb5-981271690df&title=&width=656)
#### 几何鱼眼
graphical fisheye，hyperbolic fisheye，iSphere
上述的鱼眼基本都是跟几何变换相关，但是忽视了边的链接。会造成结构的扭曲
举一个几何鱼眼的例子
**Graphical fisheye**

1. 首先从c 出发经过 xi 延伸一条线确定到边界的点 bi （如图2a）
2. 然后按照下面的公式将 xi 沿着线移动到 x'i

![image.png](https://cdn.nlark.com/yuque/0/2023/png/33613431/1693834523895-ea466c16-7d3e-4869-90dd-5843bdd2ad1d.png#averageHue=%23fbfbfb&clientId=uaba88efd-2597-4&from=paste&height=79&id=u1a9c2fba&originHeight=79&originWidth=619&originalType=binary&ratio=1&rotation=0&showTitle=false&size=10661&status=done&style=none&taskId=u166f9d56-1ae6-4f1f-9d43-0a9a381ccd6&title=&width=619)
（带入极端值可以知道，β = 0 时候在 c 点，β = 1 时在 bi 点。且容易证明 β'i > β ,所以会更靠近 bi 点）
#### 基于边向量主导
Wang等人将应力主导重新定义为矢量形式的优化问题，使用户能够显式地控制图形布局中边的长度和方向。该优化问题的形式如下：
![image.png](https://cdn.nlark.com/yuque/0/2023/png/33613431/1693832605226-9f68fe20-d348-4e72-b015-71a960edb53f.png#averageHue=%23fcfcfc&clientId=u827bcf73-915d-4&from=paste&height=46&id=uec8f6a3c&originHeight=64&originWidth=490&originalType=binary&ratio=0.8999999761581421&rotation=0&showTitle=false&size=6973&status=done&style=none&taskId=u224fee7d-d9eb-4f45-b7a3-9f9bcf4f19d&title=&width=354.4444580078125)
（eij、dij，分别指的图论距离和方向，wij归一化常数目的是为了降低远距离的点对在能量最小化中带来的影响，合起来就是基于图论的向量，布局算法得到的边向量要尽可能接近图论向量）
参考自 [https://zhuanlan.zhihu.com/p/317840611](https://zhuanlan.zhihu.com/p/317840611)
e为边的目标方向，d为边的目标长度。在这种布局方法的启发下，作者期望通过改变边的朝向跟长度，来产生一个结构相关的并且能够光滑过渡的鱼眼交互。
### 结构相关鱼眼图
引言中描述的三个要求

1. 结构保持
2. 可读性
3. 布局一致性

因此文章将结构感知的鱼眼镜头定义为一个优化问题，通过下面公式中的三个目标来解决
![image.png](https://cdn.nlark.com/yuque/0/2023/png/33613431/1693835747179-83e17cca-ba2f-4eec-9efe-32f1378b6216.png#averageHue=%23fcfcfc&clientId=u52d811c6-89ab-4&from=paste&height=154&id=u3e1bcb56&originHeight=189&originWidth=496&originalType=binary&ratio=1&rotation=0&showTitle=false&size=18828&status=done&style=none&taskId=u15fe51d4-0700-4066-9913-2a8f7f3e5f0&title=&width=403)
$Z^t$：第 t 次迭代时节点的位置
$w_{ij}^{s},w_{ij}^{r},w_{i}^{t}$：归一化的权重
$\Omega$：焦点区域内边的集合
$e_{ij}^{r},d_{ij}^{r}$：提供可读性限制
$\mathrm{z}_{\mathrm{i}}^{\mathrm{t}}\in \mathrm{Z}^{\mathrm{t}}$：表示 t 次迭代时节点的位置
最后一项表达了时序上的连续性，也就是光滑过渡
需要注意的是$\Omega$并不仅仅是可见的边，其包含了所有点对点之间的关系，因为即使两个点之间没有边相连接，也需要两点之间相互远离
#### 结构相关的限制
![image.png](https://cdn.nlark.com/yuque/0/2023/png/33613431/1693877161061-7db3b33e-f9ff-4e45-98d2-bc62f33c4fd1.png#averageHue=%23fcfcfc&clientId=u5f4b02cd-f899-4&from=paste&height=56&id=u6387a837&originHeight=86&originWidth=652&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=9719&status=done&style=none&taskId=u39d93777-8c38-421d-ab25-927fe8956eb&title=&width=422.66668701171875)
因为鱼眼镜头会非线性地扭曲布局。为了放大布局，必须大幅度地改变边的长度，并将空间上下文推向域边界。因此，不能简单地强制目标布局Zt中的边长度和方向遵循原始布局X以保持其结构。因此想法就是鼓励**Zt中的边方向遵循原始布局X**，而**Zt中的边长度则遵循由鱼眼估计的目标布局 X'**
##### 形状限制

使用上述约束可以产生一个更好地保持结构的鱼眼视图。然而，这些约束仅仅是局部的，因此较大规模的结构仍可能被扭曲，如下图，**a **是初始图，**b **是经过graphical fisheye的产出，**c **是加入了边方向限制，**d **则是加入了形状限制。
![image.png](https://cdn.nlark.com/yuque/0/2023/png/33613431/1693877825707-42e9c3f2-c5e8-4e80-9646-8f01d8236ef0.png#averageHue=%23f4f4f3&clientId=u5f4b02cd-f899-4&from=paste&height=257&id=udbc3fba8&originHeight=385&originWidth=1421&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=108994&status=done&style=none&taskId=u20c19475-4781-4668-af30-98b89e57f3d&title=&width=947.3333333333334)
为保留初始的结构，文章需要用户提供一组连接的边，称为 **Es,**比如上图 **c **中的其中一圈蓝边，那么算法就能更好的估计$d_{ij}^{s}$。在缩放的时候，有些边变长，有些边变短。因此需要平衡这种变换。
![image.png](https://cdn.nlark.com/yuque/0/2023/png/33613431/1693878259452-e56646aa-a172-40b1-ab67-5c3b1c4d978b.png#averageHue=%23fdfdfd&clientId=u14acde86-2f39-4&from=paste&height=61&id=u3c24d899&originHeight=132&originWidth=765&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=13391&status=done&style=none&taskId=ub980de7a-2be7-495f-a906-7f730383d11&title=&width=355.3333435058594)
这里估计了一个平均畸变率 ρ ，使得上图 **c **产生的结构的边长 $d\prime$和$\rho d_{ij}$之间的差距最小
ρ可以通过下面的公式算出：
![image.png](https://cdn.nlark.com/yuque/0/2023/png/33613431/1693878813548-b5d07421-0a2a-4b33-a976-4edcb572d384.png#averageHue=%23fcfcfc&clientId=u14acde86-2f39-4&from=paste&height=61&id=udd3d8487&originHeight=129&originWidth=882&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=20924&status=done&style=none&taskId=ud056b3c5-ab1f-41ad-8e91-f90c3110def&title=&width=417.3333435058594)
由此计算得到畸变率，那么对所有边长都乘上这个畸变率，就能进行统一变换，再加上上面的边方向的保持，从而保持原结构。
![image.png](https://cdn.nlark.com/yuque/0/2023/png/33613431/1693878968491-e8622abb-f331-4999-959a-b6eaba7d31a6.png#averageHue=%23fefdfd&clientId=u14acde86-2f39-4&from=paste&height=41&id=u7201e685&originHeight=91&originWidth=659&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=5968&status=done&style=none&taskId=ua336069e-4f66-4f7a-aa6f-c7e9f53887b&title=&width=293.3333435058594)
经验性的作者将 **Es **中的边权重 ωs 乘上10来加强结构的保护。
#### 可读性相关限制
![image.png](https://cdn.nlark.com/yuque/0/2023/png/33613431/1693879277624-43afc467-1773-4a80-908e-efeee1c695da.png#averageHue=%23f3f2f2&clientId=u14acde86-2f39-4&from=paste&height=365&id=u6c85f320&originHeight=548&originWidth=1031&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=126287&status=done&style=none&taskId=u17391ec3-5739-4af3-9194-24fcf8a30e9&title=&width=687.3333333333334)
##### 减少重叠节点
在焦点区域检测重叠的节点对，并使用下面的公式这些节点之间的长度进行约束
![image.png](https://cdn.nlark.com/yuque/0/2023/png/33613431/1693879517474-8a5b41a4-f250-4b2a-bd2d-9592bd20f3f9.png#averageHue=%23fdfdfd&clientId=u14acde86-2f39-4&from=paste&height=40&id=ub843bb68&originHeight=80&originWidth=781&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=9453&status=done&style=none&taskId=ub0fa338e-2f3a-452c-b6e7-1aa0ee54491&title=&width=390.3333435058594)
**ri** 和 **rj** 是节点包围盒的半径，**ε** 则是节点分离系数（设为屏幕尺寸的1%）。当然这中间还包括了那些不出现的虚拟边（也就是没有相连的两个节点之间也要分离）
##### 最大化边的夹角
Purchase的图形可读性研究指出，边交叉对人类理解图形有很大影响。完全解决边交叉又是不可能的，尤其是对于大型图形。另一方面，最近的研究表明，**最大化交叉角可以提高路径追踪任务的性能。**根据这一发现，文章通过以下步骤来最大化边交叉角（到 π）。
![image.png](https://cdn.nlark.com/yuque/0/2023/png/33613431/1693880206081-3092313c-8a51-48f4-bbdc-ab80bfdf6683.png#averageHue=%23fcfcfc&clientId=u14acde86-2f39-4&from=paste&height=63&id=u2bbef527&originHeight=104&originWidth=1035&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=16388&status=done&style=none&taskId=u8e893b63-1ea8-40a2-83f2-d0ccb85b098&title=&width=627.2726910173421)
$\alpha$：表示$e_{ij}^{r},e_{lk}^{r}$两条边的夹角
$\oplus$：表示顺时针旋转
$\ominus$：表示逆时针旋转
### 任务导向的鱼眼透镜
![image.png](https://cdn.nlark.com/yuque/0/2023/png/33613431/1693881539325-1e86f6c3-3d74-46fc-b437-85f4dc48663f.png#averageHue=%23f8f6f5&clientId=u14acde86-2f39-4&from=paste&height=512&id=ufde5408d&originHeight=845&originWidth=1954&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=1244342&status=done&style=none&taskId=u5d0446f0-d4e8-4b38-9d8f-f019c694180&title=&width=1184.2423557950594)
#### 多焦点透镜
在之前的基础上加入了多焦点的透镜，效果如下图
b : 原始多焦镜头
c : 本文的结构产生的多焦镜头
![image.png](https://cdn.nlark.com/yuque/0/2023/png/33613431/1693881572504-556e2f6d-7a6f-4371-baae-cd3511ff9574.png#averageHue=%23f8f5f4&clientId=u14acde86-2f39-4&from=paste&height=430&id=u229a5642&originHeight=709&originWidth=872&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=469924&status=done&style=none&taskId=udd46b5c8-b3c8-4906-9b6e-ada64bc9d8d&title=&width=528.4848179392486)
可以看中间的内容，改进过后的多焦点可以更好的保留聚类结构
#### 聚类透镜
在之前的基础上，用户可以制定一个凸包从而可以放大该区域包含的聚类，如图d，红色的聚类是放大聚类，用灰色背景高亮起来。
![image.png](https://cdn.nlark.com/yuque/0/2023/png/33613431/1693881707885-06822ab7-9f7f-421c-a83b-d3f096b19608.png#averageHue=%23faf8f6&clientId=u14acde86-2f39-4&from=paste&height=396&id=u416bb192&originHeight=654&originWidth=473&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=231377&status=done&style=none&taskId=u9668645a-315c-4f25-8e5b-d4d2ed3a164&title=&width=286.6666500977805)![image.png](https://cdn.nlark.com/yuque/0/2023/png/33613431/1693881734800-78ca8df5-cee0-448a-9e85-a6fec3ab6430.png#averageHue=%23d2b987&clientId=u14acde86-2f39-4&from=paste&height=409&id=u8432e8da&originHeight=675&originWidth=567&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=415186&status=done&style=none&taskId=ue3c43faa-9146-41db-ae83-ed6c50e08cb&title=&width=343.6363437747179)
#### 路径透镜
用户简单的挑选两个节点，算法找到最短路径，然后沿着这条路径定义焦点区域。这个焦点区域的半径被经验性的定为：屏幕尺寸的$\sqrt{m}/28$，其中 m 是放大系数。
![image.png](https://cdn.nlark.com/yuque/0/2023/png/33613431/1693882094419-e590c086-61b8-4b34-853a-9b0bd5fc4b63.png#averageHue=%23f1f1f1&clientId=u14acde86-2f39-4&from=paste&height=339&id=u412c4277&originHeight=560&originWidth=666&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=134919&status=done&style=none&taskId=ub45a591e-d6a6-4fb4-9965-884ff8884ab&title=&width=403.63634030681146)
![image.png](https://cdn.nlark.com/yuque/0/2023/png/33613431/1693882124157-9ec5d760-4295-486c-bcf6-d3fb68414bc3.png#averageHue=%23f3f2f2&clientId=u14acde86-2f39-4&from=paste&height=346&id=uf15a59f2&originHeight=571&originWidth=706&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=164181&status=done&style=none&taskId=u52e927c5-35ff-4793-a7c0-ef376b848fb&title=&width=427.8787631480614)
### 结果和评估
#### 定量比较
##### 实验设置
**对照组**

1. **graphical fisheye**(GF, M. Sarkar and M. H. Brown. Graphical fisheye views of graphs. In Proc. SIGCHI conference on Human Factors in Computing Systems, pp. 83–91, 1992. doi: 10.1145/142750.142763)
2. **hyperbolic fisheye**(HF, T. Munzner. Exploring large graphs in 3d hyperbolic space. IEEE Computer Graphics and Applications, 18(4):18–23, 1998. doi: 10.1109/38.689657)
3. **iSphere**(F. Du, N. Cao, Y.-R. Lin, P. Xu, and H. Tong. isphere: Focus+ context sphere visualization for interactive large graph exploration. In Proc.SIGCHI conference on Human Factors in Computing Systems, pp. 2916–2927, 2017. doi: 10.1145/3025453.3025628)

**对照参考因素**

1. 边的方向保持
2. 节点重叠和
3. 形状保持

**其他**

1. 为了进行公平比较，文章的方法只**强制执行边的方向和节点不重叠**的约束条件。
2. 比较双曲线鱼眼的时候，用了iSphere这篇文章的实现来进行比较。
3. 而iSphere在放大之后会把非焦聚区域放在球体的背面，无法比较前两项指标。

一共比较了六组方法：

1. GF（**graphical fisheye**）
2. HF（**hyperbolic fisheye**）
3. ISphere（**iSphere**）
4. GF+ours
5. HF+ours
6. ISphere+ours

前面三折都是直接解析变换，后面三者则是一个优化问题，具体看公式3
文章使用了五个具有不同节点和边数的真实数据集，具体看表1对于每个数据集，随机选择了100个焦点中心，并为每个焦点中心选择了20个随机的放大因子，范围从0.1到20。为了避免由于随机性引起的偏差，作者运行了三种方法，即Ours+GF、Ours+HF和Ours+iSphere，运行100次。
![image.png](https://cdn.nlark.com/yuque/0/2023/png/33613431/1693893715335-2a71c8f5-4a56-4a06-b0dc-97c7942a29ca.png#averageHue=%23faf8f6&clientId=ucb3a8ad2-84c4-4&from=paste&height=205&id=u8443bf76&originHeight=307&originWidth=963&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=91398&status=done&style=none&taskId=uc0bb57fd-eca7-4a9c-a680-1c414737d4d&title=&width=642)
##### 边方向保留
用Edge Orientation Offset(EOO)，边方向偏差来定义边方向偏差：
![image.png](https://cdn.nlark.com/yuque/0/2023/png/33613431/1693894218779-48b54c01-43ac-4f54-8ea7-eaf5ea54b872.png#averageHue=%23fbfbfb&clientId=ucb3a8ad2-84c4-4&from=paste&height=58&id=ud2526f62&originHeight=106&originWidth=901&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=16872&status=done&style=none&taskId=uea97083d-0a73-481d-8947-73d5aec068f&title=&width=497)
简而言之就是：1 - 所有边余弦相似度的平均值（cos值越大，夹角越小）
**即EOO越小，边方向保留的越好**
不过这里这个指标只考虑边的方向并没有考虑边长的变化，只是表明了结构保存的完整性。图6a表示了EOO，显示了应用文章方法的优化技术表现明显优于没有应用技术的
![image.png](https://cdn.nlark.com/yuque/0/2023/png/33613431/1693894584450-1af1979b-db82-45ef-880e-e3377431e427.png#averageHue=%23fafaf8&clientId=ucb3a8ad2-84c4-4&from=paste&height=575&id=ua61d6200&originHeight=862&originWidth=1204&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=213078&status=done&style=none&taskId=u794222c2-2d14-4e57-9820-be0797512e1&title=&width=802.6666666666666)
##### 节点不重叠
比较了初始输入和输出之间的节点重叠数量的差异。从图6b可以看出，效果还是不错甚至是接近于输入的数量，这说明文章描述的优化方法，不仅放大了感兴趣的结构，还改善了结果的可读性。
##### 形状保留
利用Eades等人提出的基于形状的美学方法，比较缩放前后的形状变化

1. 给定节点图，这些度量方法计算一个称为形状图的图（由每个节点的 **k** 个最近邻组成）
2. 计算输入图和放大图的形状图
3. 使用两个形状与之间的平均 Jaccard similarity 进行比较（较大的值表示形状结构保留效果好）

在iShpere的比较过程中只能考虑 2D 的xy坐标
![image.png](https://cdn.nlark.com/yuque/0/2023/png/33613431/1693895600608-0db1fbac-dc70-4b2f-96dc-d69258f1f939.png#averageHue=%23faf8f7&clientId=ucb3a8ad2-84c4-4&from=paste&height=445&id=uf87e02b8&originHeight=667&originWidth=1144&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=204812&status=done&style=none&taskId=ud714a81f-a86e-46e9-93d1-c5c5ab96aaa&title=&width=762.6666666666666)
可以看出在不同最近邻情况下，文章提供的方法能更好的保留全局形状
![image.png](https://cdn.nlark.com/yuque/0/2023/png/33613431/1693895865483-61acaf82-9411-4774-babc-07e528edab75.png#averageHue=%23fefcfb&clientId=ucb3a8ad2-84c4-4&from=paste&height=670&id=ud6a4275d&originHeight=1005&originWidth=1837&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=445069&status=done&style=none&taskId=uc908b00c-a5d9-4d8f-95e7-e26349928a1&title=&width=1224.6666666666667)
a：初始图形
b：图形鱼眼（graphical fisheye）
c：双曲线鱼眼（hyperbolic fisheye） 
d：iShpere
e：线性放大
f：文章方法结合 graphical fisheye
g：文章方法结合 hyperbolic fisheye
h：文章方法结合 iSphere
文章的结果很好地保留了正方形、五边形和直角的形状，并且重叠较少
#### 实验室研究
为了验证文章的 structure-aware fisheye 能否有效的提高图形的探索效率，文章设计了Lab study。比较了下面五个技术

1. 平移和缩放（P&Z）
2. 图形鱼眼（GF）
3. 双曲线鱼眼（HF）
4. iSphere
5. Ours + GF，因为这个表现没有很好也没有很差，表现适中所以选择这个

因为传统方法没有考虑可读性的约束，因此这里只用了边方向限制进行比较。
##### 实验设置
###### 任务
DU等人在 iSphere 文章中已经做过节点、边、路径探索的实验了，而本文方法保持了边的方向，显然可以在探索节点和边的时候表现更好，这一点已经在 pilot 实验中验证了。因此本文的任务集中到对大型图比较重要的“路径探索”上。因此作者采用 Xu等人在 **A user study on curved edges in graph visualization** 文中设计的方法，要求参与者找到随机两个节点之间最短路径的长度。由于实验难度随最短路径长度的增加呈指数增加，因此为了控制难度，实验设置随机节点对之间的最短路径长度在 3-5 之间。
###### 数据集
**图的大小**和**聚类结构**是影响用户在路径探索过程中表现的两个主要因素，聚类结果可以通过模块度（modularity measure）衡量。模块度大意味着清晰地聚类结构。Du等人在 iSphere 文中证明了**小型图中三种方法P&Z、HF和iSphere的用户表现没有显著差异。**但在大规模图，低、高模块图情况下，iSphere表现的更好，而HF表现的明显更差。
基于之前的实验，作者基于Du等人的方法生成了高模块度的图**（1024个节点和0.6的模块度）**，**平均节点度为3**（避免难度过高），使用应力主导布局生成一个初始布局，作为所有五种技术的输入。此外作者**预先计算了725个节点对，其中90%的最短边长度为5。**
###### 系统实现
系统界面支持选择机制辅助用户进行探索，用户右键点击节点会高亮节点及其相邻连接。直到用户下一次点击。
###### 参与者、设施
招募了40名志愿者（男性24人，女性16人），年龄在22至29岁之间（平均24岁）。实验在一台配备了鼠标、键盘和24英寸显示器（分辨率为1920×1080，刷新率为144Hz）的台式电脑上进行。节点-链接图以白色背景显示在一个窗口中，其中每个节点呈现为黑色点，每条边呈现为黑色线条。实验中使用了150mm×150mm的窗口大小。
图 9b-f 展示了将五种技术应用于输入图的结果。
![image.png](https://cdn.nlark.com/yuque/0/2023/png/33613431/1693898348473-d22d204c-e9b4-4997-885f-6678214ecffe.png#averageHue=%23e9e8e8&clientId=u2d96dc9f-8aee-4&from=paste&height=443&id=u072971fc&originHeight=1217&originWidth=1530&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=1285303&status=done&style=none&taskId=u3f603412-710f-4f1d-bcb2-39bd95c462c&title=&width=557)
###### 实验过程
实验开始时，向每个参与者介绍了五种技术，并演示了它们的工作原理。然后，参与者需要使用每种技术进行两次练习。任务与实际测试相同：找到一对随机选择的节点之间的最短路径。
之后每个参与者总共进行15次试验（每个技术3次），对每种技术的试验顺序和五种技术的顺序进行了随机排列，防止偏见。每次选择有60s限制，每个人大概花费15min，每种技术完成休息，全部完成后访谈。
###### 假设
基于之前的研究作者提出了以下的假设

- H1：使用直线绘制的技术（GF、P&Z 和 Ours+GF）比使用曲线绘制的方法（HF、iSphere）更有效；
- H2：鱼眼技术（GF、HF、iSphere 和 Ours+GF）比忽略上下文的方法（P&Z）更有效；
- H3：具有较低或无畸变的技术（Ours+GF 和 P&Z）比具有较强畸变的方法（GF、HF和iSphere）更有效。
###### 分析
每个实验都记录了完成时间和错误（用户输入路径长度与正确路径长度之间的绝对差）。因为每人每种技术3次测试因此可以计算平均时间和平均误差
##### 实验结果
![image.png](https://cdn.nlark.com/yuque/0/2023/png/33613431/1693899066072-8f3cf15f-4338-4a8d-91a4-3bedca15fbd1.png#averageHue=%23f6f6f6&clientId=u2d96dc9f-8aee-4&from=paste&height=237&id=ued47f95b&originHeight=579&originWidth=1358&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=118378&status=done&style=none&taskId=u757aff79-9419-439d-8f14-638e890b8aa&title=&width=556)
根据图所示，在时间方面，Ours+GF会比其他所有技术都更快，这支持了假设H1，但只是部分支持假设H2，毕竟Ours+GF和P&Z之间有大幅重叠。而在误差方面，有大规模的置信区间，也就说明了结果并不明确。尽管这样，Ours+GF的平均值还是最低的
##### 讨论
######  Hyperbolic vs. Pan&Zoom
HF和P&Z的性能与预期的一致。几乎所有的用户都表示类似的意见：“双曲鱼眼会导致布局混乱且强烈扭曲，一些边缘具有很大的曲率”，而“平移和缩放保持了稳定的布局，对于路径追踪来说直观，尽管它丢失了部分上下文。”
######  iSphere
虽然，Du等人假设iSphere在与路径追踪相关的任务中表现良好。然而，结果显示，iSphere（IS）的表现略好于双曲鱼眼（HF），但比其他三种技术差。一位用户表示：“当在iSphere中放大一个节点的细节时，其他远离的节点经常会丢失。”这是因为iSphere只渲染显示球的南半球上的节点。为了解决这个问题，用户通常需要进行更多的交互才能找到并探索缺失的节点。另一方面，大多数用户并不意识到iSphere产生的畸变。一位用户说：“iSphere的交互看起来像是对球体的旋转，和平移和缩放一样直观。”
######  Graphical fisheye 
与HF和iSphere相比，GF仍然使用直线边缘，因此完成任务所需的时间更长。然而，它的变形完全破坏了全局结构，因此其误差最大。一位用户表示：“与图形鱼眼的交互不如iSphere和双曲鱼眼直观，而且图形鱼眼的变形是最不熟悉的。”
######  Our fisheye 
虽然文章的方法基于图形鱼眼的几何变换，但用户的表现是最好的。大多数用户表示：“结构相关的鱼眼在某种程度上结合了双曲鱼眼和平移缩放的优点，直线有助于路径追踪，同时变形保持较小。并且用户交互比其他方法更快，因为点击一个节点可以放大感兴趣的结构，无需进行平移和缩放。”
#### 案例分析
##### Ego-network 探索
![image.png](https://cdn.nlark.com/yuque/0/2023/png/33613431/1693900058501-765a9ba7-4c5c-4a96-87f4-da10d527e349.png#averageHue=%23f8f6f5&clientId=uc7731b75-242a-4&from=paste&height=582&id=uce9c37b9&originHeight=873&originWidth=2009&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=1300047&status=done&style=none&taskId=u0ed0b951-8720-437a-935a-8201a999fbd&title=&width=1339.3333333333333)
文章使用了EGO-FACEBOOK数据集，其中包含4039个Facebook用户（节点）和88234个用户之间的关系（边）。由于网络已经被分成了16个社区，通过将聚类非重叠约束强加到受限应力模型中来生成初始布局，以便不同的社区能够良好地分离。然而，一些区域的边和节点仍然非常密集，使得难以比较彼此相距较远的节点。尽管简单地应用polyfocal的鱼眼可以更好地显示焦点节点周围的局部结构，但社区的结构可能会严重扭曲，如图11b 所示。而本文的方法可以解决这个问题，如图11c ，选择了来自两个不同聚类内部的两个节点。可以看到，这两个节点都与自己的聚类有一些连接，并且与红色和粉色聚类密切联系。
在未放大的布局中，例如图11a，也很难理清节点之间的关系。如顶部的红色聚类，被一些青色节点分隔开。在未放大的视图中无法看到发生了什么。但是利用本文的结构化鱼眼可以看到一些有意思的东西，从图11d 中，可以看到离群的青色节点不仅与该红色聚类的其他节点强烈关联，而且还连接到许多红色节点。这清楚地揭示了节点链接图中红色和青色聚类之间的关系。
##### 美国主要城市探索
![image.png](https://cdn.nlark.com/yuque/0/2023/png/33613431/1693900565504-ac80de62-47c1-4fc4-be96-3bf74d598d33.png#averageHue=%23f4f3f3&clientId=uc7731b75-242a-4&from=paste&height=525&id=u8cd9fabf&originHeight=787&originWidth=2009&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=585488&status=done&style=none&taskId=ua4f449e4-2671-42eb-a11e-0fd708d1bc1&title=&width=1339.3333333333333)
134个节点，代表美国的主要城市，以及338条边，代表相邻城市之间的连接。节点以包含相应城市名称的正方形呈现。节点标签的字体大小根据节点的畸变因子进行调整。图12a 显示了输入布局。图12b 使用聚类透镜放大了淡蓝色区域，节点之间重叠度降低可以看清节点label。
如图12c, 为了寻找华盛顿和堪萨斯之间的最短路径，路径透镜则能够放大最短路径周围的区域，良好的展示两个城市之间的关系。
### 讨论

1. 模型在2D屏幕坐标中完成，不是在图结构中完成。图的子结构可能不能清晰显示，特别是节点与焦点距离较远时
2. 期望与应力结合进一步增强图的结构
3. 作者期望将该方法拓展到其他种类数据集，图地图、复杂树、3D面片之类

