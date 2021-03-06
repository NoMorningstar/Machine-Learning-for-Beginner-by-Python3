# BPNN Theory
 
#### 理论推导

+ **符号说明**

    1. **神经网络的层数**m，也就是包括m-2个隐层；
    2. **输入层**为I，其节点数等于单个样本的输入属性数N_i；
    
       **隐层**输出为Hh，h为1到m-2，每一个隐层的节点数为Nh；
       
       **输出层**为O，其节点数等于单个样本的输出属性数N_o；
       
       **样本真实输出**为R；
       
    3. 层之间连接的**权重**为Wq，q为0到m-2，Wq矩阵的大小为(g, t), g为该隐层前一层的节点数，t为该隐层的节点数；
    
       对应的**偏置**为Bq，q为0到m-2，Bq矩阵的大小为(1, t), t为该隐层的节点数；
    
    4. 隐层的**激活函数**Ah，h为0到m-2。每一层的激活函数可以不同，但是大多数情形下设置为相同的；
    
       常用的激活函数：Sigmoid，Tanh，ReLU。选择激活函数时一定要注意：**输出层激活函数的输出尺度一定要和样本的输出数据是同一尺度。例如Sigmoid的输出的数值是0-1之间，因此样本的输出也应该转化到0-1之间**。
       
    5. 输出层O与样本真实输出R之间的**成本函数**C，回归问题用最小二乘函数， 分类问题用交叉熵函数；
    
    6. s为**子样本数**，也就是一次训练过程中的样本数。当s为1是在线学习；当s为k，也就是全部样本数，为增量学习；s为小于K的其他数值为批量学习，比较常用；
    
    7. **运算符号说明**：<a href="http://www.codecogs.com/eqnedit.php?latex=&plus;" target="_blank"><img src="http://latex.codecogs.com/gif.latex?&plus;" title="+" /></a>为numpy数组的广播运算加法；<a href="http://www.codecogs.com/eqnedit.php?latex=\times" target="_blank"><img src="http://latex.codecogs.com/gif.latex?\times" title="\times" /></a>为矩阵对应元素相乘；<a href="http://www.codecogs.com/eqnedit.php?latex=\bullet" target="_blank"><img src="http://latex.codecogs.com/gif.latex?\bullet" title="\bullet" /></a>表示矩阵之间的乘法；
      
+ **网络结构图示**

![image](https://github.com/Anfany/Machine-Learning-for-Beginner-by-Python3/blob/master/BPNN/Bpnn_Struct.png)


+ **样本结构说明**

     + 输入数据
      
    <a href="http://www.codecogs.com/eqnedit.php?latex=\mathbf{I}=\begin{bmatrix}&space;[x_{1}^{1}&x_{1}^{2}&space;&\cdots&space;&x_{1}^{N\_i}]&space;\\&space;[x_{2}^{1}&x_{2}^{2}&space;&\cdots&space;&x_{2}^{N\_i}]&space;\\&space;&&space;&\cdots&space;&\\&space;[x_{k}^{1}&space;&x_{k}^{2}&\cdots&space;&x_{k}^{N\_i}]&space;\end{bmatrix}" target="_blank"><img src="http://latex.codecogs.com/gif.latex?\mathbf{I}=\begin{bmatrix}&space;[x_{1}^{1}&x_{1}^{2}&space;&\cdots&space;&x_{1}^{N\_i}]&space;\\&space;[x_{2}^{1}&x_{2}^{2}&space;&\cdots&space;&x_{2}^{N\_i}]&space;\\&space;&&space;&\cdots&space;&\\&space;[x_{k}^{1}&space;&x_{k}^{2}&\cdots&space;&x_{k}^{N\_i}]&space;\end{bmatrix}" title="\mathbf{I}=\begin{bmatrix} [x_{1}^{1}&x_{1}^{2} &\cdots &x_{1}^{N\_i}] \\ [x_{2}^{1}&x_{2}^{2} &\cdots &x_{2}^{N\_i}] \\ & &\cdots &\\ [x_{k}^{1} &x_{k}^{2}&\cdots &x_{k}^{N\_i}] \end{bmatrix}" /></a>，其中每一行表示一个样本的输入，每个样本有N_i个输入属性， 样本数为k；
    
    + 输出数据
        
   <a href="http://www.codecogs.com/eqnedit.php?latex=\mathbf{R}=\begin{bmatrix}&space;[y_{1}^{1}&space;&\cdots&space;&y_{1}^{N\_o}]&space;\\&space;[y_{2}^{1}&space;&\cdots&space;&y_{2}^{N\_o}]&space;\\&space;&&space;\cdots&space;&\\&space;[y_{k}^{1}&space;&\cdots&space;&y_{k}^{N\_o}]&space;\end{bmatrix}" target="_blank"><img src="http://latex.codecogs.com/gif.latex?\mathbf{R}=\begin{bmatrix}&space;[y_{1}^{1}&space;&\cdots&space;&y_{1}^{N\_o}]&space;\\&space;[y_{2}^{1}&space;&\cdots&space;&y_{2}^{N\_o}]&space;\\&space;&&space;\cdots&space;&\\&space;[y_{k}^{1}&space;&\cdots&space;&y_{k}^{N\_o}]&space;\end{bmatrix}" title="\mathbf{R}=\begin{bmatrix} [y_{1}^{1} &\cdots &y_{1}^{N\_o}] \\ [y_{2}^{1} &\cdots &y_{2}^{N\_o}] \\ & \cdots &\\ [y_{k}^{1} &\cdots &y_{k}^{N\_o}] \end{bmatrix}" /></a>，其中每一行表示一个样本的输出，每个样本有N_o个输出属性， 样本数为k；
   
   
* **正向传播**

  <a href="http://www.codecogs.com/eqnedit.php?latex=\\&space;\mathbf{H_{1}}=\mathit{A_{0}}(\mathbf{I_{s}}\cdot&space;\mathbf{W_{0}}&space;&plus;&space;\mathbf{B_{0}})\\&space;\mathbf{H_{2}}=\mathit{A_{1}}(\mathbf{H_{1}}\cdot&space;\mathbf{W_{1}}&space;&plus;&space;\mathbf{B_{1}})\\&space;\cdots&space;\\&space;\mathbf{H_{m-2}}=\mathit{A_{m-3}}(\mathbf{H_{m-3}}\cdot&space;\mathbf{W_{m-3}}&space;&plus;&space;\mathbf{B_{m-3}})\\&space;\mathbf{O}=\mathit{A_{m-2}}(\mathbf{H_{m-2}}\cdot&space;\mathbf{W_{m-2}}&space;&plus;&space;\mathbf{B_{m-2}})\\&space;\mathbf{Cost}=\mathit{C}(\mathbf{O},\mathbf{R})&space;\\" target="_blank"><img src="http://latex.codecogs.com/gif.latex?\\&space;\mathbf{H_{1}}=\mathit{A_{0}}(\mathbf{I_{s}}\cdot&space;\mathbf{W_{0}}&space;&plus;&space;\mathbf{B_{0}})\\&space;\mathbf{H_{2}}=\mathit{A_{1}}(\mathbf{H_{1}}\cdot&space;\mathbf{W_{1}}&space;&plus;&space;\mathbf{B_{1}})\\&space;\cdots&space;\\&space;\mathbf{H_{m-2}}=\mathit{A_{m-3}}(\mathbf{H_{m-3}}\cdot&space;\mathbf{W_{m-3}}&space;&plus;&space;\mathbf{B_{m-3}})\\&space;\mathbf{O}=\mathit{A_{m-2}}(\mathbf{H_{m-2}}\cdot&space;\mathbf{W_{m-2}}&space;&plus;&space;\mathbf{B_{m-2}})\\&space;\mathbf{Cost}=\mathit{C}(\mathbf{O},\mathbf{R})&space;\\" title="\\ \mathbf{H_{1}}=\mathit{A_{0}}(\mathbf{I_{s}}\cdot \mathbf{W_{0}} + \mathbf{B_{0}})\\ \mathbf{H_{2}}=\mathit{A_{1}}(\mathbf{H_{1}}\cdot \mathbf{W_{1}} + \mathbf{B_{1}})\\ \cdots \\ \mathbf{H_{m-2}}=\mathit{A_{m-3}}(\mathbf{H_{m-3}}\cdot \mathbf{W_{m-3}} + \mathbf{B_{m-3}})\\ \mathbf{O}=\mathit{A_{m-2}}(\mathbf{H_{m-2}}\cdot \mathbf{W_{m-2}} + \mathbf{B_{m-2}})\\ \mathbf{Cost}=\mathit{C}(\mathbf{O},\mathbf{R}) \\" /></a>
    
* **反向传播**

    + **回归问题**
    
     最小二乘成本函数为：
     <a href="http://www.codecogs.com/eqnedit.php?latex=\mathbf{Cost}=\frac{1}{2s}(\mathbf{O}-\mathbf{R})^{2}" target="_blank"><img src="http://latex.codecogs.com/gif.latex?\mathbf{Cost}=\frac{1}{2s}(\mathbf{O}-\mathbf{R})^{2}" title="\mathbf{Cost}=\frac{1}{2s}(\mathbf{O}-\mathbf{R})^{2}" /></a>
    
    依据链式求导法则以及向量化，计算出成本函数对于每一个权重以及偏置的偏导数：
    
    <a href="http://www.codecogs.com/eqnedit.php?latex=\\&space;\frac{\partial&space;\mathbf{Cost}}{\partial&space;\mathbf{W_{m-2}}}=\frac{\partial&space;\mathbf{Cost}}{\partial&space;\mathbf{O}}\frac{\partial&space;\mathbf{O}}{\partial&space;\mathbf{W_{m-2}}}=\mathbf{H_{m-2}}^{T}&space;\cdot&space;{\color{Magenta}&space;(\frac{1}{s}(\mathbf{O}-\mathbf{R})\times&space;\mathrm{der}(\mathbf{O}))}\\&space;...........................................=\mathbf{H_{m-2}}^{T}&space;\cdot&space;\mathbf{D_{m-2}}=\mathbf{H_{m-2}}^{T}&space;\cdot&space;{\color{Magenta}&space;\frac{\partial&space;\mathbf{Cost}}{\partial&space;\mathbf{B_{m-2}}}}&space;\\" target="_blank"><img src="http://latex.codecogs.com/gif.latex?\\&space;\frac{\partial&space;\mathbf{Cost}}{\partial&space;\mathbf{W_{m-2}}}=\frac{\partial&space;\mathbf{Cost}}{\partial&space;\mathbf{O}}\frac{\partial&space;\mathbf{O}}{\partial&space;\mathbf{W_{m-2}}}=\mathbf{H_{m-2}}^{T}&space;\cdot&space;{\color{Magenta}&space;(\frac{1}{s}(\mathbf{O}-\mathbf{R})\times&space;\mathrm{der}(\mathbf{O}))}\\&space;...........................................=\mathbf{H_{m-2}}^{T}&space;\cdot&space;\mathbf{D_{m-2}}=\mathbf{H_{m-2}}^{T}&space;\cdot&space;{\color{Magenta}&space;\frac{\partial&space;\mathbf{Cost}}{\partial&space;\mathbf{B_{m-2}}}}&space;\\" title="\\ \frac{\partial \mathbf{Cost}}{\partial \mathbf{W_{m-2}}}=\frac{\partial \mathbf{Cost}}{\partial \mathbf{O}}\frac{\partial \mathbf{O}}{\partial \mathbf{W_{m-2}}}=\mathbf{H_{m-2}}^{T} \cdot {\color{Magenta} (\frac{1}{s}(\mathbf{O}-\mathbf{R})\times \mathrm{der}(\mathbf{O}))}\\ ...........................................=\mathbf{H_{m-2}}^{T} \cdot \mathbf{D_{m-2}}=\mathbf{H_{m-2}}^{T} \cdot {\color{Magenta} \frac{\partial \mathbf{Cost}}{\partial \mathbf{B_{m-2}}}} \\" /></a>
    
    <a href="http://www.codecogs.com/eqnedit.php?latex=\\&space;\frac{\partial&space;\mathbf{Cost}}{\partial&space;\mathbf{W_{m-3}}}=\frac{\partial&space;\mathbf{Cost}}{\partial&space;\mathbf{O}}\frac{\partial&space;\mathbf{O}}{\partial&space;\mathbf{H_{m-2}}}\frac{\partial&space;\mathbf{H_{m-2}}}{\partial&space;\mathbf{W_{m-3}}}=\mathbf{H_{m-3}}^{T}&space;\cdot&space;{\color{Magenta}&space;[(&space;\mathbf{D_{m-2}}&space;\cdot&space;\mathbf{W_{m-2}}^{T})\times&space;\mathrm{der}(\mathbf{H_{m-2}})]}\\&space;.......................................................=\mathbf{H_{m-3}}^{T}&space;\cdot&space;\mathbf{D_{m-3}}=\mathbf{H_{m-3}}^{T}&space;\cdot&space;{\color{Magenta}&space;\frac{\partial&space;\mathbf{Cost}}{\partial&space;\mathbf{B_{m-3}}}}&space;\\" target="_blank"><img src="http://latex.codecogs.com/gif.latex?\\&space;\frac{\partial&space;\mathbf{Cost}}{\partial&space;\mathbf{W_{m-3}}}=\frac{\partial&space;\mathbf{Cost}}{\partial&space;\mathbf{O}}\frac{\partial&space;\mathbf{O}}{\partial&space;\mathbf{H_{m-2}}}\frac{\partial&space;\mathbf{H_{m-2}}}{\partial&space;\mathbf{W_{m-3}}}=\mathbf{H_{m-3}}^{T}&space;\cdot&space;{\color{Magenta}&space;[(&space;\mathbf{D_{m-2}}&space;\cdot&space;\mathbf{W_{m-2}}^{T})\times&space;\mathrm{der}(\mathbf{H_{m-2}})]}\\&space;.......................................................=\mathbf{H_{m-3}}^{T}&space;\cdot&space;\mathbf{D_{m-3}}=\mathbf{H_{m-3}}^{T}&space;\cdot&space;{\color{Magenta}&space;\frac{\partial&space;\mathbf{Cost}}{\partial&space;\mathbf{B_{m-3}}}}&space;\\" title="\\ \frac{\partial \mathbf{Cost}}{\partial \mathbf{W_{m-3}}}=\frac{\partial \mathbf{Cost}}{\partial \mathbf{O}}\frac{\partial \mathbf{O}}{\partial \mathbf{H_{m-2}}}\frac{\partial \mathbf{H_{m-2}}}{\partial \mathbf{W_{m-3}}}=\mathbf{H_{m-3}}^{T} \cdot {\color{Magenta} [( \mathbf{D_{m-2}} \cdot \mathbf{W_{m-2}}^{T})\times \mathrm{der}(\mathbf{H_{m-2}})]}\\ .......................................................=\mathbf{H_{m-3}}^{T} \cdot \mathbf{D_{m-3}}=\mathbf{H_{m-3}}^{T} \cdot {\color{Magenta} \frac{\partial \mathbf{Cost}}{\partial \mathbf{B_{m-3}}}} \\" /></a>
    
    
    
     <a href="http://www.codecogs.com/eqnedit.php?latex=\\&space;\frac{\partial&space;\mathbf{Cost}}{\partial&space;\mathbf{W_{m-4}}}=\frac{\partial&space;\mathbf{Cost}}{\partial&space;\mathbf{O}}\frac{\partial&space;\mathbf{O}}{\partial&space;\mathbf{H_{m-2}}}\frac{\partial&space;\mathbf{H_{m-2}}}{\partial&space;\mathbf{H_{m-3}}}&space;\frac{\partial&space;\mathbf{H_{m-3}}}{\partial&space;\mathbf{W_{m-4}}}\\&space;\\&space;.............=\mathbf{H_{m-4}}^{T}&space;\cdot&space;{\color{Magenta}&space;[(&space;\mathbf{D_{m-3}}&space;\cdot&space;\mathbf{W_{m-3}}^{T})\times&space;\mathrm{der}(\mathbf{H_{m-3}})]}\\&space;\\&space;............=\mathbf{H_{m-4}}^{T}&space;\cdot&space;\mathbf{D_{m-4}}=\mathbf{H_{m-4}}^{T}&space;\cdot{\color{Magenta}&space;\frac{\partial&space;\mathbf{Cost}}{\partial&space;\mathbf{B_{m-4}}}}&space;\\&space;\vdots&space;\\&space;\vdots&space;\\&space;\\&space;\frac{\partial&space;\mathbf{Cost}}{\partial&space;\mathbf{W_{0}}}=\mathbf{H_{0}}^{T}&space;\cdot&space;{\color{Magenta}&space;[(&space;\mathbf{D_{1}}&space;\cdot&space;\mathbf{W_{1}}^{T})\times&space;\mathrm{der}(\mathbf{H_{1}})]}=\mathbf{H_{0}}^{T}\cdot&space;{\color{Magenta}&space;\frac{\partial&space;\mathbf{Cost}}{\partial&space;\mathbf{B_{0}}}}\\" target="_blank"><img src="http://latex.codecogs.com/gif.latex?\\&space;\frac{\partial&space;\mathbf{Cost}}{\partial&space;\mathbf{W_{m-4}}}=\frac{\partial&space;\mathbf{Cost}}{\partial&space;\mathbf{O}}\frac{\partial&space;\mathbf{O}}{\partial&space;\mathbf{H_{m-2}}}\frac{\partial&space;\mathbf{H_{m-2}}}{\partial&space;\mathbf{H_{m-3}}}&space;\frac{\partial&space;\mathbf{H_{m-3}}}{\partial&space;\mathbf{W_{m-4}}}\\&space;\\&space;.............=\mathbf{H_{m-4}}^{T}&space;\cdot&space;{\color{Magenta}&space;[(&space;\mathbf{D_{m-3}}&space;\cdot&space;\mathbf{W_{m-3}}^{T})\times&space;\mathrm{der}(\mathbf{H_{m-3}})]}\\&space;\\&space;............=\mathbf{H_{m-4}}^{T}&space;\cdot&space;\mathbf{D_{m-4}}=\mathbf{H_{m-4}}^{T}&space;\cdot{\color{Magenta}&space;\frac{\partial&space;\mathbf{Cost}}{\partial&space;\mathbf{B_{m-4}}}}&space;\\&space;\vdots&space;\\&space;\vdots&space;\\&space;\\&space;\frac{\partial&space;\mathbf{Cost}}{\partial&space;\mathbf{W_{0}}}=\mathbf{H_{0}}^{T}&space;\cdot&space;{\color{Magenta}&space;[(&space;\mathbf{D_{1}}&space;\cdot&space;\mathbf{W_{1}}^{T})\times&space;\mathrm{der}(\mathbf{H_{1}})]}=\mathbf{H_{0}}^{T}\cdot&space;{\color{Magenta}&space;\frac{\partial&space;\mathbf{Cost}}{\partial&space;\mathbf{B_{0}}}}\\" title="\\ \frac{\partial \mathbf{Cost}}{\partial \mathbf{W_{m-4}}}=\frac{\partial \mathbf{Cost}}{\partial \mathbf{O}}\frac{\partial \mathbf{O}}{\partial \mathbf{H_{m-2}}}\frac{\partial \mathbf{H_{m-2}}}{\partial \mathbf{H_{m-3}}} \frac{\partial \mathbf{H_{m-3}}}{\partial \mathbf{W_{m-4}}}\\ \\ .............=\mathbf{H_{m-4}}^{T} \cdot {\color{Magenta} [( \mathbf{D_{m-3}} \cdot \mathbf{W_{m-3}}^{T})\times \mathrm{der}(\mathbf{H_{m-3}})]}\\ \\ ............=\mathbf{H_{m-4}}^{T} \cdot \mathbf{D_{m-4}}=\mathbf{H_{m-4}}^{T} \cdot{\color{Magenta} \frac{\partial \mathbf{Cost}}{\partial \mathbf{B_{m-4}}}} \\ \vdots \\ \vdots \\ \\ \frac{\partial \mathbf{Cost}}{\partial \mathbf{W_{0}}}=\mathbf{H_{0}}^{T} \cdot {\color{Magenta} [( \mathbf{D_{1}} \cdot \mathbf{W_{1}}^{T})\times \mathrm{der}(\mathbf{H_{1}})]}=\mathbf{H_{0}}^{T}\cdot {\color{Magenta} \frac{\partial \mathbf{Cost}}{\partial \mathbf{B_{0}}}}\\" /></a>  
    
    <a href="http://www.codecogs.com/eqnedit.php?latex=\mathbf{H_{0}}=\mathbf{I_{s}}" target="_blank"><img src="http://latex.codecogs.com/gif.latex?\mathbf{H_{0}}=\mathbf{I_{s}}" title="\mathbf{H_{0}}=\mathbf{I_{s}}" /></a>，<a href="http://www.codecogs.com/eqnedit.php?latex=\mathbf{I_{s}}" target="_blank"><img src="http://latex.codecogs.com/gif.latex?\mathbf{I_{s}}" title="\mathbf{I_{s}}" /></a>为一次训练输入的s个样本组成的数据集。
    
    
    <a href="http://www.codecogs.com/eqnedit.php?latex=\mathrm{der(\mathbf{G})}" target="_blank"><img src="http://latex.codecogs.com/gif.latex?\mathrm{der(\mathbf{G})}" title="\mathrm{der(\mathbf{G})}" /></a>表示<a href="http://www.codecogs.com/eqnedit.php?latex=\mathbf{G}" target="_blank"><img src="http://latex.codecogs.com/gif.latex?\mathbf{G}" title="\mathbf{G}" /></a>的导数。
    
    
     + **分类问题**
     
     交叉熵成本函数为：
     <a href="http://www.codecogs.com/eqnedit.php?latex=\mathbf{Cost}&space;=&space;-\frac{1}{s}(\mathbf{R}\times&space;ln(\mathbf{O})&plus;(1-\mathbf{R})\times&space;ln(1-\mathbf{O}))" target="_blank"><img src="http://latex.codecogs.com/gif.latex?\mathbf{Cost}&space;=&space;-\frac{1}{s}(\mathbf{R}\times&space;ln(\mathbf{O})&plus;(1-\mathbf{R})\times&space;ln(1-\mathbf{O}))" title="\mathbf{Cost} = -\frac{1}{s}(\mathbf{R}\times ln(\mathbf{O})+(1-\mathbf{R})\times ln(1-\mathbf{O}))" /></a>
     
     计算梯度的方式和回归问题相似，就是将回归问题中的成本函数的导数
     
     <a href="http://www.codecogs.com/eqnedit.php?latex={\color{Orange}&space;\frac{1}{s}(\mathbf{O}-\mathbf{R})}" target="_blank"><img src="http://latex.codecogs.com/gif.latex?{\color{Orange}&space;\frac{1}{s}(\mathbf{O}-\mathbf{R})}" title="{\color{Orange} \frac{1}{s}(\mathbf{O}-\mathbf{R})}" /></a>
     
     变为交叉熵成本函数的导数
     
     <a href="http://www.codecogs.com/eqnedit.php?latex={\color{Orange}&space;-\frac{1}{s}\frac{\mathbf{R}-\mathbf{O}}{\mathbf{O}\times&space;(1-\mathbf{O})}}" target="_blank"><img src="http://latex.codecogs.com/gif.latex?{\color{Orange}&space;-\frac{1}{s}\frac{\mathbf{R}-\mathbf{O}}{\mathbf{O}\times&space;(1-\mathbf{O})}}" title="{\color{Orange} -\frac{1}{s}\frac{\mathbf{R}-\mathbf{O}}{\mathbf{O}\times (1-\mathbf{O})}}" /></a>
     
     即可。
    
    
* **梯度下降**

   <a href="http://www.codecogs.com/eqnedit.php?latex=\\&space;\mathbf{W_{q}}=\mathbf{W_{q}}&space;-\eta&space;\times&space;\frac{\partial&space;\mathbf{Cost}}{\partial&space;\mathbf{W_{q}}}\\&space;\\&space;\mathbf{B_{q}}=\mathbf{B_{q}}&space;-\eta&space;\times&space;\frac{\partial&space;\mathbf{Cost}}{\partial&space;\mathbf{B_{q}}}\\&space;\\&space;\mathbf{q}=\mathbf{0,1,2}\cdots\mathbf{m-2}" target="_blank"><img src="http://latex.codecogs.com/gif.latex?\\&space;\mathbf{W_{q}}=\mathbf{W_{q}}&space;-\eta&space;\times&space;\frac{\partial&space;\mathbf{Cost}}{\partial&space;\mathbf{W_{q}}}\\&space;\\&space;\mathbf{B_{q}}=\mathbf{B_{q}}&space;-\eta&space;\times&space;\frac{\partial&space;\mathbf{Cost}}{\partial&space;\mathbf{B_{q}}}\\&space;\\&space;\mathbf{q}=\mathbf{0,1,2}\cdots\mathbf{m-2}" title="\\ \mathbf{W_{q}}=\mathbf{W_{q}} -\eta \times \frac{\partial \mathbf{Cost}}{\partial \mathbf{W_{q}}}\\ \\ \mathbf{B_{q}}=\mathbf{B_{q}} -\eta \times \frac{\partial \mathbf{Cost}}{\partial \mathbf{B_{q}}}\\ \\ \mathbf{q}=\mathbf{0,1,2}\cdots\mathbf{m-2}" /></a>
