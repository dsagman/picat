# What I Wish I Knew When Learning Picat

July 2025

I have long been interested in constraint programming, which has been referred to as the "Holy Grail" of programming where the user states the problem and the computer solves it. https://dl.acm.org/doi/fullHtml/10.1145/242224.242304

While this is far from reality, constraint programming can reduce and scan the search space for NP dynamic programming problems without the user having to explictly code the backtracking/search algorithm. Examples include: shortest path, knapsack, n-queens, Soduko and so on. 

Picat is a great language for learning these concepts and also an interesting general purpose programming language. 

My background in programming is mostly procedural (Python) and pure functional (Haskell). So my challenge with Picat is multi-faceted: 1. Learn how constraint programming works in general, 2. Learn how constraint programming works in Picat, 3. Learn how Picat works.

Because Picat is a descendent of Prolog, it's core is a logic programming paradigm. And while the Picat manual is excellent, it moves along a quick clip and I had to read it very carefully to understand what I didn't understand. Think Calculus textbook. It's all documented, but makes sense sometimes only after you've learned it.

Also, because it's a niche language, there's a limited amount of online support. There's no StackExchange/Picat. (There are a few Picat questions there though!) I was lucky to get in contact with one of the key developers/users of the language, Håkan Kjellerstrand, who kindly helped me through many a head scratching moment. He also made a customized ChatGPT with Picat's documentation that has helped me as well, although like all LLMs, it can hallucinate solutions that don't work and has a tiny  training dataset compared with JavaScript.

This document is my attempt to share what I learned and is intended for programmers who have similar experience to me. A reasonable grasp of a common language such as Python, C, JavaScript, etcc or a background with a functional language such as Haskell or OCaml, but not much use of Prolog or other logic programming language, and possibly no idea what constraint programming is. 

Consider this a rough draft representing my personal learning curve. This document is not intended to be read top to bottom, but rather to help you if you get stuck where I did and hopefully give you the insight to move forward. As such, there's some redundancy between sections because I'm trying to make them self-contained as much as possible.


## About Picat

Picat is:

1. A dynamically typed hybrid of imperative, logic, functional and constraint programming. 
2. A programming language with support for Boolean Satisfiability (SAT), Mixed-Integer Programming (MIP), Satisfiability Modulo Theories (SMT), and Finite Domain (FD) Constraint Programming (CP) solvers.
3. Combines some of the best ideas of Prolog, Python, Haskell, MiniZinc/z3, OR-Tools, COIN-OR.
3. A wonderful resource for learning logic and constraint programming.

Answer: all of the above.

## Picat vs. Other Languages


| Other Language      | Picat                 
| :----------   | :----                  
| Imperative: Python, JavaScript   | = Straightforward syntax <br> = Print from any statement to debug  <br> - Libraries are limited <br> - Community is small
| Functional: Haskell, OCaml| = Functional concepts  <br> - No lambdas
| Solvers: Z3, MiniZinc  | + Full programming language vs. just a solver
| Logic: Prolog  | + Functions <br> = Unification, non-determinism, tabling
---
\+ Advantage, = Simliar, - Disadvantage

### Picat Has Common Programming Concepts

| Concept            |  Picat                        |
| :----------------  | :------                     |
| Variables          | `A=5, B=3.4, C=(1234 mod 7). |
| Strings            | `println("Hello World.")` |
| Linked Lists       | `MyList =[3,4,6,1,56,123.65,"a string?",[a,sub,list]]` |
| Arrays (O(n) access)  | `My2DArray = {{1,2},{3,4}}, println(My2dArray[1,2]).` |
| List Comprehension |  `Xs = [X : X in 1..5, X != 2].`   |
| Pattern Matching   |  `head([H\|T])=H.`  |
| Loops              |  `foreach (X in MyList) Y=X*X,println(X) end.` |
| Recursion          | `fib(0)=1. fib(1)=1. fib(N)=fib(N-1)+fib(N-2).`|
| Output           |  `print()`, `println()`, `printf()`  |
| Input             | `read_file_lines("datafile.txt").`|
| Higher Order Functions| `map(reverse,MyStringList).` or `MyStringList.map(reverse).`|

Also: Hashmaps, Sets, Ordered Sets and Binary Heaps.

### Things Picat Has In Common With Logic Progmming (Prolog) 

- Variables start with a Capital Letter.
- Atoms
- Unification
- Finite Domain Variables
- Tabling (Automatic Memoization)
- Non-Determinism

### Things Picat Has Uniquely (I think)

- Planner
- Solver polymorphism
- Full programming language 

### Things Picat Doesn't Come With

- Classes, Objects   
- Lambdas/Anonymous Functions         
- Strong Typing, Type Hinting, Monads
- Monads 

### Things I Still Don't Fully Understand

- Structs



More at: https://picat-lang.org/download/picat_compared_to_prolog_haskell_python.html



## Constraint Programming



## Stuff I Have To Remind Myself On

My primary programming languages are Python and Haskell. I often make syntax errors or cause logic problems because I forget about these items below. If you come from these or a C-syntax style language, you may also need to read this.

### Variables
- Variable names must have an initial capital letter or underscore. 
- Like Prolog, Picat programmers tend towards short, often single character variable names.
- Long variable names are bi-capitalized. 
```
A = 5. % OK

_A = 5. % OK

a = 5. % Parses, but the result is 'false'. Because the atom 'a' does not unify with the number 5.

A=5,b=4. % Parses, but fails because the atom b!=4, and therefore A does not get bound to 5.

b=4;A=5. % A is bound to 5. because b!=4 and ';' means 'or'!

NoJavaVariableNamesHere=true. % OK, but this is not normal Picat style
```

### Assignment (Binding) vs. Unification (Bind or Fail) vs. Equality (Only Numbers)

Here be dragons. Or at least a sharp corner to hit your head on when you realize your program is failing because you used the wrong one.

Logic programming uses the concepts of binding and unification. Binding is similar to assignment in other programming languages. Unification attempts to validate if the left and right side are the same, and if not either binds one side to the other or fails. Examples will help here.

```
A. % If A hasn't been defined then *** error(instantiation_error,call).

println(A). % If A hasn't been defined then _3d084e8, meaning A is pointer to the memory address 3d084e8, which is "empty" and available to binding to a value.

A=5, println(A). % A is bound to the value 5, and the unification succeeds allowing the statement to print 5. 
```

Read the above as 
 1. `A` points to an empty memory location.
 2. The empty memory can be made/shown to be equivalent to `5` through unification
 3. The unification binds `5` to `A` through the memory address
 4. `,` means the logical and
 5. Because the left side of the `,` is true we can output the value of memory address of `A` to the terminal.

Compare this with:

```
A=5, A=3, println(A). % Fails. 
```
Read the above as:
1. `A=5 `binds/unifies/assigns `5` to `A`. 
2. `A=3` attempts to unify `A` with `3`. This fails `5` cannot be made to equal `3`.
3. Because 2 is false, the overall `,` clause is false and the `println` doesn't happen.
4. NOTE: The entire clause fails. `A` will not be `5` after this clause!

PUT IN EXAMPLE WITH OR!!!


XXxXXXXXXXX


- Lists are usually decomposed using H and T as in `max([H|T])=max(H,max(T)).`


- prolog style unification
- printf is your friend or print([])
- := force vs == test vs = fail

- can’t if statement on true. need X==true or X=true =:= 

!= !== =\=

- #= domain variable
- procedure vs function
- when to use a comma (and not to)
- type error string/int
- what happens if you invent a variable
- accumulator for base case
- length is not a constraint, but sum [1: X in …] is
- no lambda but neat trick: call (or apply) using a function name in a variable. or list item! - dynamic dispatch
- $ means literal
- neat trick ; for case/multiple if then else
- =>? opposite of prolog !, but we also have ! (!)
- cond not really in the manual
- -> not really in the manual
- instead of if, sign function
- term vs string
- “” vs ‘’ string (check this one)
- using @
- 2d array notation. link to rosetta code
- bind_vars
- time and time2


## Using the Manual
- The manual is the single most important document. It has everything, but can be very terse, and so must be read closely
- Not every function/predicate has an example
- The index at the back lists all the commands and hyperlinks to them


## Syntax to Remember

-  All statements have to end in `,`, `;` or `.` 
- `,` means "and", `;` means "or" and `.` means "end of statement". 

    ```
    A = 2, B = 3.
    Name.length > 0; Name := "I'll just call you Larry.".
    ```

- `if` and `foreach` require an `end`. The statement just before this `end` doesn't need a `,`. Picat used to enforce this syntax, but doesn't anymore. Also, the condition in both should be enclosed in `()`.
    ```
    curve(Grade) = (Letter,Comment) => 
        if (Grade > 50) 
        then (Letter = A, Comment="Nice.") 
        else (Letter = F, Comment="See me.") 
        end. 

    % then keyword is optional if you enclose in ()
    curve(Grade) = Letter => 
        if (Grade > 50) 
        (Letter = A) 
        else (Letter = F) 
        end. 

    % note: multiple iterators are allowed, they are treated like nested loops
    foreach(X in 1..10, Y in 1..10) 
        A[X,Y] := my_hash_function(X,Y) % <- comma optional
    end.
    ```


## Resources

- Picat website https://picat-lang.org/ 
- The Manual https://picat-lang.org/download/picat_guide.pdf 
- Karlova University Course https://jbulin.github.io/teaching/fall/nopt042/
- A ChatGPT version that knows something about Picat. (It can get confused between Picat and Prolog.) https://chatgpt.com/g/g-EEpNUZ9H1-picat-prodigy 

### Code Exampls 
- Rosetta Code. Useful to compare to a language you know https://rosettacode.org/wiki/Category:Picat 
- The **best** resource https://hakank.org/picat/ 
- Constraint Programming Problems (multiple languages) https://www.csplib.org
- Advent of Code solutions
    + Neng-Fa Zhou https://github.com/nfzhou/aoc/tree/main
    + https://github.com/DestyNova/advent_of_code_2024
- My Advent of Code
    + 2016 https://github.com/dsagman/advent-of-code/tree/main/2016
    + Planner https://github.com/dsagman/advent-of-code/blob/main/2015/day22/day22.pi
    + Knapsack https://github.com/dsagman/advent-of-code/blob/main/2015/day24/day24.pi


### Modules

- JSON encoder https://github.com/nfzhou/json-picat

### Editor Plugins

- VS Code https://marketplace.visualstudio.com/items?itemName=arthurwang.vsc-picat
- Emacs https://github.com/nfzhou/emacs-picat-mode/tree/main
- Jupyter Kernel ipicat https://pypi.org/project/picat-kernel/

    ipicat has a bug. change
    ```
    from IPython.core.display import display, HTML, Javascript
    ```
    to
    ```
    from IPython.display import display, HTML, Javascript
    ```

### Optimization Resources
- COIN-OR open source https://github.com/coin-or/COIN-OR-OptimizationSuite
- Z3 (Microsoft) https://microsoft.github.io/z3guide/docs/logic/intro/
- OR-Tools (Google) https://developers.google.com/optimization
- CPLEX (IBM) https://ibmdecisionoptimization.github.io/tutorials/html/Linear_Programming.html


## Appendix: Using Picat For Instruction

Stuff about importing student answers as modules.