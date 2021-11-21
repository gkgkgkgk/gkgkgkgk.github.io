---
layout: post
title: "Dynamically Adjusting LOD on a Plane with Quadtrees"
date: 2021-11-17 1:10:12 -0000
categories: unity
featured-image: https://raw.githubusercontent.com/gkgkgkgk/gkgkgkgk.github.io/gh-pages/_posts/assets/quadtrees/chart.png
---
## Introduction
The goal of this project is to create a mesh that dynamically adjusts the level of detail (LOD) it has depending on where the player is. A higher LOD means that the mesh will have more vertices, and therefore a better ability to take on more detailed shapes. When the player is close to a certain part of the mesh, the LOD should be higher, as the player will be able to see that part of the mesh up close. When the player is far away, the LOD should be lower, as we don't want to waste computations on vertices that aren't in view or are too far away to see. There is not point in rendering highly detailed meshes if they are out of view or too far away to see.
#### So, What is a quadtree?
A quadtree is a tree data structure in which every single node has exactly four children.
#### Who cares?
I do! When I set out to build an dynamic LOD system for meshes, I found that a quadtree may be one of the best ways to do it.

## How it works
The basic idea is to start by generating a mesh using faces that represent a quadtree. Here is a mesh that correlates to a quadtree with height 1 and resolution 2:

<img src="https://raw.githubusercontent.com/gkgkgkgk/gkgkgkgk.github.io/gh-pages/_posts/assets/quadtrees/quad.png" width="50%"/>

As you can see in this picture, the mesh is made up of four squares, each with a minimum resoluition of 2 triangles. The "root node" in represents the parent that contains all four of these faces. In the picture below, each square represents a child node, while the combination of all four squares represent the parent node.

<img src="https://raw.githubusercontent.com/gkgkgkgk/gkgkgkgk.github.io/gh-pages/_posts/assets/quadtrees/chart.png" />

So how does this help us with increasing the LOD on this mesh? It's pretty simple. The basic idea is to examine each node on the mesh, and decide if it needs more detail depending on if the player is close enough. If the player if close enough to the node, it will break up the square into four more nodes. This process repeats until we achieve the maximum LOD that we wanted. The code below shows that process:

<img src="https://raw.githubusercontent.com/gkgkgkgk/gkgkgkgk.github.io/gh-pages/_posts/assets/quadtrees/code.png" />

This code runs as follows: when a new node is created, it checks two conditions. The first is whether or not the LOD is at its limit- if the LOD has reached its limit, it should not generate any more child nodes, because the mesh is at the highest LOD. Without this condition, the areas of the mesh that are close the player would contain way too many vertices, and the mesh would be too detailed (a lot of detail sounds great, but the whole point of dynamic LOD is to minimize the amount of vertices that we need to render at any given time). 

It then checks if the player is close enough to generate more nodes, by checking the distance between the player and the local position of the chunk/node, and seeing if it is less than the required distance for the next LOD. In the event that it is not at max LOD and the player is close enough to warrent a higher LOD, the node is divided up into four squares and four new children nodes are made. Each node is given a new size, which is half the size of its parent node, a new position, which is each quadrent of the parent node, and finally, the next LOD. After each child node is created, the ```generateChildren()``` method runs on each of them, to see if new nodes should have child nodes. This function runs on each node until the player is too far to warrent more detail or until the LOD is at its limit.

Finally, lets add the player to the scene (the player is an empty object that can be dragged around by the mouse)! As you can see, the LOD adjusts depending on where the player is on the mesh.

<img src="https://raw.githubusercontent.com/gkgkgkgk/gkgkgkgk.github.io/gh-pages/_posts/assets/quadtrees/quadgif.gif" />

Initially, the performance was not great. However, I found that marking the mesh as dymanic by running ```mesh.MarkDynamic();``` significantly improves the performance, enough to not have to worry about further optimizations. The ```mesh.MarkDynamic()``` function tells the graphics API to use a dynamic buffer for the vertices in the mesh, which makes it more efficient to update the mesh.
## Conclusion
This project was a great way to learn about quadtrees, and how they can be used to dynamically adjust the LOD of a mesh. Eventually, I would like to apply this concept to massive, procedurally generated terrains that the player could explore, and with the help of a dynamic LOD system, this could be achieved with good performence. 

## Resources
 * Thank you to [SimonDev](https://www.youtube.com/channel/UCEwhtpXrg5MmwlH04ANpL8A), who created a [video on quadtrees and world generation](https://www.youtube.com/watch?v=YO_A5w_fxRQ) and helped me understand these concepts.
 * [GeeksForGeeks Article](https://www.geeksforgeeks.org/quad-tree/)
 * [Website for playing with quadtrees](https://google.github.io/closure-library/source/closure/goog/demos/quadtree.html)
 * [Unity Docs on MarkDynamic()](https://docs.unity3d.com/ScriptReference/Mesh.MarkDynamic.html)