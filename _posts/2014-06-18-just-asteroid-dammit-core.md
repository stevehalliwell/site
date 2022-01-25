---
id: 427
title: Just Asteroid Dammit Core
date: 2014-06-18T11:47:45+10:00
author: Steve Halliwell
layout: post
guid: http://stevehalliwell.com/?p=427
permalink: /just-asteroid-dammit-core/
enablewpts:
  - "0"
dsq_thread_id:
  - "4713636511"
et_enqueued_post_fonts:
  - 'a:2:{s:6:"family";a:2:{s:21:"et-gf-source-sans-pro";s:100:"Source+Sans+Pro:200,200italic,300,300italic,regular,italic,600,600italic,700,700italic,900,900italic";s:10:"et-gf-lato";s:75:"Lato:100,100italic,300,300italic,regular,italic,700,700italic,900,900italic";}s:6:"subset";a:7:{i:0;s:8:"cyrillic";i:1;s:5:"greek";i:2;s:10:"vietnamese";i:3;s:5:"latin";i:4;s:9:"greek-ext";i:5;s:9:"latin-ext";i:6;s:12:"cyrillic-ext";}}'
categories:
  - Blog
  - Unity
---
Didn&#8217;t get to spend as much time on this as I would have liked. But we do have a ship and asteroids and sub asteroids spawning and floating around with the 2d physics system.

I made them all super bouncy by adding a PhysicsMaterial2D as the default material was a little flat. The trick for my wall wrap is boxes on the edges of the screen using a OnTriggerExit2D, which flips their position based on a scale and if their velocity matches a dot product. The dot product ensures that they are trying to leave the screen not enter it from that side, without it objects would flicker back and forth between sides.

<a href="https://dl.dropboxusercontent.com/u/53300249/JustAstDammit%20V0.1/JAD_V0.1.html" target="_blank">Try it here</a>