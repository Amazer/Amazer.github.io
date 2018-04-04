
---
title: 3D向量叉乘的理解和记忆
categories: 引擎
tags: [3D,数学]
copyright: true
---


## 1.2D向量的叉乘
	
	
-  **U** × **V** = **U**<sub>x</sub> * **V**<sub>y</sub> - **U**<sub>y</sub> * **V**<sub>x<sub>


## 2.3D向量的叉乘

- 3D笛卡尔坐标系的三个坐标轴两两垂直，并且有着如下的关系：

<!--more-->
>    x轴向量 × y轴向量=z轴向量
	y轴向量 × z轴向量=x轴向量
	z轴向量 × x轴向量=y轴向量

>	即：
	x轴向量=y轴向量 × z轴向量
	y轴向量=z轴向量 × x轴向量
	z轴向量=x轴向量 × y轴向量
	(xyz的顺不能变)

如果用i,j,k代表x,y,z轴的单位向量有：

>	i = j × k
	j = k × i
	k = i × j

 3D向量的叉乘结果，是一个3D向量，也就是说，在i j k三个方向的向量的组合。
 那么，分别获得三个方向上的值就可以了。
		
设 **N** = **U** × **V**

x轴方向的量，由yz两个轴向量叉乘得到：
> **i = j × k**
	 **N**<sub>x</sub> = **U**<sub>yz</sub> × **V**<sub>yz</sub>
	 &nbsp; &nbsp;&nbsp;  = **U**<sub>y</sub> *  **V**<sub>z</sub> - **U**<sub>z</sub> * **V**<sub>y</sub>


y轴方向的量，由zx两个轴向量叉乘得到：
> **j = k × i**
	 **N**<sub>y</sub> = **U**<sub>zx</sub> × **V**<sub>zx</sub>
	 &nbsp; &nbsp;&nbsp;  = **U**<sub>z</sub> *  **V**<sub>x</sub> - **U**<sub>x</sub> * **V**<sub>z</sub>
		
z轴方向的量，由xy两个轴向量叉乘得到：
>  **k = i × j**
     **N**<sub>z</sub> = **U**<sub>xy</sub> × **V**<sub>xy</sub>
     &nbsp; &nbsp;&nbsp;  = **U**<sub>x</sub> *  **V**<sub>y</sub> - **U**<sub>y</sub> * **V**<sub>x</sub>

最终，得到叉乘向量的结果是：
		**U** x **V** = <**U**<sub>y</sub> *  **V**<sub>z</sub> - **U**<sub>z</sub> * **V**<sub>y</sub>  , **U**<sub>z</sub> *  **V**<sub>x</sub> - **U**<sub>x</sub> * **V**<sub>z</sub> , **U**<sub>x</sub> *  **V**<sub>y</sub> - **U**<sub>y</sub> * **V**<sub>x</sub>>

