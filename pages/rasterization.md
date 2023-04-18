---
# permalink: /about/
layout: single
title: "Rasterization"
classes: wide
header:
  image: /assets/images/teaser/teaser.png
  caption: "Image credit: [**Yun**](http://yun-vis.net)"
last_modified_at: 2023-04-18
---

# Rasterization

## DDA

GridMesh.cs
```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Serialization;

// Pre-setting before scripts
// Step1: Create a material, name White and choose shader "Sprites -> Default"

[RequireComponent(typeof(MeshFilter), typeof(MeshRenderer))]
public class GridMesh : MonoBehaviour
{
    public Mesh mesh;
    private Vector3[] _vertices;
    private Color[] _colors;
    private int[] _triangles;

    // Grid setting
    public float cellSize;
    public Vector3 gridoffset;
    public int gridSize;
    
    // Use this for initialization
    void Awake()
    {
        mesh = GetComponent<MeshFilter>().mesh;
        Vector3 gridPos = new Vector3(0, 0, 0);
        GetComponent<Transform>().position = gridPos;
        
        Debug.Log($"position = {GetComponent<Transform>().position}");
    }

    // Start is called before the first frame update
    void Start()
    {
        CreateProceduralGrid();
        UpdateMesh();
    }

    void CreateProceduralGrid()
    {
        // Set array sizes
        _vertices = new Vector3[4 * gridSize * gridSize];
        _colors = new Color[4 * gridSize * gridSize];
        _triangles = new int[6 * gridSize * gridSize];
        
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
                
                _colors[v + 0] = _colors[v + 1] = _colors[v + 2] = _colors[v + 3] = new Color(1, 1, 1, 1);

                _triangles[t + 0] = v;
                _triangles[t + 1] = _triangles[t + 4] = v + 1;
                _triangles[t + 2] = _triangles[t + 3] = v + 2;
                _triangles[t + 5] = v + 3;

                v += 4;
                t += 6;
            }
        }
    }

    void UpdateMesh()
    {
        // Clear all vertex data and all triangle indices
        mesh.Clear();
        // Assign our vertices and triangles to mesh object
        mesh.vertices = _vertices;
        mesh.colors = _colors;
        mesh.triangles = _triangles;

        mesh.RecalculateNormals();
    }

    // Update is called once per frame
    void Update()
    {
    }
}
```

DDA.cs
```csharp
using System;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

// Pre-setting before scripts
// Step1: Create an empty game object, name Source and add material
// Step2: Create an empty game object, name Target and add material
// Step3: Create an empty game object, name Grid and attach GridMesh script and add material
// Step4: Create an empty game object, name DDA and attach DDA script

public class DDA : MonoBehaviour
{
    private GameObject _source;
    private GameObject _target;
    private GameObject _gridMesh;

    // Use this for initialization
    void Awake()
    {
        _source = GameObject.Find("Source");
        _target = GameObject.Find("Target");
        _gridMesh = GameObject.Find("Grid");

        Vector3 gridPosition = _gridMesh.transform.position;
        float cellSize = _gridMesh.GetComponent<GridMesh>().cellSize;
        float vertexOffset = cellSize * 0.5f;

        Vector3 sourcePos = new Vector3(
            0,
            gridPosition.y,
            0
        );
        Vector3 targetPos = new Vector3(
            (_gridMesh.GetComponent<GridMesh>().gridSize - 1) * cellSize,
            gridPosition.y,
            (_gridMesh.GetComponent<GridMesh>().gridSize - 1) * cellSize
        );

        // Initialize positions
        _source.transform.position = sourcePos;
        _target.transform.position = targetPos;
    }

    // Start is called before the first frame update
    void Start()
    {
    }

    // Update is called once per frame
    void Update()
    {
        Vector3 gridPosition = _gridMesh.transform.position;

        // Update positions
        Vector3 sourcePos = _source.transform.position;
        sourcePos.y = gridPosition.y;
        Vector3 targetPos = _target.transform.position;
        targetPos.y = gridPosition.y;

        // Debug.Log($"sourcePos = {sourcePos }");
        _source.transform.position = sourcePos;
        _target.transform.position = targetPos;

        ComputeDDA();
    }

    void ComputeDDA()
    {
        Vector3 sourcePos = _source.transform.position;
        Vector3 targetPos = _target.transform.position;

        // Create new colors array where the colors will be created.
        Color[] colors = _gridMesh.GetComponent<GridMesh>().mesh.colors;
        // Reset colors to white
        for (int i = 0; i < colors.Length; i++)
        {
            colors[i].r = colors[i].g = colors[i].b = colors[i].a = 1;
        }

        float dx = targetPos.x - sourcePos.x;
        float dz = targetPos.z - sourcePos.z;
        float m = dz / dx;

        // m <= 1
        if ( m <= 1)
        {
            float x = sourcePos.x;
            float z = sourcePos.z;
            DrawPixel(colors, (int)Math.Round(x), (int)Math.Round(z));

            for (int i = 0; i < dx; i++)
            {
                x += 1;
                z += m;
                DrawPixel(colors, (int)Math.Round(x), (int)Math.Round(z));
            }
        }
        // m > 1
        else
        {
            m = dx/dz;
            float x = sourcePos.x;
            float z = sourcePos.z;
            DrawPixel(colors, (int)Math.Round(x), (int)Math.Round(z));

            for (int i = 0; i < dz; i++)
            {
                x += m;
                z += 1;
                DrawPixel(colors, (int)Math.Round(x), (int)Math.Round(z));
            }
        }
        
        _gridMesh.GetComponent<GridMesh>().mesh.colors = colors;
    }

    void DrawPixel(Color[] colors, int x, int z)
    {
        //Debug.Log($"(x,z) = ({x},{z})");
        int gridSize = _gridMesh.GetComponent<GridMesh>().gridSize;
        if (x >= gridSize || z >= gridSize) return;

        //Debug.Log($"vertices.Length = {vertices.Length}");
        // assign the array of colors to the Mesh.
        int v = 4 * (x * gridSize + z);
        if (v + 3 >= 400)
        {
            Debug.Log($"v = {v}");
            Debug.Log($"(x,z) = ({x},{z})");
        }

        colors[v] = new Color(0, 0, 0, 1);
        colors[v + 1] = new Color(0, 0, 0, 1);
        colors[v + 2] = new Color(0, 0, 0, 1);
        colors[v + 3] = new Color(0, 0, 0, 1);
    }
}
```

---
# External Resources
