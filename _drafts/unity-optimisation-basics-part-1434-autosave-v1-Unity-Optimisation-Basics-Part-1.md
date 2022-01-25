---
id: 438
title: Unity Optimisation Basics Part 1
date: 2014-07-08T21:24:55+10:00
author: Steve Halliwell
layout: revision
guid: http://stevehalliwell.com/434-autosave-v1/
permalink: /?p=438
---
I recently decided to run the unity profiler over JSD and see what could be improved, this is just a quick review of my findings.

# Searching is bad.

I already avoid all Find and GetComponents when ever I can so there weren&#8217;t many of them to find. I don&#8217;t recall if it was AltDevBlog or the Unity Blog itself or in a Unite or Learn video but it doesn&#8217;t matter. GameObject.Find\*, Object.Find\* and GetComponent are all expensive and wherever it was, explained that its simply because unity does a linear search through the collection to find them. Makes sense in a way, even if they were O(log n) it would still be expensive to do them all the time and would require the internal storage to be not cache sensitive or lazy.

# Collision layers, use them.

JSD has a lot of trigger rigidbodies, I had set it up so bullets didn&#8217;t overlap with withselves and never gave it another thought, until I saw 126 calls to OnTriggerEnter in 1 frame. Lots of bullets overlapping enemies however. It did a few checks and threw it away. Changing bullets to be in either a only collides with player layer and vice versa (an only collides with enemies layer) reduced this down to much more acceptable levels. This also drastically reduced the number of contact pairs the phys engine has to create and maintain which greatly lowered the overall and consistly high amount of cpu time spent in the physics engine.

# yield rarely

The bullet spawners had a yield still in them from an earlier debugging attempt to prevent infinite loops during testing, this seemingly harmless line

<pre class="lang:js decode:true">yield return null;</pre>

was causing an additional 10% overhead on that function call. This again is not surprising, if you haven&#8217;t have a look at <a class="vt-p" href="http://www.altdev.co/2011/07/07/unity3d-coroutines-in-detail/" target="_blank">http://www.altdev.co/2011/07/07/unity3d-coroutines-in-detail/</a> its not an inconsiderable amount of work to make that co-routine happen, even if the line with the yield never gets hit.

# Pools are not a silver bullet

I use object pools (of my own creation) to avoid trashing memory which is mandatory on mobile if not everywhere. You should avoid allocs during gameplay like the plague. I found however that activating objects (.activeInHierarchy = true) is still quite expensive.

Surprisingly, splines are not using much at all, neither is overall rendering.