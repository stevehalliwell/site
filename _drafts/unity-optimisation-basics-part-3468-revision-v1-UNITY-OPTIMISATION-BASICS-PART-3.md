---
id: 472
title: UNITY OPTIMISATION BASICS PART 3
date: 2014-07-23T21:19:13+10:00
author: Steve Halliwell
layout: revision
guid: http://stevehalliwell.com/468-revision-v1/
permalink: /?p=472
---
# Tags

Which of the following should execute faster?

<pre class="lang:default decode:true">if(gameObject.tag == "Player")
if(gameObject.tag == "Player".GetHashCode())
if(gameObject.tag == toCompare)
if(gameObject.tag.GetHashCode() == hashToCompare)
if(gameObject.CompareTag(toCompare))</pre>

If you guessed any of them, you are wrong. That&#8217;s right, calling them 10000 times a second there is no single method that consistently executes faster than the others. (Not entirely true but I&#8217;m making a point here.)

So the compiler optimised out the literal string for us, that&#8217;s good to know. Lets look at the profile results.

[<img loading="lazy" class="alignnone size-full wp-image-469" src="http://stevehalliwell.com/wp-content/uploads/2014/07/tagtest.png" alt="tagtest" width="686" height="361" srcset="http://stevehalliwell.com/wp-content/uploads/2014/07/tagtest.png 686w, http://stevehalliwell.com/wp-content/uploads/2014/07/tagtest-300x157.png 300w" sizes="(max-width: 686px) 100vw, 686px" />](http://stevehalliwell.com/wp-content/uploads/2014/07/tagtest.png){.vt-p}

J&#8217;accuse. Everything is allocing but CompareTag, the method in gameobject that is recommended by Unity as the method to, you guessed it, compare tags. Where at all possible use CompareTag. the GC is your enemy. It&#8217;s an interesting situation. The alloc comes from get_tag(), which is triggered by the .tag property, which could be a call to unity native code that results in a mono string being created or just due to the immutable nature of strings in mono. I know I&#8217;m guilty of it too but if you can find a .tag anywhere in your code base do your darnedest to get rid of it.

# Strings

Speaking of strings. String concat or string.Format or StringBuilder for simple wave counter in JSD.

<pre class="lang:default decode:true">//sb is a StringBuilder
sb.Length = 0;
sb.Append(WaveString);	//"Wave: "
sb.Append(lvl.curIndex);
sb.Append(post); //" of "
sb.Append(lvl.infos.Count);
txt.text = sb.ToString();

txt.text = "Wave: " + (lvl.curIndex).ToString() + " of " + lvl.infos.Count.ToString();

txt.text = string.Format("Wave: {0} of {1}", lvl.curIndex, lvl.infos.Count);</pre>

Format looks nicer, kinda, probably a lot if you like printf.

[<img loading="lazy" class="alignnone size-full wp-image-470" src="http://stevehalliwell.com/wp-content/uploads/2014/07/stringtest.png" alt="stringtest" width="673" height="324" srcset="http://stevehalliwell.com/wp-content/uploads/2014/07/stringtest.png 673w, http://stevehalliwell.com/wp-content/uploads/2014/07/stringtest-300x144.png 300w, http://stevehalliwell.com/wp-content/uploads/2014/07/stringtest-672x324.png 672w" sizes="(max-width: 673px) 100vw, 673px" />](http://stevehalliwell.com/wp-content/uploads/2014/07/stringtest.png){.vt-p}

Well crap, Format is out, it&#8217;s the slowest and the most trashy. StringBuilder vs regular concatenation is a bit trickier. For my purposes I can avoid calling the function for all but when the current wave actually changes, so less than once a second, keeping the alloc as low as possible suits me.

&nbsp;