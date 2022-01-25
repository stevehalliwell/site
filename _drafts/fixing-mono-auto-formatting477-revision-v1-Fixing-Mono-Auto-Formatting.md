---
id: 480
title: Fixing Mono Auto-Formatting
date: 2014-07-30T21:26:18+10:00
author: Steve Halliwell
layout: revision
guid: http://stevehalliwell.com/477-revision-v1/
permalink: /?p=480
---
So at work Unity and Mono seem quite unhappy with the lack of admin rights. The most annoying consequences of that is Mono defaulting to a crazy auto formatting. With a project open in mono we can fix this by changing two settings.

Go Project->Solution Options. In Code Formatting change c# source code to Microsoft Visual Studio.

Then the same settings in Mono. Tools->Options. Again in the source code section, Code Formatting, c# to Microsoft Visual Studio.

While you&#8217;re there I also normally use [<img loading="lazy" class="alignnone size-full wp-image-479" src="http://stevehalliwell.com/wp-content/uploads/2014/07/behaviour.png" alt="behaviour" width="621" height="477" srcset="http://stevehalliwell.com/wp-content/uploads/2014/07/behaviour.png 621w, http://stevehalliwell.com/wp-content/uploads/2014/07/behaviour-300x230.png 300w" sizes="(max-width: 621px) 100vw, 621px" />](http://stevehalliwell.com/wp-content/uploads/2014/07/behaviour.png){.vt-p}