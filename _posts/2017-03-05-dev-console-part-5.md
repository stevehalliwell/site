---
id: 804
title: Dev Console Part 5
date: 2017-03-05T12:41:51+10:00
author: Steve Halliwell
layout: post
guid: http://stevehalliwell.com/?p=804
permalink: /dev-console-part-5/
enablewpts:
  - "0"
dsq_thread_id:
  - "5782165040"
et_enqueued_post_fonts:
  - 'a:2:{s:6:"family";a:2:{s:21:"et-gf-source-sans-pro";s:100:"Source+Sans+Pro:200,200italic,300,300italic,regular,italic,600,600italic,700,700italic,900,900italic";s:10:"et-gf-lato";s:75:"Lato:100,100italic,300,300italic,regular,italic,700,700italic,900,900italic";}s:6:"subset";a:7:{i:0;s:8:"cyrillic";i:1;s:5:"greek";i:2;s:10:"vietnamese";i:3;s:5:"latin";i:4;s:9:"greek-ext";i:5;s:9:"latin-ext";i:6;s:12:"cyrillic-ext";}}'
image: /wp-content/uploads/2017/03/console-autocomplete-steps4.gif
categories:
  - Blog
---
Previously [Part 4](http://stevehalliwell.com/dev-console-part-4/).

The Console is settling and getting to a point where I think I&#8217;m ready to split it out into it&#8217;s own repository and out of &#8216;under construction&#8217;.

It bothered me that my previous AddStaticTypeByString required you to know the assembly that the thing came from, so instead of using the System.Type version we grab all loaded assemblies and ask them in sequence if they have a type that matches the name.

The big thing I wanted to be able to do is map game data to the console. For me, in Unity, this is typically a [ScriptableObject](https://docs.unity3d.com/ScriptReference/ScriptableObject.html) or something a bit fancier. The concept is the same though, there is some object with data that other classes refer to that defines common values. For this I needed to refactor my previous ConsoleHelper Add* to support object instances. Not a big deal, all the Reflection functions in use want an object, we&#8217;d just been giving them a null as everything was static previously, now it&#8217;s just lots of passing object down. This also meant that if you pass an object you don&#8217;t need to pass type and if you pass a type we could assume it was static if we wanted to.

<img loading="lazy" class="aligncenter size-full wp-image-808" src="http://stevehalliwell.com/wp-content/uploads/2017/03/console-instance-value.gif" alt="" width="608" height="350" /> 

In a more complex terminal window hitting tab repeatedly will cycle through all the possible things you could be trying to type. I have only attempted to get this working from within the App and not via the Browser. To achieve this I&#8217;m now keeping the result[] from a call to the complete function and if the autocomplete is requested again before changing the partial phrase we move the index forward through the cached result array and show that text. In the Unity Text we are replacing the text and keeping the current cursor position and setting the cursor end position to the end of the string. There are still some edge cases that are unhandled given my simplistic approach, like moving the cursor manually not resetting the array of autocompletes.

<img loading="lazy" class="aligncenter size-full wp-image-810" src="http://stevehalliwell.com/wp-content/uploads/2017/03/console-autocomplete-steps4.gif" alt="" width="400" height="556" /> 

I wanted to be able to find a command without knowing where it might be. This I thought would be especially useful in one of the previously discussed situations where users customise their console but others might want to use it. E.g. you know that you can change gravity, but it isn&#8217;t where you thought it would be at UnityEngine.Physics.gravity. The find command then &#8216;needs&#8217; to be able to traverse every node in the CommandTree but I didn&#8217;t want the find command to have to be overly aware of how CommandTree works nor did I want the CommandTree to have a slightly different version of Find that ignores heirarchy and can return more than 1 item. What solved it was a [visitor pattern](https://en.wikipedia.org/wiki/Visitor_pattern), [c# closure](http://www.codethinked.com/c-closures-explained) (first result curiously not from msdn) and recursion.

We have something like this in CommandTree. Where each command will pass itself to the visitor and if the visitor requests it, then recur for all child commands.

<pre class="lang:default decode:true">public void Visit(System.Func&lt;CommandTree,bool&gt; visitor)
{
    if(visitor(this))
    {
        foreach (var item in SubCommands)
        {
            item.Visit(visitor);
        }
    }
}</pre>

and something like this to use it.

<pre class="lang:default decode:true">[Command("find", "Searches through all commands and conducts a partial match against the given string", false)]
public static void Find(string s)
{
    s = s.ToLower();

    StringBuilder sb = new StringBuilder();
    System.Func&lt;CommandTree, bool&gt; findNames = (CommandTree t) =&gt;
    {
        if (t.Command != null && t.Command.localName.ToLower().Contains(s))
        {
            sb.AppendLine(t.FullCommandPath);
            return false;   //no point checking children as they will match this already
        }

        //try it's children
        return true;
    };

    instance.m_commands.Visit(findNames);

    Console.Log(sb.ToString());
}</pre>

This gets the job done in a way I can deal with, it also meant that I could remove CUDLR&#8217;s previous help command, as it built and stored a giant string as commands were added so it could display it later. I didn&#8217;t want that giant string to be around always. So now I have an all command, that uses the same visitor style.

<img loading="lazy" class="aligncenter size-full wp-image-816" src="http://stevehalliwell.com/wp-content/uploads/2017/03/console-find.gif" alt="" width="600" height="354" /> 

In the previous post I mentioned that I profile system might be a useful thing people could create, well the idea stuck so I started adding it. I now store a ScriptableObject with a bunch of csv TextAssets in it.

Example csv

<pre class="lang:default decode:true ">typeName,nameInConsole,bindingFlags,partsToBind
UnityEngine.Time,Unity.Time,"DeclaredOnly, Static, Public","Methods, Props, Fields"
UnityEngine.Physics,Unity.Physics,"DeclaredOnly, Static, Public","Methods, Props, Fields"
UnityEngine.Physics2D,Unity.Physics2D,"DeclaredOnly, Static, Public","Methods, Props, Fields"
UnityEngine.Application,Unity.Application,"DeclaredOnly, Static, Public","Methods, Props, Fields"
</pre>

That gets converted to a List of.

<pre class="lang:default decode:true ">[Flags]
public enum PartsToBind
{
	//0 is none
	Methods = (1 &lt;&lt; 0),
	Props 	= (1 &lt;&lt; 1),
	Fields 	= (1 &lt;&lt; 2),
}

//typenames and namesinconsole must match length
[System.Serializable]
public class ClassByNameData
{
    public ClassByNameData() { }
    public ClassByNameData(string TypeName, string NameInConsole = null)
    {
        typeName = TypeName;
        nameInConsole = NameInConsole;
    }

    public string typeName = string.Empty;
	public string nameInConsole = null;
	public BindingFlags bindingFlags = PublicStatic;
	public PartsToBind partsToBind = PartsToBind.Methods | PartsToBind.Fields | PartsToBind.Props;
}</pre>

And here we are loading that example csv that is called Default in the console to add additional elements.  
<img loading="lazy" class="aligncenter size-full wp-image-814" src="http://stevehalliwell.com/wp-content/uploads/2017/03/console-profile-example-2.gif" alt="" width="600" height="354" /> 

One other bit I found I needed was an attribute to flag, fields, props, methods or classes to be ignored by a AddAllToConsole. I created an attribute called CommandIgnore that I can apply to any thing and then my console helper variants will skip over it. The primary use I have for this is being able to have static classes or global data that I want in the console but that still allows me to refactor those classes in the usual way and not worry about exposing partial or useless functions to the console.