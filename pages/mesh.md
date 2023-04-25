---
# permalink: /about/
layout: single
title: "Mesh"
classes: wide
header:
  image: /assets/images/teaser/teaser.png
  caption: "Image credit: [**Yun**](http://yun-vis.net)"  
last_modified_at: 2023-04-18
---

# Procedural Mesh

MyMesh.cs
```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

// Pre-setting before scripts
// Step1: Create an empty game object, name MeshObject
// Step2: Add Component -> Mesh Filter (A Reference to the mesh)
// Step3: Add Component -> Mesh Render (A Mesh Renderer component renders a mesh)

// The RequireComponent attribute automatically adds required components as dependencies.
[RequireComponent(typeof(MeshFilter))]
[RequireComponent(typeof(MeshRenderer))]
[RequireComponent(typeof(YValue))]

// MonoBehaviour is the base class that every Unity script has to be inherited.  A Unity script, that is derived from a MonoBehaviour, serves a bunch of predefined functions(Awake, Start, Update, OnTriggerEnter, etc.) that are executed when an event occurs.
public class MyMesh : MonoBehaviour
{
    private Mesh _mesh;
    private YValue _yValue;

    private Vector3[] _vertices;
    private int[] _triangles;

    void Awake()
    {
        _yValue = GetComponent<YValue>();
        _mesh = GetComponent<MeshFilter>().mesh;
    }

    // These functions are known as event functions since they are activated by Unity in response to events that occur during gameplay
    // Start is called before the first frame update
    // void Start()
    // Update is called once per frame
    void Update()
    {
        // CreateTriangleData();
        CreateQuadData();
        UpdateMesh();
    }

    void CreateTriangleData()
    {
        // Create an array of vertices
        _vertices = new Vector3[] {new Vector3(0, 0, 0), new Vector3(0, 0, 1), new Vector3(1, 0, 0)};
        
        // Create an array of integers
        _triangles =  new int[] {0, 1, 2};
    }

    void CreateQuadData()
    {
        // Create an array of vertices
        _vertices = new Vector3[] {new Vector3(0, _yValue.value, 0), new Vector3(0, 0, 1), new Vector3(1, 0, 0), new Vector3(1, 0, 1)};
        
        // Create an array of integers
        _triangles =  new int[] {0, 1, 2, 2, 1, 3};
    }

    void UpdateMesh()
    {
        // Clear all vertex data and all triangle indices
        // Auto-formating Crtl+Alt+Enter
        _mesh.Clear();
        // Assign our vertices and triangles to mesh object
        _mesh.vertices = _vertices;
        _mesh.triangles = _triangles;
        
        _mesh.RecalculateNormals();
    }
}
```

MyMesh6V.cs
```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

// Pre-setting before scripts
// Step1: Create an empty game object, name MeshObject
// Step2: Add Component -> Mesh Filter (A Reference to the mesh)
// Step3: Add Component -> Mesh Render (A Mesh Renderer component renders a mesh)

// The RequireComponent attribute automatically adds required components as dependencies.
[RequireComponent(typeof(MeshFilter))]
[RequireComponent(typeof(MeshRenderer))]
[RequireComponent(typeof(YValue))]

// MonoBehaviour is the base class that every Unity script has to be inherited.  A Unity script, that is derived from a MonoBehaviour, serves a bunch of predefined functions(Awake, Start, Update, OnTriggerEnter, etc.) that are executed when an event occurs.
public class MyMesh6V : MonoBehaviour
{
    private Mesh _mesh;
    private YValue _yValue;
        
    private Vector3[] _vertices;
    private int[] _triangles;

    void Awake()
    {
        _yValue = GetComponent<YValue>();
        _mesh = GetComponent<MeshFilter>().mesh;
    }

    // These functions are known as event functions since they are activated by Unity in response to events that occur during gameplay
    // Start is called before the first frame update
    // void Start()
    // Update is called once per frame
    void Update()
    {
        // CreateTriangleData();
        CreateQuadData();
        UpdateMesh();
    }

    void CreateTriangleData()
    {
        // Create an array of vertices
        _vertices = new Vector3[] {new Vector3(0, 0, 0), new Vector3(0, 0, 1), new Vector3(1, 0, 0)};
        
        // Create an array of integers
        _triangles =  new int[] {0, 1, 2};
    }

    void CreateQuadData()
    {
        // Create an array of vertices
        _vertices = new Vector3[] { new Vector3(0, _yValue.value, 0), new Vector3(0, 0, 1), new Vector3(1, 0, 0), 
                                    new Vector3(1, 0, 0), new Vector3(0, 0, 1), new Vector3(1, 0, 1)};
        
        // Create an array of integers
        _triangles =  new int[] {0, 1, 2, 3, 4, 5};
    }

    void UpdateMesh()
    {
        // Clear all vertex data and all triangle indices
        _mesh.Clear();
        // Assign our vertices and triangles to mesh object
        _mesh.vertices = _vertices;
        _mesh.triangles = _triangles;
        
        _mesh.RecalculateNormals();
    }
}
```

YValue.cs
```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

// Pre-setting before scripts

public class YValue : MonoBehaviour
{
    public float value = 0.0f;
    
    // Start is called before the first frame update
    void Start()
    {
    }

    // Update is called once per frame
    void Update()
    {
    }
}
```

CubData.cs
```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public static class CubeData
{
    public static Vector3[] vertices =
    {
        new Vector3(1, 1, 1),
        new Vector3(-1, 1, 1),
        new Vector3(-1, -1, 1),
        new Vector3(1, -1, 1),
        new Vector3(-1, 1, -1),
        new Vector3(1, 1, -1),
        new Vector3(1, -1, -1),
        new Vector3(-1, -1, -1)
    };

    public static int[][] faceTriangles =
    {
        new int[] { 0, 1, 2, 3 },
        new int[] { 5, 0, 3, 6 },
        new int[] { 4, 5, 6, 7 },
        new int[] { 1, 4, 7, 2 },
        new int[] { 5, 4, 1, 0 },
        new int[] { 3, 2, 7, 6 }
    };

    public static Vector3[] faceVertices(int dir)
    {
        Vector3[] fv = new Vector3[4];
        for (int i = 0; i < fv.Length; i++)
        {
            fv[i] = vertices[faceTriangles[dir][i]];
        }

        return fv;
    }
}
```

CubeMesh.cs
```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

// Pre-setting before scripts

// The RequireComponent attribute automatically adds required components as dependencies.
[RequireComponent(typeof(MeshFilter), typeof(MeshRenderer))]
public class CubeMesh : MonoBehaviour
{
    private Mesh _mesh;
    private List<Vector3> _vertices;
    private List<int> _triangles;

    void Awake()
    {
        _mesh = GetComponent<MeshFilter>().mesh;
    }

    void Start()
    {
        CreateCube();
        UpdateMesh();
    }

    void CreateCube()
    {
        _vertices = new List<Vector3>();
        _triangles = new List<int>();

        for (int i = 0; i < 6; i++)
        {
            CreateFace(i);
        }
    }

    void CreateFace(int dir)
    {
        _vertices.AddRange(CubeData.faceVertices(dir));
        int vCount = _vertices.Count;

        _triangles.Add(vCount - 4);
        _triangles.Add(vCount - 4 + 1);
        _triangles.Add(vCount - 4 + 2);
        _triangles.Add(vCount - 4);
        _triangles.Add(vCount - 4 + 2);
        _triangles.Add(vCount - 4 + 3);
    }

    void UpdateMesh()
    {
        // Clear all vertex data and all triangle indices
        _mesh.Clear();
        // Assign our vertices and triangles to mesh object
        _mesh.vertices = _vertices.ToArray();
        _mesh.triangles = _triangles.ToArray();
        _mesh.RecalculateNormals();
    }
}
```

Grid.cs
```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Serialization;

[RequireComponent(typeof(MeshFilter), typeof(MeshRenderer))]
public class Grid : MonoBehaviour
{
    private Mesh _mesh;
    private Vector3[] _vertices;
    private int[] _triangles;

    // Grid setting
    public float cellSize;
    public Vector3 gridoffset;
    public int gridSize;

    // Use this for initialization
    void Awake()
    {
        _mesh = GetComponent<MeshFilter>().mesh;
    }

    // Start is called before the first frame update
    void Start()
    {
        CreateContiguousGrid();
        // CreateProceduralGrid();
        UpdateMesh();
    }

    void CreateContiguousGrid()
    {
        // Set array sizes
        _vertices = new Vector3[(gridSize + 1) * (gridSize + 1)];
        _triangles = new int[6 * gridSize * gridSize];

        // Set tracker integers
        int v = 0;
        int t = 0;

        // Set vertex offset
        float vertexOffset = cellSize * 0.5f;

        for (int x = 0; x <= gridSize; x++)
        {
            for (int z = 0; z <= gridSize; z++)
            {
                //_vertices[v] = new Vector3((x * cellSize) - vertexOffset, 0, (z * cellSize) - vertexOffset);
                _vertices[v] = new Vector3((x * cellSize) - vertexOffset, (x+z)*0.3f, (z * cellSize) - vertexOffset);
                v++;
            }
        }

        // reset vertex tracker
        v = 0;

        for (int x = 0; x < gridSize; x++)
        {
            for (int z = 0; z < gridSize; z++)
            {
                _triangles[t + 0] = v;
                _triangles[t + 1] = _triangles[t + 4] = v + 1;
                _triangles[t + 2] = _triangles[t + 3] = v + (gridSize + 1);
                _triangles[t + 5] = v + (gridSize + 1) + 1;
                v++;
                t += 6;
            }
            v++;
        }
    }

    void CreateProceduralGrid()
    {
        // Set array sizes
        _vertices = new Vector3[4 * gridSize * gridSize];
        _triangles = new int[6 * gridSize * gridSize];

        /*
        _vertices = new Vector3[4];
        _triangles = new int[6];
        */

        // Set tracker integers
        int v = 0;
        int t = 0;

        // Set vertex offset
        float vertexOffset = cellSize * 0.5f;

        for (int x = 0; x < gridSize; x++)
        {
            for (int z = 0; z < gridSize; z++)
            {
                Vector3 cellOffset = new Vector3(x * cellSize, 0, z * cellSize);
                
                _vertices[v + 0] = new Vector3(-vertexOffset, 0, -vertexOffset) + cellOffset + gridoffset;
                _vertices[v + 1] = new Vector3(-vertexOffset, 0, vertexOffset) + cellOffset + gridoffset;
                _vertices[v + 2] = new Vector3(vertexOffset, 0, -vertexOffset) + cellOffset + gridoffset;
                _vertices[v + 3] = new Vector3(vertexOffset, 0, vertexOffset) + cellOffset + gridoffset;
                /*
                _vertices[v + 0] = new Vector3(-vertexOffset, x+z, -vertexOffset) + cellOffset + gridoffset;
                _vertices[v + 1] = new Vector3(-vertexOffset, x+z, vertexOffset) + cellOffset + gridoffset;
                _vertices[v + 2] = new Vector3(vertexOffset, x+z, -vertexOffset) + cellOffset + gridoffset;
                _vertices[v + 3] = new Vector3(vertexOffset, x+z, vertexOffset) + cellOffset + gridoffset;
                */
                _triangles[t + 0] = v;
                _triangles[t + 1] = _triangles[t + 4] = v + 1;
                _triangles[t + 2] = _triangles[t + 3] = v + 2;
                _triangles[t + 5] = v + 3;

                v += 4;
                t += 6;
            }
        }

        // Populate the vertices and triangle array
        /*
        _vertices[0] = new Vector3(-vertexOffset, 0, -vertexOffset);
        _vertices[1] = new Vector3(-vertexOffset, 0, vertexOffset);
        _vertices[2] = new Vector3(vertexOffset, 0, -vertexOffset);
        _vertices[3] = new Vector3(vertexOffset, 0, vertexOffset);

        _triangles[0] = 0;
        _triangles[1] = _triangles[4] = 1;
        _triangles[2] = _triangles[3] = 2;
        _triangles[5] = 3;
        */
    }

    void UpdateMesh()
    {
        // Clear all vertex data and all triangle indices
        _mesh.Clear();
        // Assign our vertices and triangles to mesh object
        _mesh.vertices = _vertices;
        _mesh.triangles = _triangles;

        _mesh.RecalculateNormals();
    }

    // Update is called once per frame
    void Update()
    {
    }
}
```

UnityPlane.cs
```csharp
```

---
# External Resources

## Data Annotation [Doc](https://www.codeproject.com/Articles/256183/DataAnnotations-Validation-for-Beginner)

Data annotations (available as part of the System. ComponentModel. DataAnnotations namespace) are attributes that can be applied to classes or class members to specify the relationship between classes, describe how the data is to be displayed in the UI, and specify validation rules.

## Component-Based Architecture [Doc](https://www.tutorialspoint.com/software_architecture_design/component_based_architecture.htm)

Component-Oriented Versus Object-Oriented Programming [Doc](https://www.oreilly.com/library/view/programming-net-components/0596102070/ch01s02.html)