---
layout: post
title: "Dynamically Adjusting LOD on a Plane with Quadtrees"
date: 2021-11-17 1:10:12 -0000
categories: unity
---

# Quadtrees
### What is a quadtree?
A quadtree is a tree data structure in which every single node has exactly four children.
### Who cares?
I do! When I set out to build an dynamic level of detail system for 3D meshes, I found that a quadtree may be the best ways to do it.
## Introduction
The goal of this project is to create a mesh that adjusts the level of detail (LOD) it has depending on where the player is. A higher LOD means that the mesh will have more vertices, and therefore a better ability to take on more detailed shapes. When the player is close to a certain part of the mesh, the LOD should be higher, as the player will be able to see that part of the mesh up close. When the player is far away, the LOD should be lower, as we don't want to waste computations on vertices that aren't in view or are too far away to see.
## How it works
The basic idea is to generate a mesh using vertices that represent a quadtree. We start out by generating a mesh made up of a quadtree with height 1 and resolution 2:
INSERT PICTURE HERE.
As you can see in this picture, the mesh is made up of four squares, each with a resoluition of 2 triangles. There is a root node that contains all four of the children nodes, as seen in this graph:
INSERT PICTURE WITH LABELED NODES AND QUADTREE
So how does this help us with increasing the LOD on this mesh? It's pretty simple. The basic idea is to examine each node on the mesh, and decide if it needs more detail depending on if the player is close enough. If the player if close enough to the node, it will break up the square into four more nodes. This process repeats until we achieve the maximum LOD that we wanted. The code below shows that process:
INSERT CODE SCREENSHOT
This code runs as follows: when a new node is created, it first checks two conditions. The first is whether or not the LOD is at its limit- if the LOD has reached its limit, it should not generate any more child nodes. It then checks if the player is close enough to generate more nodes, by checking the distance between the player, the local position of the chunk/node, and seeing if it is less than the required distance for the next LOD. In the event that it is not at max LOD and the player is close enough to warrent a higher LOD, four new children nodes are made. Each node is given a new size, which is half of its parent node, a new position, which is each quadrent of the parent node, and finally, the new LOD. After each child node is created, the ```generateChildren()``` method runs on each of them.

Finally, lets add the player to the scene! As you can see, the LOD adjusts depending on where the player is on the mesh:
INSERT GIF
