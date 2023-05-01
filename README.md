Download Link: https://assignmentchef.com/product/solved-cis450-programming-lab-assignment-2-diffuse-the-binary-bomb-lab
<br>
<strong> </strong><span style="font-size: 2.61792em; letter-spacing: -1px;">1. Problem Statement</span>




This assignment deals with using the GDB Debugger and friends to analyze code segments and diffuse a binary bomb. The bomb is a 32-bit binary ELF executable, <strong>kaboom. </strong>The bomb is also available as a 64-bit binary ELF executable, <strong>kaboom64</strong>. For this assignment, you should test your solution on a CS Linux machine – in particular, we will grade the lab using cslinux.cs.ksu.edu.




<h1>2. Logistics</h1>

<strong> </strong>

The only file to be submitted via K-State OnLine will be your answers in a plain ASCII text file, lab2.txt. As you go through the exercises today, reference the following for help on GDB:




<ul>

 <li>Complete documentation on GDB is available online at <a href="http://sources.redhat.com/gdb/current/onlinedocs/gdb.html">http://sources.redhat.com/gdb/current/onlinedocs/gdb.html</a><a href="http://sources.redhat.com/gdb/current/onlinedocs/gdb.html">.</a></li>

 <li>While running GDB, type <strong>help</strong>  or  <strong>help &lt;command&gt;</strong> for more information.</li>

 <li>The GDB help manual (man) page: $ <strong>man gdb</strong></li>

</ul>

We will also use the tool <strong>objdump</strong> in this lab. You can find help on objdump here: <em> </em>

<ul>

 <li><a href="https://sourceware.org/binutils/docs/binutils/objdump.html">http://sourceware.org/binutils/docs/binutils/</a><a href="https://sourceware.org/binutils/docs/binutils/objdump.html"><strong>objdump</strong></a><a href="https://sourceware.org/binutils/docs/binutils/objdump.html">.html</a></li>

 <li>And from objdump help output: <strong>objdump –help</strong></li>

</ul>

<h1>3. Tutorial</h1>




To get started, follow the steps of this tutorial. You will use the following programs:




<ul>

 <li>A C program that represents a simple binary bomb, boom.c, and the resulting executable created using gcc</li>

</ul>

with the command:  <strong>gcc -fno-stack-protector -fno-inline -fno-omit-frame-pointer –g –O1 –o boom boom.c</strong>

<ul>

 <li>Binary executable files, <strong>kaboom</strong> and <strong>kaboom64</strong>.</li>

</ul>

You are encouraged to discuss GDB commands and the steps of this tutorial section with other students. You can find the files used in the lab in <strong>/pub/cis450/programs/Lab2.tgz. </strong>

<h1>Generating Assembly Code: The –S flag</h1>

Download the code from /pub/cis450/programs/Lab2: <strong> </strong>

<strong>$cd ~/cis450; cp /pub/cis450/programs/Lab2.tgz .; tar xvzf Lab2.tgz; cd Lab2 </strong>

Copy the gzipped tar file from the public directory to our local directory and uncompress (z) and extract (x) files (f). Generate the output by invoking the <strong>make </strong>command.

<strong>$make </strong>

<strong>gcc -fno-stack-protector -fno-inline -fno-omit-frame-pointer -S -O1 boom.c gcc -fno-stack-protector -fno-inline -fno-omit-frame-pointer -g -O1 -o boom   </strong>

Note that you don’t get the source, kaboom.c, for the binary bomb we will be diffusing for this lab, you already have the executable, <strong>kaboom</strong>. If you get a “Permission denied” error when you try to debug kaboom below, just <strong>add execute (x)</strong> permission using the command: <strong>chmod +x  kaboom </strong>

<h1>Compiling for GDB: The -g flag</h1>

In order to create a symbol table for source-level debugging, you must compile with the ‘-g’ option. This option <strong>generates</strong> additional debugging information, such as the symbol table, and stores this extra information inside the executable.  The –O1 flag tells the compiler to perform Level-1 optimization. The –S flag tells the compiler to generate assembly code in the output file boom.s.

By looking at the source code, <strong>boom.c</strong>, determine what valid input could be entered by a user to diffuse the bomb. Execute the program (without GDB), and observe the results. Even if you blow up this test bomb, you will not lose any points.  Then, take a look at the assembly code generated for boom.c, and complete the mapping between the function operations and the corresponding assembly code.

#define MAGIC_NUMBER 13

..




void phase_1_of_1 () {    int args, x, y;

int sum = 0;




args = fscanf (stdin, “%d %d”, &amp;x, &amp;y);    if (args != 2)        explode();




sum = x+y;




if ((x&lt;=0)||(y&lt;=0))

explode();




if (sum != MAGIC_NUMBER)

explode(); }

<strong> </strong>

<strong> </strong>

<strong> </strong>

<strong> </strong>

<strong> </strong>

<strong> </strong>

<h1>Starting GDB</h1>

After you have compiled a C source file with <strong>-g</strong>, you can run this program with GDB and obtain a listing of the source code within the debugger; e.g., the source code is embedded inside the executable.




Start debugging boom using the command: $ <strong>gdb boom</strong>. The GDB prompt should appear: (gdb)




% <strong>gdb boom</strong>




Note that at any time, you can <strong>exit GDB </strong>with the quit command <strong>quit</strong>

<strong> </strong>

<h1>Running a program in GDB: run</h1>

You can execute a program within GDB with the command <strong>run</strong>

    Execute boom. The program should run normally.

(gdb) <strong>run</strong>

Starting program: ..boom..when prompted for input, enter “3 10”

<h1>3 10</h1>

You safely defused the bomb. Well done.

(gdb)  <strong>quit </strong>

 <strong>run args</strong> sets the command-line arguments to be input to your process.




Of course, just running the program isn’t generally enough to diffuse a bomb. We now look at some of the GDB commands used to examine the program as it executes.

<h1>Setting a breakpoint: break</h1>

You can set a <strong>breakpoint</strong> so that GDB will stop the execution at a certain line in the source code.  Use the command break, or the shortcut b, as follows:




<strong> break &lt;function name&gt;   break &lt;linenumber&gt;   break &lt;filename:function name&gt;  </strong>

<strong> break &lt;filename:linenumber&gt; </strong><strong> </strong>




You can also set a breakpoint at the memory address of an instruction:




<strong> break *&lt;address&gt; </strong><strong> </strong>




You can display all active breakpoints with the command: <strong>info break</strong> To delete a breakpoint, use one of the following:  <strong><sub>delete &lt;breakpoint number&gt;</sub></strong>




Set a breakpoint in the first line of the main function in boom and execute this program:




$ <strong>gdb boom </strong>

GNU gdb (Ubuntu 7.12.50.20170314-0ubuntu1.1) 7.12.50.20170314-git

(gdb) <strong>break main</strong>

Breakpoint 1 at 0x7fd: file boom.c, line 31. (gdb) <strong>break phase_1_of_1 </strong>

Breakpoint 2 at 0x79a: file boom.c, line 14.

(gdb) <strong>break explode</strong>

Breakpoint 3 at 0x780: file boom.c, line 9.

(gdb) <strong>run </strong>

Starting program: /net/files.cis.ksu.edu/exports/public/cis450/programs/Lab2/boom

Breakpoint 1, main () at boom.c:31

31      int main() {

(gdb) list

26

<ul>

 <li>if (sum != MAGIC_NUMBER)</li>

 <li>explode();</li>

 <li>}</li>

</ul>

30

<ul>

 <li>int main() {</li>

 <li>printf(“Welcome to the demo bomb.
”);</li>

 <li>printf (“Phase 1
”);</li>

 <li>phase_1_of_1();</li>

 <li>printf(“You safely defused the bomb. Well done.
”);</li>

</ul>

(gdb)<strong>cont </strong>Continuing.

Welcome to the demo bomb.

Phase 1




Breakpoint 2, phase_1_of_1 () at boom.c:14

14      void phase_1_of_1 () {

(gdb) <strong>where </strong>

#0  phase_1_of_1 () at boom.c:14

#1  0x0000555555554823 in main () at boom.c:34

(gdb) <strong>list 14 </strong>

<ul>

 <li>void explode() {</li>

 <li>printf(“KABOOM!!!
”);</li>

 <li>exit(1);</li>

 <li>}</li>

</ul>

13

<ul>

 <li>void phase_1_of_1 () {</li>

 <li>int args, x, y;</li>

 <li>int sum = 0;</li>

</ul>

17

18         args = fscanf (stdin, “%d %d”, &amp;x, &amp;y);

(gdb) <strong>cont </strong>

Continuing.

<strong>3 10 </strong>

You safely defused the bomb. Well done.

[Inferior 1 (process 8708) exited normally]

(gdb) <strong>quit </strong>




You can add a breakpoint at any point <strong>before</strong> you have called <strong>run</strong> or <strong>after</strong> you have called run and the program is paused.

<strong>Stepping through a function, by line: next, step, and continue to examine a program as it executes: </strong>

<strong> </strong>

<strong>next</strong> executes one line of source code in the current function.

<strong>step</strong> is similar to next, but will <strong>step <em>inside</em></strong> a function that is called on the current line.  <strong>next &lt;N&gt;</strong>  and  <strong>step &lt;N&gt;</strong>  increment N lines, rather than just one line.

<strong>cont</strong>inue executes the program until the next break point




The <strong>list</strong> command displays source code around your most recent listing; <strong>list &lt;number&gt;</strong> displays code starting at a particular line.  At any time, you can use <strong>continue</strong>(or just <strong>cont</strong>) to proceed automatically to the next breakpoint or until the program terminates.




<h1>Displaying data: print and watch</h1>

You can use the <strong>print</strong> command, or its shortcut <strong>p</strong>, to display the value of a variable in the current function. print is very flexible; you can dereference pointers, display addresses of variables, and index into arrays with the *, &amp;, and [] operators. A watchpoint is a breakpoint that stops execution whenever the value of a variable or expression changes and displays the old and new values of that expression. Set a watchpoint with the command <strong>watch</strong> <strong>&lt;expr&gt;</strong>

<ul>

 <li>Start boom from the beginning; <strong>gdb boom</strong>.</li>

 <li>Set a breakpoint at the <strong>phase_1_of_1</strong> function, and run the program:</li>

 <li>Use the <strong>print</strong> command to display the values of some variables; e.g, <strong>x</strong>, note that it is initially 0.</li>

 <li>Set another breakpoint after the fscanf( ) call, say line 19: <strong>break 19. </strong>Continue execution, and enter <strong>3 10</strong>, after entering the data, the breakpoint should be hit and print the value of x again. It should be 3.</li>

 <li>Set a watchpoint on a local variable; e.g., <strong>sum</strong>, and <strong>continue</strong> execution until the program terminates.</li>

</ul>

(gdb) <strong>cont </strong>Continuing.

3 10




Breakpoint 2, phase_1_of_1 () at boom.c:19

19         if (args != 2)

(gdb) <strong>print x </strong>

$2 = 3

(gdb) <strong>watch sum </strong>

Watchpoint 3: sum

(gdb) <strong>cont </strong>Continuing.




Watchpoint 3: sum




<strong>Old value = 0 New value = 13 </strong>phase_1_of_1 () at boom.c:24 24         if ((x&lt;=0)||(y&lt;=0))

(gdb)







Examining the call stack

    The <strong>backtrace</strong> command can be called at any time in GDB, and shows which function is associated with the current point of execution. You can call backtrace to see how the function got to where it is, that is, through what function calls. backtrace will display the current <em>call stack</em>, with one line per <em>frame</em>, where each frame effectively corresponds to a function call.




The commands <strong>up</strong> and <strong>down</strong> move “up” and “down” the call stack, that is, they will reset the current frame that is visible to you in GDB.  The commands <strong>where</strong> and <strong>info stack</strong> are equivalent to <strong>backtrace</strong>. With no arguments, <strong>frame</strong> displays information about the current stack frame. <strong> </strong>

<h1>Displaying machine instructions: disas</h1>




The command disas, or disas &lt;function&gt;, disassemble – displays assembly instructions for a given function (default is the current frame).




(gdb) <strong>disas phase_1_of_1 </strong>

Dump of assembler code for function phase_1_of_1:

0x000000000000079a &lt;+0&gt;:     push   %rbp

0x000000000000079b &lt;+1&gt;:     mov    %rsp,%rbp

0x000000000000079e &lt;+4&gt;:     sub    $0x10,%rsp

0x00000000000007a2 &lt;+8&gt;:     lea    -0x8(%rbp),%rcx

..

—Type &lt;return&gt; to continue, or q &lt;return&gt; to quit—<strong>q </strong>

Quit

Use the ‘&amp;’ operator to find the address of a function or variable; e.g.,

(gdb) <strong>print &amp;phase_1_of_1 </strong>

$1 = (void (*)()) 0x79a &lt;phase_1_of_1&gt;

(gdb)

<h1>Examining memory and registers</h1>




You can use the command <strong>x</strong> to examine the contents of a <strong>memory address</strong>. For example, to display instructions at address 0x400635, the first four lines of phase_1_of_1( ), you can use:




(gdb) x/4i 0x079a

0x79a &lt;phase_1_of_1&gt;:        push   %rbp

0x79b &lt;phase_1_of_1+1&gt;:      mov    %rsp,%rbp

0x79e &lt;phase_1_of_1+4&gt;:      sub    $0x10,%rsp

0x7a2 &lt;phase_1_of_1+8&gt;:      lea    -0x8(%rbp),%rcx

(gdb)

Here, /4i is an optional parameter, indicating that GDB should display 4 lines of instructions. Other formats are available, such as x/d for decimal, x/x for hex, and x/s for strings.  You can also use the command print/x $reg to display the value of <strong>register</strong> $reg. Again, /x indicates that GDB should display the output in hex format. To display the contents of every registers, use the command:  <strong>info reg</strong>isters.

<h1>Stepping through and into a function, by instruction: nexti and stepi</h1>

We saw above how we can use next and step to step through our program, line by line. The commands <strong>nexti</strong> and <strong>stepi</strong> are similar, but execute<strong> one instruction </strong>rather than one line our source code. These commands can be useful for lines that contain several operations or function calls.

<strong>Using </strong><strong> objdump to examine binary files.  </strong>

In addition to GDB, you can also use the program objdump to display a range of info about a binary file.




<strong>objdump -d</strong> disassembles a file (similar to <strong>disas</strong> in gdb).




<strong>objdump -t</strong> displays symbol table information

<ul>

 <li>Disassemble the executable boom. What information do you see that you did not get from GDB’s disas command?</li>

 <li>Use objdump and grep to find symbol table entry for the function phase_1_of_1; e.g., <strong>objdump –t boom | grep phase_1_of_1</strong></li>

</ul>

<strong>Problems to turn on for Lab #2: </strong>

<strong> </strong>

<h1>Problem Statement</h1>

The bomb is a 32-bit binary ELF executable, <strong>kaboom. </strong>For this assignment, you should use a CS Linux machine for the assignment. No source code is provided. Determine what input strings should be passed to the program <strong>kaboom</strong> via standard input so that the built-in bomb doesn’t explode. Each time the bomb explodes you will lose 0.5 points up to a maximum of 20 points lost.




<h1>Background</h1>

This bomb program is compiled using: <strong>gcc –Og –fno-stack-protector –fno-inline -fno-omit-frame-pointer –m32 -o kaboom kaboom.c; it can be found at /pub/cis450/programs/Lab2/kaboom</strong>, the 64-bit version is also available as <strong>kaboom64</strong>.

<strong> </strong>

Since the –Og switch is present and the -g switch is not present, the binary is optimized for debugging (no optimizations that mangle the code are included), but the binary contains a limited amount of debugging information. Usually adding the option –g works best for debugging with source code, but we don’t want you to be able to see the original source code. In this lab, you will be working with the assembly code directly.




<h1>Examining the Bomb</h1>

The symbol table is sometimes useful to identify calls to standard library functions, (e.g., printf), as well as the bomb’s own functions. Note that the symbol table is always present in the executable, even if the executable was compiled without the -g switch. You can look at all the bomb’s symbol table by using nm: <strong>nm kaboom </strong>

…

080486bf T explode

…

080486da T phase_1_of_5

08048734 T phase_2_of_5

080487b8 T phase_3_of_5

0804885b T phase_4_of_5

08048958 T phase_5_of_5

080486bd T report_explosion

08048600 T report_solution




Examine the symbols marked with a T (capital t), and ignore the ones that start with an _ (underscore), b, B, U, or R; e.g., <strong>nm kaboom | grep T. </strong>These are names of functions from the C program that was used to compile the bomb. Notice that there is a function called <strong>explode()</strong>; can you guess what this function does?  Next, take a look at the <strong>printable strings</strong> from the executable file using the command: <strong>strings kaboom | less</strong>




In this way, you may find clues that will help you defuse some of the phases of your bomb. You can use objdump to disassemble the bomb: <strong>objdump -d kaboom | less</strong>, or dump it to a file: <strong>objdump -d kaboom &gt; kaboom.dump</strong> to view with any text editor. In addition, the assembly version of kaboom is available as <strong>kaboom.s. </strong>




Part of the object dump (assembly code) for our example bomb:




kaboom:     file format elf32-i386

.LC4:

.string “KABOOOOM, THE BOMB EXPLODED!!!”

.. explode:     pushl   %ebp     movl    %esp, %ebp     pushl   %ebx     subl    $16, %esp     call    __x86.get_pc_thunk.bx     addl    $_GLOBAL_OFFSET_TABLE_, %ebx     leal    <a href="/cdn-cgi/l/email-protection" class="__cf_email__" data-cfemail="f7d9bbb4c3b7b0b8a3b8b1b1">[email protected]</a>(%ebx), %eax     pushl   %eax     call    <a href="/cdn-cgi/l/email-protection" class="__cf_email__" data-cfemail="5e2e2b2a2d1e0e120a">[email protected]</a>     movl    $1, (%esp)     call    <a href="/cdn-cgi/l/email-protection" class="__cf_email__" data-cfemail="72170a1b0632223e26">[email protected]</a>

phase_1_of_6:                       (ignoring tag lines starting with ‘.’)     pushl   %ebp     movl    %esp, %ebp     pushl   %ebx     subl    $20, %esp     call    __x86.get_pc_thunk.bx     addl    $_GLOBAL_OFFSET_TABLE_, %ebx     leal    -16(%ebp), %eax     pushl   %eax     leal    -12(%ebp), %eax     pushl   %eax

leal    <a href="/cdn-cgi/l/email-protection" class="__cf_email__" data-cfemail="7957353a4c393e362d363f3f">[email protected]</a>(%ebx), %eax     pushl   %eax

movl    <a href="/cdn-cgi/l/email-protection" class="__cf_email__" data-cfemail="80f3f4e4e9eec0c7cfd4">[email protected]</a>(%ebx), %eax     pushl   (%eax)     call    <a href="/cdn-cgi/l/email-protection" class="__cf_email__" data-cfemail="550a0a3c263a366c6c0a332636343b3315051901">[email protected]</a>     addl    $16, %esp     cmpl    $2, %eax     jne .L16

movl    -12(%ebp), %edx     testl   %edx, %edx     jle .L17

leal    0(,%edx,8), %eax     subl    %edx, %eax     cmpl    -16(%ebp), %eax     jne .L18

movl    -4(%ebp), %ebx     leave     ret

.L16:     subl    $12, %esp     pushl   $1     call    explode

.L17:     subl    $12, %esp     pushl   $1     call    explode

.L18:     subl    $12, %esp     pushl   $1

call    explode




Look at the code of explode(); try to figure out what it does.

Note, the string at .LC4 is <strong>“KABOOOOM, THE BOMB EXPLODED!!!”:</strong>




<h1>Running the bomb</h1>

The bomb can be invoked by using: <strong>./kaboom </strong>or inside the debugger using <strong>gdb kaboom</strong>, and <strong>run – WARNING, WARNING, WARNING, be ready to stop the execution at an appropriate spot to prevent an explosion — set the breakpoint before you execute the “run” command. </strong>

<strong> </strong>

The program waits for you to enter a string. You can enter the input from the keyboard, or redirect input to come from a text file: <strong>./kaboom &lt; lab2.txt  </strong>

The bomb then examines the string, and either explodes, or not.




<h1>GDB (GNU DeBugger)</h1>




Now all we need to do is completely understand the assembly code, and then we can defuse the bomb. In this problem, we will be dealing with a lot of code, which can be difficult to understand. Even if we do a good job, we might make a mistake and accidentally detonate the bomb. This is where the debugger, gdb, comes in. It lets us step through the assembly code as it runs, and examine the contents of registers and memory. We can also set breakpoints at arbitrary positions in the program. Breakpoints are points in the code where program execution is instructed to stop. In this way, we can let the debugger run without interruption over large portions of code, such as code that we already understand or believe is error-free.




<h1>Starting gdb</h1>

Start gdb by specifying what executable to debug: <strong>gdb kaboom </strong>

<strong> </strong>

We can run the bomb in the debugger just as we would outside the debugger, except that we can instruct the program to stop at certain locations and inspect current values of memory and registers. As a last resort, we can use (Ctrl-C) to stop the program and panic out. But this is not recommended and is usually not necessary, as long as we positioned our breakpoints appropriately.




To start a program inside gdb: (gdb) <strong>run</strong>




To start a program inside gdb, with certain input parameters:

(gdb) <strong>run [parameters]</strong>




Examples:

(gdb) <strong>run &lt; lab2.txt</strong>

(equivalent to ./kaboom &lt; lab2.txt , just this time inside gdb)

<h1>(gdb) run -d 1</h1>

(equivalent to ./kaboom -d 1; this is a made-up example in the specific case of the bomb program, as ‘kaboom’ supports no such parameters; this example is meant to demonstrate how things would work in general)




<h1>Exiting gdb</h1>

To exit gdb and return to the shell prompt:

(gdb) <strong>quit </strong>




Note that exiting gdb means you lose all of your breakpoints that you set in this gdb session. When you re-run gdb, you need to re-specify any breakpoints that you want to re-use. A common mistake is to forget this and then let the debugging proceed straight into the explode() routine.




<h1>Breakpoints</h1>

We wouldn’t be using gdb if all we did was run the program without any interruptions. We need to stop program execution at certain key positions in the code, and then examine program behavior around those positions. How do we pick a good location for a breakpoint?




First, we can always set a breakpoint at ‘main’, since every C program has a function called ‘main’. Dr. Evil accidentally gave us ‘boom.c’ in the previous lab. By examining this code and output above, we see that we might want a breakpoint at ‘phase_1_of_6’, as this is where our input is tested.




<h1>(gdb) break phase_1_of_6</h1>




Note: if you mis-type the name of the routine, gdb will print a warning and not set any breakpoints. Also, note that program execution will always stop just BEFORE executing the instruction you set the breakpoint on.




Another essential breakpoint to set is on the explode routine. For inputs that don’t solve the puzzle, this breakpoint will be your last safeguard before explosion. I recommend ALWAYS setting this breakpoint. In addition to that, I recommend setting another breakpoint inside explode(), positioned after the call to the routine that prints “KABOOM..”, but before the call to the routine that notifies the GTA of the explosion. This can be useful if you accidentally enter explode(), but don’t notice that you hit the safeguard breakpoint. After several hours of debugging, when concentration drops down in a moment of weakness, it can happen that you accidentally instruct the program to keep on going. Then, the second breakpoint will save you.




<h1>Terminating program execution within gdb</h1>

We can terminate the program at any time using kill:

(gdb) <strong>kill</strong>

Note that this doesn’t exit gdb, and all your breakpoints remain active. You can re-run the program using the run command, and all breakpoints will still apply.

<strong> </strong>

<strong> </strong>

<h1>Stepping through the code</h1>

To execute a single machine instruction, use:

(gdb) <strong>stepi </strong>

<strong> </strong>

Note that if you use ‘stepi’ on a callq instruction, the debugger will proceed inside the called function. Also note that pressing &lt;ENTER&gt; re-executes the last gdb command. To execute several ‘stepi’ instructions one after another, type ‘stepi’ once, and then press &lt;ENTER&gt; several times in a row.




Sometimes we want to execute a single machine instruction, but if that instruction is a call to a function, we want the debugger to execute the function without our intervention. This is achieved using ‘nexti’: (gdb) <strong>nexti </strong>




Program will be stopped as soon as control returns from the function; e.g., at the instruction immediately after the call in the caller function.




If you accidentally use stepi to enter a function call, and you really don’t want to debug that function, you can use ‘finish’ to resume execution until the current function returns. Execution will stop at the machine instruction immediately after the ‘callq’ instruction in the caller function, just as if we had called ‘nexti’ in the first place: (gdb) <strong>finish </strong>

<strong> </strong>

Note: make sure the current function can really be run safely without your intervention. You don’t want it to call explode(). To instruct the program to execute (without your intervention) until the next breakpoint is hit, use : (gdb) <strong>cont </strong>

<strong> </strong>

Note that the same warning as in the case of ‘finish’ applies. If program contains debugging information (-g switch to gcc; not the case for this assignment, but otherwise usually the case), we can also step a single C statement: (gdb) <strong>step</strong>




Or, if next instruction is a function call, we can use ‘next’ to execute the function without our intervention. This is just like nexti, except that it operates with C code as opposed to machine instructions: (gdb) <strong>next </strong>




<strong>Examining registers </strong>

To inspect the current values of registers:

<h1>(gdb) info reg</h1>

This prints out the current values of all registers.




To inspect the current values of a specific register:

(gdb) <strong>p $edx</strong>

To print the value in hex notation:

<h1>(gdb) p/x $edx</h1>

To see the address of the next machine instruction to be exectued:

(gdb) <strong>frame </strong>or, equivalently, you can inspect the instruction pointer register:

<h1>(gdb) p/x $eip</h1>




Normally, when debugging a C/C++ program for which the source code is available (but not for this assignment), you can also inspect the call-stack (a list of all nested function calls that led to the current function being executed): (gdb) <strong>where </strong>




<h1>Examining memory</h1>

To inspect the value of memory at location &lt;address&gt;:

(gdb) x/NFU &lt;address&gt;

where: N = number of units to display, F = output format (hex=h, signed decimal=d, unsigned decimal=u, string=s, char=c), U = defines what constitutes a unit: b=1 byte, h=2 bytes, w=4 bytes, g=8 bytes. Note that output format and unit definition characters are mutually distinct from each other.

<strong> </strong>

<strong>Examples: </strong>

To use hex notation, and print two consecutive 64-bit words, starting from the address 0x400746 and higher:

(gdb) <strong>x/2xg 0x5655579a </strong>

To use hex notation, and print five consecutive 32-bit words, starting from the address 0x400746:

(gdb) <strong>x/5xw 0x5655579a</strong>

To print a single 32-bit word, in decimal notation, at the address 0x400746:

(gdb) <strong>x/1dw 0x5655579a </strong>




<h1>To Turn In</h1>

Upload a copy of the input strings used to diffuse the bomb, <strong>kaboom</strong>; e.g., lab2.txt. Note that the number of explosions will be reported automatically via e-mail. If you successfully diffuse the bomb, that will also be reported via e-mail.

<strong> </strong>

<strong>Prizes: </strong>Prizes will be given to students who solve the most phases in the least amount of time. An e-mail message is also sent when you solve all phases, so let that one go.