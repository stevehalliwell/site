---
id: 414
title: Just Flap Dammit Core
date: 2014-06-08T16:51:09+10:00
author: Steve Halliwell
layout: post
guid: http://stevehalliwell.com/?p=414
permalink: /just-flap-dammit-core/
enablewpts:
  - "0"
dsq_thread_id:
  - "5127241512"
et_enqueued_post_fonts:
  - 'a:2:{s:6:"family";a:2:{s:21:"et-gf-source-sans-pro";s:100:"Source+Sans+Pro:200,200italic,300,300italic,regular,italic,600,600italic,700,700italic,900,900italic";s:10:"et-gf-lato";s:75:"Lato:100,100italic,300,300italic,regular,italic,700,700italic,900,900italic";}s:6:"subset";a:7:{i:0;s:8:"cyrillic";i:1;s:5:"greek";i:2;s:10:"vietnamese";i:3;s:5:"latin";i:4;s:9:"greek-ext";i:5;s:9:"latin-ext";i:6;s:12:"cyrillic-ext";}}'
categories:
  - Blog
  - Unity
---
I posed my new Scripting for Game Development students with creating a core prototype of Flappy Bird. Doesn&#8217;t take too long to get the core running and its well under 100 lines of code.

<a class="vt-p" href="https://dl.dropboxusercontent.com/u/53300249/JustFlapDammit%20V0.1/Build.html" target="_blank">You can try mine here</a>

For me, the trick at this point to getting it to feel even remotely good is totally bullshit physics values. Right now the bird is .5m, gravity is -16m/s/s, mass is 1kg with a drag of .4 and a impulse strength of ~10n.

**Update: **With the addition of about 15 more lines we now have personal bests to beat.

<a class="vt-p" href="https://dl.dropboxusercontent.com/u/53300249/JustFlapDammit%20V0.2/build.html" target="_blank">Try out version 0.2 here</a>