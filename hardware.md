# TABLE OF CONTENTS
* [SECTION 1](#section_1)
* [Header](#header)
    - [header guard](#include_guard)
    - [Enabling interoprability bewteen c and c++](#enabling-interoperability-between-c-and-c)
    - [Unicode macro](#unicode-macro)




<br/>
<br/>
<br/>
<br/>
<br/>


# PRACTISE QUESTIONS.
1. What is #ifndef and how do you end it?.
2. Explain how to enable interoperabiility between c and c++ programs.




<br/>
<br/>
<br/>
<br/>
<br/>

#
#
# Section 1
# C programming language

# HEADER

### include guard

Called Include Guard (or a Header Guard), They prevent a very common error 
called Redefinition, which happens if the same header file is accidentally 
included more than once in a single project.

Without these guards, if you have a complex project where multiple files 
include tinydir.h, the compiler would try to define the same structs and 
functions multiple times. In C, you aren't allowed to define the same thing 
twice, so the compiler would throw a "Redefinition Error" and stop building 
your app.


```c
#ifndef TINYDIR_H
#define TINYDIR_H
```

### Enabling interoperability between c and c++

```c
#ifdef __cplusplus
extern "C" {
#endif
 
 ///code that must respect this rule

 #ifdef __cplusplus
}
#endif

````

**____cplusplus__** is a predefined macro, If you are using a C compiler
(like gcc), this macro does not exist. The compiler ignores everything 
inside that #ifdef block.

If you are using a C++ compiler (like g++), the compiler automatically
defines __cplusplus. This tells the header: "Hey, a C++ program is reading me 
right now, so I need to behave differently."

**__extern "C"__** <br/>

C and C++ handle function names differently when they compile

C keeps names simple. A function called tinydir_open stays tinydir_open in
the compiled code.

C++ supports Function Overloading (having two functions with the same name 
but different arguments). To make this work, C++ "mangles" the names. 
tinydir_open might become something unrecognizable like _Z12tinydir_openPv.

If a C++ program tries to call tinydir_open, it will look for the mangled
version and fail to find it because the library was compiled as plain C.
extern "C" tells the C++ compiler: "Stop mangling! Treat these functions like
standard C functions."

### Unicode macro

```c
#if ((defined _UNICODE) && !(defined UNICODE))
#define UNICODE
#endif

#if ((defined UNICODE) && !(defined _UNICODE))
#define _UNICODE
#endif
```

In the world of C and Windows development, these two macros are used to 
tell the compiler how to handle text strings.

* UNICODE (No underscore): This is used by the Windows API (the header files 
like windows.h). When this is defined, functions like MessageBox are directed
to their "Wide" version (MessageBoxW), which supports international
characters.

* _UNICODE (With underscore): This is used by the C Runtime Library (the
standard functions like header.h). When this is defined, functions like
_tcslen (a generic string length function) are directed to wcslen (the
wide-character version).

It prevents a situation where your standard C functions are trying to use
16-bit characters while your Windows UI functions are still expecting 8-bit
characters, which would cause your app to crash or display "Chinese" looking
gibberish.

