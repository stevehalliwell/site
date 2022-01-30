---
id: 446
title: Unity Optimisation Basics Part 2
date: 2014-07-13T21:47:25+10:00
author: Steve Halliwell
layout: post
guid: http://stevehalliwell.com/?p=446
permalink: /unity-optimisation-basics-part-2-2/
enablewpts:
  - "0"
dsq_thread_id:
  - "4163774413"
et_enqueued_post_fonts:
  - 'a:2:{s:6:"family";a:2:{s:21:"et-gf-source-sans-pro";s:100:"Source+Sans+Pro:200,200italic,300,300italic,regular,italic,600,600italic,700,700italic,900,900italic";s:10:"et-gf-lato";s:75:"Lato:100,100italic,300,300italic,regular,italic,700,700italic,900,900italic";}s:6:"subset";a:7:{i:0;s:8:"cyrillic";i:1;s:5:"greek";i:2;s:10:"vietnamese";i:3;s:5:"latin";i:4;s:9:"greek-ext";i:5;s:9:"latin-ext";i:6;s:12:"cyrillic-ext";}}'
categories:
  - Blog
  - Unity
---
# Math is hard

Currently in JSD, the Seeking missiles are taking up WAY more cpu per frame than I would have anticipated. As much as 30% of the total cpu load per frame when there&#8217;s about 40-60 of them active. This is pretty much all its doing.

<pre class="lang:c# decode:true">//increment 2 timers

if(timer 1)
{
    deactivate self
    activate effect
}

if(no target)
{
    range rand on array
}

if(has valid target)
{
   get pos
   update effect
}

2d rotate at speed to face target
translate</pre>

Part of the problem is gameobject compares aren&#8217;t trival, <a class="vt-p" href="http://blogs.unity3d.com/2014/05/16/custom-operator-should-we-keep-it/" target="_blank">see</a>. The  translate and smooth rotate, are expensive. For almost all of the other objects in the game these happen during enable and never again. If I made the missile a &#8216;plasma ball&#8217; then I could simplify the maths a bit here by not needing to update the orientation of the object .

# SetActive()

I was doing a little bit of hunting around to find more on the cost of GameObject.Activate, which gets hit in the profiler for SetActive(true). I found <a class="vt-p" href="http://answers.unity3d.com/questions/58884/instantiate-vs-gameobjectactivetrue.html" target="_blank">this</a>. Needless to say I had to test this theory that enable might be better than active. So Lets dig in.

This section is 3 methods running simultaneously so you can visualise the ratio of performance they require.

First up Instantiate and Destroy 100 simple boxes with RigidBody every frame.
![](/wp-content/uploads/2014/07/all_instHiLi.gif)
![](/wp-content/uploads/2014/07/all_destHiLi.gif)
I&#8217;ve highlighted the Destroy here too. Its separate as Destroy seems to add the item to a list Unity keeps internally and handles next frame (as I understand it.). Most of the memory here seems to be in native land not in GC land but we are still generating some garbage. Its also responsible for a big chunk of the &#8216;Other&#8217; time, which I can only assume is Unity native code creating the GO, components and serialise, deserialising from the prefab.

Next, Deactivate 100 in a list, activate the next 100 in the list. This occilates each frame. This is the basics of an Object Pool.

![](/wp-content/uploads/2014/07/all_activateHiLi.gif)
Not a crazy amount faster but it is faster and there is no Destroy and no GC to speak of.

Ok lets try this enable disable trick.

![](/wp-content/uploads/2014/07/all_endisHiLi.gif):O

&nbsp;

Lets see them in isolation.

Instantiate and Destroy &#8211; 9.3ms to 10.3ms

![](/wp-content/uploads/2014/07/solo_instNoGC.gif)Inst without the GC showing

![](/wp-content/uploads/2014/07/solo_instGC.gif)Inst WITH the GC showing, ouch.

&nbsp;

Activate/Deactivate &#8211; 6.7ms to 8.9ms

![](/wp-content/uploads/2014/07/solo_act.gif)Not as good as we&#8217;d like but definitely better and no GC.

&nbsp;

Enable/Disable on collider and renderer &#8211; 2.5ms to 3.0ms

![](/wp-content/uploads/2014/07/solo_endisMinamal.gif)Little bit of GC but this is insanely good, what&#8217;s going on.

Enable/Disable Generlised &#8211; 8.3ms &#8211; 15ms  
Here I add the 2d variants, audio and animator components to the mix to create a more general solution, not the object being made just to the enable disable code, as activate/deactivate and instantiate/destroy would handle any combo and any number of components. In real world it would need any scripts you&#8217;ve added to the component to be disabled too, most likely.

![](/wp-content/uploads/2014/07/solo_endisGeneral.gif)

GC is going crazy due to all of the GetComponent, remember that .rigidbody or .audio etc. are properties that basically end up calling GetComponent.

Ok, so once we try to extend the enable/disable trick to a more general solution it becomes worse than just instantiating and destroying. It is a good trick to remember though, if you had a very simple object it is a far more surgical method.

For the sake of science I tested this same set up for all modes with 10 and 500 cubes. Physics engine didn&#8217;t much care for 500 each, it actially made GameObject.Activate way more expensive than anything else. But the test was running at fractions of an fps due to way too many overlapping cubes.

With 10, it tells a different story.
![](/wp-content/uploads/2014/07/all_10obj.png)

Yes the order of best is the same, Inst about .8ms, Act about 0.7ms, Enab about 0.4ms. But those spikes, those spikes are where the Enable/Disable takes 7ms, yes SEVEN up from 0.4. It&#8217;s entirely down to it triggering a GC collect.

What if it only ever asks for things that do exist? I tried it with a few empty component scripts I create, no more GC. Remove them from the prefab, GC comes back. So if it only ever looks for components that do exist then its faster with no down sides. Its worth investigating a custom per prefab pool that uses this knowledge.

Well now I have to test what a find all Behaviours will do&#8230; Well that kinda sucks. Collider and Renderer are not behaviours, so you need to special case them. Something like

<pre class="lang:c# decode:true">Component[] comps = go.GetComponents&lt;Component&gt;();

foreach(Component c in comps)
{
	Behaviour asBeh = c as Behaviour;
	if(asBeh != null)asBeh.enabled = state;
	else{
		Collider asCol = c as Collider;
		if(asCol != null)asCol.enabled = state;
		else{
			Renderer asRend = c as Renderer;
			if(asRend != null) asRend.enabled = state;
		}
	}
}</pre>

But that totally works and is nicer to look at, kinda. It runs at 0.3ms average but those spikes don&#8217;t go away, due to us now using GetComponents alloc&#8217;ing an array.

# So not set active?

Well no, its a general solution that handles all cases, is mostly unintrusive to your codebase and can easily be written to never incur any GC Alloc. More over, its worst case is far more predictable and acceptable than the others in their general usage. Given that the GC is mostly out of our control and Unity doesn&#8217;t provide a no alloc way of fetching components the we cannot control the perf spikes due to the garbage being created. So unless you have a very specific set of components that you know exist and can access directly or already have around, you probably don&#8217;t want to enable/disable. All that being said, I&#8217;ve added &#8216;Intrustive enable disable pool object&#8217; to my todo list.

# Don&#8217;t do things you don&#8217;t need to

This isn&#8217;t at all Unity specific, its just general programming. What&#8217;s the best way of optimising &#8216;this&#8217; code? Not doing it at all. For example, in some of my testing I wrote this;

<pre class="lang:c# decode:true">if(go.renderer != null) go.renderer.enabled = state;
if(go.collider != null) go.collider.enabled = state;
if(go.collider2D != null) go.collider2D.enabled = state;
if(go.guiText != null) go.guiText.enabled = state;
if(go.audio != null) go.audio.enabled = state;
if(go.camera != null) go.camera.enabled = state;
if(go.animation != null) go.animation.enabled = state;
</pre>

Works and looks fine. But it&#8217;s (potentially) doing twice as many GetComponents as it needs to. Changing it to this

<pre class="lang:c# decode:true">Renderer ren = go.renderer;
Collider col = go.collider;
Collider2D col2D = go.collider2D;
GUIText gt = go.guiText;
AudioSource au = go.audio;
Camera cam = go.camera;
Animation an = go.animation;

if(ren != null) ren.enabled = state;
if(col != null) col.enabled = state;
if(col2D != null) col2D.enabled = state;
if(gt != null) gt.enabled = state;
if(au != null) au.enabled = state;
if(cam != null) cam.enabled = state;
if(an != null) an.enabled = state;
</pre>

gave a non-trival performance improvement. Doesn&#8217;t lower GC Alloc as this seems to only come from requesting a Component that does not exist. It&#8217;s the same reason Unity recommends that you grab local references to any and all components to you need to access in your scripts during Start or OnEnable instead of during Update, FixedUpdate, etc.