---
title: "Me, C++ and Functional Programming on a Good Day"
categories:
  - Programming
tags:
  - C++
  - Functional
last_modified_at: 2024-06-24
---

Hi!


I was procrastinating as always in my free time when I suddenly felt this big empty hole inside me about functional programming and C++. So I decided to write this blog post to delve deep into the topic. Of course I’m joking. I always wanted to write such a blog post. I just didn’t have the courage. Anyways let’s get into it.


So what is functional programming? Because I don’t want to make abrupt mistakes I’m going to use the help of our lord and savior Wikipedia. It says: “functional programming is a programming paradigm where programs are constructed by applying and composing functions”. Yes, whatever Wikipedia says. So with that out of our way I hope you now have a better idea about functional programming. If not you can always search on your own for more.

Alright so what was this blog about? Uhh C++? Functional Programming? How?


## Enter The New ISO Standard

 
With Concepts in C++20 and all the other stuff we had before, we now have the tools to build a somewhat functional programming paradigm for ourselves in C++. But before that we may need to familiarize ourselves with a few things.

### Map:

It’s easy. It takes a group of things and applies a function on them, then produces a new group. Let me show you with good ol’ C++. See? It’s possible in C++ and understandable. Let’s run it in CompilerExplorer and see what it does. 


<iframe width="800px" height="800px" src="https://godbolt.org/e?readOnly=true&hideEditorToolbars=true#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:59,endLineNumber:7,positionColumn:1,positionLineNumber:6,selectionStartColumn:59,selectionStartLineNumber:7,startColumn:1,startLineNumber:6),source:'%23include+%3Calgorithm%3E%0A%23include+%3Ciostream%3E%0A%23include+%3Cvector%3E%0A%0Avoid+square(std::vector%3Cint%3E%26+num)+%7B%0A++++auto+x+%3D+std::transform(num.begin(),+num.end(),+num.begin(),%0A++++++++++++++++++++++++++++%5B%5D(int%26+x)+%7B+return+x+*+x%3B+%7D)%3B%0A%0A++++std::for_each(num.begin(),+num.end(),%0A++++++++++++++++++%5B%5D(const+auto%26+elem)+%7B+std::cout+%3C%3C+elem+%3C%3C+%22+%22%3B+%7D)%3B%0A%7D%0A%0Aauto+main()+-%3E+int+%7B%0A++++std::vector%3Cint%3E+vec_nums+%3D+%7B1,+2,+3,+4,+5%7D%3B%0A++++%0A++++square(vec_nums)%3B%0A%0A++++return+0%3B%0A%7D'),l:'5',n:'1',o:'C%2B%2B+source+%231',t:'0')),k:100,l:'4',m:61.6600790513834,n:'0',o:'',s:0,t:'0'),(g:!((h:executor,i:(argsPanelShown:'1',compilationPanelShown:'0',compiler:g141,compilerName:'',compilerOutShown:'0',execArgs:'',execStdin:'',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,libs:!(),options:'',source:1,stdinPanelShown:'1',wrap:'1'),l:'5',n:'0',o:'Executor+x86-64+gcc+14.1+(C%2B%2B,+Editor+%231)',t:'0')),header:(),l:'4',m:38.3399209486166,n:'0',o:'',s:0,t:'0')),l:'3',n:'0',o:'',t:'0')),version:4"></iframe>

Run it and you can see it simply returns a squared list of our vector. Well that's map out of the way.

### Fold:

Fold is like map but instead of giving one group, it gives us only one element. It “reduces” lists into one thing. Here’s the example code in the C++:

 
<iframe width="800px" height="800px" src="https://godbolt.org/e?readOnly=true&hideEditorToolbars=true#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:59,endLineNumber:8,positionColumn:59,positionLineNumber:8,selectionStartColumn:59,selectionStartLineNumber:8,startColumn:59,startLineNumber:8),source:'%23include+%3Calgorithm%3E%0A%23include+%3Cfunctional%3E%0A%23include+%3Ciostream%3E%0A%23include+%3Cnumeric%3E%0A%23include+%3Cvector%3E%0A%0Avoid+square(std::vector%3Cint%3E%26+num)+%7B%0A++++std::cout+%3C%3C+std::accumulate(num.begin(),+num.end(),+1,+std::multiplies%3Cint%3E())%3B%0A%7D%0A%0Aauto+main()+-%3E+int+%7B%0A++++std::vector%3Cint%3E+vec_nums+%3D+%7B1,+2,+3,+4,+5%7D%3B%0A%0A++++square(vec_nums)%3B%0A%0A++++return+0%3B%0A%7D'),l:'5',n:'1',o:'C%2B%2B+source+%231',t:'0')),k:100,l:'4',m:61.6600790513834,n:'0',o:'',s:0,t:'0'),(g:!((h:executor,i:(argsPanelShown:'1',compilationPanelShown:'0',compiler:g141,compilerName:'',compilerOutShown:'0',execArgs:'',execStdin:'',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,libs:!(),options:'',source:1,stdinPanelShown:'1',wrap:'1'),l:'5',n:'0',o:'Executor+x86-64+gcc+14.1+(C%2B%2B,+Editor+%231)',t:'0')),header:(),l:'4',m:38.3399209486166,n:'0',o:'',s:0,t:'0')),l:'3',n:'0',o:'',t:'0')),version:4"></iframe>


By running it we see it multiplied all of our results into one single value of 120. Pretty simple huh?
Well you might also ask what that 1 is. That’s the initial value. Change it and see how it changes everything.


## Knock! Knock! Who’s there? Concepts!
	
I’m pretty sure you come across this situation in programming C++ templates where you wanted to make sure only certain kinds of arguments were passed into your functions. And if not, at least the compiler showed you proper error messages. Well concepts try to solve that. Also they are a good tool to do functional programming since we can enforce some certain things.

Okay let’s see them in action. Remember our last example? I’ll modify that. And then pass a vector of strings to it. Well it’ll look like this.


<iframe width="800px" height="800px" src="https://godbolt.org/e?readOnly=true&hideEditorToolbars=true#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:14,endLineNumber:19,positionColumn:14,positionLineNumber:19,selectionStartColumn:14,selectionStartLineNumber:19,startColumn:14,startLineNumber:19),source:'%23include+%3Calgorithm%3E%0A%23include+%3Cfunctional%3E%0A%23include+%3Ciostream%3E%0A%23include+%3Cnumeric%3E%0A%23include+%3Cvector%3E%0A%23include+%3Cconcepts%3E%0A%0Atemplate+%3Ctypename+T%3E%0Arequires+std::integral%3CT%3E%0Aauto+square(const+std::vector%3CT%3E%26+num)+%7B%0A++++auto+x+%3D+std::cout+%3C%3C+std::accumulate(num.begin(),+num.end(),+1,+std::multiplies%3Cint%3E())%3B%0A%7D%0A%0Aauto+main()+-%3E+int+%7B%0A++++std::vector%3Cstd::string_view%3E+vec_nums+%3D+%7B%22hello%22,+%22hi%22,+%22bye%22,+%22ciao%22%7D%3B%0A%0A++++square(vec_nums)%3B%0A%0A++++return+0%3B%0A%7D'),l:'5',n:'1',o:'C%2B%2B+source+%231',t:'0')),k:100,l:'4',m:61.6600790513834,n:'0',o:'',s:0,t:'0'),(g:!((h:executor,i:(argsPanelShown:'1',compilationPanelShown:'0',compiler:clang1810,compilerName:'',compilerOutShown:'0',execArgs:'',execStdin:'',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,libs:!(),options:'-std%3Dc%2B%2B23',overrides:!(),runtimeTools:!(),source:1,stdinPanelShown:'1',wrap:'1'),l:'5',n:'0',o:'Executor+x86-64+clang+18.1.0+(C%2B%2B,+Editor+%231)',t:'0')),header:(),l:'4',m:38.3399209486166,n:'0',o:'',s:0,t:'0')),l:'3',n:'0',o:'',t:'0')),version:4"></iframe>


Thank you for reading the blog post. In the future I’ll post more in this blog. This post was just a short start. I hope you enjoyed it.
