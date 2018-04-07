---
title: '[引擎]之四：unity中查看简单mesh顶点顺序的小工具——修改' 
toc: true
copyright: true
date: 2018-04-05 14:59:18
tags:
---

[这里](https://amazer.github.io/2018/04/04/en_2/)制作的小工具，功能有点单薄，只能查看顶点的顺序。
在写CubeMesh的时候,需要查看顶点的位置信息和uv信息。
因此，添加这两个功能：

<!--more-->

## <font size=4>(1) 添加查看顶点位置和uv信息</font>
在[MeshViewerEditor](http://blog.csdn.net/yanchezuo/article/details/78969956)中，添加两个方法，来显示顶点位置信息和uv信息。因为目前只会在简单的mesh查看时用到，所以不必太考虑性能什么的，先有再追求。以下是新加的代码：
``` c#
		// 显示顶点位置Str
        private void AddVerticeStr(ref StringBuilder sb, Vector3 vert)
        {
            sb.Append(",vertice:" + vert);
        }
        // 显示uv信息Str
        private void AddUVStr(ref StringBuilder sb,Vector2 uv)
        {
            sb.Append(",uv:" + uv);
        }


```
## <font size=4>(2) 兼容顶点数量和uv数量不同的情况</font>
在新建Mesh的过程中，先添加顶点，之后才会给每个顶点添加uv坐标。所以，有可能uv坐标的数量和顶点数量不一致。这个时候，兼容一下。修改显示uv信息的方法：
``` c#
        private void AddUVStr(ref StringBuilder sb, ref List<Vector2> uvList, int index)
        {
            if (uvList.Count > index)
            {
                sb.Append(",uv:" + uvList[index]);
            }
        }

```
嗯，完成了，看一下运行效果：
![运行效果](http://img.blog.csdn.net/20180105114543782?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveWFuY2hlenVv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
## <font size=4>(3) 添加是否显示信息的选项</font>
有时候，希望只看顶点信息或者uv信息什么的，不要全部显示。
所以，在Inspector中添加是否显示信息的选项，就是这样：
![Inspector中的显示](http://img.blog.csdn.net/20180105115003242?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveWFuY2hlenVv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
Unity中的Editor类，重写OnInspectorGUI()方法，添加自己想要的选项。代码如下：
``` c#
        private bool showInfo = true;
        private bool showUV = true;
        private bool showVertice = true;
        public override void OnInspectorGUI()
        {
            base.OnInspectorGUI();
            showInfo = GUILayout.Toggle(showInfo, "Show Info");
            showVertice = GUILayout.Toggle(showVertice, "Show Vertice");
            showUV = GUILayout.Toggle(showUV, "Show UV");

        }

```
然后使用这几个布尔值就可以了。
## <font size=4>(4) 完整代码</font>
```
using UnityEditor;
using UnityEngine;
using System.Collections.Generic;
using System.Text;
namespace YanCheZuo
{
    [CustomEditor(typeof(TestMeshViewer))]
    public class MeshViewerEditor : Editor
    {
        private bool showInfo = true;
        private bool showUV = true;
        private bool showVertice = true;
        public override void OnInspectorGUI()
        {
            base.OnInspectorGUI();
            showInfo = GUILayout.Toggle(showInfo, "Show Info");
            showVertice = GUILayout.Toggle(showVertice, "Show Vertice");
            showUV = GUILayout.Toggle(showUV, "Show UV");

        }
        private void OnSceneGUI()
        {
            if (!showInfo)
            {
                return;
            }
            GUIStyle style = new GUIStyle();
            style.normal.textColor = Color.red;
            TestMeshViewer viewer = target as TestMeshViewer;
            Dictionary<Vector3, StringBuilder> posList = new Dictionary<Vector3, StringBuilder>();

            for (int i = 0, imax = viewer.verticesList.Count; i < imax; ++i)
            {
                Vector3 vPos = viewer.transform.TransformPoint(viewer.verticesList[i]);
                StringBuilder sb;
                if (posList.TryGetValue(vPos, out sb))
                {
                    StringBuilder str = new StringBuilder("index:" + i);
                    AddVerticeStr(ref str, vPos);
                    AddUVStr(ref str, ref viewer.uvList, i);
                    sb.AppendLine(str.ToString());
                }
                else
                {
                    sb = new StringBuilder();
                    StringBuilder str = new StringBuilder("index:" + i);
                    AddVerticeStr(ref str, vPos);
                    AddUVStr(ref str, ref viewer.uvList, i);
                    sb.AppendLine(str.ToString());
                    posList.Add(vPos, sb);
                }

                Handles.Label(vPos, sb.ToString(), style);
            }
        }

        private void AddVerticeStr(ref StringBuilder sb, Vector3 vert)
        {
            if (!showVertice)
            {
                return;
            }
            sb.Append(",vertice:" + vert);
        }
        private void AddUVStr(ref StringBuilder sb, ref List<Vector2> uvList, int index)
        {
            if (!showUV)
            {
                return;
            }

            if (uvList.Count > index)
            {
                sb.Append(",uv:" + uvList[index]);
            }
        }
    }
}

```
只看index和uv效果：
![index_uv效果](http://img.blog.csdn.net/20180105115350217?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveWFuY2hlenVv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

## <font size=4>(5) 最后</font>
生产工具决定生产力。定制工具比不可少。之后还会根据需要进行工具的更新。
积跬步以至千里。

以上。
