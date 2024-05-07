# Discussion and Reflection


The bulk of this project consists of a collection of five
questions. You are to answer these questions after spending some
amount of time looking over the code together to gather answers for
your questions. Try to seriously dig into the project together--it is
of course understood that you may not grasp every detail, but put
forth serious effort to spend several hours reading and discussing the
code, along with anything you have taken from it.

Questions will largely be graded on completion and maturity, but the
instructors do reserve the right to take off for technical
inaccuracies (i.e., if you say something factually wrong).

Each of these (six, five main followed by last) questions should take
roughly at least a paragraph or two. Try to aim for between 1-500
words per question. You may divide up the work, but each of you should
collectively read and agree to each other's answers.

[ Question 1 ] 

For this task, you will three new .irv programs. These are
`ir-virtual?` programs in a pseudo-assembly format. Try to compile the
program. Here, you should briefly explain the purpose of ir-virtual,
especially how it is different than x86: what are the pros and cons of
using ir-virtual as a representation? You can get the compiler to to
compile ir-virtual files like so: 

racket compiler.rkt -v test-programs/sum1.irv 

(Also pass in -m for Mac)

sub1.irv
(mov-lit r0 4)
(mov-lit r1 5)
(mov-lit r2 10)
(sub r2 r1)
(sub r2 r0)
(print r2)

add1.irv
(mov-lit r0 4)
(mov-lit r1 5)
(mov-lit r2 10)
(add r2 r1)
(add r2 r0)
(print r2)

div1.irv
(mov-lit r0 2)
(mov-lit r1 6)
(div r1 r0)
(print r1)

IR-virtual uses virtual registers, which are not limited by the hardware. While x86 has a fixed number of physical registers, which makes IR-virtual easier to program and optimization at higher levels of the compiler without worrying about the register location. IR-virtual also simplifies operation into more generic forms unlike x86 where you have to deal with addressing models and specific instruction for inputs and outputs. But since IR-virtual does not directly correspond to the physical machine instructions, we need to translate those virtual instructions to actual machine codes, which can cause a lot of error. Moreover, since it does not directly work on the hardware, it has less control over the hardware and may miss opportunities to leverage some hardware features.
[ Question 2 ] 

For this task, you will write three new .ifa programs. Your programs
must be correct, in the sense that they are valid. There are a set of
starter programs in the test-programs directory now. Your job is to
create three new `.ifa` programs and compile and run each of them. It
is very much possible that the compiler will be broken: part of your
exercise is identifying if you can find any possible bugs in the
compiler.

For each of your exercises, write here what the input and output was
from the compiler. Read through each of the phases, by passing in the
`-v` flag to the compiler. For at least one of the programs, explain
carefully the relevance of each of the intermediate representations.

For this question, please add your `.ifa` programs either (a) here or
(b) to the repo and write where they are in this file.

add1.ifa
(+ 1 2)
<img width="596" alt="Screenshot 2024-05-07 at 1 41 04 AM" src="https://github.com/ch0118/p5/assets/114699894/bd8d0a69-9b9d-4997-8e69-fc1d1a7921eb">

sub1.ifa
(- 2 1)
<img width="609" alt="Screenshot 2024-05-07 at 1 41 50 AM" src="https://github.com/ch0118/p5/assets/114699894/a957e6f3-cbec-4ede-a0a6-c8b72e3deeb4">

mul1.ifa
(* 2 3)
<img width="552" alt="Screenshot 2024-05-07 at 1 42 16 AM" src="https://github.com/ch0118/p5/assets/114699894/81fb95ae-7e16-4d96-955d-f9fa0a2886d1">
In the IfArith phrase, it defines what mul1 does, then in the IfArith-tiny phrase, it simplifies complex constructs into basic operations. Then ANF phrase makes the control flow and data dependencies explicit, and in the IR-virtual phrase, it allows the compiler to focus on logical instruction flow, then finally in the x86 phrase, it is the actual code that the CPU executes.

[ Question 3 ] 

Describe each of the passes of the compiler in a slight degree of
detail, using specific examples to discuss what each pass does. The
compiler is designed in series of layers, with each higher-level IR
desugaring to a lower-level IR until ultimately arriving at x86-64
assembler. Do you think there are any redundant passes? Do you think
there could be more?

In answering this question, you must use specific examples that you
got from running the compiler and generating an output.

from IfArith to IfArith-Tiny, it breaks (* 2 3) into more simple form, since (* 2 3) is already a simple form, it does not change, then when it get pass into ANF it turns into '(let ((x1254 2)) (let ((x1255 3)) (let ((x1256 (* x1254 x1255))) x1256))), which each operand and result is stored explicitly in a temporary variable. Then when it gets passed on to IR-virtual it became 
(label lab1257) (mov-lit x1254 2)
(label lab1258) (mov-lit x1255 3)
(label lab1259) (mov-reg x1256 x1254)
(imul x1256 x1255)
(return x1256) 
then being mapped into actual x86 assembly
mov esi, 2
mov [rbp-24], esi
mov esi, 3
mov [rbp-16], esi
mov esi, [rbp-24]
mov edi, [rbp-16]
imul eax, edi
mov [rbp-8], eax
mov rax, [rbp-8]

[ Question 4 ] 

This is a larger project, compared to our previous projects. This
project uses a large combination of idioms: tail recursion, folds,
etc.. Discuss a few programming idioms that you can identify in the
project that we discussed in class this semester. There is no specific
definition of what an idiom is: think carefully about whether you see
any pattern in this code that resonates with you from earlier in the
semester.

I think pattern matching is the one that stood out the most to me since our last few projects and exercises also uses pattern matching. Tail recursion is also something that I spot that we worked on through out the semester along with folding as well.

[ Question 5 ] 

In this question, you will play the role of bug finder. I would like
you to be creative, adversarial, and exploratory. Spend an hour or two
looking throughout the code and try to break it. Try to see if you can
identify a buggy program: a program that should work, but does
not. This could either be that the compiler crashes, or it could be
that it produces code which will not assemble. Last, even if the code
assembles and links, its behavior could be incorrect.

To answer this question, I want you to summarize your discussion,
experiences, and findings by adversarily breaking the compiler. If
there is something you think should work (but does not), feel free to
ask me.

Your team will receive a small bonus for being the first team to
report a unique bug (unique determined by me).

[ High Level Reflection ] 

In roughly 100-500 words, write a summary of your findings in working
on this project: what did you learn, what did you find interesting,
what did you find challenging? As you progress in your career, it will
be increasingly important to have technical conversations about the
nuts and bolts of code, try to use this experience as a way to think
about how you would approach doing group code critique. What would you
do differently next time, what did you learn?

