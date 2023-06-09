---
# permalink: /about/
layout: single
title: "Linear Primitives"
classes: wide
header:
  image: /assets/images/teaser/teaser.png
  caption: "Image credit: [**Yun**](http://yun-vis.net)"
last_modified_at: 2023-05-02
---

# Ray

## Raycasting

Raycasting.cs
```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Raycasting : MonoBehaviour
{
    Ray _ray;
    // Container for hit data
    private RaycastHit _hitData;
    public LayerMask layers;
    
    // Start is called before the first frame update
    void Start()
    {
        // Creates a Ray from this object, moving forward
        _ray = new Ray(transform.position, transform.forward);
        
        // Creates a Ray from the center of the viewport
        // _ray = Camera.main.ViewportPointToRay(new Vector3 (0.5f, 0.5f, 0));
        
        // Creates a Ray from the mouse position
        _ray = Camera.main.ScreenPointToRay(Input.mousePosition);
        
        layers = LayerMask.GetMask("World") | LayerMask.GetMask("Water");
        // layers = 1<<9;
    }

    // Update is called once per frame
    void Update()
    {
        FireRay();
    }
    
    void FireRay()
    {
        _ray = new Ray(transform.position, transform.forward);
        // Visualize the ray
        Debug.DrawRay(_ray.origin, _ray.direction * 10);

        // one of the most common ways to use Raycast is using the Physics Class, which returns a boolean true or false, depending on if the Ray hit anything.
        // The out keyword in Unity is used to return extra information from a function.
        if (Physics.Raycast(_ray, out _hitData, 10,layers))
        {
            Vector3 hitPosition = _hitData.point;
            float hitDistance = _hitData.distance;
            // Reads the Collider name
            string name = _hitData.collider.name;
            // Gets a Game Object reference from its Transform
            GameObject hitObject = _hitData.transform.gameObject;
        
            Debug.Log("layers = " + layers.value);
        }
    }
}
```

## Raycasting Reflection

RaycastReflection.cs
```csharp
using System;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

// Pre-setting before scripts
// Step1: Create 3 cube game objects, name Wall 1-3 and adjust the sizes.
// Step2: Create a cube game object, name Origin and attach the script
// Step3: Reflection set to 3 and MaxLength set to 100
// Step4: Add "Mirror" tag to the Wall 1-3
// Step5: Enable LineRenderer.CornerVertices to make the line looks smoother.
// Step6: Enable LineRenderer.EndCapVertices to make the line looks smoother.
// Step7: Change the width or add material...

[RequireComponent(typeof(LineRenderer))]
public class RaycastReflection : MonoBehaviour
{
    [SerializeField]
    int reflections;
    [SerializeField]
    float maxLength;

    private LineRenderer _lineRenderer;
    private Ray _ray;
    private RaycastHit _hitData;
    private Vector3 _direction;

    void Awake()
    {
        _lineRenderer = GetComponent<LineRenderer>();
    }

    // Start is called before the first frame update
    void Start()
    {
    }

    // Update is called once per frame
    void Update()
    {
        _ray = new Ray(transform.position, transform.forward);

        // positionCount returns the number of vertices in the line. The example below shows a line with 3 vertices.
        _lineRenderer.positionCount = 1;
        // Set the position of a vertex in the line.
        _lineRenderer.SetPosition(0, transform.position);
        float remainingLength = maxLength;

        for (int i = 0; i < reflections; i++)
        {
            if(Physics.Raycast(_ray.origin, _ray.direction, out _hitData, remainingLength))
            {
                _lineRenderer.positionCount += 1;
                _lineRenderer.SetPosition(_lineRenderer.positionCount - 1, _hitData.point);
                remainingLength -= Vector3.Distance(_ray.origin, _hitData.point);
                _ray = new Ray(_hitData.point, Vector3.Reflect(_ray.direction, _hitData.normal));
                if (_hitData.collider.tag != "Mirror")
                    break;
            }
            else
            {
                _lineRenderer.positionCount += 1;
                _lineRenderer.SetPosition(_lineRenderer.positionCount - 1, _ray.origin + _ray.direction * remainingLength);
            }
        }        
    }
}
```

# Curve

## Bézier Curve

Curve2D.cs
```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

// Pre-setting before scripts
// Step1: Create an empty game object, name Curve.
// Step2: Create 4 empty game objects, name ControlPoint1-4 and select icon for them (under inspector text).
// Step3: Drag the curve script to the Curve game object and set control points to 4
// Step4: Drag control points to the corresponding control point elements

public class Curve2D : MonoBehaviour
{
    [SerializeField]
    private Transform[] controlPoints;

    private Vector2 _gizmosPosition;
    
    // Start is called before the first frame update
    void Start()
    {
        
    }

    // Update is called once per frame
    void Update()
    {
        
    }
    
    private void OnDrawGizmos()
    {
        for(float t = 0; t <= 1; t += 0.05f)
        {
            _gizmosPosition = Mathf.Pow(1 - t, 3) * controlPoints[0].position + 3 * Mathf.Pow(1 - t, 2) * t * controlPoints[1].position + 3 * (1 - t) * Mathf.Pow(t, 2) * controlPoints[2].position + Mathf.Pow(t, 3) * controlPoints[3].position;

            Gizmos.DrawSphere(_gizmosPosition, 0.25f);
        }

        Gizmos.DrawLine(new Vector2(controlPoints[0].position.x, controlPoints[0].position.y), new Vector2(controlPoints[1].position.x, controlPoints[1].position.y));
        Gizmos.DrawLine(new Vector2(controlPoints[2].position.x, controlPoints[2].position.y), new Vector2(controlPoints[3].position.x, controlPoints[3].position.y));
    }
}
```

Curve3D.cs
```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

// Pre-setting before scripts
// Step1: Create an empty game object, name Curve.
// Step2: Create 4 empty game objects, name ControlPoint1-4 and select icon for them (under inspector text).
// Step3: Drag the curve script to the Curve game object and set control points to 4
// Step4: Drag control points to the corresponding control point elements

public class Curve3D : MonoBehaviour
{
    [SerializeField]
    private Transform[] controlPoints;

    private Vector3 _gizmosPosition;
    
    // Start is called before the first frame update
    void Start()
    {
        
    }

    // Update is called once per frame
    void Update()
    {
        
    }
    
    private void OnDrawGizmos()
    {
        for(float t = 0; t <= 1; t += 0.05f)
        {
            _gizmosPosition = Mathf.Pow(1 - t, 3) * controlPoints[0].position + 3 * Mathf.Pow(1 - t, 2) * t * controlPoints[1].position + 3 * (1 - t) * Mathf.Pow(t, 2) * controlPoints[2].position + Mathf.Pow(t, 3) * controlPoints[3].position;

            Gizmos.DrawSphere(_gizmosPosition, 0.25f);
        }

        Gizmos.DrawLine(new Vector3(controlPoints[0].position.x, controlPoints[0].position.y, controlPoints[0].position.z), new Vector3(controlPoints[1].position.x, controlPoints[1].position.y, controlPoints[1].position.z));
        Gizmos.DrawLine(new Vector3(controlPoints[2].position.x, controlPoints[2].position.y, controlPoints[2].position.z), new Vector3(controlPoints[3].position.x, controlPoints[3].position.y, controlPoints[3].position.z));    }
}
```

CurveFollow.cs
```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

// Pre-setting before scripts
// Step1: Create a cube game object called Follower and attach the script to it.
// Step2: Change the Route size in the inspector to 1 and drag the Curve object there.
// Step3: Create the 2nd Curve object and change the Route size to 2 in the inspector.

public class CurveFollow : MonoBehaviour
{
    [SerializeField]
    Transform[] routes;

    private int _routeToGo;
    private float _tParam;
    private Vector3 _objectPosition;
    private float _speedModifier;
    private bool _coroutineAllowed;

    // Start is called before the first frame update
    void Start()
    {
        _routeToGo = 0;
        _tParam = 0f;
        _speedModifier = 0.3f;
        _coroutineAllowed = true;
    }

    // Update is called once per frame
    void Update()
    {
        if (_coroutineAllowed)
        {
            StartCoroutine(GoByTheRoute(_routeToGo));
        }
    }
    
    private IEnumerator GoByTheRoute(int routeNum)
    {
        _coroutineAllowed = false;

        Vector3 p0 = routes[routeNum].GetChild(0).position;
        Vector3 p1 = routes[routeNum].GetChild(1).position;
        Vector3 p2 = routes[routeNum].GetChild(2).position;
        Vector3 p3 = routes[routeNum].GetChild(3).position;

        while (_tParam < 1)
        {
            _tParam += Time.deltaTime * _speedModifier;

            _objectPosition = Mathf.Pow(1 - _tParam, 3) * p0 + 3 * Mathf.Pow(1 - _tParam, 2) * _tParam * p1 + 3 * (1 - _tParam) * Mathf.Pow(_tParam, 2) * p2 + Mathf.Pow(_tParam, 3) * p3;

            transform.position = _objectPosition;
            yield return new WaitForEndOfFrame();
        }

        _tParam = 0.0f;
        _routeToGo += 1;

        if (_routeToGo > routes.Length - 1)
        {
            _routeToGo = 0;
        }

        _coroutineAllowed = true;
    }
}
```

---
# External Resources

