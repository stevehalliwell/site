---
id: 842
title: 'Dev Console Part 6: The Saga Continues'
date: 2020-06-03T10:43:56+10:00
author: Steve Halliwell
layout: revision
guid: http://stevehalliwell.com/836-revision-v1/
permalink: /?p=842
---
In the intervening time (years). CUDLR has become &#8216;deprecated&#8217;, it&#8217;s no longer being supported and there&#8217;s a few things we added to it anyway so. Let&#8217;s review the landscape again.

  * https://github.com/DylanYasen/unity-console
  * https://github.com/yasirkula/UnityIngameDebugConsole
  * https://github.com/stillwwater/command_terminal
  * https://github.com/mikelovesrobots/unity3d-console
  * https://github.com/gzuidhof/GConsole
  * https://github.com/pointcache/Unity3d-BeastConsole

A more crowded field but none doing exactly all the things I wanted. So we strip this back, re-org, let&#8217;s see what we have.

Ideally, I&#8217;d like to build directly off something existing like javascript, or lua, etc. Using Jint or Moonsharp. And both of those are actually very pleasant to do. Our refactor was very useful here, the DevConsole, the uGUI part required not changes just the binding class to route the call to the lua instance (Using [FungusLua](https://github.com/snozbot/fungus/wiki/fungus_lua), which is [MoonSharp](https://www.moonsharp.org/)) But I hit my head against the same problem in both of them, it&#8217;s the tab completion. They are introspective but not across interop, at least not simply or obviously. So I can add a c# object or static functions etc. to them but my autocomplete will not populate for them. For example exposing Time to lua, will show that there is an object called Time in auto completion but not any of its sub elements.

But that&#8217;s fine, we&#8217;ll return to the script execution desire at a later date. Our requirements haven&#8217;t really changed and it all still works, but with CUDLR being over, I don&#8217;t want to be maintaining the web side of it on my own.

What we want is <span style="background-color: #f4f4f4; font-family: 'Courier 10 Pitch', Courier, monospace; font-size: 11.2px;">Time.timeScale 1</span> to execute, autocomplete and maybe populate a help message. A single console/terminal, UI as a separate concern to the command storage, binding, and execution. Simple binding of static methods static variables objects, and bind object instance fields, props and members under a container. We&#8217;d already built up a lot on top of CUDLR, type binding, and uGUI interface, so we get rid of the server and all the supporting elements and were still running and not really CUDLR anymore.

At present we&#8217;re hiding the conversion checks inside the binding function and we&#8217;re using object boxing to have a generic in and out. We could use hardwiring or perhaps [c#&#8217;s upcoming code generators](https://devblogs.microsoft.com/dotnet/introducing-c-source-generators/) to clean this up in the future.

This is meant for unity, so I wanted to try using UPM. Unity&#8217;s package manager has support for adding a required package via giturl and there&#8217;s a [great guide here](https://medium.com/openupm/how-to-maintain-upm-package-part-1-7b4daf88d4c4) talking through the setup and automation of the conversion to the layout that Unity expects. Working as an [embedded package](https://docs.unity3d.com/Manual/upm-embed.html), was recommended for development as you get the &#8216;same&#8217; results as users of the package. If trying this yourself be aware that there is some fiddlyness in the initial setup, folder names, and which parts of the package.json are required to load required some manual file changes as Unity may unload them if it thinks they are not configured correctly. Similarly if an assembly within a package fails to compile it appears to be able to do so silently.

Its live on github https://github.com/stevehalliwell/Hug with instructions for using via UPM. There&#8217;s certainly more to do, it&#8217;s still in development, if you try it out and find bugs or have suggestions I&#8217;d love to hear them in the Issues section on github.