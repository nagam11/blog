Recently, I finished an ARKit project featuring some elements of the the Solar System (namely: the Sun, Earth and the Moon). 
While the ARKit part on itself was not significantly difficult, working with SCNVector3's and understanding the composition
of all the elements was a bit more challenging than I thought. Hence, I wanted to share some key learnings as well as technical
details 
of the implementation. One major topic addressed here was: Rotating nodes around the x/y/z axis and around 
other nodes or points of reference.

[Setting](#setting)

[Walkthrough](#walkthrough)

[Measurements](#measurements)

### Setting
We want to implement three nodes: the Sun, the Earth and the Moon. Each of these nodes rotates around the y axis (vertical axis)
with a certain speed. Morever, the Moon orbits and Earth and the Earth (and its satellite, the Moon) orbit together the Sun. In ARKit,
we can implement the nodes using SCNNodes(3D Nodes) from SceneKit. Rotating the nodes can be implemented using
SCNActions. To place SCNNodes in the 3D space, we need to address the space by using SCNVector3's, which are representations 
of three-component vectors. SceneKit uses [three-component vectors](https://developer.apple.com/documentation/scenekit/scnvector3) 
for a variety of purposes, such as describing node or vertex positions, surface normals, and scale or translation transforms.
The remainder of this post is a walkthrough of the code in the project as well as an overview of the measurements I used in the app. 
>  **Note:** Below, I use the terms nodes and systems as synonyms. That it because all systems and all standalone astronomical objects are implemented as nodes. I use the term system when either multiple nodes are child nodes of a specific parent or when the node itself is performing more than one rotation with a different reference point (or axis). 

### Walkthrough 
SCNVector3's make use of three space dimensions (x,y,z). So in simpler words, the cordinate systems looks like this:
![0]({{ site.baseurl}}/images/s_0.png)

It resembles the **OpenGL** coordinate system, which is described more thorougly [here](http://www.falloutsoftware.com/tutorials/gl/gl0.htm).
We will place the Sun in the middle of the 'Real World' coordinate system, which corresponds to the current location of the device. This is the coordinate (0,0,0) or the origin. However, we will do that later. Firstly, we want to create a sub-system of the solar system, which is the Earth-Moon system. We will place it 70cm away from the camera(also called negative space), so -0.7 points on the z axis. 
```
let earthMoonSystem = SCNNode()
earthMoonSystem.position = SCNVector3(0,0,-0.7)
```
![1]({{ site.baseurl}}/images/s_1.png)

---

In the Earth-Moon system, the Earth itself is placed in the origin (at the coordinate (0,0,0)) of it, while the Moon orbits around.
However, we want the Earth to be in the center of the sub-system and not in the center of the real world system, since the Sun
has to be there. Thus, we add the Earth as a child node of the Earth-Moon system. By doing this, we are placing the Earth
at the origin of its parent node, which is the Earth-Moon system and which already has an offset on the z axis. Consequently, **Earth's relative coordinates(in regard to its parent) are (0,0,0). Its absolute(in regard to the real world system) coordinates are (0,0,-0.7).**
```
let earthNode = self.createObject(radius: 0.04, materialName: "earth.jpg", position: SCNVector3(0,0,0))
earthMoonSystem.addChildNode(earthNode)
self.rotateObject(node: earthNode, duration: rotation_speeds[0], from: SCNVector4Make(0, 1, 0, 0), to: SCNVector4Make(0, 1, 0, Float(Double.pi) * 2.0), key: "earth_own_rotation")   
```
![2]({{ site.baseurl}}/images/s_2.png)
![3]({{ site.baseurl}}/images/s_3.png)

---

Next, we want to add the Moon as a satellite of the Earth. However, the Moon also rotates around the y axis. So the question is: How to make the Moon rotate around itself and also around the Earth since we can only rotate nodes around a certain axis?
The answer is: We can do this by separately rotating the Moon node around itself and then adding it as a child node of a Moon-Rotation system(containing only the Moon but implementing the two different rotations using two different reference points). **The latter will be the one rotating around its y axis and will thus rotate the Moon, its child node, at the same time.** 
```
let moonRotationNode = SCNNode()
```
![4]({{ site.baseurl}}/images/s_4.png)

We add the Moon node with an offset (-10cm) on the z axis and rotate it around its y axis.
```
let moonNode = self.createObject(radius: 0.01, materialName: "moon.jpg", position: SCNVector3(0,0,-0.1))
self.rotateObject(node: moonNode, duration: rotation_speeds[1], from: SCNVector4Make(0, 1, 0, 0), to: SCNVector4Make(0, 1, 0, Float.pi * 2.0), key: "moon_own_rotation")
```
![5]({{ site.baseurl}}/images/s_5.png)
![6]({{ site.baseurl}}/images/s_6.png)

---

Now, here's the tricky part. At the moment, the Moon-Rotation system is not related to other systems and its absolute and relative origin is (0,0,0). What we want to achieve, is having the whole system have an absolute origin(in regard to real world coordinates) at the coordinate (0,0,-0.7). That is where our Earth-Moon System has its own origin. 
**Therefore, we want to align both systems at this coordinate so that they can overlap.**

![7]({{ site.baseurl}}/images/s_7.gif)
```
earthMoonSystem.addChildNode(moonRotationNode)
```

We now have following systems and their respective coordinate systems/orientations.
![8]({{ site.baseurl}}/images/s_8.png)
![9]({{ site.baseurl}}/images/s_9.png)
![10]({{ site.baseurl}}/images/s_10.png)

---

Finally, we can rotate the Moon-Rotation system as a whole. 
```
self.rotateObject(node: moonRotationNode, duration: rotation_speeds[2], from: SCNVector4Make(0, 1, 0, 0), to: SCNVector4Make(0, 1, 0, Float(Double.pi) * 2.0), key: "moon_earth_rotation")
```
![11]({{ site.baseurl}}/images/s_11.gif)
 
## Measurements
### Orbits durations

| Name          | Nominal Duration(days)   | App fast duration (sec)  | App slow duration (sec)|
| ------------- |:-------------:| -----:| -----:|
| Orbit: Moon -> Earth   | 27 days | 2 | 54| 
| Orbit: Earth -> Sun      | 365 days      |   27 |730|
| Moon -> Moon      |  27 days | 2 | 54 |
| Earth -> Earth      |   1 day |0.07| 2 |
| Sun -> Sun      |  25 days |1.8| 50 |
{:.mbtablestyle}

**Result**: Implemented both simulation speeds. Users can click on any node to change the speed of the whole system, which is proportional to the real speed.

### Distance 

| Name          | Distance(km) | Distance in App(cm)|
| ------------- |:-------------:| -----:|
| Earth-Sun   | 150 * 10^6 | 3902 (39m) |
| Earth-Moon   | 384,400 | 10  |
{:.mbtablestyle}

**Result**: Proportional distance does not 'work'. Either the Sun will be very very far away and thus not in the field of vision or the Earth and the Moon will 'collide'(very very small distance). I am using a 'reasonable' distance so that all elements are visible but it does not reflect the real distance.

### Size(Radius)

| Name          | Radius(km)   | Radius in App(m) big | Radius in App(m) small|
| ------------- |:-------------:| -----:| -----:|
| Earth   | 6371 | 0.03 | 0.00915| 
| Sun     | 695,700 |  3.27 |1|
| Moon    |  1737 | 0.008 | 00.25 |
{:.mbtablestyle}

**Result**: Proportional size does not 'work'. Either the Sun will be very big or the Earth system will be very small. I am using a 'reasonable' size/radius so that all elements are visible but it does not reflect the real size.
