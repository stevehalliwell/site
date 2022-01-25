---
id: 421
title: Just Invaders Dammit Core
date: 2014-06-15T20:31:47+10:00
author: Steve Halliwell
layout: revision
guid: http://stevehalliwell.com/420-revision-v1/
permalink: /?p=421
---
Next up was space invaders.

<a class="vt-p" href="https://dl.dropboxusercontent.com/u/53300249/JustInvadersDammit%20V0.1/JustInvadersDammit%20V0.1.html" target="_blank">Try it here</a>

With this one the its a few little tricks that are to be learned from. Don&#8217;t want the player to go off the screen? Well there&#8217;s heaps of whats to get that to work but for a core gameplay prototype, just put a clamp in there and dial it in. In this case player&#8217;s transform x is not allowed to go beyond -2.9-2.9.

Want a grid of enemies, well generate them. The enemy manager takes a starting point transform and how many rows and cols you want and how far apart they should be. In this case that seemed easier than copy pasting GOs. It also lines up nicely as we then child all those enemies to the enemy manager and it controls their movement and uses their colliders to determine when to change direction. It also chooses which enemy should fire.

I&#8217;m using triggers on constrained rigidbodies on almost everything here.