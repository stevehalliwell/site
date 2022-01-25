---
id: 573
title: On Post-Mortems
date: 2015-05-05T12:27:26+10:00
author: Steve Halliwell
layout: post
guid: http://stevehalliwell.com/?p=573
permalink: /on-post-mortems/
enablewpts:
  - "0"
dsq_thread_id:
  - "4160677581"
et_enqueued_post_fonts:
  - 'a:2:{s:6:"family";a:2:{s:21:"et-gf-source-sans-pro";s:100:"Source+Sans+Pro:200,200italic,300,300italic,regular,italic,600,600italic,700,700italic,900,900italic";s:10:"et-gf-lato";s:75:"Lato:100,100italic,300,300italic,regular,italic,700,700italic,900,900italic";}s:6:"subset";a:7:{i:0;s:8:"cyrillic";i:1;s:5:"greek";i:2;s:10:"vietnamese";i:3;s:5:"latin";i:4;s:9:"greek-ext";i:5;s:9:"latin-ext";i:6;s:12:"cyrillic-ext";}}'
categories:
  - Blog
---
# The Goal

To improve future projects, for yourself and others. This goes beyond just not making the same mistakes again. It’s also about finding optimisations and efficiencies, the flip side, what was really good about the project and how can we ensure those good things occur again, more frequently, more reliably. You’ll propose changes to process, policy, behaviour, culture, technology, workflows, pipelines all with the goal of minimising undesirable traits and maximising the positive traits of the project (sprint, milestone, whatever) that just concluded.

# Mind set

If you’re having trouble nailing down how this should play out.

It’s part coroner in CSI. Stating observations. Explaining circumstances and investigating evidence. This is what happened and why it happened the way it did. What allowed for and caused this trait to manifest.

“Yes the impact killed them but without the preceding 20 story fall they’d be fine. Also this was no suicide, the heavy, pre-mortem bruising on the torso and lower back, indicate they were pushed backwards into and over the railing of the building.”

&nbsp;

It’s part mad scientist, making notes for the next round of tests. “The subject did exhibit the desired increased strength and reflexes, the increases in agent _Y_ and slight alterations in chemical formula from the previous round of testing have had their desired effect. Unfortunately the… overheating problem is still resulting in brain damage and spontaneous cardiac arrest during sustained exertion. We cannot lower the testing period any more without falling outside of given parameters, we either need to develop some sort of cooling harness for the subjects or alter the base formula or introduce some sort of temperature stabilizer to the mixture itself.” This comes in when it is unclear the causation of the trait you are examining, so then you are creating the boundaries for your proposal.

# Not a dev diary

They are useful, especially as a first step to reflection, but do not fall into the trap of writing a dev diary instead of a post mortem. There are a couple of big differences.

A dev diary is a record of things that happened, could be added features could be bugs found/fixed, could be anything really. It merely records them. Some of this will definitely overlap with a post mortem, you cannot make recommendations for the future without explaining the circumstance/event and reasoning behind said recommendation.

&nbsp;

Other part of the difference is the distinction between data and knowledge. If you are not familiar with this distinction check out the links below. The super condensed version is data is just an item or event, something which can be observed/recorded. Information is data collected and categorised/organised in some way that makes it useful. Knowledge is more meta, it is the relationships or insights or understanding that is taken from the information.

<http://www.cognitivedesignsolutions.com/KM/Understanding.htm>

<http://www.informationisbeautiful.net/2010/data-information-knowledge-wisdom/>

A Dev diary will tend towards data and information, a Post Mortem seeks to grow, foster and pass on knowledge or wisdom.

&nbsp;

I’m going to get a tad academic here for a moment, but don’t worry these concepts are also very applicable to games, the obvious ones being tutorials and skill atoms.

The cognitive dimension being used/displayed. A post mortem will require not just recollection and regurgitation of information. It requires reflection on yourself, the project as a product and the processes/procedures used during the project. It requires you to analyse that reflection for cause and effect. Why and what allowed/forced it to be that way. Then propose (create) solutions/recommendations based on those reflections and their causes/ramifications.

<http://www.celt.iastate.edu/teaching-resources/effective-practice/revised-blooms-taxonomy/>

&nbsp;

A dev diary is remembering and understanding. A Post mortem is across a broad range of these areas. It will most likely require application of conceptual knowledge of related areas to propose a solution, possibly even creation. Evaluation of known things and hypothetical things. Requiring reflection and self-knowledge. In short, pretty much every cell in the grid could be used in a non-trivial post-mortem. If you are doing a post mortem as a student, the person grading your post mortem will be looking for many if not all of those things on show in your writings (or conversations or vlogs or whatever your institute accepts as a submission).

# A contrived example

Let’s run through a simple scenario of falling over in the shower, in a couple of different ways, to try and highlight this difference.

&nbsp;

Example X

Issue: Fell during morning shower and injured left knee and left wrist.

Circumstance: Morning shower is part of routine.

Cause: Shower was slippery.

Recommendation: Be more cautious in the shower.

&nbsp;

Example Y

Issue: Fell during morning shower and injured left knee and left wrist.

Circumstance: Shower was more slippery than usual.

Cause: After further investigation it was found that the shower had been cleaned very recently, by a third party, making the surface unexpectedly slippery.

Recommendation: Be more cautious in the shower and check if the surface has been recently cleaned.

&nbsp;

Example Z

Issue: Fell during morning shower and injured left knee and left wrist.

Circumstance: Shower was more slippery than usual, it had been cleaned very recently making the surface unexpectedly slippery.

Cause: Poor communication, scheduling and signposting. Communication is a two way street, more pro-active communication is also required.

Recommendation: If cleaning is not already on a public schedule it should be, so all parties can be aware if they choose to be. When cleaning is happening and has recently occurred there should be a visual signpost, post-it note or similar there with time stamp. This will be better visual queue than the cleaning itself which may go unnoticed. This will also cover the eventuality that a cleaning occurs off schedule or parties are not present during the announcement of the commencement of cleaning.

&nbsp;

Example X falls into the ‘don’t work with anyone named bob’ category, it states something happened and that it was undesirable. It doesn’t explain how that came to pass or how to manage/avoid it in the future.

Example Y spends time explaining the surrounding events. It’s analysis and proposal just digs further in. It ultimately still doesn’t get the the root of the problem, it’s analysing symptoms of something we might actually be able to improve or fix.

Example Z states the shower as the event that displayed the problem, then analyses it to discover a cause underneath. You can think of this similar to Why is the kettle boiling? Is it because electrons are being forced through a metal coil with high resistance, resulting in heat which is then transferred to the water, or is it because you wanted a cup of tea. In a post mortem you probably want to state both of them. How it happened, why it happened and then propose management of it for future projects. Discussing if your proposals target the how or the why the event occurred.

# Check out other&#8217;s post mortems:

<http://www.pixelprospector.com/the-big-list-of-postmortems/>

<http://www.gamasutra.com/features/postmortem/>