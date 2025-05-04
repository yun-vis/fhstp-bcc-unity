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

# GameObjects

Every object in your game is a GameObject, from characters and collectible items to lights, cameras and special effects. 
 
* Position: Position of the Transform in the x, y, and z coordinates.
* Rotation: Rotation of the Transform around the x-axis, y-axis, and z-axis, measured in degrees.
* Scale: Scale of the Transform along the x-axis, y-axis, and z-axis. The value “1” is the original size (the size at which you imported the GameObject).
* Enable Constrained Proportions: Force the scale to maintain its current proportions, so that changing one axis changes the other two axes. Disabled by default.

# Gizmo

In Unity, gizmos are visual aids drawn in the Scene view to help with debugging and visualization during development, not during runtime. 

* For position: Click the Pivot/Center button on the left to toggle between Pivot and Center.
  * Pivot positions the Gizmo at the actual pivot point of the GameObject, as defined by the Transform component.
  * Center positions the Gizmo at a center position based on the selected GameObjects.
* For rotation: Click the Local/Global button on the right to toggle between Local and Global.
  * Local keeps the Gizmo’s rotation relative to the GameObject’s.
  * Global clamps the Gizmo to world space orientation.

# Rotation

## Euler Angle vs. Quaternion

* Pre-setting before scripts
  * Step1: Create a GameObeject MyCubes that is composed of multiple cubes. Press "v" to enable vertex snapping during translation.
  * Step2: Set up Pivot vs. Center (calculated by BoundingBox) of the another two empty GameObject
What is a Bounding Box? A bounding box (Axis-Aligned Bounding Box and Oriented Bounding Box) is an automatically-created invisible box that defines the rough size of an entity.
  
* Global vs local rotation
  * Step1: Create an empty game object, name Containter and drag MyCubes to be the child
* Pivot point
  * Step1: Add a method RotateAroundPoint() in the script
* RotateAround
  * Step1: Create an empty game object, name SolarSystem.
  * Step2: Create three sphere game objects, name Sun (s=1), Earth (s=0.5), and Moon (s=0.3). Drag game objects to be nested in hierarchy of SolarSystem. Attach the script to Earth and later Moon.

Rotation.cs
```csharp
using System;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Rotation : MonoBehaviour
{
    // For animation
    float degreesPerSecond = 20;

    // Initialization, usually instantiation of objects
    // and setting up references to other objects.
    private void Awake()
    {
    }

    // Start is called before the first frame update
    void Start()
    {
        // Change object position
        transform.position = new Vector3(0, 5, 0);

        // Change object rotation in Euler angles
        // The rotation as Euler angles in degrees.
        transform.eulerAngles = new Vector3(0, 45, 0);

        // There are in principle 2 types of rotations in CG. 
        // One is using Euler angle, and the other is using Quaternion. 
        // Behind the scenes, Unity calculates rotation using Quaternions,
        // meaning that the rotation property of an object is not actually a Vector 3,
        // it’s a Quaternion.

        // The rotation value you see in the Inspector is the real Quaternion rotation
        // converted to a Vector 3 value, an Euler Angle,
        // which is rotation expressed as an XYZ value for pitch, yaw and roll.

        // Copies another object's rotation
        // Quaternion objectRotation = _otherObject.transform.rotation;
        // transform.rotation = objectRotation;

        // Instead of working in Quaternions, you can convert a Vector 3 Euler Angle rotation into a Quaternion.
        // A Quaternion that stores the rotation of the Transform in world space.
        // equal to transform.eulerAngles =  new Vector3(0, 45, 0);
        transform.rotation = Quaternion.Euler(new Vector3(0, 45, 0));


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
        // With/without roatating the parent object Container -30 degrees around the X-Axis
        transform.eulerAngles = new Vector3(0, 45, 0);
        // same as above
        transform.rotation = Quaternion.Euler(new Vector3(0, 45, 0));


        // Local Rotation
        // With/without roatating the parent object Container -30 degrees around the X-Axis
        transform.localEulerAngles = new Vector3(0, 0, -30);
        // same as above
        transform.localRotation = Quaternion.Euler(new Vector3(0, 0, -30));

        // Creating a parent object is an easy way to move the pivot point of an object.
    }

    // Update is called once per frame
    void Update()
    {
        RotateAroundCenter();
        // RotateAroundPoint();
        // RotateOrbit();
        // RotateOrbitSlant();
    }

    void RotateAroundCenter()
    {
        // Time.deltaTime (the duration of the last frame) to make sure that the rotation is smooth and consistent.
        // This is to make sure that, even if the framerate changes, the amount of movement over time remains the same.
        // In this case, it changes the amount of rotation from 20 degrees per frame to 20 degrees per second.
        transform.Rotate(new Vector3(0, degreesPerSecond, 0) * Time.deltaTime);
    }

    void RotateAroundPoint()
    {
        Vector3 pivotPoint = new Vector3(-0.5f, 0, 0.5f);

        // Rotates around the pivot point and the Y-Axis by 90 degrees
        // Vector3.left for the X-Axis, Vector3.up for the Y-Axis and Vector3.forward for the Z-Axis.
        transform.RotateAround(pivotPoint, Vector3.up, degreesPerSecond * Time.deltaTime);
    }
}
```

SolarSystem.cs
```csharp
using System;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;


public class SolarSystem : MonoBehaviour
{
    // For animation
    float degreesPerSecond = 20;
    public Transform ObjectToOrbit;
    public Vector3 Direction;
    public float Angle;
    public float Radius;

    // Initialization
    private void Awake()
    {
        // transform.position = new Vector3(0, 0, 0);
        ObjectToOrbit = transform.parent.transform;

        // Unit vector
        Direction = (transform.position - ObjectToOrbit.transform.position).normalized;
        // Distance between the parent and the object
        Radius = Vector3.Distance(ObjectToOrbit.transform.position, transform.position);
    }

    // Start is called before the first frame update
    void Start()
    {
    }

    // Update is called once per frame
    void Update()
    {
        //RotateOrbit();
        RotateOrbitSlant();
    }

    void RotateOrbit()
    {
        Debug.Log( ObjectToOrbit.position );
        // Revolution
        transform.RotateAround(ObjectToOrbit.position, Vector3.up, degreesPerSecond * Time.deltaTime);
        // Rotation
        // Difference with RotateOrbitSlant(): the parent angle will be added to children angle
        // transform.Rotate( Vector3.up, 0.5f);
        // transform.Rotate( Vector3.up, 0.0f);
    }

    void RotateOrbitSlant()
    {
        Angle += degreesPerSecond * Time.deltaTime;
        Angle %= 360;
        // Debug.Log("angle = " + angle);
     
        // Rotate a vector by multiplying it by a Quaternion
        // Vector3.forward Z-Axis unit vector
        Vector3 orbit = Vector3.forward * Radius;
        // orbit = Quaternion.Euler(0, angle, 0) * orbit;
        orbit = Quaternion.LookRotation(Direction) * Quaternion.Euler(0, Angle, 0) * orbit;
        // Revolution
        transform.position = ObjectToOrbit.transform.position + orbit;
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