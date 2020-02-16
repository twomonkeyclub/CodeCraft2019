2019华为软件精英挑战赛
===============
成渝赛区：初赛第4，复赛第19

<div align=center><img width="679" height="34" src="https://github.com/twomonkeyclub/CodeCraft2019/blob/master/result/chusai.jpg"/></div>

<div align=center><img width="679" height="34" src="https://github.com/twomonkeyclub/CodeCraft2019/blob/master/result/fusai.jpg"/></div>


初赛通过规划车辆路径和出发时间，使得系统调度时间最短，复赛在初赛的基础上添加优先车辆和预置车辆，我们通过自定义路权函数，伪动态更新路权和发车策略来分图调整参数，达到最优时间。


0准备，如何参赛？
------------
更多资料，请关注公众号 **“两猿圈”**.
> * **过来人告诉你：0准备，如何参加华为软挑！！！**
> * **带你丰富互联网相关项目经验，轻松应对校招！！！**
> * **项目模块详细讲解，在公众号内持续更新！！！**

<div align=center><img src="https://github.com/twomonkeyclub/TinyWebServer/blob/master/root/test1.jpg" height="258"/> </div>

初赛思路
------------
* 发车策略
	* 道路顺序调整
	```C++
	1. 取cross文件中第一个路口为标准，通过DFS将文件中其他道路的顺序调整为和第一个路口一致
	```
	
	* 车辆分组
	```C++
	1. 道路顺序调整后，人为规定cross文件中每条路之间的关系为上、下、左、右
	2. 将所有车辆按行驶路线分为四组，右下，左上，右上，左下，然后右下和左上为一组，右上和左下为一组发车
	3. 通过dijkstra算法求出所有车辆的最短路径，根据每辆车走过的道路之间的关系（上下左右的数量），判断具体为哪一组
	4. 调整两组车辆的发车间隔
	```
	
	* 车辆实际出发时间
	```C++
	1. 将每组车辆按车辆行驶速度排序，速度大的车辆优先发车，按速度依次发车，时间间隔依次加50时间片
	```	

* 路权函数
	* 自定义路权函数
	```C++
	1. 创建有向图，其中路权函数考虑了长度length、车道数lines、道路限速speed、单双向道twoWay，道路使用情况numOfUSe
	2. 长度length、车道数lines、道路限速speed路权元素需要归一化，其中长度元素占比相较于其他要高
	3. 测试发现，车辆死锁极易发生在单向道及其附近，其他元素相同的情况下，将单向道的权重设置比双向道高，优先走双向道
	4. 当道路使用次数较多时，则默认道路阻抗较大，通过调整参数尽量少选择该道路
	```
		
	* 伪动态更新路权函数
	```C++
	1. 目的：使相同起止点的车辆选择尽量选择不同的道路，另外，调整道路阻抗
	2. 循环所有路口，以路口的最大发车数目为限制条件，每次路口只发一辆车，更新道路使用次数，进而更新路权函数
	```
 
复赛思路
------------
复赛由于添加了预置车辆和优先车辆，并更新了评分公式，将优先车辆的调度时间单独计算。我们只改变了发车策略，优先调度预设车辆和优先车辆。另外，复赛可以重新规划不超过10%的预设车辆。

* 发车策略
	* 车辆分组
	```C++
	1. 人为规定cross文件中每条路之间的关系为上、下、左、右
	2. 将非预设非优先级车辆按行驶路线分为四组，右下，左上，右上，左下，然后右下和左上为一组，右上和左下为一组发车
	3. 将非预设优先级车辆同样分组
	3. 通过dijkstra算法求出所有车辆的最短路径，根据每辆车走过的道路之间的关系（上下左右的数量），判断具体为哪一组
	4. 调整非预设非优先级两组车辆的发车间隔，经测试，优先级可一起发车（仅对复赛、初赛数据）
	```
	
	* 车辆实际出发时间
	```C++
	1. 将每组车辆按车辆行驶速度排序，速度大的车辆优先发车，按速度依次发车，时间间隔依次加50时间片
	2. 先发优先级车辆，再发非预设非优先级车辆
	```	
* 选择策略 
	```C++
	1. 优先选择预设车辆中的优先级车辆
	2. 若预设中优先级车辆较少，选择预设非优先车辆中预设规划路径长的车辆
	```

