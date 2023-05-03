---
# permalink: /about/
layout: single
title: "Transformation"
classes: wide
header:
  image: /assets/images/teaser/teaser.png
  caption: "Image credit: [**Yun**](http://yun-vis.net)"
last_modified_at: 2023-05-02
---

# Rotation

## Euler Angle vs. Quaternion

Rotation.cs
```csharp
using System;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

// Pre-setting before scripts
// Step1: Create a cube game object, name RotatedCube and attach the script to it.

// Global vs local rotation
// Step1: Create an empty game object, name Container and drag RotatedCube to be the child

// Pivot point
// Step1: Create an empty game object, name Pivot and drag RotatedCube to be the child
// Step2: Rotate the pivot (parent object)

// RotateAround
// Step1: Create a sphere game object, name Sun, Earth, and Moon. Drag game objects to be nested
// Attach script to Earth and later Moon.

public class Rotation : MonoBehaviour
{
    private GameObject _otherObject;

    private Transform _objectToOrbit;
    private float _degreesPerSecond = 20;
    private Vector3 _direction;
    private float _angle;
    private float _radius;
    
    // Initialization
    private void Awake()
    {
        _otherObject = new GameObject();
        // transform.position = new Vector3(0, 0, 0);
        _objectToOrbit = transform.parent.transform;
        
        // Unit vector
        _direction = (transform.position - _objectToOrbit.transform.position).normalized;
        // Distance between the parent and the object
        _radius = Vector3.Distance(_objectToOrbit.transform.position, transform.position);
    }

    // Start is called before the first frame update
    void Start()
    {
        // Change object position
        /*
        Vector3 newPosition = new Vector3(0, 5, 0);
        transform.position = newPosition;
        */

        // Change object rotation
        /*
        Vector3 newRotation = new Vector3(0, 45, 0);
        transform.eulerAngles = newRotation;
        */

        // There are in principle 2 types of rotations in CG. 
        // One is using Euler angle, and the other is using Quaternion. 
        // Behind the scenes, Unity calculates rotation using Quaternions,
        // meaning that the rotation property of an object is not actually a Vector 3,
        // it’s a Quaternion.

        // The rotation value you see in the Inspector is the real Quaternion rotation
        // converted to a Vector 3 value, an Euler Angle,
        // which is rotation expressed as an XYZ value for pitch, yaw and roll.

        // Copies another object's rotation
        /*
        Quaternion objectRotation = otherObject.transform.rotation;
        transform.rotation = objectRotation;
        */

        // Instead of working in Quaternions, you can convert a Vector 3 Euler Angle rotation into a Quaternion.
        /*
        transform.rotation = Quaternion.Euler(new Vector3(0, 45, 0));
        */
        
        // Generally speaking, Unity uses Quaternions because they’re efficient and avoid
        // some of the problems of using Euler Angles, such as gimbal lock, which is the
        // loss of a degree of movement when two axes are aligned the same way.
        // rotation vs. Rotate()
        
        // Local rotation vs world rotation
        // By default, when setting rotation directly, you’re setting the object’s world rotation
        // Setting the world rotation of a child object will rotate it into that position absolutely.
        
        // Local position/rotation/scale, is that transformation relative to its parent.
        // Global is the combination of a GameObject's local transformation with all of
        // its parents, to the root of the scene.
        
        // World/Global Rotation
        /*
        transform.eulerAngles = new Vector3(0,0,0);
        */
        // Local Rotation
        /*
        transform.localEulerAngles = new Vector3(0,0,-30);
        */
        // Creating a parent object is an easy way to move the pivot point of an object.
    }

    
    // Update is called once per frame
    void Update()
    {
        // Time.deltaTime (the duration of the last frame) to make sure that the rotation is smooth and consistent.
        // This is to make sure that, even if the framerate changes, the amount of movement over time remains the same.
        // In this case, it changes the amount of rotation from 20 degrees per frame to 20 degrees per second.

        
        transform.Rotate(new Vector3(0, _degreesPerSecond* Time.deltaTime, 0));
        
        
        // RotateAroundPoint();
        // RotateOrbit();
        // RotateOrbitSlant();
    }
    
    Vector3 _pivotPoint = new Vector3 (-0.5f,0,0.5f);
    void RotateAroundPoint()
    {
        // Rotates around the pivot point and the Y-Axis by 90 degrees
        // Vector3.left for the X-Axis, Vector3.up for the Y-Axis and Vector3.forward for the Z-Axis.
        transform.RotateAround(_pivotPoint, Vector3.up, _degreesPerSecond* Time.deltaTime);
    }
    
    void RotateOrbit()
    {
        // Debug.Log( objectToOrbit.position );
        // Revolution
        transform.RotateAround(_objectToOrbit.position, Vector3.up, _degreesPerSecond * Time.deltaTime);
        // Rotation
        // Difference with RotateOrbitSlant(): the parent angle will be added to children angle
        // transform.Rotate( Vector3.up, 0.5f);
        // transform.Rotate( Vector3.up, 0.0f);
    }
    
    void RotateOrbitSlant()
    {
        // Debug.Log( objectToOrbit.position );
        // transform.RotateAround(objectToOrbit.position, Vector3.up, degreesPerSecond * Time.deltaTime);
        
        _angle += _degreesPerSecond * Time.deltaTime;
        _angle %= 360;
        // Debug.Log("angle = " + angle);
        // Vector3.forward Z-Axis unit vector
        Vector3 orbit = Vector3.forward * _radius;
        // orbit = Quaternion.Euler(0, angle, 0) * orbit;
        orbit = Quaternion.LookRotation(_direction) * Quaternion.Euler(0, _angle, 0) * orbit;
        // Revolution
        transform.position = _objectToOrbit.transform.position + orbit;
        // Rotation
        // transform.Rotate( Vector3.up, 0.5f);
    }
}
```

---
# External Resources

## Quaternion

- Understanding Quaternions [Doc](https://www.allaboutcircuits.com/technical-articles/dont-get-lost-in-deep-space-understanding-quaternions/)

- Quaternions and Rotations [Doc](https://graphics.stanford.edu/courses/cs348a-17-winter/Papers/quaternion.pdf)