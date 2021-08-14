---
tags: assembly
toc: True
---
# Introduction
In order to keep my assembly-related documents easy to read and understand, I've written some macros for the GNU-assembler (also known as GAS).

These macros will also make it easier for you to program in assembly language.

The macros are not restricted to Arm Cortex-M; they can be used for other Arm architectures as well.

This document is not meant as being a "manual" for the GNU assembler, but rather as a detailed guide on some of the most common directives.

I'll try and keep using the tutorial-style, so you'll know exactly what is going on and why.

# This document will teach you:
how to instruct your assembler to be more convenient
how to write macros
the most important assembler directives
how to shorten your sources for clarity
# Command line options
When you invoke the GNU assembler, there are a few useful command-line options that you should know about:

    -mimplicit-it=always

            This option allows GAS to automatically generate IT instructions for conditional instruction execution.

            That means your code will be easier to read, but it also means that it might cause other assemblers to complain when assembling your code.

    -defsym SYMBOL=value

            Assigns a value to a symbol. This is useful for conditional assembling. The value is not optional and must be specified.

            For instance: -defsym DEBUG=1

    -mcpu=cortex-m3

            This directive sets the cpu type. Other examples are:

    -mcpu=cortex-m4

    -mcpu=cortex-a7

I recommend that you make sure your Makefile specifies -mimplicit-it=always to the assembler.

# A few assembler directives
Assembler directives have nothing to do with assembly language.

The assembler directives are used to tell the assembler to do something.

For instance: Defining a symbol, change sections, repeat code, change the location counter, etc.

The .macro directive defines the start of a new macro, the name of the macro and the macro arguments.

                    .macro              macroname [argument{,arguments}]

WARNING: Do NOT place any numbers in the argument list as the assembler can NOT handle this. Such errors will NOT be reported by the assembler and then you may find out after your product has shipped that things do not work as expected.

Note: I recommend using TABs rather than SPACEs. Personally my tab-width is 4 characters, and I use 5 tabs before each column (so that they line up nicely, that is). If your favorite editor can tell the difference between assembly language files and other files, such as .c or .cpp files, then I recommend a tab width of 20. I find 20 characters for each field being a good balance.

You can specify a qualifier for each argument.

By default, an argument is optional; that means you can omit it if you want; this will make it 'blank'.

The ':req' qualifier tells the assembler that the argument is required.

The ':vararg' qualifier tells the assembler that one or more arguments may optionally follow.

Example:

                    .macro              myMacro first:req,second,more:vararg

The 'first' argument is required. The second argument is optional. More arguments may optionally follow.

As the first argument is required, we will know it's there, so we can use it straight away without checking anything.

                    .byte               \first

In order to find out whether or not an argument is specified, you can use the .ifb directive (if blank).

It can be useful for providing a default value for instance:

                    .ifb                \second

                    .byte               0

                    .else

                    .byte               \second

                    .endif

You can also use the .ifnb directive to test if an argument is not blank:

                    .ifnb               \more

                    myMacro             \more

                    .endif

So if more arguments follow, we'll invoke our macro again with those arguments as the first arguments.

To define the end of a macro, use the .endm directive.

                    .endm

If you need to have labels inside macros, you should know that you'll need to use a workaround for labels given via arguments.

This workaround is necessary, because you are allowed to use colons in macro argument names.

Thus so the assembler can tell what the name of the argument is, you can add a \() between the argument name and the colon...

\argument\():

The \() is necessary here, in order to separate the label name from the colon.

Speaking of labels in macros, there is a special symbol, which counts up by one, each time a new macro is used.

The name of this symbols is \@ - you can use it like the following:

                    .macro              loadString rD,string

                    ldr                 r0,=string\@

                    movs                r1,#2

                    .section            .rodata

string\@:           .asciz              "\string"

                    .text

                    .endm

The .text directive switches the current section to the .text section.

The .text section is normally used for storing code in.

This is usually going in your flash-memory of your microcontroller (but you can customize your linker-script, so that it puts it somewhere else)

                    .text

                    (put your code here)

The .data directive switches the current section to the .data section.

You can use the .data section for storing all kind of various data, which will be copied to the microcontroller's RAM, when your program starts up:

Binary values, strings, pointers, etc.

                    .data

hello_string:       .asciz              "Hello World!\n"

If you do not wish your data to be copied to SRAM, place them in the .rodata section.

There is no special directive for this, so you will have to use .section:

                    .section            .rodata

The .bss directive lets you place zero-initialized data in the .bss section:

                    .bss

                    .align              2

myVariable:         .space              4

myBuffer:           .space              256

The .space directive reserves a number of bytes in the current section. By default, it will be filled with zeroes.

If .space is not used in the .bss section, you can specify the value that should be used for filling as the second argument.

Note: section names other than .text, .rodata, .data and .bss needs extra arguments in order to work as you expect.

Example:

                    .section            .ramcode,"ax",%progbits

(Your linker-script needs to support those sections you specify, otherwise the sections will not be included in your output)

The .func directive can be used to tell the assembler that we're starting a function. This is only used for debugging information.

                    .func               name[,label]

A function definition needs to be paired with the .endfunc directive, in order to mark the end of the function:

                    .endfunc

The .type directive allows you to tell the assembler what type a symbol is. Most of the time we just use %function and %object.

                    .type               hexTable,%object

...or...

                    .type               qsort,%function

The .size directive can be used to tell the assembler how much space the data that a symbol points to is using.

For instance...

                    .size               qsort,.-qsort

... will calculate the total size in bytes of the function 'qsort', so that the linker can exclude the function if it's unused.

Note: When used inside an expression, the dot '.' means the current value of the location counter.

To create an equate, which is a symbol that is used only while assembling your code, you can use the .equ directive:

                    .equ                lives,3

...once the symbol is defined, its value can not be changed in the remaining part of the source code.

There is a symbol type, which resembles .equ; this is a .set symbol and you can change this as often as you want.

                    .set                counter,2

...the above assigns a value of 2 to the symbol called counter; but we can change it whenever we want...

                    .set                counter,counter+1

...here we add 1 to the counter, so now it would hold the value 3

A very important directive is the .rept directive. It repeats whatever is inside the .rept / .endr block.

Example:

                    .rept               128

                    str                 r0,[r1],#4

                    .endr

The above code will create 128 instructions that store a 32-bit word from r0 to memory at address r1 and advance r1 by 4 bytes.

The .rept / .endr directives can be used to make blocks of code that must run as quickly as possible.

You can combine .rept / .endr with counters, for instance:

                    .set                counter,0

                    .rept               10

                    str                 r0,[r1,#counter]

                    .set                counter,counter+8

                    .endr

Here r1 will stay the same all the time, but the counter will be changed each time .set is met.

You can also use .rept / .endr to generate tables, for instance in the .rodata section.

You may also find the .incbin directive useful for including binary data.

Those data can be anything you want, such as look-up tables, sounds or for instance button-pictures if you're using LCD displays.

In the following example, 'myTable' will be aligned on a 4-byte boundary (.align will align the location counter on a (1 << argument) byte boundary).

After that, the binary data from "filename.dat" will be included directly.

If you specify an offset and a length, .incbin also checks that the offset and length is in range and reports error if it isn't.

In our example, we'll start by reading from offset 16 in the file, and then read 1024 bytes.

                    .align              2

myTable:

                    .incbin             "filename.dat",16,1024

... The .incbin directive does not align the location counter for you at all.

You have to use .align for this purpose, also for any data/code that follows the .incbin directive.

Arm-specific assembler directives
The .syntax directive allows you to set the instruction set syntax.

I recommend setting the syntax to unified as the first thing in your sources (right after your .include files)

                    .syntax             unified

-This will make sure that the GNU assembler is using a modern syntax for Arm THUMB instructions.

For further information, see the binutils documentation: Using as: Instruction Set Syntax

The .thumb_func directive tells the assembler that the next symbol will point to Arm THUMB code, so ...

                    .thumb_func

myFunction:

                    movs                r0,#10

                    bx                  lr

... sets the least significant bit of myFunction's value to 1.

This allows the processor to detect that the code the myFunction symbol points to, is THUMB code.

It should be used for all functions labels.

The .pool directive allows the assembler to put a "literal pool" where this directive is placed.

The literal pool contains large values that you can't load using the "mov" or "movw" instructions.

For instance...

myFunction:         ldr                 r0,=123456789

                    ...

                    ...

                    bx                  lr

                    .pool

...will place a 32-bit word holding the value 123456789 right after the 'bx lr' instruction.

The following directives can be used to output values with a specific number of bits:

                    .byte               value{,value}

                    .2byte              value{,value}

                    .4byte              value{,value}

                    .8byte              value{,value}

...I prefer those over .hword, .short, .word, .int, .long, etc., because they do not explicitly guarantee that the size will be the same on all architectures.

Normally you would place such values either after a function (preferrably right after the literal pool) or in the .data section.

The .include directive allows you to reference source or header files just like C's #include directive.

Example:

                    .include            "myfile.i"

The macros
Using some of the above mentioned directives, we can now construct some useful macros. Feel free to add your own

                    .macro              GLOBFUNC  name:req,names:vararg

                    .global             \name

                    .type               \name,%function

                    .ifnb               \names

                    GLOBFUNC            \names

                    .endif

                    .endm

                    .macro              FUNCTION  name:req,names:vararg

                    GLOBFUNC            \name,\names

                    .func               \name,\name

                    .thumb_func

\name\():

                    .endm

                    .macro              ENDFUNC  name:req

                    .pool

                    .size               \name,.-\name

                    .endfunc

                    .endm

                    .macro              GLOBOBJ  name:req,names:vararg

                    .global             \name

                    .type               \name,%object

                    .ifnb               \names

                    GLOBOBJ             \names

                    .endif

                    .endm

                    .macro              OBJECT    name:req,names:vararg

                    GLOBOBJ             \name,\names

\name\():

                    .endm

                    .macro              ENDOBJ    name:req

                    .size               \name,.-\name

                    .endm

                    .macro              SECTION   name:req

                    .section            \name,"ax",%progbits

                    .endm

                    .macro              .rodata

                    .section            .rodata

                    .endm

You can save the macros to a file called "macros.i" and then include the file from your assembly source.

I recommend that you start your source files with the following 2 lines:

                    .include            "macros.i"

                    .syntax             unified
