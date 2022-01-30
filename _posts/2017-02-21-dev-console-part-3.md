---
id: 772
title: Dev Console Part 3
date: 2017-02-21T21:25:42+10:00
author: Steve Halliwell
excerpt: "Now to get to making binding easy to do. GConsole, CUDLR, etc. all make use of c# Attributes to attach additional information to methods. If you've used Unity much you probably have seen Range or Tooltip, these are used by unity editor drawer code to change how things are shown in the inspector."
layout: post
guid: http://stevehalliwell.com/?p=772
permalink: /dev-console-part-3/
enablewpts:
  - "0"
dsq_thread_id:
  - "5784042660"
et_enqueued_post_fonts:
  - 'a:2:{s:6:"family";a:2:{s:21:"et-gf-source-sans-pro";s:100:"Source+Sans+Pro:200,200italic,300,300italic,regular,italic,600,600italic,700,700italic,900,900italic";s:10:"et-gf-lato";s:75:"Lato:100,100italic,300,300italic,regular,italic,700,700italic,900,900italic";}s:6:"subset";a:7:{i:0;s:8:"cyrillic";i:1;s:5:"greek";i:2;s:10:"vietnamese";i:3;s:5:"latin";i:4;s:9:"greek-ext";i:5;s:9:"latin-ext";i:6;s:12:"cyrillic-ext";}}'
image: /assets/images/2017/02/console-gravity.gif
categories:
  - Blog
---
Previously [Part 2](http://stevehalliwell.com/dev-console-part-2/).

Now to get to making binding easy to do. GConsole, CUDLR, etc. all make use of c# [Attributes](https://www.tutorialspoint.com/csharp/csharp_attributes.htm) to attach additional information to methods. If you&#8217;ve used Unity much you probably have seen [Range](https://docs.unity3d.com/ScriptReference/RangeAttribute.html) or [Tooltip](https://docs.unity3d.com/ScriptReference/TooltipAttribute.html), these are used by unity editor drawer code to change how things are shown in the inspector. The existing console systems use attributes to give names, descriptions, help text and other data to methods that the console system can get access to and report to the user, [CUDLR attribute here](https://github.com/proletariatgames/CUDLR/blob/master/CUDLR/Scripts/Attributes.cs). Then the console system can find all types that have these attributes and add them to their internal list of commands. So that&#8217;s understood and kinda solved.

I wanted to be able to add methods, fields and properties to the console without having to attach attributes to them. Partly, if it isn&#8217;t _**stupid**_ easy to do, I know at some point I&#8217;ll stop adding things to the console as a matter of course. The other part is that I want to be able to add elements to the console that I cannot add attributes to, for example Unity [Physics](https://docs.unity3d.com/ScriptReference/Physics.html) class. A solution involving writing a facade for Unity built-ins just so I can add attributes to them isn&#8217;t a good use of anyone&#8217;s time. Here&#8217;s what I used while determining the method.

<pre class="lang:default decode:true">ConsoleHelper.AddAllStaticsToConsole(typeof(ConsoleStaticCommandsTest));


//putting these in a static class so we can easily bind all of them automatically
public static class ConsoleStaticCommandsTest
{
    public static int testField;

    public static float testProp { get; set; }

    public static Vector3 vec3;
    
    public static void test()
    {
        Debug.Log("test print");
    }

    public static void PrintInt( int i)
    {
        Debug.Log(i.ToString());
    }
    
    public static void PrintFloat(float f)
    {
        Debug.Log(f.ToString());
    }
    
    public static void PrintIntThenFloat(int i, float f)
    {
        Debug.Log(i.ToString() +": " + f.ToString());
    }

    public static void PrintIntStringFloat(int i, string str, float f)
    {
        Debug.Log(i.ToString() + ", " + str + ", " + f.ToString());
    }

    public class Something { }
    public static void MethodWithUnsupportedParamType(Something s)
    {

    }
}
</pre>

Reflection to the rescue again. AddAllStaticsToConsole uses GetMethods with BindFlags to filter non public and non static items out, we also then fitler out all &#8216;special&#8217; methods, these things that get auto generated for properties, the get_ and set_. It also uses GetFields and GetProperties in a similar fashion. These all have their own types but basically we loop through the array of results and try to add them to the console if they take are convertible (we discussed this in [Part 2](http://stevehalliwell.com/dev-console-part-2/)) and we know the name from the reflection information. We refactor this into related sub functions so we can reuse them as this gets more powerful.

Moment of truth.

<pre class="lang:default decode:true">ConsoleHelper.AddAllStaticsToConsole(typeof(Physics));</pre>

![](/assets/images/2017/02/console-gravity.gif) 

At this point, I&#8217;ve also switched to CUDLR from GConsole. CUDLR it&#8217;s built to work remotely via the browser and it made more sense add in-game access to CUDLR rather than make GConsole usable from the browser. CUDLR also already had the concept of a hierarchy which I wanted. I ended up making a number of changes and adding some features to CUDLR which I&#8217;ll go into in the next part.

[Part 4 here.](http://stevehalliwell.com/dev-console-part-4/)