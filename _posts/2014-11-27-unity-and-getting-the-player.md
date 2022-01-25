---
id: 536
title: Unity and getting The Player
date: 2014-11-27T08:37:03+10:00
author: Steve Halliwell
layout: post
guid: http://stevehalliwell.com/?p=536
permalink: /unity-and-getting-the-player/
enablewpts:
  - "0"
dsq_thread_id:
  - "4229660796"
et_enqueued_post_fonts:
  - 'a:2:{s:6:"family";a:2:{s:21:"et-gf-source-sans-pro";s:100:"Source+Sans+Pro:200,200italic,300,300italic,regular,italic,600,600italic,700,700italic,900,900italic";s:10:"et-gf-lato";s:75:"Lato:100,100italic,300,300italic,regular,italic,700,700italic,900,900italic";}s:6:"subset";a:7:{i:0;s:8:"cyrillic";i:1;s:5:"greek";i:2;s:10:"vietnamese";i:3;s:5:"latin";i:4;s:9:"greek-ext";i:5;s:9:"latin-ext";i:6;s:12:"cyrillic-ext";}}'
categories:
  - Blog
  - Unity
---
So I saw something very much like

<pre class="lang:c# decode:true">if(renderer.isVisible)
{
    GameObject.FindGameObjectWithTag(“Player”).GetComponent&lt;Player&gt;().somebool = true;
}

if(!renderer.isVisible)
{
    GameObject.FindGameObjectWithTag(“Player”).GetComponent&lt;Player&gt;().somebool = false;
}

</pre>

A few things first. If its performant then it’s ok, sure. I don’t have the Unity source code so I can only infer things from what they have talked about.

Lets psuedo what this is probably trying to do

<pre class="lang:default decode:true ">if the player is visible/on screen
    disable something in them
else
    enable that thing in them</pre>

&nbsp;

Now lets create the psuedo for what is actually happening

<pre class="lang:default decode:true">tmp = linear search through all this game objects components for something base typed Renderer
if tmp is visible
    tmpGO = Linear search through all GOs in scene for one with tag Player
    tmpComp = linear search through all components in tmpGO or something of type Player
    tmpComp somebool to false</pre>

That’s pretty expensive and not what we actually wanted.

Lets talk the quick n dirty way to make this a bit more like what the original pseudo intended. The original code indicates that there is only 1 player and we want access to it somewhere else. Sounds a lot like the exact conditions for a singleton. For a MonoBehaviour we can do this in a number of ways. We’ll discuss more robust solutions another time. So the least robust but quickest is.

In the Player.cs

<pre class="lang:c# decode:true ">public static Player inst;

void Awake()
{
    inst = this;
}</pre>

&nbsp;

and then the orig code becomes

<pre class="lang:c# decode:true">if(renderer.isVisible)
{
    Player.inst.somebool = true;
}


if(!renderer.isVisible)
{
    Player.inst.somebool = false;
}</pre>

&nbsp;

Further

<pre class="lang:c# decode:true">Player.inst.somebool = renderer.isVisible;</pre>

&nbsp;

If somebool is a property and not a field or has knock on effects, we perhaps even go further and look at using the OnBecameVisible, OnBecameInvisible messages.