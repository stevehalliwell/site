---
id: 886
title: Particles and Fluids
date: 2020-09-10T20:27:59+10:00
author: Steve Halliwell
layout: revision
guid: http://stevehalliwell.com/849-revision-v1/
permalink: /?p=886
---
OK, so years ago, I was playing around with a concept based on a control via vector fields. The player avatar was a floating particle/light mote. The player moved by dragging their finger(s) on device or mouse cursor, mobile was the target platform, this motion would add velocity to the underlying vector field and cause the particle/light mote to move. This also included bi-directional influence of particles. Meaning the velocity of every particle would have a small impact on the underlying vector field and vice versa, a particle would sample the underlying vector field and attempt to match velocity.

<div style="width: 640px;" class="wp-video">
  <video class="wp-video-shortcode" id="video-886-57" width="640" height="360" preload="metadata" controls="controls"><source type="video/mp4" src="http://stevehalliwell.com/wp-content/uploads/2020/08/particles_demo_orig.mp4?_=57" /><a href="http://stevehalliwell.com/wp-content/uploads/2020/08/particles_demo_orig.mp4">http://stevehalliwell.com/wp-content/uploads/2020/08/particles_demo_orig.mp4</a></video>
</div>

The original, manual thread world load method.

It had a couple of nice side effects, you could create currents by simply making particle systems emit in certain shapes and velocities. I also could sample and write back physics objects too, allowing shapes to be cut into the vector field with natural buffeting, divergence and trailing flow around them. The size and number of particles in a given area would have a natural dampening effect, and via diffusion/viscocity the vector field itself had channel flows occuring and objects would calmly drift long distances.

It was however, CPU intensive. To get it running on a mobile at 30fps or higher required threading, probably still does. This was long before the [Unity Job system](https://docs.unity3d.com/Manual/JobSystem.html) was generally available and long before ECS or Burst were on the horizon. The approach I used was similar though, in that it&#8217;s the c# threadpool and went wide by giving chunks of the arrays to work through to different threads.

Anyway, in going over older prototypes and projects for sharing on [itch,](https://stevehalliwell.itch.io/) I wanted to update this project to more recent version of Unity to make a build, this was low hanging fruit for playing around with [Jobs](https://docs.unity3d.com/Manual/JobSystem.html) and [Burst](https://docs.unity3d.com/Packages/com.unity.burst@1.3/manual/index.html).

The system prior to jobs was built around collecting particle systems and affectors. Collecting all affectors that are within range of each particlesystem that wants to be affected. The component on the ParticleSystem then uses the Affector to update its particle velocities. The VectorField was a special case of this logic. In the same way, it uses each particle to lookup the grid position and update the velocity at the at cell based on the velocity of the particle.

In jobifiying all of this, the VectorField internal updates, dampening, advecting, are easy to move from the previous manual threading to jobs, the boilerplate is all new but the guts are basically the same. Logic is moved to Unity.Mathematics and structs added to hold the vectorfield data that is fed to the jobs. That change alone would let me create much larger vectorfields should I wish, but the bulk of our frame time is now very much spent updating particles, not the field itself.

<div style="width: 640px;" class="wp-video">
  <video class="wp-video-shortcode" id="video-886-58" width="640" height="360" preload="metadata" controls="controls"><source type="video/mp4" src="http://stevehalliwell.com/wp-content/uploads/2020/08/particles_demo_jobs.mp4?_=58" /><a href="http://stevehalliwell.com/wp-content/uploads/2020/08/particles_demo_jobs.mp4">http://stevehalliwell.com/wp-content/uploads/2020/08/particles_demo_jobs.mp4</a></video>
</div>

The updated version using Unity Jobs and Burst.

To make comparing performance easy, we add new logic paths to the affectors, they can now be requested to complete their tasks of updating particles via jobs. The base for Affectors class gets this added to it

<pre class="lang:default decode:true">public abstract void KickOffJobs(ParticleAffectorAware paa);</pre>

And the script that updates particles has something akin to

<pre class="lang:default decode:true">private void OnParticleUpdateJobScheduled()
{
	if (!useJobs)
		return;
	
	affectors.Clear();
	var ourBounds = psr.bounds;
	var ourRect = Rect.MinMaxRect(ourBounds.min.x, ourBounds.min.y, 	ourBounds.max.x, ourBounds.max.y);

	affectorManager.GetAllThatOverlap(ourRect, affectors);

	foreach (var item in affectors)
	{
		item.KickOffJobs(this);
	}
}</pre>

The particle systems themselves have to schedule jobs inside of [OnParticleUpdateJobScheduled](https://docs.unity3d.com/2019.3/Documentation/ScriptReference/MonoBehaviour.OnParticleUpdateJobScheduled.html). Find all Affectors relevant to them and request they schedule work to operate on that particle system.

<img loading="lazy" class="aligncenter size-full wp-image-855" src="http://stevehalliwell.com/wp-content/uploads/2020/08/jobified.jpg" alt="" width="879" height="627" srcset="http://stevehalliwell.com/wp-content/uploads/2020/08/jobified.jpg 879w, http://stevehalliwell.com/wp-content/uploads/2020/08/jobified-300x214.jpg 300w, http://stevehalliwell.com/wp-content/uploads/2020/08/jobified-768x548.jpg 768w, http://stevehalliwell.com/wp-content/uploads/2020/08/jobified-400x284.jpg 400w" sizes="(max-width: 879px) 100vw, 879px" /> 

In these demos the feedback and feedforward for rigidbodies did not move outside of update as there were so few of them it was not required. However, in a scenario where there were many rigidbodies we&#8217;d introduce a step that gathers all rbs into a buffer that matches the dimensions of the field and then add a job that accumulates that buffer into the field itself on the next frame.

Here I take a detour into more elaborate fluid sim. Since we can now do this sim in under 4ms, we can make it do more. This lead me to;

  * <http://www.dgp.toronto.edu/~stam/reality/Talks/FluidsTalk/FluidsTalkNotes.pdf>
  * <https://pdfs.semanticscholar.org/847f/819a4ea14bd789aca8bc88e85e906cfc657c.pdf>
  * <https://www.openprocessing.org/sketch/455868/>
  * <https://github.com/nornagon/stam-stable-fluids>
  * <https://github.com/finallyjustice/stablefluids>

In among the papers and talks and wrestling with the iterative projection/divergance solver, I&#8217;ve arrived at this.

<div style="width: 640px;" class="wp-video">
  <video class="wp-video-shortcode" id="video-886-59" width="640" height="360" preload="metadata" controls="controls"><source type="video/mp4" src="http://stevehalliwell.com/wp-content/uploads/2020/08/fluid-sim.mp4?_=59" /><a href="http://stevehalliwell.com/wp-content/uploads/2020/08/fluid-sim.mp4">http://stevehalliwell.com/wp-content/uploads/2020/08/fluid-sim.mp4</a></video>
</div>

A fluid sim with density, vorticity, and turbulance via some math.noise. I&#8217;m changing the viscosity and diffusion with the sliders.

These bubbles though.  
<img loading="lazy" class="aligncenter size-full wp-image-865" src="http://stevehalliwell.com/wp-content/uploads/2020/08/bubbles.jpg" alt="" width="991" height="773" srcset="http://stevehalliwell.com/wp-content/uploads/2020/08/bubbles.jpg 991w, http://stevehalliwell.com/wp-content/uploads/2020/08/bubbles-300x234.jpg 300w, http://stevehalliwell.com/wp-content/uploads/2020/08/bubbles-768x599.jpg 768w" sizes="(max-width: 991px) 100vw, 991px" />  
I am not a fan of these bubbles, that&#8217;s all the worker threads stalling as they either waiting for work or waiting for access to data. I&#8217;m using JobHandle combines so that I could build the job list and await on it between frames rather than within the current frame (Note I didn&#8217;t end up wanting to wait across frames anyway). The cuplrit here is, as the job chain gets longer the cpu time taken to by the JobHandle system to combine gets longer, in a non linear fashion, a quick look at the timings would suggest O(n^2), so for my current circumstances it&#8217;s actually better to insert more completes per frame.  
<img loading="lazy" class="aligncenter size-full wp-image-866" src="http://stevehalliwell.com/wp-content/uploads/2020/08/smaller_bubbles.jpg" alt="" width="994" height="773" srcset="http://stevehalliwell.com/wp-content/uploads/2020/08/smaller_bubbles.jpg 994w, http://stevehalliwell.com/wp-content/uploads/2020/08/smaller_bubbles-300x233.jpg 300w, http://stevehalliwell.com/wp-content/uploads/2020/08/smaller_bubbles-768x597.jpg 768w" sizes="(max-width: 994px) 100vw, 994px" /> Much better, total time is almost halved. We still have some bubbles but much more managably so. It&#8217;s generating a chain of solver and edge constraint steps and the jobs scheduled prior complete so fast we see these small stalls. It would be worth investigating the cost of running all these steps in 1 big off loaded thread, see if burst can run the task on 1 thread faster than we can generate the dep chain to go wide. It does not seem likely, the iterative solver step is the heaviest of our jobs. But being a good programmer, so we test our performance.

<img loading="lazy" class="aligncenter size-full wp-image-870" src="http://stevehalliwell.com/wp-content/uploads/2020/08/singleThreadProjectStep.jpg" alt="" width="871" height="682" srcset="http://stevehalliwell.com/wp-content/uploads/2020/08/singleThreadProjectStep.jpg 871w, http://stevehalliwell.com/wp-content/uploads/2020/08/singleThreadProjectStep-300x235.jpg 300w, http://stevehalliwell.com/wp-content/uploads/2020/08/singleThreadProjectStep-768x601.jpg 768w" sizes="(max-width: 871px) 100vw, 871px" /> 

OK, good, confirmed. Doing the entire Projection / Divergance step on 1 thread is much slower, not linearly though, it&#8217;s made up of 40 very small jobs (with iterative solver steps set to 20). Anyway let&#8217;s put all that back into our original demo scene. With some tweaking and testing I&#8217;ve found that less solver steps is very tolerable for the complexity and speeds of my demo.

Putting the new fluid sim back into something akin to our original scene&#8230;

<div style="width: 640px;" class="wp-video">
  <video class="wp-video-shortcode" id="video-886-60" width="640" height="360" preload="metadata" controls="controls"><source type="video/mp4" src="http://stevehalliwell.com/wp-content/uploads/2020/08/new-fluid-sim.mp4?_=60" /><a href="http://stevehalliwell.com/wp-content/uploads/2020/08/new-fluid-sim.mp4">http://stevehalliwell.com/wp-content/uploads/2020/08/new-fluid-sim.mp4</a></video>
</div>

Wonderful, still needs some tweaking to match the gameplay values we want but otherwise in a great spot and left with a small bundle of scripts I&#8217;ll certainly be taking forward into every 2D project in the foreseeable future.

## Additional Tid Bits

In all previous tests, I&#8217;ve got a simple index check that skips over the index if its on the edge. It&#8217;s very subtle but this actually slows it down by a small percent, around 4% infact.

<img loading="lazy" class="aligncenter size-full wp-image-884" src="http://stevehalliwell.com/wp-content/uploads/2020/08/with-and-without-bounds-checking.jpg" alt="" width="894" height="458" srcset="http://stevehalliwell.com/wp-content/uploads/2020/08/with-and-without-bounds-checking.jpg 894w, http://stevehalliwell.com/wp-content/uploads/2020/08/with-and-without-bounds-checking-300x154.jpg 300w, http://stevehalliwell.com/wp-content/uploads/2020/08/with-and-without-bounds-checking-768x393.jpg 768w" sizes="(max-width: 894px) 100vw, 894px" /> It&#8217;s actually faster to do this work and allow for better branch prediction and allow burst a better chance to vectorise the workload.

Further, in all previous tests we&#8217;ve add safety checks on in the burst compiler. It was only fair, the C# equivilant has checks that we can&#8217;t turn off. That makes our little demo run in only ~60% of previous time. Alllowing for even larger grids.

<img loading="lazy" class="aligncenter size-full wp-image-885" src="http://stevehalliwell.com/wp-content/uploads/2020/08/with-and-without-safety-checks.jpg" alt="" width="895" height="474" srcset="http://stevehalliwell.com/wp-content/uploads/2020/08/with-and-without-safety-checks.jpg 895w, http://stevehalliwell.com/wp-content/uploads/2020/08/with-and-without-safety-checks-300x159.jpg 300w, http://stevehalliwell.com/wp-content/uploads/2020/08/with-and-without-safety-checks-768x407.jpg 768w" sizes="(max-width: 895px) 100vw, 895px" /> 

As grids get larger more of our frame ends up being used by generating noise to perturb the field. But that&#8217;s a job for another day, HA unintentional pun.

Note: all videos and screencaps are taken on a desktop with a Ryzen 1700.