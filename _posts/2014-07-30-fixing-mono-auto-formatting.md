---
id: 477
title: Fixing Mono Auto-Formatting
date: 2014-07-30T21:26:18+10:00
author: Steve Halliwell
layout: post
guid: http://stevehalliwell.com/?p=477
permalink: /fixing-mono-auto-formatting/
enablewpts:
  - "0"
dsq_thread_id:
  - "4472212856"
et_enqueued_post_fonts:
  - 'a:2:{s:6:"family";a:2:{s:21:"et-gf-source-sans-pro";s:100:"Source+Sans+Pro:200,200italic,300,300italic,regular,italic,600,600italic,700,700italic,900,900italic";s:10:"et-gf-lato";s:75:"Lato:100,100italic,300,300italic,regular,italic,700,700italic,900,900italic";}s:6:"subset";a:7:{i:0;s:8:"cyrillic";i:1;s:5:"greek";i:2;s:10:"vietnamese";i:3;s:5:"latin";i:4;s:9:"greek-ext";i:5;s:9:"latin-ext";i:6;s:12:"cyrillic-ext";}}'
categories:
  - Blog
  - Unity
---
So at work Unity and Mono seem quite unhappy with the lack of admin rights. The most annoying consequences of that is Mono defaulting to a crazy auto formatting. With a project open in mono we can fix this by changing two settings.

Go Project->Solution Options. In Code Formatting change c# source code to Microsoft Visual Studio.

Then the same settings in Mono. Tools->Options. Again in the source code section, Code Formatting, c# to Microsoft Visual Studio.

While you&#8217;re there I also normally use [<img loading="lazy" class="alignnone size-full wp-image-479" src="http://stevehalliwell.com/wp-content/uploads/2014/07/behaviour.png" alt="behaviour" width="621" height="477" srcset="http://stevehalliwell.com/wp-content/uploads/2014/07/behaviour.png 621w, http://stevehalliwell.com/wp-content/uploads/2014/07/behaviour-300x230.png 300w" sizes="(max-width: 621px) 100vw, 621px" />](http://stevehalliwell.com/wp-content/uploads/2014/07/behaviour.png){.vt-p}