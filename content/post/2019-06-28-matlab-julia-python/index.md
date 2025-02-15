---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "Matlab vs. Julia vs. Python"
subtitle: ""
summary: ""
authors: []
tags: []
categories: []
date: 2019-06-28T14:57:36-04:00
lastmod: 2019-06-28T14:57:36-04:00
featured: false
draft: false

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: ""
  focal_point: ""
  preview_only: false

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects: []
---
I've used [MATLAB](https://www.mathworks.com/matlab) for over 25 years. (And before that, I even used [MATRIXx](https://www.sciencedirect.com/science/article/pii/S1474667017616793), a late, unlamented attempt at a spinoff, or maybe a ripoff.) It's not the [first language I learned to program in](https://en.wikipedia.org/wiki/BASIC), but it's the one that I came of age with mathematically. Knowing MATLAB has been very good to my career. 

However, it's impossible to ignore the rise of Python in scientific computing. MathWorks must feel the same way: not only did they add the ability to [call Python directly from within MATLAB](https://www.mathworks.com/help/matlab/call-python-libraries.html), but they've adopted borrowed some of its language features, such as more aggressive [broadcasting](https://www.mathworks.com/help/matlab/matlab_prog/compatible-array-sizes-for-basic-operations.html) for operands of binary operators. 

It's reached a point where I have been questioning my continued use of MATLAB in both research and teaching. Yet so much comes easily to me there, and I have so much invested in materials for it, that it was hard to rally motivation to really learn something new. 

Enter the MATLAB-based [textbook](https://tobydriscoll.net/FNC) I've co-written for introductory computational math. The book has over 40 functions and 160 computational examples, and it covers what I think is a thorough grounding in the use of MATLAB for numerical scientific computing. So partly as self-improvement, and partly to increase the usefulness of the book, I set out this year to translate the codes into [Julia](https://github.com/tobydriscoll/fnc-extras/tree/master/julia) and [Python](https://github.com/tobydriscoll/fnc-extras/tree/master/python). This experience has led me to a particular perspective on the three languages in relation to scientific computing, which I attempt to capture below.

I will mostly set aside the issues of cost and openness. MATLAB, unlike Python and Julia, is neither beer-free nor speech-free. This is indeed a huge distinction—for some, a dispositive one–but I want to consider the technical merits. For many years, MATLAB was well beyond any free product in a number of highly useful ways, and if you wanted to be productive, then cost be damned. It's a separate consideration from the Platonic appeal of a language and ecosystem. 

When you do set cost aside, a useful frame for a lot of the differences among these languages lies in their origins. MATLAB, the oldest of the efforts, prioritized math, particularly numerically oriented math. Python, which began in earnest in the late 1980s, made computer science its central focus. Julia, which began in 2009, set out to strike more of a balance between these sides. 

## MATLAB 

Originally, every value in MATLAB was an array of double-precision floating point numbers. Both aspects of this choice, arrays and floating point, were inspired design decisions. 

The IEEE 754 standard for floating point wasn't even adopted until 1985, and memory was measured in K, not G. Floating point doubles weren't the most efficient way to represent characters or integers, but they were what scientists, engineers, and, increasingly, mathematicians wanted to use most of the time. Furthermore, variables did not have to declared and memory did not have to be explicitly allocated. Letting the computer handle those tasks, and whisking data types out of the way, freed up your brain to think about the algorithms that would operate on the data. 

Arrays were important because numerical algorithms in linear algebra were coming into their own, in the form of [LINPACK](https://en.wikipedia.org/wiki/LINPACK) and [EISPACK](https://en.wikipedia.org/wiki/EISPACK). But accessing them with the standard bearer in scientific computing, FORTRAN 77, was a multistep process that involved declaring variables, calling cryptically named routines, compiling code, and then examining data and output files. Writing a matrix multiplication as `A*B` and getting the answer printed out right away was a game-changer.  

MATLAB also made graphics easy and far more accessible. No fiddly machine-specific libraries with low-level calls, just `plot(x,y)` and you saw pretty much what anyone else with MATLAB would see. There were more innovations, like baked-in complex numbers, sparse matrices, tools to build cross-platform graphical user interfaces, and a leading-edge suite of ODE solvers, that made MATLAB *the* place to do scientific computing at the speed of thought. 

However, design that was ideal for interactive computations, even lengthy ones, was not always conducive to writing good and performant software. Moving data around between many functions required juggling lots of variables and frequent consultation of documentation about input and output arguments. One function per disk file in a flat namespace was refreshingly simple for a small project, but a headache for a large one. Certain programming patterns (vectorization, memory preallocation) had to be applied if you wanted to avoid speed bottlenecks. Scientific computing was now being applied to far more domains, with vast amounts of different native types of data. Etc. 

MathWorks responded by continuing to innovate within MATLAB: inline functions, nested functions, variable closures, numerous data types, object-oriented features, unit testing frameworks, and on and on. Each innovation was probably the solution to an important problem. But the accumulation of 40 years of these changes has had the side effect of eroding the simplicity and unity of concept. In 2009 I wrote a [book](https://tobydriscoll.net/project/learning-matlab/) that pretty well covered what I considered the essentials of MATLAB in less than 100 pages. As far as I know, all of those things are still available. But you need to know a lot more now to call yourself proficient. 

## Python

In a sense the history of Python seems to be almost a mirror image of MATLAB's. Both featured an interactive command line (now widely called a REPL, for "read-eval-print loop") and freedom from variable declarations and compilation. But MATLAB was created as a playground for numerical analysts, while Python was created with hackers in mind. Each then grew toward the other audience through revisions and extensions.

To my eye, Python still lacks mathematical appeal. You have ugliness and small annoyances such as `**` instead of `^`, `@` for matrix multiplication (a recent innovation!), a `shape` rather than size of a matrix, row-oriented storage, etc. If you believe that `V.conj().T@D**3@V` is an elegant way to write $V^*D^3V$, then you may need to see a doctor. And there's zero-indexing (as opposed to indexes that start at 1). I've [read the arguments](https://www.cs.utexas.edu/users/EWD/transcriptions/EWD08xx/EWD831.html), and I don't find them decisive. It's clearly a matter of preference—the stuff of online holy wars—because you can cite ungainly examples for either convention. What I find decisive is that we have decades of mathematical practice indexing vectors and matrices from one, and most pseudocode makes that assumption. 

Beyond the petty annoyances, I find the Python+NumPy+SciPy ecosystem to be kludgy and inconsistent. Exhibit A is the fact that despite the language being rather devoted to object orientation, there exists a matrix class, and yet its use is [discouraged and will be deprecated](https://docs.scipy.org/doc/numpy/user/numpy-for-matlab-users.html). Perhaps MATLAB has simply corrupted me, but I find matrices to be an important enough type of object to keep around and promote. Isn't a major selling point of OOP that you can have `*` do different things for arrays and matrices? There are many other infelicities in this regard. (Why do I need a command called [spsolve](https://docs.scipy.org/doc/scipy/reference/generated/scipy.sparse.linalg.spsolve.html#scipy.sparse.linalg.spsolve)? Can't I just call `solve` on a sparse matrix? And on and on.) 

There are also places where the numerical ecosystem looks a little thin to me. For instance, the [quadrature and ODE solvers](https://docs.scipy.org/doc/scipy/reference/integrate.html) look like a minimal set in 2019. AFAICT there are no methods for DAEs, DDEs, symplectic solvers, or implicit solvers that allow inner Krylov iterations. Have a look at the references for these functions; they're mostly 30 or more years old—still good, but very far from complete. The Matplotlib package is an amazing piece of work, and for a while it looked better than MATLAB, but I find it quite lacking in 3D still. 

Some experts argue that there are deep reasons why Python code struggles to keep up in execution speed with compiled languages. I'm amused by the results of searching for ["python is too slow"](https://www.google.com/search?q=python+is+too+slow&oq=python+is+too+slow). The champions of Python make a lot of the same arguments/apologies that folks did for MATLAB back in the day. That doesn't mean they're wrong, but [there's more than just a perception problem](https://modelingguru.nasa.gov/docs/DOC-2676). 

I think I get why Python has been so exciting to many people in scientific computing. It has a some MATLAB-ish syntax and power, available from a REPL. It has great tools around it and plays well with other languages and areas of computing. It offered that at no cost and with much better long-term reproducibility. Clearly, it works well for a lot of people who probably see little reason to change. 

But for the things I know how to do in scientific computing, Python feels much more like a chore to learn and use than I'm used to. We won't know for a while whether it will continue to sweep through the community or has already neared its peak. I have no special predictive powers, but I'm bearish. 

## Julia 

Julia has the advantages and disadvantages of being a latecomer. I applaud the Julia creators for [thinking they could do better](https://julialang.org/blog/2012/02/why-we-created-julia):

> We want a language that’s open source, with a liberal license. We want the speed of C with the dynamism of Ruby. We want a language that’s homoiconic, with true macros like Lisp, but with obvious, familiar mathematical notation like Matlab. We want something as usable for general programming as Python, as easy for statistics as R, as natural for string processing as Perl, as powerful for linear algebra as Matlab, as good at gluing programs together as the shell. Something that is dirt simple to learn, yet keeps the most serious hackers happy. We want it interactive and we want it compiled.

To a great extent, I believe they have succeeded. Late along the road to version 1.0 they seemed to downplay the REPL a bit, and there were some almost gratuitous lurches away from MATLAB. (How exactly is `LinRange` better than `linspace`?) These are quibbles, though. 

This is the first language I've used that goes beyond ASCII. I still get an unreasonable amount of satisfaction from using variables like `ϕ` and operators like `≈`. It's more than cosmetic; being able to look more like the mathematical expressions we write is real plus, though it does complicate teaching and documentation a bit. 

Working in Julia exposed to me that I picked up some programming habits because of MATLAB's choices, not inherent superiority. Vectorization is not natural for many things. It's eye-opening to find in Julia that you can vectorize any function just by adding a dot to its name. Constructing a matrix through a [comprehension](https://docs.julialang.org/en/v1/manual/arrays/#Comprehensions-1) makes nested loops (or `meshgrid` tricks) look like buggy whips in comparison, and avoiding a matrix altogether via a [generator](https://docs.julialang.org/en/v1/manual/arrays/#Generator-Expressions-1) for a simple summation feels like getting something for nothing. (I'm aware that Python has similar language features.) 

The big feature of *multiple dispatch* makes some things a lot easier and clearer than object orientation does. For instance, suppose you have Wall and Ball classes in a traditional object-oriented language. Which class should detect a collision of a Ball with a Wall? Or do you need a Room class to play referee? These kinds of questions can drive me to distraction. With multiple dispatch, data is packaged into object types, but the methods that operate on data are not bound to a class. So 
```julia
function detect_collision(B::Ball,W::Wall)
```
knows about the types but is defined independently of them. It's taken quite a bit of programming for me to appreciate how interesting and potentially important the notion of multiple dispatch is for extending the language. 

The numerical ecosystem has been evolving rapidly. My number one example is [DifferentialEquations.jl](http://docs.juliadiffeq.org/latest/index.html), written by the amazing [Chris Rackauckas](http://chrisrackauckas.com/). If this software doesn't win the Wilkinson prize soon, the system is broken. Just go to the site and prepare to be converted. 

I have yet to see the big speed gains over MATLAB that Julia promises. Partly that's my relative inexperience and the kinds of tasks I do, but it's also partly because MathWorks has done an incredible job automatically optimizing code. It's not an aspect of coding that I focus on most of the time, anyway.

Programming in Julia has taken me a while to feel comfortable with (perhaps I'm just getting old and crystallized). It makes me think about data types more than I would want, and there's always the sneaking suspicion that I've missed the Right Way to do something. But for daily use, I'm about as likely to turn to Julia as MATLAB now. 

## The bottom line

MATLAB is the corporate solution, especially for engineering. It's probably still the easiest to learn for basic numerical tasks. Meticulous documentation and decades of contributed learning tools definitely matter. 

MATLAB is the BMW sedan of the scientific computing world. It's expensive, and that's before you start talking about accessories (toolboxes). You're paying for a rock-solid, smooth performance and service. It also attracts a [disproportionate amount of hate](https://www.google.com/search?q=i+hate+matlab). 

Python is a Ford pickup. It's ubiquitous and beloved by many (in the USA). It can do everything you want, and it's built to do some things that other vehicles can't. Chances are you're going to want to borrow one now and then. But it doesn't offer a great pure driving experience.

Julia is a Tesla. It's built with an audacious goal of changing the future, and it might. It may also become just a footnote. But in the meantime you'll get where you are going in style, and with power to spare. 
 