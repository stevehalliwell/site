---
id: 748
title: Dev Console Part 2
date: 2017-02-19T21:23:36+10:00
author: Steve Halliwell
excerpt: "Working with GConsole and want to be able to wrap existing functions more automatically. In GConsole then when you eval the input string it matches the first part against all 'command' that have been registered."
layout: post
guid: http://stevehalliwell.com/?p=748
permalink: /dev-console-part-2/
enablewpts:
  - "0"
dsq_thread_id:
  - "5784042734"
et_enqueued_post_fonts:
  - 'a:2:{s:6:"family";a:2:{s:21:"et-gf-source-sans-pro";s:100:"Source+Sans+Pro:200,200italic,300,300italic,regular,italic,600,600italic,700,700italic,900,900italic";s:10:"et-gf-lato";s:75:"Lato:100,100italic,300,300italic,regular,italic,700,700italic,900,900italic";}s:6:"subset";a:7:{i:0;s:8:"cyrillic";i:1;s:5:"greek";i:2;s:10:"vietnamese";i:3;s:5:"latin";i:4;s:9:"greek-ext";i:5;s:9:"latin-ext";i:6;s:12:"cyrillic-ext";}}'
categories:
  - Blog
---
<div class="et_pb_section et_pb_section_1 et_section_regular">
  <div class="et_pb_row et_pb_row_1">
    <div class="et_pb_column et_pb_column_4_4 et_pb_column_1    et_pb_css_mix_blend_mode_passthrough et-last-child">
      <div class="et_pb_module et_pb_text et_pb_text_1 et_pb_bg_layout_light  et_pb_text_align_left">
        <div class="et_pb_text_inner">
          <p>
            <a href="http://stevehalliwell.com/dev-console-1/">Part 1 here.</a>
          </p>
          
          <p>
            Working with <a href="https://github.com/gzuidhof/GConsole">GConsole</a> and want to be able to wrap existing functions more automatically<br /> <code></code>
          </p>
          
          <pre>public static bool AddCommand(string command, string description, Func&lt;string, string=""&gt;; method, string helpText = null)</pre>
          
          <p>
            &nbsp;
          </p>
        </div>
      </div>
      
      <!-- .et_pb_text -->
      
      <div class="et_pb_module et_pb_text et_pb_text_2 et_pb_bg_layout_light  et_pb_text_align_left">
        <div class="et_pb_text_inner">
          <p>
            That&#8217;s how we get things in the console. In GConsole then when you eval the input string it matches the first part against all &#8216;command&#8217; that have been registered. If it finds a match it calls the &#8216;method&#8217; with the substring of the input string from after the command name till end of string. So for ease of use I wanted to be able to wrap an arbitrary function in a Func<string,string>.
          </p>
          
          <p>
            That&#8217;s a few separate tasks
          </p>
          
          <ul>
            <li>
              Converting string to type
            </li>
            <li>
              Determining number of parameters
            </li>
            <li>
              Knowing param types in the first place
            </li>
          </ul>
          
          <p>
            Determining number of params. To start with during testing I simply used .Splint(&#8220;,&#8221;) on the inputstring, this meant that as long they were comma separated they where different. I knew this was just to get other things tested out as things like sentences within quotes and vector3s etc. want commas in them but not to be a separate param. <a href="https://en.wikipedia.org/wiki/Regular_expression">Regex </a>was the solution as things got more complicated.
          </p>
          
          <p>
            &nbsp;
          </p>
          
          <pre>Regex regex = new Regex(@"\(.*?\)|\[.*?\]|"".*?""|[^\s]+");
MatchCollection matches = regex.Matches(s);
string[] res = new string[matches.Count];
for (int i = 0; i &lt; res.Length; i++)
{
 res[i] = matches[i].Value;
}
</pre>
          
          <p>
            &nbsp;
          </p>
        </div>
      </div>
      
      <!-- .et_pb_text -->
      
      <div class="et_pb_module et_pb_text et_pb_text_3 et_pb_bg_layout_light  et_pb_text_align_left">
        <div class="et_pb_text_inner">
          <p>
            This will hold out for a while but it&#8217;s not going to handle object hierarchies, like json, should the Console get to that level of complexity in the future.
          </p>
          
          <p>
            &nbsp;
          </p>
          
          <p>
            Converting strings to types, assuming we know what it should be. ints and floats etc. have builtin Parse functions, if it&#8217;s a string, it already is one. For the immediate future, vectors were the other thing I really wanted, they just require breaking up the string and using multiple float Parse. Now we need a way to access these in a way that makes sense for our use case. For me it was a lookup, keyed by Type and storing a Func<object, string>. When these are eventually used in an automated fashion we put the conversion inside try catch blocks so if the conversion fails we report that to the user via the console output and don&#8217;t call the function.
          </p>
          
          <p>
            &nbsp;
          </p>
          
          <p>
            Knowing parameter types in the first place. Reflection is very powerful. In fact we&#8217;ll end up using it for far more than just this. We get the <a href="https://msdn.microsoft.com/en-us/library/system.reflection.methodinfo(v=vs.110).aspx">method info</a> out of the type. That can give us attributes applied to the method, which GConsole and CUDLR etc. already use to mark up classes or methods with names that they should be stored under in the console as well as description text to display to the user. What I need right now though is the the <a href="https://msdn.microsoft.com/en-us/library/system.reflection.methodbase.getparameters(v=vs.110).aspx">parameters of the method</a>. Then we get something like this.
          </p>
          
          <p>
            &nbsp;
          </p>
          
          <pre>var pList = item.GetParameters();

if (!StringToType.Supports(pList))
 continue;

System.Func&lt;string, string&gt; wrappedFunc = (string stringIn) =&gt;
{
 string[] sParams = StringToType.ParamStringToElements(stringIn);
 object[] parameters;

 if (StringToType.StringArrayToObjects(sParams, pList, out parameters))
 {
 item.Invoke(null, parameters);
 return "";
 }

 return "Could not call " + item.Name;
};
</pre>
          
          <p>
            &nbsp;
          </p>
        </div>
      </div>
      
      <!-- .et_pb_text -->
      
      <div class="et_pb_module et_pb_text et_pb_text_4 et_pb_bg_layout_light  et_pb_text_align_left">
        <div class="et_pb_text_inner">
          <p>
            Next step will be more automating binding and now that we&#8217;ve proved out some concepts, moving to CUDLR.
          </p>
          
          <p>
            <a href="http://stevehalliwell.com/dev-console-part-3/">Part 3 here.</a>
          </p>
        </div>
      </div>
      
      <!-- .et_pb_text -->
    </div>
    
    <!-- .et_pb_column -->
  </div>
  
  <!-- .et_pb_row -->
</div>

<!-- .et_pb_section -->