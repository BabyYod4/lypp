# Introducing the LY++ language

LY++ The universal block oriented programming language that supports the most common iterative, functional and object oriented languages like C++, C#, C, Java, Haskell, Python and many more! [Concept]

**Please read this code (.pdf file) and hold down your first impression**
<img src="https://user-images.githubusercontent.com/59289792/74105362-66629580-4b5d-11ea-85d2-0072cc30f484.png" data-canonical-src="https://user-images.githubusercontent.com/59289792/74105362-66629580-4b5d-11ea-85d2-0072cc30f484.png" width="900" height="500" />

See code: [lypp_example_syntax.pdf](https://github.com/BabyYod4/lypp/files/4177125/example_syntax.pdf)

## Background and Story:
During my life as a student and worker i have had to code in many different programming languages ranging from C, C++, Java, Python, C#, (a bit of haskel), PHP, JS, etc.. During this time i had always wondered why there is no one 'unified' language that could do it all. As the years progressed i quickly started to understand wht this would be impossible as many people have a different preference when they code. This can be something simple as the way namespaces are used, to something more complex as if values are immutable or not. 

In these recent couple of months i have questioned and 'debated' with people about the different reasons they use or prefer X over Y and Z over X. But the more questions i started to ask the less this concept of:
"You should use X because its better than Y in these ways" -- "No idiot the reason Y does this is because ..." would start to matter. 

This inspired me to come up with a "solution" that could work for everyone (as far as i am concerend). Why not create something which allows different programming languages to be able to communicate with each other easily (without having to fiddle with `make` files etc) in a matter that satisfies most of the people using these languages. 

Now you can encapsulate a block of code from language A and use it nativly, but still be ensured that a block of code of langauge B can access or use the code from A in some manner. 

This will be a project i will do in my free time (mostly in the weekends) next to some other project of my company, so i don't expect the development to be that fast. Another thing to consider is that I will probally change allot of code to be more optimized in the future as i will progress in my studies from Bachlor to Masters to PhD, so i will have better knowledge in Computer Science and Engineering. 

The real question i would like to ask you guys is: **What do you think of the idea? What would you change/add/remove?**

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

The reason i have chosen C++ as a base, is because i have the most expecrience in it (compared to other languages) and i believe that it can be considered the 'Swiss Army Knife of Programming'. Another reason it that its one of the few languages i know that allowed for manual memory allocation, which means i could easily simulate a language that uses a garbage collector, but would never be able to simulate C++ in a language that has a garbage collector. (aka C++ can simulate [Java, etc] but [Java,etc] can never simulate C++. Ofcourse i could also use `C` as a base but trying to simulate Objected Oriented code in it is a real eye sore, and believe it or not i have a social life with family and friends ;D. I'd rather not walk that razor blade. 

The reason i am using these brackets `<, >` for the language specific code, is that as far i am aware no major language every uses these bracket as a substitude of a scope. Most use something like `{, }` or `(, )` or `start; end;`

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

(2) Only the standard library of each programmign language and the LY++ Library will be supported in the first stages of the project. This is because the project would not benefit from trying to support all the libraries. Instead i want to focus on the LY++ parser it self to make it faster, and make it detect/translate code from other languages more accuratly in C++. 
- But with that i would have to note that i will base much of the LY++ library on pre-existing libraries like QT etc so i would this most of the pre made libraries should suffice for the early adopters/users/testers. 

(3) Compile time error messages will be in the C++ language format for the first versions of LY++, so i dont have to look/learn the way these messages are display in each different language. This will come in the future how ever as i am aware that each person is used to "how their error messages are displayed". 

## Roadmap and future ideas

[ Limiting my self only to major languages like C, Haskel, Java, C#, C++, Python ]
1. First I want to start by allowing `<blocks>` to translate code from the syntax of other programming languages but ignore their own standard library. I am just interested if i can create a basic parser with some `ML` that can detect a language and create an apporiate `C++` translation. 
2. Next I want to ensure that the parser can distinguish between the different blocks in the tree and ensure that they can include/use each others code realiably
3. When the basic building 'blocks' (you see what i did there :p) of the parser are done, I want to do the grindier job, which is learning how the standard library of these languages work and ensure the parser can distinguish between them/translate correct `C++` code. 

[ Basic parser is done, now i can focus on supporting the more recent of niches like Rust, Ruby, Cobalt, Lisp, Go, Kotlin, R, ... ]
1. First i want to support Rust, Go and Kotlin, than i will look into supporting some more requested languages. 

[ Making the language/parser finally usefull! ]
After i am satisfied with the amount of supported languages, i can start on developing the reason this language/parser existst, namely the 'Erlang like' features. 
1. Ensuring that terminated tasks(or those that crash) do not affect the execution of other tasks. 
2. Allowing people to simulate a Tree of tasks from a config file (somthing like `.json` should suffice) when they want to add another node without disrupting the execution of the current tree. 
3. Allowing people to append the execution tree without disrupting the execution of the current tree. 
4. Allowing people to change or remove a node in the execution tree without disrupting the execution of the current tree. 
5. Giving people easier tools to debug which node of the execution tree causes issues. 
6. Allowing LY++ to be ran inside a VM. 
7. Allowing multiple VM to be linked in a tree with their own execution tree. 
    - So if a program that is ran on VM1 crashed the entire VM (somehow) VM2 will not be affected and can detect/respond to this failure.
    
## Things that interest myself

I always wanted to create my own 'mathematical programming' language from scratch, so i might work on this project after the main roadmap of LY++ is finished (meaning everything to make it a usefull language/parser). 

Another i have not talked about is how the `make` files are generated. In the beginning i would opt to generate these automatically without any use configuration, but in the future i would like to implement some system that can import a `.json` file with some settings and than generate apporiate `cmake` of `make` files. For example you could have an option to more easily use libraries **That are suported** within specific blocks. For example:
```json
{"task": {
  "id": "robot-code",
  "UseCustomBuildFiles": "False",
  "CustomBuildFileDir": "-",
  "UseExternalLibs": {
    "ExternalLibs": [
      {"name": "ArUco", "type": "shared"},
      {"name": "OpenCV", "type": "file", "path": "path/to/file.so"},
      ...
    ]
  }
}}
...
```

## TLDR

forgot to mentin what file extentions will be used xD. `.lypp` for sources and `lyhh` for headers. Sorry i got a bit carried away with the different examples
