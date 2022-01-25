---
id: 534
title: Shader updates and inspectors
date: 2014-11-11T21:05:43+10:00
author: Steve Halliwell
layout: revision
guid: http://stevehalliwell.com/524-revision-v1/
permalink: /?p=534
---
It&#8217;s been a busy few weeks. I&#8217;ve been knocked out a bit with a cold/flu thing and PAX. So there&#8217;s not as much to report as I would like but here&#8217;s the highlights.

PAX was great. Being in the bigger venue meant we could actually get to panels without waiting multiple hours in the cold and rain. Got to see lots of panels with indie devs and talk to them on the show floor. Numerous people told me GCAP is probably what I actually wanted to go to and their not wrong. It just wasn&#8217;t going to work out this year. Next year hopefully.

# Shaders

[<img loading="lazy" class="alignnone size-large wp-image-525" src="http://stevehalliwell.com/wp-content/uploads/2014/11/updated-substances-and-all-the-maps-shader-1024x577.jpg" alt="updated substances and all the maps shader" width="640" height="360" srcset="http://stevehalliwell.com/wp-content/uploads/2014/11/updated-substances-and-all-the-maps-shader-1024x577.jpg 1024w, http://stevehalliwell.com/wp-content/uploads/2014/11/updated-substances-and-all-the-maps-shader-300x169.jpg 300w" sizes="(max-width: 640px) 100vw, 640px" />](http://stevehalliwell.com/wp-content/uploads/2014/11/updated-substances-and-all-the-maps-shader.jpg)

I&#8217;ve updated the shader in a bunch of ways. It&#8217;s more optimised, previously it was just using a separate texture for each map, now its all packed in channels. Considerably less total gpu ram usage and runs faster, few things that were previously floatx and how just single floats and things that used to be separate samples are now just swizzles. This simple sample scene is running comfortable at hunders of frames even on my mac air.

[<img loading="lazy" class="alignnone size-medium wp-image-526" src="http://stevehalliwell.com/wp-content/uploads/2014/11/ao-sample-grey-scale-300x139.jpg" alt="ao sample grey scale" width="300" height="139" srcset="http://stevehalliwell.com/wp-content/uploads/2014/11/ao-sample-grey-scale-300x139.jpg 300w, http://stevehalliwell.com/wp-content/uploads/2014/11/ao-sample-grey-scale.jpg 839w" sizes="(max-width: 300px) 100vw, 300px" />](http://stevehalliwell.com/wp-content/uploads/2014/11/ao-sample-grey-scale.jpg)

Grey AO

[<img loading="lazy" class="alignnone size-medium wp-image-527" src="http://stevehalliwell.com/wp-content/uploads/2014/11/ao-sample-colour-300x140.jpg" alt="ao sample colour" width="300" height="140" srcset="http://stevehalliwell.com/wp-content/uploads/2014/11/ao-sample-colour-300x140.jpg 300w, http://stevehalliwell.com/wp-content/uploads/2014/11/ao-sample-colour.jpg 835w" sizes="(max-width: 300px) 100vw, 300px" />](http://stevehalliwell.com/wp-content/uploads/2014/11/ao-sample-colour.jpg)

Coloured AO

I was talking to Tony at work about what I&#8217;d been messing with and he mentioned that it really bugged him that AO was always grey scale. So I have a first draft coloured AO in the shader and a node to generate it from diffuse, ao and height in Substance Designer. The early results are interesting, its kind of a very local faked GI or PRT. In any event I like the way it looks, it makes everything appear much richer instead of washed out.

[<img loading="lazy" class="alignnone size-full wp-image-528" src="http://stevehalliwell.com/wp-content/uploads/2014/11/custom-material-inspector.jpg" alt="custom material inspector" width="372" height="514" srcset="http://stevehalliwell.com/wp-content/uploads/2014/11/custom-material-inspector.jpg 372w, http://stevehalliwell.com/wp-content/uploads/2014/11/custom-material-inspector-217x300.jpg 217w" sizes="(max-width: 372px) 100vw, 372px" />](http://stevehalliwell.com/wp-content/uploads/2014/11/custom-material-inspector.jpg)

Next up was a <a href="http://docs.unity3d.com/Manual/SL-CustomMaterialEditors.html" target="_blank">custom material inspector for the shader</a>, just so it can hide properties that aren&#8217;t being used, eg. here lightramp textures are not shown as they are not turned on, same with occlusion strength. Also I wanted to have both sliders and numbers showing for float ranges.

Also created a simple light ramp and gradient ramp texture creator (the yellow and blue ball above is using a dual colour ramp shader) so I didn&#8217;t have to keep going back n forth to photoshop etc. to create ramps. This is done by creating a <a href="http://docs.unity3d.com/Manual/editor-EditorWindows.html" target="_blank">custom editor window</a>.

[<img loading="lazy" class="alignnone size-full wp-image-529" src="http://stevehalliwell.com/wp-content/uploads/2014/11/texture-ramp-creator.jpg" alt="texture ramp creator" width="409" height="151" srcset="http://stevehalliwell.com/wp-content/uploads/2014/11/texture-ramp-creator.jpg 409w, http://stevehalliwell.com/wp-content/uploads/2014/11/texture-ramp-creator-300x110.jpg 300w" sizes="(max-width: 409px) 100vw, 409px" />](http://stevehalliwell.com/wp-content/uploads/2014/11/texture-ramp-creator.jpg)

&nbsp;

# Generators

I wanted to be able to chain together and merge different patterns and randoms etc. to generate dynamic anim curve style data. Specifically so I could not have to create custom animations for things like moving/flickering lights in the scene I&#8217;m working on. My first attempt worked great and code made sense and was a small and easy to maintain and extend. Unfortunately the way I was using inher didn&#8217;t play nice with unity inspector. So I redeveloped it as flat (and right now just a single class). The code is not as nice (it&#8217;s not soul code anymore) but it is entirely configurable from within the unity inspector.

[<img loading="lazy" class="alignnone size-full wp-image-530" src="http://stevehalliwell.com/wp-content/uploads/2014/11/custom-generator-inspector.jpg" alt="custom generator inspector" width="351" height="786" />](http://stevehalliwell.com/wp-content/uploads/2014/11/custom-generator-inspector.jpg)

This required both a <a href="http://unity3d.com/learn/tutorials/modules/intermediate/editor/building-custom-inspector" target="_blank">custom inspector</a> for GenerateDriver class and <a href="http://docs.unity3d.com/Manual/editor-PropertyDrawers.html" target="_blank">PropertyDrawer </a>for Generator class. I&#8217;m particularly fond if the real time preview. This head bob a few of these moving different local pos axis and lerping between idle and moving bob based on velocity, ~10 lines.

[<img loading="lazy" class="alignnone size-full wp-image-531" src="http://stevehalliwell.com/wp-content/uploads/2014/11/headbob_generator.gif" alt="headbob_generator" width="443" height="253" />](http://stevehalliwell.com/wp-content/uploads/2014/11/headbob_generator.gif)

&nbsp;

Finally here is some very early stuff I&#8217;ve been working on for the Red Room. Mostly just upping my proficiency with Substance Designer.

[<img loading="lazy" class="alignnone size-large wp-image-532" src="http://stevehalliwell.com/wp-content/uploads/2014/11/redroomwall_preview-1024x576.jpg" alt="redroomwall_preview" width="640" height="360" srcset="http://stevehalliwell.com/wp-content/uploads/2014/11/redroomwall_preview-1024x576.jpg 1024w, http://stevehalliwell.com/wp-content/uploads/2014/11/redroomwall_preview-300x168.jpg 300w, http://stevehalliwell.com/wp-content/uploads/2014/11/redroomwall_preview.jpg 1459w" sizes="(max-width: 640px) 100vw, 640px" />](http://stevehalliwell.com/wp-content/uploads/2014/11/redroomwall_preview.jpg)

[<img loading="lazy" class="alignnone size-large wp-image-533" src="http://stevehalliwell.com/wp-content/uploads/2014/11/redroomfloor_preview-1024x568.jpg" alt="redroomfloor_preview" width="640" height="355" srcset="http://stevehalliwell.com/wp-content/uploads/2014/11/redroomfloor_preview-1024x568.jpg 1024w, http://stevehalliwell.com/wp-content/uploads/2014/11/redroomfloor_preview-300x166.jpg 300w, http://stevehalliwell.com/wp-content/uploads/2014/11/redroomfloor_preview.jpg 1461w" sizes="(max-width: 640px) 100vw, 640px" />](http://stevehalliwell.com/wp-content/uploads/2014/11/redroomfloor_preview.jpg)

&nbsp;