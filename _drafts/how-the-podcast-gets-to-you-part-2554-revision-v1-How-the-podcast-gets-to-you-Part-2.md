---
id: 558
title: How the podcast gets to you, Part 2
date: 2015-02-23T11:01:59+10:00
author: Steve Halliwell
layout: revision
guid: http://stevehalliwell.com/554-revision-v1/
permalink: /?p=558
---
In part 1 I touched on the website the recording process, this time editing a processes.

# Editing

After recording I have 2ish gig of wav files on the mac book air. I take this home after recording finishes and copy all the raw data into a new ISO standard date format folder on my NAS. I won&#8217;t edit tonight as that takes up to an hour and its after mid night normally at this point.

When it comes time to actually edit, I get all the raw wavs on my pc, copied from the NAS. As mentioned previously there&#8217;s are in parts, broken up so I know where to edit, just have to cut top n tail. I record all the warm up and prep time. Why? Well some of it may be relevant to future discussions but actually it&#8217;s because sometimes its quite funny and I want to make sure we have it for future use. When recording we all simultaneously clap once, to mark the start and end of &#8216;for real&#8217; parts of the recording, this makes it easier to spot in editing and gives us a point of mentality shift, from warmin up to radio time.

Steps for editing in Adobe Audition

  1. Find sample of background noise only, normally easy to find immediately before the clap. Sample and apply removal to entire file.
  2. Find starting clap, select from beginning of file to just after clap, save selection as _pre and then delete it.
  3. Find ending clap, select it and delete. No need to save as this is normally only seconds long and is just a recording of me getting up to stop the recording and restart it.
  4. Depending on the recording at this point I might use the DePopper, DeClipper and DeEsser
  5. Now I have a de-noised sample of just the podcast content but it&#8217;s all over the place. Aaron is super loud, Tony and I are quiet. I have a favourite set up for doing a bunch of processing in 1 button it does&#8230;
  6. Normalisation
  7. Eq &#8211; very slight boost to low and high freq, makes voice sound fuller
  8. Dynamics processing &#8211; making quiet things louder
  9. After those are complete, its time for Match Volume. So loudness is actually quite tricky, I&#8217;ll leave it up to you, dear reader, to look up LUFS. After some research I found the loudness for most podcasts is -18 LUFS. So we use that too.

After all that I have 1 section, normally the discussion of 1 game, ready. Repeat for each section normally 4, 1 for each game and the wrap up. Most of those steps are very quick, Match Volume, Noise Removal and the De* can take minutes each as they are more intensive.

Now comes Merge. I start a multitrack session place each clip 1 after the other in order and then export the multitrack session to file, a wave.

With this giant wave file of the entire podcast I now run the delete silence process over it. I have it set up to look for silences longer than 1 seconds and reduce them to half a second. This removes gaps between my top n tail cuts and any long pauses in the conversation where one of us, normally me, is collecting thoughts before responding.

Now export as mp3. 96kb is more than enough for our voices. Unfortunately Audition doesn&#8217;t seem to allow me to edit the id3 tag, so I have a separate program for editing the mp3 tags. Then the edited wavs and the mp3 are copied back to the NAS in their respective folders and the mp3, which is normally around 100meg is uploaded via ftp to our hosting. Given my woeful upload speed this takes about 1.5hrs.

# Processes

We don&#8217;t currently have a LOT of stuff that we have to do each week to get this thing out the door. But even with so little we&#8217;ve got some very clear processes we go through each week. We have a shared google drive, that houses business related documents, the random rolls and a folder for each of us to take notes about the game we play that week, we don&#8217;t peek in each other&#8217;s folder.

Everything we post via wordpress is cross posted to our twitter and our fb page. This is normally a podcast+post on sunday night and a mid week post revealing the games for the upcoming week. For each of these we use the policy of &#8216;eyes on&#8217;. We start the post and save it as draft and notify the others that they need to check it. By that we mean we&#8217;ll read over it, make changes as required. This catches spelling mistakes and means that nothing is first draft. If I start the post then both Tony and Aaron will have made changes and bettered it by the time I look at it again before posting. It also greatly helps in terms of keeping a unified voice of &#8216;The1st10minutes&#8217; so things don&#8217;t obviously feel like Steve wrote it as opposed to Tony etc.

For actual content, we know what games each of us are playing but we stop each other from talking about it before we go to record, thus &#8220;Save it for the podcast, buddy!&#8221;

We start a timer for 10 minutes from the moment we hit New Game, in whatever it is we are playing. Failing bizarre circumstance we play it as the developer intended. When the timer goes off, we stop playing and start taking notes. Normally we use OBS to record the play through so we can scrub back through if need be to refresh our memory and fact check. As for notes we keep developer, year of release, platforms, publisher. Apart from that we all take notes a little bit differently. I scribble down taking points, things that I want to mention, how long till I was in control or things that jump to mind on reflection.

That&#8217;s about it for now, I&#8217;m sure there&#8217;ll be more soon.