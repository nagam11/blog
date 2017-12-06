Recently, I finished an ARKit project featuring some of the elements of the Solar System (namely: the Sun, Earth and the Moon). 
While the ARKit part on itself was not significantly difficult, working with SCNVector3's and understanding the composition
of all the elements was a bit more challenging than I thought. Hence, I wanted to share some key learnings as well as technical
details 
ing the implementation. One major topic addressed here was: Rotating nodes around the x/y/z axis and around 
other nodes or points of reference.

### Setting
We want to implement three nodes: the Sun, the Earth and the Moon. Each of these nodes rotates around the y axis (vertical axis)
with a certain speed. Morever, the Moon orbits and Earth and the Earth (and its satellite: the Moon) orbit together the Sun. In ARKit,
we can implement the nodes using SCNNodes(3D Nodes) from SceneKit. Rotating the moving the nodes can be implemented using
SCNActions. To place SCNNodes in the 3D space, we need to address the space by using SCNVector3's, which are representations 
of three-component vectors. SceneKit uses [three-component vectors](https://developer.apple.com/documentation/scenekit/scnvector3) 
for a variety of purposes, such as describing node or vertex positions, surface normals, and scale or translation transforms. 
>  **Note:** Below, I use the terms nodes and systems as synonyms. That it because all systems and all standalone astronomical objects are implemented as nodes. I use the term system when either multiple nodes are child nodes of a specific parent or when the node itself is performing more than one rotation with a different reference point (or axis). 

### Walkthrough 
SCNVector3's make use of three space dimensions (x,y,z). So in simpler words, the cordinate systems looks like this:
![0]({{ site.baseurl}}/images/s_0.png)

It resembles the **OpenGL** coordinate system, which is described more thorougly [here](http://www.falloutsoftware.com/tutorials/gl/gl0.htm).
We will place the Sun in the middle of the 'Real World' coordinate system, which corresponds to the current location of the device. This is the coordinate (0,0,0) or the Origin. However, we will do that later. Firstly, we want to create a sub-system of the solar system, which is the Earth-Moon system. We place it 70cm away from the camera(also called negative space), so -0.7 points on the z axis. 
```
let earthMoonSystem = SCNNode()
earthMoonSystem.position = SCNVector3(0,0,-0.7)
```
![1]({{ site.baseurl}}/images/s_1.png)

---

In the Earth-Moon system, the Earth itself is placed in the origin (coordinate (0,0,0)) of it, while the Moon orbits around.
However, we want the Earth to be in the center of the sub-system and not in the center of the real world system, since the Sun
has to be there. Thus, we add the Earth as a child node of the Earth-Moon system. By doing this, we are placing the Earth
in the origin of its parent node, which is the sub-system and which already has an offset on the z axis. Consequently, **Earth's relative coordinates(in regard to its parent) are (0,0,0). Its absolute(in regard to the real world system) coordinates are (0,0,-0.7).**
```
let earthNode = self.createObject(radius: 0.04, materialName: "earth.jpg", position: SCNVector3(0,0,0))
earthMoonSystem.addChildNode(earthNode)
self.rotateObject(node: earthNode, duration: rotation_speeds[0], from: SCNVector4Make(0, 1, 0, 0), to: SCNVector4Make(0, 1, 0, Float(Double.pi) * 2.0), key: "earth_own_rotation")   
```
![2]({{ site.baseurl}}/images/s_2.png)
![3]({{ site.baseurl}}/images/s_3.png)

---

Next, we want to add the Moon as a satellite of the Earth. However, the Moon also rotates around the y axis. So the question is: How to make the Moon rotate around itself and also around the Earth since we can only rotate nodes around a certain axis?
The answer is: We can do this by separetely rotating the Moon node around itself and then adding it as a child node of a Moon-Rotation system(containing only the Moon but implementing the two different rotations using two different reference points). **The latter will be the one rotating around its y axis and will thus rotate the Moon, its child node, at the same time.** 
```
let moonRotationNode = SCNNode()
```
![4]({{ site.baseurl}}/images/s_4.png)

We add the Moon node with an offset (-10cm) on the z axis and rotate it around its y axis. Lastly, we add it as a child of the Moon-Rotation-System
```
let moonNode = self.createObject(radius: 0.01, materialName: "moon.jpg", position: SCNVector3(0,0,-0.1))
self.rotateObject(node: moonNode, duration: rotation_speeds[1], from: SCNVector4Make(0, 1, 0, 0), to: SCNVector4Make(0, 1, 0, Float.pi * 2.0), key: "moon_own_rotation")
```
![5]({{ site.baseurl}}/images/s_5.png)
![6]({{ site.baseurl}}/images/s_6.png)

---

Now, here's the tricky part. At the moment, the Moon-Rotation-System is not related to other systems and its absolute and relative origin is (0,0,0). What we want to achieve, is having the whole system have a relative origin at the coordinate (0,0,-0.7). That is where our Earth-Moon System has its own origin. **Therefore, we want to align both systems at this coordinate so that they can overlap. **
![7]({{ site.baseurl}}/images/s_7.gif)
```
earthMoonSystem.addChildNode(moonRotationNode)
```

We now have following systems and their respective coordinate systems/orientations.
![8]({{ site.baseurl}}/images/s_8.png)
![9]({{ site.baseurl}}/images/s_9.png)
![10]({{ site.baseurl}}/images/s_10.png)
self.rotateObject(node: moonRotationNode, duration: rotation_speeds[2], from: SCNVector4Make(0, 1, 0, 0), to: SCNVector4Make(0, 1, 0, Float(Double.pi) * 2.0), key: "moon_earth_rotation")

---

Finally, we can rotate the Moon-Rotation-System as a whole. 
```
self.rotateObject(node: moonRotationNode, duration: rotation_speeds[2], from: SCNVector4Make(0, 1, 0, 0), to: SCNVector4Make(0, 1, 0, Float(Double.pi) * 2.0), key: "moon_earth_rotation")
```
![11]({{ site.baseurl}}/images/s_11.gif)
