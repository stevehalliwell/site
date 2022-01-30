---
id: 714
title: Dev Console Part 1
date: 2017-02-12T14:51:18+10:00
author: Steve Halliwell
layout: post
guid: http://stevehalliwell.com/?p=714
permalink: /dev-console-1/
enablewpts:
  - "0"
dsq_thread_id:
  - "5783893274"
et_enqueued_post_fonts:
  - 'a:2:{s:6:"family";a:2:{s:21:"et-gf-source-sans-pro";s:100:"Source+Sans+Pro:200,200italic,300,300italic,regular,italic,600,600italic,700,700italic,900,900italic";s:10:"et-gf-lato";s:75:"Lato:100,100italic,300,300italic,regular,italic,700,700italic,900,900italic";}s:6:"subset";a:7:{i:0;s:8:"cyrillic";i:1;s:5:"greek";i:2;s:10:"vietnamese";i:3;s:5:"latin";i:4;s:9:"greek-ext";i:5;s:9:"latin-ext";i:6;s:12:"cyrillic-ext";}}'
image: /wp-content/uploads/2017/02/halflife1console.jpg
categories:
  - Blog
---

<p>
  So I was working on a project that targets mobile and I&#8217;d run into a problem that didn&#8217;t replicate in editor or on Android, only on iOS. I really just wanted to put a bunch of logs in and see what was happening. I wanted an in game dev console. If you aren&#8217;t sure what I mean by that, if you&#8217;ve played with an Id or Valve game you&#8217;ve probably enabled developer console at some point, seen logs pop up in the top left or hit ` to change maps or enable net_graph.
</p>

![](/wp-content/uploads/2017/02/halflife1console.jpg)

<p>
  Similarly, Bethesda games tend to have powerful in game consoles that can execute the same sort of commands as the scripting language. Which leads to amazing things like this <a href="https://www.youtube.com/watch?v=_1jGnFt78H8">Monster Factory Fallout4</a>.
</p>

<p>
  So I went hunting, surely this is something people have already attempted, if not solved.
</p>

<p>
  <a href="https://github.com/mminer/consolation">https://github.com/mminer/consolation</a><br /> <a href="https://github.com/Wenzil/UnityConsole">https://github.com/Wenzil/UnityConsole</a><br /> <a href="https://github.com/gzuidhof/GConsole">https://github.com/gzuidhof/GConsole</a><br /> <a href="https://github.com/hecomi/uREPL">https://github.com/hecomi/uREPL</a><br /> <a href="https://github.com/proletariatgames/CUDLR">https://github.com/proletariatgames/CUDLR</a><br /> <a href="https://github.com/SpaceMadness/lunar-unity-console">https://github.com/SpaceMadness/lunar-unity-console</a><br /> Yep, people have tried this before.
</p>

<p>
  To fix my problem I just needed to see logs so I went with something very similar to Consolation. It uses the old immediate mode Unity GUI calls. I didn&#8217;t care for that, pixel based sizes and <a href="https://docs.unity3d.com/Manual/class-GUISkin.html">GUI Skins</a> are not fun to work with. The idea is the same though, hook up <a href="https://docs.unity3d.com/ScriptReference/Application.LogCallback.html">Unity Log Callback</a> and append some strings to an auto scrolling <a href="https://docs.unity3d.com/ScriptReference/UI.Text.html">UI.Text</a>. I also made it fade out nicely with a <a href="https://github.com/dentedpixel/LeanTween">LeanTween</a>.
</p>

<p>
  It did let me find my problem, it was a file and directory formatting problem that meant a file was failing to be found on iOS. Fixed.
</p>

<p>
  I left the dev console enabled and found that I like having the logs visible during development. It makes me feel more aware of how the game is running and more connected to the code that I wrote. Rather than it feeling like<strong> </strong>a disconnected artifact of a build process. For this particular project I had already started building a developer only panel, that gives run-time access to a number of slider values and player resets and unlock shortcuts. So the logical step was to make this an actual console not just a way of seeing logs and move the functionality that I was manually building and adding to the . For this I looked to GConsole as it was simple to integrate and small enough that I could read all the code myself if it didn&#8217;t meet my needs. I quickly found that I wanted more from it.
</p>

<p>
  I want to keep working on this and add more features to it, no one of the consoles I found that already exists quite does all the things that I want.
</p>

<p>
  So here are some non-ordered requirements;
</p>

<ul>
  <li>
    See logs <ul>
      <li>
        preview as they come in
      </li>
      <li>
        expand console to scroll back through them
      </li>
    </ul>
  </li>
  
  <li>
    Run commands, eg quit app, load a scene, give player xp
  </li>
  <li>
    Inspect and change vars, eg player&#8217;s hp, gravity, spawn timer
  </li>
  <li>
    Inspect and change cl vars, eg resolution, vsync
  </li>
  <li>
    Get snapshots of data, eg. GameObject hierarchy, Active enemies
  </li>
  <li>
    Not have to reinvent all functionality that exists elsewhere just for the console <ul>
      <li>
        This is crucial, if functionality that already has to be manually wrapped or duplicated for the console to access it, then it just doesn&#8217;t get done as the cost of get that functionality wrapped and configurable by the console vs adding new features or bug fixing or polishing just doesn&#8217;t hold up.
      </li>
    </ul>
  </li>
</ul>

<p>
  Some examples of what we would like the console input and outputs to look like
</p>

<pre class="">//some desired gets
Physics.gravity
- 0.0, -9.81, 0.0
Player.hp
- 10
Application.loadedLevelName
- MainMenu

//they should also support sets like
Physics.gravity 0,-10,0
Render.vsync 0
Player.coins 1000</pre>
          
<p>
  GConsole has a simple to understand code base, part of how it achieves this is by only accepting <span class="pl-en">Func</span><<span class="pl-k">string</span>, <span class="pl-k">string</span>> as methods. This pushes the developer to wrap existing functionality in a function that takes a single string that could be anything from the user. I know that I&#8217;d get sick of writing string extraction and conversion code and wrapping existing functionality.
</p>

<p>
  CUDLR is awesome, it operates like a slim http server in your game that you write custom routes (routes a common web concept). It&#8217;s designed to be able to remotely debug and see the console via the browser, so locally or if you know the IP you can get the console logs and send commands and fetch routes. But it ships with no built in way of doing that from within the game itself which I&#8217;d want to do when I&#8217;m on a phone or tablet. CUDLR still gives a string[] as the parameter to it&#8217;s commands and would require custom wrapping of existing functions.
</p>

<p>
  I&#8217;m going to keep experimenting and pushing this forward. Should you wish to see some in progress code you can check out https://bitbucket.org/steve_halliwell/aid-common console stuff is currently in UnderConstruction/ConsoleHelper
</p>

<p>
  <a href="http://stevehalliwell.com/dev-console-part-2/">Part 2 here.</a>
</p>