---
id: 852
title: Particles and Fluids Part 1
date: 2020-08-03T21:50:13+10:00
author: Steve Halliwell
layout: revision
guid: http://stevehalliwell.com/849-revision-v1/
permalink: /?p=852
---
OK, so years ago, I was playing around with a concept for a control scheme based on vector fields. The player avatar was a floating particle/light mote. The player moved by dragging their finger(s) on device or mouse cursor, mobile was the target platform, this motion would add velocity to the underlying vector field and cause the particle/light mote to move. This also included bi directional influence of particles. Meaning the velocity every particle would have a small impact on the underlying vector field and vice versa, a particle would sample the underlying vector field and attempt to match velocity.

(video)

It had a couple of nice side effects, you could create currents by simply making particle systems emit in certain shapes and velocities. You can sample and write back physics objects too, allowing shapes to be cut into the vector field with natural buffeting, divergence and trailing flow around them. The size and number of particles in a given area would have a natural dampening effect and by propagating velocities on the vector field itself channel flows would occur and objects would calmly drift long distances.

It was however, CPU intensive. To get it running on a mobile at 30fps or higher required threading, probably still does. This was long before the [Unity Job system](https://docs.unity3d.com/Manual/JobSystem.html) was generally available and long before ECS or Burst were on the horizon. The approach used was similar in that it&#8217;s the c# threadpool and went wide by giving chunks of the arrays to work through to different tasks.

(perf and stats and snippets of the original)

Anyway, in going over older prototypes and projects for sharing on [itch, shameless plug](https://stevehalliwell.itch.io/). I wanted to update this project to more recent version of Unity to make a build, this is low hanging fruit for playing around with [Jobs](https://docs.unity3d.com/Manual/JobSystem.html) and [Burst](https://docs.unity3d.com/Packages/com.unity.burst@1.3/manual/index.html).