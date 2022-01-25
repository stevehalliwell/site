---
id: 420
title: Just Invaders Dammit Core
date: 2014-06-15T20:31:47+10:00
author: Steve Halliwell
layout: post
guid: http://stevehalliwell.com/?p=420
permalink: /just-invaders-dammit-core/
enablewpts:
  - "0"
et_enqueued_post_fonts:
  - 'a:2:{s:6:"family";a:2:{s:21:"et-gf-source-sans-pro";s:100:"Source+Sans+Pro:200,200italic,300,300italic,regular,italic,600,600italic,700,700italic,900,900italic";s:10:"et-gf-lato";s:75:"Lato:100,100italic,300,300italic,regular,italic,700,700italic,900,900italic";}s:6:"subset";a:7:{i:0;s:8:"cyrillic";i:1;s:5:"greek";i:2;s:10:"vietnamese";i:3;s:5:"latin";i:4;s:9:"greek-ext";i:5;s:9:"latin-ext";i:6;s:12:"cyrillic-ext";}}'
categories:
  - Blog
  - Unity
---
Next up was space invaders.

<a class="vt-p" href="https://dl.dropboxusercontent.com/u/53300249/JustInvadersDammit%20V0.1/JustInvadersDammit%20V0.1.html" target="_blank">Try it here</a>

With this one the its a few little tricks that are to be learned from. Don&#8217;t want the player to go off the screen? Well there&#8217;s heaps of whats to get that to work but for a core gameplay prototype, just put a clamp in there and dial it in. In this case player&#8217;s transform x is not allowed to go beyond -2.9-2.9.

Want a grid of enemies, well generate them. The enemy manager takes a starting point transform and how many rows and cols you want and how far apart they should be. In this case that seemed easier than copy pasting GOs. It also lines up nicely as we then child all those enemies to the enemy manager and it controls their movement and uses their colliders to determine when to change direction. It also chooses which enemy should fire.

I&#8217;m using triggers on constrained rigidbodies on almost everything here.