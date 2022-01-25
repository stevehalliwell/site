---
id: 231
title: Just Shmup Dammit PA3
date: 2014-01-08T17:08:38+10:00
layout: revision
guid: http://stevehalliwell.com/193-revision-v1/
permalink: /?p=231
---
Just pushed out Alpha 3 update last night. So I had to rewrite the upgrade storage system because it was driving me crazy not its basically a readonly Dict<string, double[]> stored for look up. Player stats then look at this and store a local cached copy appropriate for their level and only look at that again when they level up a stat. Much easier to work with.

[<img loading="lazy" class="alignnone size-medium wp-image-194" alt="2013-11-27 10.15.17" src="http://stevehalliwell.com/wp-content/uploads/2013/11/2013-11-27-10.15.17-168x300.png" width="168" height="300" srcset="http://stevehalliwell.com/wp-content/uploads/2013/11/2013-11-27-10.15.17-168x300.png 168w, http://stevehalliwell.com/wp-content/uploads/2013/11/2013-11-27-10.15.17-576x1024.png 576w, http://stevehalliwell.com/wp-content/uploads/2013/11/2013-11-27-10.15.17-624x1109.png 624w, http://stevehalliwell.com/wp-content/uploads/2013/11/2013-11-27-10.15.17.png 720w" sizes="(max-width: 168px) 100vw, 168px" />](http://stevehalliwell.com/wp-content/uploads/2013/11/2013-11-27-10.15.17.png)

Also got rid of the intuition numbers and am now pulling data out of calculated [spreadsheet data](https://docs.google.com/spreadsheet/ccc?key=0AnIiDGqoOqoSdER6RmNFc25ObzJQRzVLbTZPazJQSnc&usp=sharing). And moved to Unity 4.3, so now all the bullets are sprites not just quads. It didn&#8217;t really seem to change the median performance but it may have smoothed it out a bit.

Missiles now indicate their target enemy with a blue laser sight and they slow down over time, which leads into the 2 new upgrades missile speed and missile agility.

Slomo indicator, hourglass on the left, so that you can tell at what speed and if you have slomo activated. Also smoothed out the timescale changes so it doesn&#8217;t jump from 1 to .6 in 1 frame, now it lerps between them.

On the horizon: facebook integration, analytics, more meshes, more textures, more bullet patterns, landscape support.