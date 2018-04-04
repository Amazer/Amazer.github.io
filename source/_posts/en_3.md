
---
title: '[引擎]之三:在Unity3D中自定义CubeMesh'
categories: '引擎'
tags: '引擎'
toc: true
copyright: true
---


之前的文章[mesh在Unity中的简单使用](http://blog.csdn.net/yanchezuo/article/details/78969457)说过，会创建CubeMesh。嗯，来了。


* <a href="#t1">1.顶点的生成</a>
* <a href="#t2">2.生成三角面</a>
* <a href="#t3">3.生成uv坐标</a>
* <a href="#t4">4.看看效果</a>
* <a href="#t5">5.理论和对比</a>
* <a href="#t6">6.修改</a>
* <a href="#t7">7.总结</a>
* <a href="#t8">8.预告</a>

<!--more-->

之前已经知道如何创建一个简单的片mesh,那么同理，CubeMesh的创建，同样是从顶点、三角面、uv坐标三个方面生成。
##### (1) <a name="t1">顶点的生成</a>

一个cube只需要8个顶点就可以表示了。以中心点为（0,0,0），边长为1，按前面顶点index为0,1,2,3，后面顶点index为4,5,6,7顺时针顺序添加顶点，顺序如下图所示：
![顶点顺序](http://img.blog.csdn.net/20180104193620015?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveWFuY2hlenVv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

``` c#
Mesh m = new Mesh();
// 前面
_verticesList.Add(new Vector3(0.5f, -0.5f, -0.5f));	// index 0
_verticesList.Add(new Vector3(-0.5f, -0.5f, -0.5f));	// index 1
_verticesList.Add(new Vector3(-0.5f, 0.5f, -0.5f));	// index 2
 _verticesList.Add(new Vector3(0.5f, 0.5f, -0.5f));	// index 3
 
// 后面
_verticesList.Add(new Vector3(0.5f, -0.5f, 0.5f));		// index 4
_verticesList.Add(new Vector3(-0.5f, -0.5f, 0.5f));		// index 5
_verticesList.Add(new Vector3(-0.5f, 0.5f, 0.5f));		// index 6
_verticesList.Add(new Vector3(0.5f, 0.5f, 0.5f));	// index 7
// mesh赋值
m.SetVertices(_verticesList);

```

##### (2) <a name="t2">生成三角面</a>
和前面一样，按照顺时针方向，顶点index尽量由小到大排列，添加：
``` c#
            // 前面
            _triList.Add(0);
            _triList.Add(1);
            _triList.Add(2);


            _triList.Add(0);
            _triList.Add(2);
            _triList.Add(3);


            // 后面
            _triList.Add(4);
            _triList.Add(7);
            _triList.Add(5);

            _triList.Add(5);
            _triList.Add(7);
            _triList.Add(6);


            // 左面
            _triList.Add(1);
            _triList.Add(5);
            _triList.Add(6);

            _triList.Add(1);
            _triList.Add(6);
            _triList.Add(2);


            // 右面
            _triList.Add(0);
            _triList.Add(3);
            _triList.Add(4);

            _triList.Add(3);
            _triList.Add(7);
            _triList.Add(4);

            // 上面
            _triList.Add(2);
            _triList.Add(6);
            _triList.Add(3);

            _triList.Add(3);
            _triList.Add(6);
            _triList.Add(7);

            // 下面
            _triList.Add(1);
            _triList.Add(4);
            _triList.Add(5);

            _triList.Add(0);
            _triList.Add(4);
            _triList.Add(1);
            
			// 赋值给mesh
            m.SetTriangles(_triList,0);

```
##### (3) <a name="t3">生成uv坐标</a>
按照之前的想法，每个顶点对应一个uv坐标，所以，也是只需要8个uv坐标就可以了。先让uv坐标为0或1，看看效果：

``` c#
            _uvList.Add(new Vector2(1,0));          
            _uvList.Add(new Vector2(0,0));
            _uvList.Add(new Vector2(0,1));
            _uvList.Add(new Vector2(1,1));

            _uvList.Add(new Vector2(1,0));
            _uvList.Add(new Vector2(0,0));
            _uvList.Add(new Vector2(0,1));
            _uvList.Add(new Vector2(1,1));
		
			// 赋值给mesh
            m.SetUVs(0,_uvList);

```
##### (4) <a name="t4">看看效果</a>

完整代码如下，挂到GameObejct上就可以了：

```c#
using UnityEngine;
using System.Collections.Generic;
namespace YanCheZuo
{
    [RequireComponent(typeof(MeshFilter))]
    public class TestCubeMesh : MonoBehaviour
    {
        private MeshFilter _meshFilter;
        private Mesh _mesh;

        private List<Vector3> _verticesList = new List<Vector3>();
        private List<Vector2> _uvList = new List<Vector2>();
        private List<int> _triList = new List<int>();

        private void Start()
        {
            _meshFilter = this.GetComponent<MeshFilter>();
            _mesh = _meshFilter.mesh;

            RebuildMesh();

        }
        private void RebuildMesh()
        {
            _mesh = GetCubeMesh();
            _meshFilter.mesh = _mesh;
        }

        private Mesh GetCubeMesh()
        {
            Mesh m = new Mesh();

            _verticesList.Add(new Vector3(0.5f, -0.5f, -0.5f));
            _verticesList.Add(new Vector3(-0.5f, -0.5f, -0.5f));
            _verticesList.Add(new Vector3(-0.5f, 0.5f, -0.5f));
            _verticesList.Add(new Vector3(0.5f, 0.5f, -0.5f));

            _verticesList.Add(new Vector3(0.5f, -0.5f, 0.5f));
            _verticesList.Add(new Vector3(-0.5f, -0.5f, 0.5f));
            _verticesList.Add(new Vector3(-0.5f, 0.5f, 0.5f));
            _verticesList.Add(new Vector3(0.5f, 0.5f, 0.5f));

            m.SetVertices(_verticesList);

            _uvList.Add(new Vector2(1,0));          
            _uvList.Add(new Vector2(0,0));
            _uvList.Add(new Vector2(0,1));
            _uvList.Add(new Vector2(1,1));

            _uvList.Add(new Vector2(1,0));
            _uvList.Add(new Vector2(0,0));
            _uvList.Add(new Vector2(0,1));
            _uvList.Add(new Vector2(1,1));

            m.SetUVs(0,_uvList);


            // 前面
            _triList.Add(0);
            _triList.Add(1);
            _triList.Add(2);


            _triList.Add(0);
            _triList.Add(2);
            _triList.Add(3);


            // 后面
            _triList.Add(4);
            _triList.Add(7);
            _triList.Add(5);

            _triList.Add(5);
            _triList.Add(7);
            _triList.Add(6);


            // 左面
            _triList.Add(1);
            _triList.Add(5);
            _triList.Add(6);

            _triList.Add(1);
            _triList.Add(6);
            _triList.Add(2);


            // 右面
            _triList.Add(0);
            _triList.Add(3);
            _triList.Add(4);

            _triList.Add(3);
            _triList.Add(7);
            _triList.Add(4);

            // 上面
            _triList.Add(2);
            _triList.Add(6);
            _triList.Add(3);

            _triList.Add(3);
            _triList.Add(6);
            _triList.Add(7);

            // 下面
            _triList.Add(1);
            _triList.Add(4);
            _triList.Add(5);

            _triList.Add(0);
            _triList.Add(4);
            _triList.Add(1);

            m.SetTriangles(_triList,0);

            return m;

        }
    }
}

```


mesh生成ok,运行后的效果如下图所示：
![运行结果](http://img.blog.csdn.net/20180104195152317?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveWFuY2hlenVv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

好像不对！！前面和后面的uv是对的，其他的几个面uv并不是我想想中的那样！

##### (5) <a name="t5">理论和对比</a>
在《3D游戏编程大师技巧》中，有几种类型的模型文件解析，基本的结构是，一个完整的顶点列表，在三角面的定义中，指定需要的顶点index和uv坐标。例如：
```
vertices:
v0,v1,v2,v3,v4,...,vn
faces:			// 每行代表一个三角面的定义
<0,uv0>	<1,uv1>	<2,uv2>		
<0,uv3>	<2,uv4>	<4,uv5>
....
```
Unity中，没有面的定义。并且需要uv的数量和顶点的数量一样。即一个顶点对应一个uv坐标。
查看Unity原生的CubeMesh，效果是这样的：
![Unity原生的CubeMesh](http://img.blog.csdn.net/20180104200223065?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveWFuY2hlenVv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

查看其顶点信息，如下图所示：
![Unity原生的CubeMesh信息](http://img.blog.csdn.net/20180104200403257?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveWFuY2hlenVv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

可以看出，同样的一个位置的顶点由三个。
**因为6个面，每个面的uv都不相同，所以，其实需要8*3=24个顶点来表示一个CubeMesh**。
那么，只能用24个顶点和24个uv来表示了。

##### (6) <a name="t6">修改</a>
比较方便的一点是，利用之前的工具[MeshViewer](http://blog.csdn.net/yanchezuo/article/details/78969956),可以很方便的找到顶点的对应顺序。
按照 前面、后面、上面、下面、左面、右面，每个面顶点从右下角开始顺时针的顺序构建mesh,顶点顺序分别为：

- 前面 0,1,2,3
- 后面 4,5,6,7
- 上面 8,9,10,11
- 下面 12,13,14,15
- 左面 16,17,18,19
- 右面 20,21,22,23
有了查看工具的帮助，耐心构建24个顶点，12个三角面，并按照顺序给出24uv坐标，完整代码如下：
``` c#
using UnityEngine;
using System.Collections.Generic;
namespace YanCheZuo
{
    [RequireComponent(typeof(MeshFilter))]
    public class TestCubeMesh2 : MonoBehaviour
    {
        private MeshFilter _meshFilter;
        private Mesh _mesh;

        private List<Vector3> _verticesList = new List<Vector3>();
        private List<Vector2> _uvList = new List<Vector2>();
        private List<int> _triList = new List<int>();

        private void Start()
        {
            _meshFilter = this.GetComponent<MeshFilter>();
            _mesh = _meshFilter.mesh;

            RebuildMesh();

        }
        private void RebuildMesh()
        {
            _mesh = GetCubeMesh();
            _meshFilter.mesh = _mesh;
        }

        private Mesh GetCubeMesh()
        {
            Mesh m = new Mesh();
            // 本来，只需要8个顶点就可以了。
            // 但是，由于6个四边形面的纹理坐标，不能共用顶点的纹理坐标
            // 所以，需要8x3个顶点（同样的顶点被3个四边形面共用)
            

            // 前面 0,1,2,3
            _verticesList.Add(new Vector3(0.5f, -0.5f, -0.5f));
            _verticesList.Add(new Vector3(-0.5f, -0.5f, -0.5f));
            _verticesList.Add(new Vector3(-0.5f, 0.5f, -0.5f));
            _verticesList.Add(new Vector3(0.5f, 0.5f, -0.5f));

            // 后面 4,5,6,7
            _verticesList.Add(new Vector3(0.5f, -0.5f, 0.5f));
            _verticesList.Add(new Vector3(-0.5f, -0.5f, 0.5f));
            _verticesList.Add(new Vector3(-0.5f, 0.5f, 0.5f));
            _verticesList.Add(new Vector3(0.5f, 0.5f, 0.5f));

            // 上面 8,9,10,11
            _verticesList.Add(new Vector3(0.5f, 0.5f, -0.5f));
            _verticesList.Add(new Vector3(-0.5f, 0.5f, -0.5f));
            _verticesList.Add(new Vector3(-0.5f, 0.5f, 0.5f));
            _verticesList.Add(new Vector3(0.5f, 0.5f, 0.5f));

            // 下面 12,13,14,15
            _verticesList.Add(new Vector3(0.5f, -0.5f, -0.5f));
            _verticesList.Add(new Vector3(-0.5f,-0.5f, -0.5f));
            _verticesList.Add(new Vector3(-0.5f, -0.5f, 0.5f));
            _verticesList.Add(new Vector3(0.5f, -0.5f, 0.5f));

            // 左面 16,17,18,19
            _verticesList.Add(new Vector3(-0.5f, -0.5f, -0.5f));
            _verticesList.Add(new Vector3(-0.5f,-0.5f, 0.5f));
            _verticesList.Add(new Vector3(-0.5f, 0.5f, 0.5f));
            _verticesList.Add(new Vector3(-0.5f, 0.5f, -0.5f));

            // 右面 20,21,22,23
            _verticesList.Add(new Vector3(0.5f, -0.5f, -0.5f));
            _verticesList.Add(new Vector3(0.5f,-0.5f, 0.5f));
            _verticesList.Add(new Vector3(0.5f, 0.5f, 0.5f));
            _verticesList.Add(new Vector3(0.5f, 0.5f, -0.5f));


            m.SetVertices(_verticesList);

            // 前面
            _uvList.Add(new Vector2(1,0));          
            _uvList.Add(new Vector2(0,0));
            _uvList.Add(new Vector2(0,1));
            _uvList.Add(new Vector2(1,1));

            // 后面
            _uvList.Add(new Vector2(0,0));
            _uvList.Add(new Vector2(1,0));          
            _uvList.Add(new Vector2(1,1));
            _uvList.Add(new Vector2(0,1));

            // 上面
            _uvList.Add(new Vector2(1,0));          
            _uvList.Add(new Vector2(0,0));
            _uvList.Add(new Vector2(0,1));
            _uvList.Add(new Vector2(1,1));

            // 下面

            _uvList.Add(new Vector2(0,0));
            _uvList.Add(new Vector2(1,0));
            _uvList.Add(new Vector2(1,1));
            _uvList.Add(new Vector2(0,1));

            // 左面
            _uvList.Add(new Vector2(1,0));
            _uvList.Add(new Vector2(0,0));
            _uvList.Add(new Vector2(0,1));
            _uvList.Add(new Vector2(1,1));

            // 右面
            _uvList.Add(new Vector2(0,0));
            _uvList.Add(new Vector2(1,0));
            _uvList.Add(new Vector2(1,1));
            _uvList.Add(new Vector2(0,1));

            m.SetUVs(0,_uvList);

            // 前面
            _triList.Add(0);
            _triList.Add(1);
            _triList.Add(2);


            _triList.Add(0);
            _triList.Add(2);
            _triList.Add(3);


            // 后面
            _triList.Add(4);
            _triList.Add(7);
            _triList.Add(5);

            _triList.Add(5);
            _triList.Add(7);
            _triList.Add(6);

            // 上面
            _triList.Add(8);
            _triList.Add(9);
            _triList.Add(10);

            _triList.Add(8);
            _triList.Add(10);
            _triList.Add(11);

            // 下面
            _triList.Add(14);
            _triList.Add(12);
            _triList.Add(15);

            _triList.Add(12);
            _triList.Add(14);
            _triList.Add(16);

            // 左面
            _triList.Add(16);
            _triList.Add(17);
            _triList.Add(18);

            _triList.Add(16);
            _triList.Add(18);
            _triList.Add(19);


            // 右面
            _triList.Add(20);
            _triList.Add(23);
            _triList.Add(21);

            _triList.Add(21);
            _triList.Add(23);
            _triList.Add(22);


            m.SetTriangles(_triList,0);

            return m;

        }
    }
}

```
运行结果如下图所示：
![CubeMesh2运行结果](http://img.blog.csdn.net/20180104201944583?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveWFuY2hlenVv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

结果正确，nice!
##### (7) <a name="t7">总结</a>
1. 共用顶点的面，如果uv坐标不能共用的话，Unity中需要添加重复的顶点。
2. 工欲善其事必先利其器。有了查看Mesh信息的工具，事半功倍。不容易出错，出了错也比较容易找到问题所在。
##### (8) <a name="t8">预告</a>
接下来，将会给自定义的mesh贴上正确的mineCraft元素的贴图。
期间，会修改查看Mesh信息的工具，显示我们需要的信息。
