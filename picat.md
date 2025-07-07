# What I Wish I Knew When Learning Picat

July 2025

I've been trying to learn Picat. I am primarily interested in constraint programming, which has been referred to as the "Holy Grail" of programming where the user states the problem and the computer solves it. https://dl.acm.org/doi/fullHtml/10.1145/242224.242304

While this is far from reality, constraint programming can reduce and scan the search space for NP dynamic programming problems without the user having to explictly code the backtracking/search algorithm. Examples include: shortest path, knapsack, n-queens, Soduko and so on.

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
NoJavaVariableNamesHere=true. % OK, but you're not going to do this
```

### Assignment (Binding) vs. Unification (Bind or Fail) vs. Equality (Only Numbers)

Here be dragons. Or at least a sharp corner to hit your head on when you realize your program is failing because you used the wrong one.

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