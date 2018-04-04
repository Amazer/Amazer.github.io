
---
title: "[引擎]之二：Unity中查看简单mesh顶点顺序的小工具"
categories: '引擎'
tags: ['引擎','mesh']
copyright: true
---


因为要在Unity3d中检验在《3D游戏编程大师技巧》中学习到的东西，写了一个查看简单mesh的顶点顺序的小工具。
效果如下（当前也可以将uv坐标等其他信息加入到显示中）：

<!--more-->

![工具效果](http://img.blog.csdn.net/20180104133806421?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveWFuY2hlenVv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)



工具创建步骤：

1. 创建一个脚本，读取mesh中的顶点列表

``` c#
using UnityEngine;
using System.Collections.Generic;
namespace YanCheZuo
{
    [RequireComponent(typeof(MeshFilter))]
    public class TestMeshViewer : MonoBehaviour
    {
        private MeshFilter _meshFilter;
        private Mesh _mesh;

        public List<Vector3> verticesList = new List<Vector3>();
        public List<Vector2> uvList = new List<Vector2>();
        public List<int> triList = new List<int>();

        private void Start()
        {
            _meshFilter = this.GetComponent<MeshFilter>();
            _mesh = _meshFilter.mesh;

            ReadMeshInfo();

        }
        private void ReadMeshInfo()
        {

            for (int i = 0, imax = _mesh.vertexCount; i < imax; ++i)
            {
                verticesList.Add(_mesh.vertices[i]);
                uvList.Add(_mesh.uv[i]);
            }

            for (int i = 0, imax = _mesh.triangles.Length; i < imax; ++i)
            {
                triList.Add(_mesh.triangles[i]);
            }
        }
    }
}

```

2. 创建编辑器脚本，在Scene中绘制顶点顺序

``` c#
using UnityEditor;
using UnityEngine;
using System.Collections.Generic;
using System.Text;
namespace YanCheZuo
{
    [CustomEditor(typeof(TestMeshViewer))]
    public class MeshViewerEditor : Editor
    {
        private void OnSceneGUI()
        {
            GUIStyle style = new GUIStyle();
            style.normal.textColor = Color.red;
            TestMeshViewer viewer = target as TestMeshViewer;
            Dictionary<Vector3, StringBuilder> posList=new Dictionary<Vector3, StringBuilder>();

            for (int i = 0, imax = viewer.verticesList.Count; i < imax; ++i)
            {
                Vector3 vPos = viewer.transform.TransformPoint(viewer.verticesList[i]);
                
                StringBuilder sb;
                if (posList.TryGetValue(vPos, out sb))
                {
                    sb.AppendLine("index:" + i);
                }
                else
                {
                    sb = new StringBuilder();
                    sb.AppendLine("index" + i);
                    posList.Add(vPos, sb);

                }

                Handles.Label(vPos, sb.ToString(),style);
            }
        }
    }
}

```

* 使用方法：
把TestMeshViewer挂到有MeshFilter组件的GameObject上。

* 以上
