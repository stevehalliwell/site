---
id: 568
title: Moving projects to Unity 5
date: 2015-04-16T14:08:59+10:00
author: Steve Halliwell
layout: post
guid: http://stevehalliwell.com/?p=568
permalink: /moving-projects-to-unity-5/
enablewpts:
  - "0"
dsq_thread_id:
  - "4179291425"
et_enqueued_post_fonts:
  - 'a:2:{s:6:"family";a:2:{s:21:"et-gf-source-sans-pro";s:100:"Source+Sans+Pro:200,200italic,300,300italic,regular,italic,600,600italic,700,700italic,900,900italic";s:10:"et-gf-lato";s:75:"Lato:100,100italic,300,300italic,regular,italic,700,700italic,900,900italic";}s:6:"subset";a:7:{i:0;s:8:"cyrillic";i:1;s:5:"greek";i:2;s:10:"vietnamese";i:3;s:5:"latin";i:4;s:9:"greek-ext";i:5;s:9:"latin-ext";i:6;s:12:"cyrillic-ext";}}'
categories:
  - Blog
  - Unity
---
After Unity 5 released I decided to upgrade a few of my older projects and see what happened. So I opened up SourceTree and made sure there was a commit to safely rollback to if everything exploded.

# JSD

Just Shmup Dammit. Unity removed the shortcut properties .rigidbody .audio etc. as they were performance traps. They look like direct variable access, they are not. I&#8217;ve already talked about this in a previous performance post. This and 1 instance of a tag compare were the only things that changed in the automatic upgrade process. The tag compare was a compile error, all that needed to be done was remove quotes around a variable name.

Everything then ran as before.

# JGDLib

Unity updated the scripts, highlighting a few places where I had been lazy and had multiple .rigidbody in the same block. Other than that all test scenes ran as before.

# Project Red-Room

Well and truly still in it&#8217;s early stages, first person exploration game. Obviously required first person controller of some sort and makes heavy use of Pro Core&#8217;s ProBuilder. This asset required a new version to function correctly in Unity5. This required a fair bit of back n forth, lots of discard local changes, only due to a bug in the version I was using at the time, which I assume has been fixed since, it was only converting ProBuilder prefabs if they were in the currently active scene when I imported the newer version of the asset. This might be down to how Unity5 changed prefab storage in scenes.

I had been piggybacking off of Unity&#8217;s first person controller and using many of Unity&#8217;s Image effects. All of these have changed. At the very least in 5 they are all C# now. That&#8217;s great but it meant redoing some settings to update to them. The newer first person controller makes more sense, to me at least. It&#8217;s 1 script not 3 interacting js behaviours. However it has lost some functionality, handling moving platforms and easy ways to toggle off features. So these had to be added back into the newer version.

PhysX 2 to 3 changed a bit. My door prefab&#8217;s were extremely broken. They were having the physics freakout we all love with ragdolls. Hinge&#8217;s needed new settings, so the door wasn&#8217;t trying to be pulled inside the doorframe/wall next to it. The other change that got me was the door frame itself, previously a solid single object was no longer allowed due to the way I had set it up, it was not a convex shape. So altering the door prefab to use 2 boxes and 2 capsules instead of the door frame mesh itself solved this issue.

Certain shaders seem to now be much slower than previously. A grid of 100+ characters was causing frames to drop to the teens. I&#8217;m fairly certain it was not previously. Fortunately in this case there was no reason for them to be using a special shader so changing their material to use the new Unity 5 standard shader has solved this problem. This put the fps in the same area back up to well above 60.

Fog has changed. It and many other settings now live in the Lighting window and also requires that the global fog image effect is added to the camera.