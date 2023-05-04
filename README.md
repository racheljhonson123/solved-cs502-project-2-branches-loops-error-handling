Download Link: https://assignmentchef.com/product/solved-cs502-project-2-branches-loops-error-handling
<br>
In this project, we will enrich the simple arithmetic expression language we de ned in the rst project.

At the end of the project, we will be able to parse full programs; the only things missing will be functions and arrays. Our parser will generate an intermediate representation in the form of an Abstract Syntax Tree (AST).

Step 1: Getting Started

The project has been designed and tested for Linux/Mac OS. If you only have Windows installed on your personal laptop, consider running Linux in a VM or using the lab machines for the project.

If you use remote access to work on your project, please use one of the lab machines pod1-1 to pod1-20 with the suf x cs.purdue.edu (e.g. pod1-1.cs.purdue.edu) Download the skeleton le <a href="https://www.cs.purdue.edu/homes/wang603/cs502/proj2.tar.gz">here</a>.

tar xzvf proj2.tar.gz cd proj2

Step 2: Project Structure

Browse the Files

A description of the different les is provided below. The les you need to complete are <strong>Parser.scala</strong>, <strong>SemanticAnalyzer.scala</strong>, <strong>Interpreter.scala</strong>, and <strong>Compiler.scala</strong>. Following the path of the rst project, we are going to implement the parser step by step. Follow the comments in the code and nd the <strong>TODOs</strong>. The other part are also independent and can be develop separately.src/main/scala/project2/Util.scala

This le de nes multiple classes that are used to generate the code and run it on your machine. Nothing needs to be modi ed, but it is recommanded to read it and have an idea of what is happening behind the scenes.

<h3>gen/bootstrap.c</h3>

As we are generating assembly code which is OS dependent, we are using GCC to do the heavy lifting for us.

The boostrap le is a generic C le that is calling a function <strong>entry_point</strong> and is printing the result in <strong>stdout</strong>. Our compiler will generate the le <strong>gen/gen.s</strong> and will be assembled and bootstrapped by gcc: gcc bootstrap.c gen.s -o out

<strong>out</strong> will then print the result of our compiled expression.

<h3>src/main/scala/project2/Main.scala</h3>

The main function is de ned in this le. The data ow in this le can be seen as:

You will be implementing many different parsers in order to test them through the main function.

The AST generated will then go through the semantic analyzer. If there are no errors, then the code is going to be interpreted or compiled. We provide you with one interpreter and one compiler. You will have to write one of each yourself.

As our language becomes more complex, it will become more useful to read the code from a source le. A folder with sample les <strong>examples/</strong> has been provided. Here some examples how to run the code:

sbt &gt; run

Usage: run PROG [OPTION]    or: run FILE [OPTION]

OPTION: compX86, intStack, intValue (default)

&gt; run examples/valid_arithm.scala              —&gt; interpreter with the provided interpreter

&gt; run examples/valid_arithm.scala compX86      —&gt; x86 compiler (your code) &gt; run examples/valid_arithm.scala intStack     —&gt; stack interpreter (your code) However, it is still possible to run code from the command line, as in project 1:

sbt

&gt; run “val x = 5; x”

&gt; run “val x = 5; x” compX86

&gt; run “val x = 5; x” intStack

<h3>src/main/scala/project2/Parser.scala</h3>

This class contains the de nition of our intermediate language.

As we discussed in class, parsing the le one character at a time is not enough when we introduce more complex constructs. Also, our single digit numbers were a little bit limiting. The <strong>Scanner</strong> class de ned in this le tokenizes the code. You will have to implement the <strong>getNum</strong> method. Check out the other methods implemented there, as they are going to be useful for the next part.

The rest of the le is the de nition of each parser we are building, starting from a generic precedence arithmetic parser to the full language we want to target, including variables, if statements, and loops. What you have to do for this project is highlighted with TODOs in the code. We encourage you to read the comments and code carefully before proceeding.

<h3>src/main/scala/project2/SemanticAnalyzer.scala</h3>

While the parser is in charge of verifying that the input string follows the de ned syntax, the semantic analyzer veri es that the program described is meaningful and follows the rules. For example: val x = 2; x = 5

has a valid syntax. However ‘val’ is immutable: it is not allowed to be reassigned to.

Using the functions <strong>error</strong> and <strong>warn</strong>, enforce the following semantic rules:

unary operators supported “+”, “-“. Raise: unde ned unary operator binary operators supported “+”, “-“, “/”, “*”. Raise: unde ned binary operator

boolean operators supported “==”, “!=”, “&lt;=”, “&gt;=”, “&lt;“, “&gt;”. Raise: unde ned boolean operator

‘val’ can not be assigned. Raise: reassignment to val identi er needs to be de ned in the current scope before being used. Raise: unde ned identi er

Note that we allow a variable to be declared multiple times. The last de nition will be used when a reference to it is found. In this case only a warning is raised.

<h3>src/main/scala/project2/Interpreter.scala</h3>

As we have seen in class, now that our programing language accepts more than just mathematical expressions, we need to de ne the required behavior of our language. An interpreter can be used to de ne this meaning.

We provide you with an interpreter that is fully functional. Your job is to complete the <strong>Stack-based </strong>interpreter.

<h3>src/main/scala/project2/Compiler.scala</h3>

In this le, one compiler emitting Scala-like code has already been implemented. You need to implement a second compiler that can convert our AST into x86_64 code.

In the previous project, we considered two different ways of generating the assembly code, each of which has pros and cons: <strong>Stack-based</strong> code, which is not very ef cient, but can handle arbitrarily complex expressions; and <strong>Register-based</strong> code, which is ef cient, but can not handle arbitrarily complex expressions. <strong>For this assignment, we will use a register-based approach.</strong>

<h3>src/test/scala/project2/*Test.scala</h3>

These les contain some unit tests for the rst parsers. You will have to write your own tests for the others. There are some functions given to you in order to make the implementation easier.