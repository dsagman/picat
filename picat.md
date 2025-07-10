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

Note: Title inspired by What I Wish I Knew When I was Learning Haskell, which helped me learn that language. https://github.com/sdiehl/wiwinwlh

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
| Imperative: Python, JavaScript   | = Straightforward syntax <br> = Print from any statement to debug  <br> - Libraries are limited <br> - C ommunity is small
| Functional: Haskell, OCaml | = Functional concepts  <br> - No lambdas
| Solvers: Z3, MiniZinc  | + Full programming language vs. just a solver
| Logic: Prolog  | + Functions <br> = Unification, non-determinism, tabling
---
\+ Advantage, = Simliar, - Disadvantage

### Picat Has Common Programming Concepts

| Concept            |  Picat                        |
| :----------------  | :------                     |
| Comments          | `% percent sign for comments. no block comments |
| Variables          | `A=5, B=3.4, C=(1234 mod 7).` |
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
- Reduce
- Acyclic
- Contains


More at: https://picat-lang.org/download/picat_compared_to_prolog_haskell_python.html

## Note About Example Code 

Each clause in Picat ends with a `.`. In example code each line is fully distinct as if it had been typed into the Picat interactive interpreter. For example:

```
A=5. % A=5. But only in this clause. And if you prefer spaces around your operators, please go ahead.
A="A Wildebeest!". % A is a string. Again, only here.
println(A). % fails, A isn't assigned to anything.
```


## Constraint Programming

Some examples here. Let's try not to use the same ones from the manual!


## Picat Isn't Python

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

b=4;A=5. % A is bound to 5. because b!=4 and ';' means 'or'.

NoJavaVariableNamesHere=true. % OK, but this is not normal Picat style, but you live your best self however you want.
```

### Assignment (Binding) vs. Unification (Bind or Fail) vs. Equality (Only Numbers)

Here be dragons. Or at least a sharp corner to hit your head on when you realize your program is failing because you used the wrong one.

Logic programming uses the concepts of binding and unification. Binding is similar to assignment in other programming languages. Unification attempts to validate if the left and right side are the same, and if not either binds one side to the other or fails. Examples will help here.

#### Default binding

When a variable is first parsed by Picat, it is in an uninstantiated state. Trying to use it will result in an error.

```
A. %*** error(instantiation_error,call). 
```

However, it can be accessed because Picat will instantiate an unbounded variable that points to an empty memory location. 

```
println(A). % If A hasn't been defined/bound then _3d084e8 (or some other memory address) 
```

#### Unification: the `=` and `is` operators

The `=` operator performs one function that looks like two. It can assign a variable to an expression for it's first use and it can check the equality of a variable and an expression on subsequent uses. But these are both the same operation: unification. 

Unification is central to logic programming, but it also underlies type inference, type checking and pattern matching in functional programming languages such as Haskell and OCaml. However, unless you have tried to build a type inference engine, you are unlikey to have heard the term used in functional programming. 

Unification results either in success/true/yes or fail/false/no. (The slash indicates these are the synonyms.) Key point: a successful unification results in an unbound variable being bound. Let's look at some examples.

*Rabbit Hole: Mercury is a functional/logic language that includes unification. Unlike Picat, it is purely function and strongly typed. https://mercurylang.org/ Verse is also functional/logic, but is not yet publicly available except for programming games such as Fortnight. Yes, really. https://files.gotocon.com/uploads/slides/conference_65/2896/original/GOTO.pdf*

```
A=5, println(A). % A is bound to the value 5, and the unification succeeds allowing the statement to print 5. 

5=A, println(A). % A is bound to 5 because unification is bidirectional. <- IMPORTANT
```

Read the above as 
 1. `A` points to an empty memory location as a default binding.
 2. The default binding can be made/shown to be equivalent to `5` through unification.
 3. The unification binds `5` to `A` at the memory address.
 4. `,` means the logical and operation. 
 5. Because the left side of the `,` is true we can output the value of memory address of `A` to the terminal.
 6. As noted above `A=5` is identical to `5=A` because unification is bidirectional. <- Important

Compare this with:

```
A=5, A=3, println(A). % Fails/false/no. 
```

Read the above as:
1. `A=5 `binds/unifies/assigns `5` to `A`. 
2. `A=3` attempts to unify `A` with `3`. This fails `5` cannot be made to equal `3`.
3. Because step 2 is false, the overall `,` clause is false and the `println` doesn't happen.
4. The entire clause fails. `A` will not be bound to `5` after this clause! <- Important

```
A=5, A!=3, println(A). % Succeeds/true/yes.

A!=3, A=5, println(A). % Fails/false/no.
```

`!=` is "not unifiy". In the first line above `A` does get bound to `5`. The second line fails because for `A!=3` the variable `A` has not been instantiated or bound to a value and therefore cannot be unified with `3`.

Now look at this. Did you expect it?

```
A=B, println([A,B]). % A and B point to the same memory -> [_10a58,_10a58] 

B=5,A=B, println(A). % B unifies/binds to 5, A binds to B, A is 5 -> 5.

A=B,B=5, println(A) % B unifies with A, B binds to 5, A is now 5. <- Important
```

Above is a two variable example. Both left and right in `A=B` are unbound variables, which means the unification points them to same memory location. Therefore whenever A or B gets bound to a value A will be also.

`is` comes from Prolog and is for unifying on numeric values only. It allows binding across integer and float numeric data types. I have never found a reason to use it instead of `=`.

```
 A is 5. % A is bound to 5.
 
 5.0 is 5 % Succeds/true/yes. Equivalent to =.= below.
 ```

#### Equality: the `==` and `=:=` operators

If you want to test equality without unifying or binding, then `==` does this, just like Python or JavaScript. (Well, not like JavaScript, which has `===` and more rabbit holes about equality.)

```
A=5,A==5. % Succeeds/true/yes

A==5. % Fails/false/no. Picat provides the amusing error: ** Warning: superfluous call to ==

A=3,A!==5. % Succeeds/true/yes

A=5,A!==5. % Fails/false/no. A is not bound to 5. <- Important
```

Note: Two terms can be equivalent with `==` even if they don't point to the same memory.

Numerical equality can also be checked with `=:=`, which considers float and interger types the same and allows for a difference of 0.00000001. Also, not numerically equal is `=\=`. For example:

```
5=5.0 % Fails/false/no

5=:=5.0 % Succeeds/true/yes

5=:=5.1 % Fails/false/no

5=:=5.000001 % Fails/false/no

5=:=5.000000000001 % Succeeds/true/yes

5=\=6 % Succeeds/true/yes
```

#### Assignment: The `:=` operator, `bind_vars()` 

To force the binding of a value to a variable, `:=` is the operator. Variables in Picat are mutable, meaning `:=` overwrites the previous value. `bind_vars()` lets you assign a value to a structure such as a list or array. Some examples.

```
A:=5. % A is bound to 5.

A=3,A:=5. % A is bound to 5.

A:=5,A=3. % Fails/false/no. A is not bound to 5.

A=new_list(5), bind_vars(A,"b"). A = [[b],[b],[b],[b],[b]]

A=new_array(2,3), bind_vars(A,0). A = {{0,0,0},{0,0,0}}
```

### Program Structure and Control Flow

Picat programs consist of statements which can be combined into longer clauses inside procedures or functions. Picat statements are technically rules or facts. This is more correct than thinking of them as statements because all of the rules and facts are stored in a database of the program. This is also how Prolog works.

Fun fact: Because a program is a database, you can alter the rules and facts on the fly with `cl_facts`. See the end of this section for a perfectly legitimate abuse of this.


#### The `main` function.

The default entry point for a Picat program is a `main` function. If you call a Picat program from the command line, `main` will be run unless you overrride it.

Also, the standard file extension for Picat is `.pi`.

```
% hello.pi
main => println("Hello Picat!").
```

Then run it from a terminal.
```
picat hello.pi
Hello Picat!
```

#### Whitespace

Whitespace does not matter except for one space needed after the end of clauses Picat doesn't care if you smush everything together.


#### Statement delimiters

All statements have to end in `,`, `;` or `.` `,` means "and", `;` means "or" and `.` means "end of statement". 

```
A=2, B=3.
Name.length > 0; Name := "I'll just call you Larry.".
```

#### Control flow: `if` and `foreach`

`if` and `foreach` require an `end`. The statement just before this `end` doesn't need a `,`. Picat used to enforce this syntax, but doesn't anymore. The condition in `foreach` has to be enclosed in `()`. 

```
curve(Grade) = (Letter,Comment) => 
   	if Grade > 50 
    	then (Letter = A, Comment="Nice.") 
    	else (Letter = F, Comment="See me.") 
    end. 
```

The `then` keyword is optional if you enclose the condition in `()`

```
curve(Grade) = Letter => 
if (Grade > 50) (Letter = A) else (Letter = F) end. 
```


```                                                                                         % multiple iterators are allowed, they are treated like nested loops
foreach(X in 1..10, Y in 1..10) 
   	A[X,Y] := my_hash_function(X,Y) % <- comma optional
end.
```

procedures, functions


### Helper functions for accumlating results

....Haskell fold....


### Some control flow tricks

```
main =>
    Data = read_file_lines("day.txt").head,
 	cl_facts([$part(1)]), % a global fact
	printf("Part 1 Answer: %w\n",parse(Data).sum),

	cl_facts([$part(2)]), % override global fact
	printf("Part 2 Answer: %w\n",parse(Data).sum).

parse([]) = [].
parse([H|T]) = R =>
    if H = '(' then (R = parse1(T)) else (R = [1]++parse(T)) end.

parse1(L) = R =>
    Ix = find_first_of(L,'x'), Ip = find_first_of(L,')'),
    M = take(L,Ix-1).to_int, N = slice(L,Ix+1,Ip-1).to_int, 
    TT = slice(L,Ip+1+M),    % tail to leave
    part(Part),
    (Part = 1, R = [M*N]++parse(TT);
     Part = 2, R = [N*sum(parse(slice(L,Ip+1,Ip+M)))]++parse(TT)).')'


```

In this code the `parse` ...global variable
use of ; instead of it.
use of accumulator via helper function


## Non-determinism

A key feature of logic programming languages is implicit backtracking from failure to try and achieve success. This is also why they lend themselve to constraint programming. 

#### Lists


#### `?=>`


### Errors I Make

#### Unification vs Assignment

Invariably I make an error where I use `=` but I need to use `:=`. This happens when I am editing code and moving things around and lose track of the first time I bind a variable versus when I either test it or mutate it. 

This can be prevented by only ever using `:=` to do assignment, `==` to test equality and not mutating any variables. However, these limitations limit the use of the power of Picat to do non-deterministic binding and make it more complex to track a shared state.

Here's an example:







###






Show a longer example with a bug.




XXxXXXXXXXX


- Lists are usually decomposed using H and T as in `max([H|T])=max(H,max(T)).`


- printf is your friend or print([])
- := force vs == test vs = fail

- can’t if statement on true. need X==true or X=true =:= 

!= !== =\=

- , and ;  and .  also => and ! flow
- #= domain variable
- procedure vs function
- => versus =
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















