---
id: 428
title: Just Asteroid Dammit Core
date: 2014-06-18T11:47:45+10:00
author: Steve Halliwell
layout: revision
guid: http://stevehalliwell.com/427-revision-v1/
permalink: /?p=428
---
Didn&#8217;t get to spend as much time on this as I would have liked. But we do have a ship and asteroids and sub asteroids spawning and floating around with the 2d physics system.

I made them all super bouncy by adding a PhysicsMaterial2D as the default material was a little flat. The trick for my wall wrap is boxes on the edges of the screen using a OnTriggerExit2D, which flips their position based on a scale and if their velocity matches a dot product. The dot product ensures that they are trying to leave the screen not enter it from that side, without it objects would flicker back and forth between sides.

<a href="https://dl.dropboxusercontent.com/u/53300249/JustAstDammit%20V0.1/JAD_V0.1.html" target="_blank">Try it here</a>