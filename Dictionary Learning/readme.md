# Dictionary Learning（字典学习）
字典学习的数学描述如下，

$$\min_{\mathbf{D},\mathbf{X}}\left \|\mathbf{Y}-\mathbf{D}\mathbf{X}\right \|^2_F,\ s.t. \forall i,\left \|x_i\right \|_0\leq T_0, $$

或者，也可以描述为如下，

$$\min\sum_{i}\left \|x_i\right \|_0,\ s.t.\min_{\mathbf{D},\mathbf{X}}\left \|\mathbf{Y}-\mathbf{D}\mathbf{X}\right \|^2_F\leq T_1, $$

注意到，$\left \| x_i\right \| _0$为零阶范数，但在求解过程中为了方便常常用一阶范数代替。

用拉格朗日乘子法可以将上述约束问题转化为如下无约束问题，

$$\min_{\mathbf{D},\mathbf{X}}\left \|\mathbf{Y}-\mathbf{D}\mathbf{X}\right \|^2_F+\lambda\left \|x_i\right \|_1,$$

注意到，这里有两个需要优化的变量$\mathbf{X}$和$\mathbf{D}$，可以交替的固定一个变量优化另一个变量。

假设$\mathbf{X}$已知，记$\mathbf{d}_k$为字典矩阵$\mathbf{D}$的第$k$列向量，$\mathbf{x}^k_T$为查询矩阵$\mathbf{X}$的第$k$行向量，那么有如下推导，

$$\left \|\mathbf{Y}-\mathbf{D}\mathbf{X}\right \|^2_F=\left \|\mathbf{Y}-\sum_{j=1}^{K}\mathbf{d}_j\mathbf{x}_T^j\right \|^2_F=\left \|\left (\mathbf{Y}-\sum_{j\neq 1}\mathbf{d}_j\mathbf{x}_T^j \right )-\mathbf{d}_k\mathbf{x}_T^k\right \|^2_F=\left \| \mathbf{E_k}-\mathbf{d}_k\mathbf{x}_T^k\right \|^2_F, $$

其中，$\mathbf{E_k}=\mathbf{Y}-\sum_{j\neq 1}\mathbf{d}_j\mathbf{x}_T^j$，因此现在的优化目标为，

$$\min_{\mathbf{d}_k,\mathbf{x}_T^k}\left \| \mathbf{E_k}-\mathbf{d}_k\mathbf{x}_T^k\right \|^2_F,$$

注意到，这里在优化求解前应该做进一步的过滤，目的是把$\mathbf{x}_T^k$中已经为$0$的对应位置都过滤掉，而后再对非$0$的位置求最优化问题，因此，将上述最优化问题过滤成如下形式，

$$\min_{\mathbf{d}_k,\mathbf{x}_T^{'k}}\left \| \mathbf{E^{'}_k}-\mathbf{d}_k\mathbf{x}_T^{'k}\right \|^2_F,$$

优化上述问题，可以将$\mathbf{E_k^{'}}$做奇异值分解（SVD），记最大的奇异值为$\sigma_{max}$，最大奇异值对应的左奇异矩阵$\mathbf{U}$的列向量为$\mathbf{u}_{max}$，对应的右奇异矩阵$\mathbf{V}$的行向量为$\mathbf{v}_{max}$，则令$\mathbf{d}_k=\mathbf{u}_{max}$，$\mathbf{x}_T^{'k}=\sigma_{max}\mathbf{v}_{max}$，再更新到原来的$\mathbf{x}_T^k$。（如果求得的奇异值矩阵的奇异值是从大到小排列，那么上述$max=1$，可以替换表示为$\mathbf{x}_T^{'k}=\Sigma\left (1,1 \right )V(\cdot,1)^T$）。

K-SVD算法
* Step 1：初始化。可以从$\mathbf{Y}$中随机选取$K$个列向量或者取$\mathbf{Y}$的左奇异矩阵的前$K$个列向量初始化$\mathbf{D}^{(0)}$
* Step 2：稀疏编码。利用更上一步的字典矩阵$\mathbf{D}^{(j)}$来获得新的$\mathbf{X}^{(j)}$
* Step 3：计算精度，若精度达到要求，转Step 5，否则转Step 4
* Step 4：逐列更新字典矩阵$\mathbf{D}^{(j)}$（如2.0节所讲）
* Step 5：学习完成