# Introducing the LY++ language

LY++ The universal block oriented programming language that supports the most common iterative, functional and object oriented languages like C++, C#, C, Java, Haskell, Python and many more! [Concept]

**Please read this code (.pdf file) and hold down your first impression**
<img src="https://user-images.githubusercontent.com/59289792/74105362-66629580-4b5d-11ea-85d2-0072cc30f484.png" data-canonical-src="https://user-images.githubusercontent.com/59289792/74105362-66629580-4b5d-11ea-85d2-0072cc30f484.png" width="700" height="350" />

See code: [lypp_example_syntax.pdf](https://github.com/BabyYod4/lypp/files/4177125/example_syntax.pdf)

## Background and Story:
During my life as a student and worker i have had to code in many different programming languages ranging from C, C++, Java, Python, C#, (a bit of haskel), PHP, JS, etc.. During this time i had always wondered why there is no one 'unified' language that could do it all. As the years progressed i quickly started to understand wht this would be impossible as many people have a different preference when they code. This can be something simple as the way namespaces are used, to something more complex as if values are immutable or not. 

In these recent couple of months i have questioned and 'debated' with people about the different reasons they use or prefer X over Y and Z over X. But the more questions i started to ask the less this concept of:
"You should use X because its better than Y in these ways" -- "No idiot the reason Y does this is because ..." would start to matter. 

This inspired me to come up with a "solution" that could work for everyone (as far as i am concerend). Why not create something which allows different programming languages to be able to communicate with each other easily (without having to fiddle with `make` files etc) in a matter that satisfies most of the people using these languages. 

Now you can encapsulate a block of code from language A and use it nativly, but still be ensured that a block of code of langauge B can access or use the code from A in some manner. 

## So what is this about?
The example code you have read shows you how 'block oriented' programming can be used to make multiple programming languages compatible with each other. 

In block oriented programming you (as the name implies) seperate your code in individual blocks. These blocks are language independent (meaning each programming language can be used). Communication between blocks is also language indepentent and is possible by specifying which block to use with `<block use="{id1}; {id2};">`. 
- By default when a block is included all of its content is accesable (this includes functions, variables, etc) unless that specific block has specified what parts of the block can be included inside an `<interface>`. 
- Other blocks can only include tnterfaces that contain functions and variables. This is to ensure that block of all languages can use the contents (See Quircks and Limitations)
- The user can also make blocks 'un-includable' as folowed: `<block mode="private" ... >`. This means a compile time error will be generated when another block tries to include it. 

As mentioned before each block can contain code from another programming language which optionally can also utilize the LY++ library. 

Instead of having one `main()` function as a starting point you have so called `<tasks>` which can contain a `main()` function. Each `<tasks>` is in reality a seperate executable running in a thread. These `<tasks>` can communicate with each other or share on or more `<blocks>` with each other through a `pipeline`. This pipeline is synchronized automatically with all the `<tasks>`'s  to reduce latency. 

A collection of `<tasks>`'s will always be structered in a `tree` where the failure of one task will not consitute the failure of all the tasks. This is extremely similar to how [Erlang](https://www.erlang.org/) was implemented in a VM.

## How it realy works:
In reality LY++ is nothing more than a glorified C++ 'pre-parser' meaning it will translate all of the source code into a single c++ (or multiple if requested) file which can be compiled easily with almost every generic `g++` compiler (see image below). 
![explain](https://user-images.githubusercontent.com/59289792/74106411-26a0ab80-4b67-11ea-9617-dfb15eb73299.png)

## Quircks and Limitations

(1) Only functions and variables can be accessed in an `<interface>`: Because each programming paradigm has different rulesets, it would be almost impossible to satisfy the needs and want of each one of them. 
- For example in functional programming its considered a sin to have mutable variables when calling/using a function, but in iterative and object oriented programming this is the norm.
    - This reason caused the descision to let functional languages only include code from other functional languages. 
- Another example would be that an iterative language like C has no concept of a `class` so it should be not possible to use classes in that language. 
    - I know you can technically create C classes but its never to be intended to be used that way, and have this like C linkages but just follow the example. 
    
For that reason there should be a ruleset for each paradigm on what kind of `<interfaces>` can be included by each paradigm.
| Paradigm | Can work with paradigm | Can include |
| --- | --- | --- |
| Iterative | Iterative, Functional | variables, functions |
| Functional | Functional | functions |
| Object Oriented | Functional, Iterative, Object Oriented | variables, functions |

(1a) If for what ever reason you dont want to be limited by the rulesets defined by your paradigm you could use the option: 
`<block breakrules="True" ...>` to ignore the ruleset during compilations. 
- For example this way `Haskell` code can call a `C` function that uses mutuable variables

(1b) Iterative languages can also work with object oriented languages if the content inside the `<interface>` is iterative. This means that you for example could use `C++` code in `C`. This does not require any settings, but does require you to change the code in the `<interface>`. Because you are not breaking the ruleset of the iterative language. 
- Something you could do for example is:
```c++
<block id="foo" lang="C++", ...>
  static SomeClass foo;
  <interface>
    void foo_bar(){ foo.bar(); } 
  </interface>
</block

<block id="hello" lang="C", use="foo" ...>
  foo_bar();
</block>
```
