
---
title: "[引擎]之一：mesh在Unity中的简单使用"
categories: [引擎]
tags: ['引擎']
copyright: true
---


* 读过 《3D游戏编程大师技巧》之后在Unity中的试验

* 理论中，Mesh（网格）的基本组成为顶点，由顶点组成的面，一般情况下，都指定的是三角面。有了顶点之后，三角面的由顶点index顺序指定就可以了。
* 此外，顶点color,uv坐标等信息，也可以存储下来。
* 在Unity中，使用的是**左手坐标系**，所以顶点**顺时针**方向为三角形的正面。

<!--more-->

现在，我有一张贴图，如图所示：

![Unity中的贴图](http://img.blog.csdn.net/20180104121421458?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveWFuY2hlenVv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

哈哈，显然这是一张MineCraft的纹理贴图。
我的目标是，在mesh中只显示贴图中泥土的那块。
因为是第一次手动创建mesh，所以，第一步，创建一个只有两个三角面的平面，并且赋上uv，让贴图泥土的块显示出来。如下图所示，显示红框框中的部分：
![贴图选中部分](http://img.blog.csdn.net/20180104121840989?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveWFuY2hlenVv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

###### 1.  顶点列表，这里，在xz平面中定义平面，如图所示：
![自定义平面](http://img.blog.csdn.net/20180104122741606?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveWFuY2hlenVv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

依次按照index为0,1,2,3的顺序加入到顶点列表中即可：

```  c#
// 定义顶点列表
public List<Vector3> _verticesList=new List<Vector3>();	
```

``` c#
// 赋值
_verticesList.Add(new Vector3(0, 0, 0)); 
_verticesList.Add(new Vector3(1, 0, 0));
_verticesList.Add(new Vector3(0, 0, 1));
_verticesList.Add(new Vector3(1, 0, 1));
```

``` c#
// 赋值给mesh
_mesh.SetVertices(_verticesList);
```



###### 2. 三角面
按照**顺时针**顺添加三角面。3个index的排列顺序有多种，一般建议，按照**由小到大**的顺序添加：
``` c#
// 定义三角面列表
public List<int> _triList = new List<int>();
```

``` c#
// 第一个三角面，index顺序为 0,2,1
_triList.Add(0);
_triList.Add(2);
_triList.Add(1);

// 第二个三角面，index顺序为 1,2,3
_triList.Add(1);
_triList.Add(2);
_triList.Add(3);
```
``` c#
// 赋值给mesh。Unity中，可以有submesh。0表示主mesh
_mesh.SetTriangles(_triList, 0);
```
###### 3. uv坐标
uv坐标系如下图所示：
![uv坐标系](http://img.blog.csdn.net/20180104130633219?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveWFuY2hlenVv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

并且，uv坐标的**范围是归一化**的，即**[0,1]**。
uv坐标如下图所示：
![这里写图片描述](http://img.blog.csdn.net/20180104131204351?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveWFuY2hlenVv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

所以，给对应的顶点index设置uv坐标就可以了：
``` c#
// 定义uv坐标list
public List<Vector2> _uvList=new List<Vector2>();

```
``` c#
// 赋值
_uvList.Add(new Vector2(0f, 0.75f));
_uvList.Add(new Vector2(0.25f, 0.75f));
_uvList.Add(new Vector2(0f, 1f));
_uvList.Add(new Vector2(0.25f, 1f));


```
``` c#
// 赋值给mesh。Unity中，可以有多套uv。这里赋值给第一套。
_mesh.SetUVs(0, _uvList);
```




最终效果，在scene中表现为：
![最终效果](http://img.blog.csdn.net/20180104132001692?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveWFuY2hlenVv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

下面是完整代码。使用方法是，把下面的脚本挂到一个有MeshFilter的GameObject上：
``` c#
using UnityEngine;
using System.Collections.Generic;
namespace YanCheZuo
{
    /// <summary>
    /// 创建了一个平面，1x1的平面，并贴图左上角的1/4
    /// </summary>
    [RequireComponent(typeof(MeshFilter))]
    public class TestMesh : MonoBehaviour
    {
        private Mesh _mesh;
        private MeshFilter _meshFilter;

        public List<Vector3> _verticesList=new List<Vector3>();
        public List<Vector2> _uvList=new List<Vector2>();
        public List<Color> _colorList=new List<Color>();
        public List<int> _triList = new List<int>();
        private void Start()
        {
            _meshFilter = this.GetComponent<MeshFilter>();
            _mesh = _meshFilter.mesh;
            RebuildMesh();
        }

        private void RebuildMesh()
        {
            _mesh = new Mesh();
            _mesh.name = "yanchezuo_mesh";
            _verticesList.Add(new Vector3(0, 0, 0));
            _verticesList.Add(new Vector3(1, 0, 0));
            _verticesList.Add(new Vector3(0, 0, 1));
            _verticesList.Add(new Vector3(1, 0, 1));

            _mesh.SetVertices(_verticesList);


            float startX = 0f;
            float endX=0.25f;

            float startY = 0.75f;
            float endY = 1f;

            float width = 0.25f;
            int index = 0;

            _uvList.Add(new Vector2(startX+width*index, startY));
            _uvList.Add(new Vector2(endX+width*index, startY));
            _uvList.Add(new Vector2(startX+width*index, endY));
            _uvList.Add(new Vector2(endX+width*index, endY));
            _mesh.SetUVs(0, _uvList);

            // 顺时针三角形顺序
            _triList.Add(0);
            _triList.Add(2);
            _triList.Add(1);

            _triList.Add(1);
            _triList.Add(2);
            _triList.Add(3);

            _mesh.SetTriangles(_triList, 0);

            _meshFilter.mesh = _mesh;

        }
    }
}

```

* 总结
mesh的基本组成和用法为，顶点列表，三角面列表，顶点的uv坐标列表。
* 下一篇文章预告：
下一篇会写一个cubeMesh。处理方式如果按着今天的做法，并得不到想要的效果。所以，会探究一下。
