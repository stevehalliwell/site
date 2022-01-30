---
id: 507
title: Parallax Offset
date: 2014-10-21T15:13:10+10:00
author: Steve Halliwell
layout: post
guid: http://stevehalliwell.com/?p=507
permalink: /parallax-offset/
enablewpts:
  - "0"
dsq_thread_id:
  - "4164203368"
et_enqueued_post_fonts:
  - 'a:2:{s:6:"family";a:2:{s:21:"et-gf-source-sans-pro";s:100:"Source+Sans+Pro:200,200italic,300,300italic,regular,italic,600,600italic,700,700italic,900,900italic";s:10:"et-gf-lato";s:75:"Lato:100,100italic,300,300italic,regular,italic,700,700italic,900,900italic";}s:6:"subset";a:7:{i:0;s:8:"cyrillic";i:1;s:5:"greek";i:2;s:10:"vietnamese";i:3;s:5:"latin";i:4;s:9:"greek-ext";i:5;s:9:"latin-ext";i:6;s:12:"cyrillic-ext";}}'
categories:
  - Blog
  - Portfolio
  - Unity
---
So sometime last week, while playing Substance Designer for a I’m working on, I put some height maps into Unity’s Parallax Bump Spec shader. This sent me down an interesting rabbit hole. First off Unity’s parallax is an interesting hack/shortcut.

<pre class="lang:default decode:true">half h = tex2D (_ParallaxMap, IN.uv_BumpMap).w;
float2 offset = ParallaxOffset (h, _Parallax, IN.viewDir);
IN.uv_MainTex += offset;
IN.uv_BumpMap += offset;

inline float2 ParallaxOffset( half h, half height, half3 viewDir )
{
	h = h * height - height/2.0;
	float3 v = normalize(viewDir);
	v.z += 0.42;
	return h * (v.xy / v.z);
}</pre>

&nbsp;

No parallax

![](/wp-content/uploads/2014/10/normalmaponly.jpg)

Unity&#8217;s parallax

![](/wp-content/uploads/2014/10/unity-para.jpg)

That’s it. Very cheap and for low heights the effect is still impressive. But at large heights and longer objects the textures begin to slide, thing start to look like they’re made out of chrome and their texture is actually the environment map. Why? Well its not how Parallax offset mapping/parallax occlusion mapping/Steep parallax offset mapping/relief mapping etc. work. They all rely on some form of ray marching through texels of a height field/map. Basically we have some starting point, the uv being render, we then move in the view direction across the surface of the texture incrementally. The distance per step is determined by viewing angle. Each step it samples height, if the height is less, we’ve intersected the height field surface. When it can be afforded a binary search is then performed between the previous and current sample to find a more accurate height for this frag, it greatly reduces height banding. This gives us a new ‘depth’ of the frag and a uv offset to sample all other maps with. here <http://graphics.cs.brown.edu/games/SteepParallax/> and here give good overviews <http://www.gamedev.net/page/resources/_/technical/graphics-programming-and-theory/a-closer-look-at-parallax-occlusion-mapping-r3262>

Parallax Offset

![](/wp-content/uploads/2014/10/para.jpg)

Well occlusion had to come next didn’t it. Its expensive. It uses the same principle. Once you find the offset, you then use the same ray marching ideas to move towards the surface of the height field in the direction of the light dir. If you make it to the surface without dipping below the height then not occluded, if you do hit something the light doesn’t reach you.

Smoothing these out was needed, I wanted to find a cheap way to remove the misses and the aliasing. My current method is to use lower mips. One of the recommendations for height map aliasing is smoother maps in the first place, which I have also played with a bit in my demo scene. Mips will smooth for me automatically (they’re already there I just needed to use them) and will still allow for extreme close ups to have that detail. Unfortunately it was not as easy as tex2dlod, unity surface shaders don’t support it it seems. But they do support tex2dGrad. This worked, much of the aliasing and sparkling was gone the very sharp edge remained. To smooth it (or rather make it less regular), compare the sample to that of an even lower lod and fade based on difference between the two.

Parallax Occlusion

![](/wp-content/uploads/2014/10/para-occ.jpg)

The solution is not overly accurate but it is smooth, consistent and predictable. There is little to no, sliding, popping or sparkling. Also it only works in dx11 mode in Unity. I wanted to see if it was possible using only Unity’s surface shaders instead of writing glsl or cg from scratch, it is but it means changing renderer causes it to fail, due to using tex2dgrad inside control flow statements: X6077. This strikes me as odd as I’m fairly sure what I’m doing is legal, esp since unity graphics emulation thinks it’ll work on openGL ES 2.0. It might be pure related to Unity’s conversion from surface shader to cg is renderer dependant and is stumbling somewhere.

Some of the problems with this method;  
Everything is pushed in (away from surface norm), so things shrink.  
For great heights texels on slopes get pulled in ways they were never intended to, could try to remedy this with tangent space tri-planar mapping, at least add some noise over the stretch.  
Its expensive, some quick numbers out of the Unity viewport, para occ is ~450fps, para without occ is ~650fps, Unity’s offset is 2000-400fps. That’s on a gtx670 in a window that is about 1600&#215;900.

After much fiddling with tuning paras, like min and max steps, I was happy… for a time. I wanted to see what else I could push with the other map types out of Substance. Ambient Occlusion was the next map of interest. I’ve previously used AO in substance as an information source for the diffuse map. I tried mixing AO with both depth and nDotL (normal . light). The results again and not accurate at all but the effect was surprisingly good, to me at least. It gave fake soft shadows across the surface.

Directional Ambient Occlusion

![](/wp-content/uploads/2014/10/amb-dir-only.jpg)

Final result.

![](/wp-content/uploads/2014/10/all.jpg)

This is by no means the best, most complete or most performant way to accomplish these tasks. Displacement mapping would most likely be much faster and produce smoother results.

&nbsp;

Different angle

Normal map only

![](/wp-content/uploads/2014/10/none-skew.jpg)

&nbsp;

Unity Parallax

![](/wp-content/uploads/2014/10/unity-skew.jpg)

&nbsp;

Parallax Occlusion with Directional Ambient

![](/wp-content/uploads/2014/10/para-all-skew.jpg)

&nbsp;