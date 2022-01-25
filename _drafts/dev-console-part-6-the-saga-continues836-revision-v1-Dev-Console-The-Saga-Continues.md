---
id: 838
title: 'Dev Console: The Saga Continues'
date: 2020-05-25T05:42:38+10:00
author: Steve Halliwell
layout: revision
guid: http://stevehalliwell.com/836-revision-v1/
permalink: /?p=838
---
In the intervening time (years). CUDLR has become &#8216;deprecated&#8217;, it&#8217;s no longer being supported and there&#8217;s a few things we added to it anyway so. Let&#8217;s review the landscape again.

<insert updated links to new console projects>

A more crowded field but none doing exactly all the things I wanted. So we strip this back, re-org, let&#8217;s see what we have.

Ideally, I&#8217;d like to build directly off something existing like javascript, or lua, etc. Using Jint or Moonsharp. And both of those are actually very pleasant to do. But I hit my head against the same problem in both of them. They are introspective but not across interop, at least not simply or obviously. So I can add a c# object or static functions etc. to them but my autocomplete will not populate for them. But that&#8217;s fine, we&#8217;ll return to the script execution desire at a later date. What we want is

<pre class="lang:lua decode:true">Time.timeScale 1</pre>

To execute, autocomplete and maybe populate a help via a tooltip.

We want a single console/terminal, we want the ui as a separate concern to the command storage, binding, and execution.

&nbsp;