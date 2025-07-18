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


More at: https://picat-lang.org/download/picat_compared_to_prolog_haskell_python.html

## Using the Manual

https://picat-lang.org/download/picat_guide.pdf
- The manual is the single most important document for learning Picat. It has everything, but can be very terse, and so must be read closely.
- The index at the back lists all the commands and hyperlinks to them. I use this more than any other method when programming in Piact.

## Note About Example Code 

Each clause in Picat ends with a `.`. In example code each line is fully distinct for variable scope.

`%` begins a comment.

For example:

```
A=5. % A=5. 
```
But `A` only equals `5` in this clause. After the `.`, `A` goes out of scope.

And if you prefer spaces around your operators, please go ahead.

```
A="A Wildebeest!". % A is a string. 
```
Again, `A` is in scope only until the `.` and doesn't exist after.
```
B=A+A. %** Error  : Free variable in expression: '+' 
```
 `A` hasn't been defined in this clause. 
 
 But using a `,` provides a continuous scope for `A`.
```
A=5,B=A+A. %A=5 and B=10.
```

## Constraint Programming

Constraint programming let's you solve problems that require searching through possible solutions. How can you place queens on a chessboard so that no queen is able to take another? (N-queens) Can a knight on chessboard visit every square once and end by returning to its starting square? (Knights tour) The best route for a travelling salesman? (Travelling Salesman) The quickest way out of a maze? (Shortest Path) How best to choose items to fill a suitcase? (Knapsack) How to complete a partially filled Soduku puzzle? (Soduku)

All of these, and many many more can be found via the links in the references section. 

The main concept here is the minimization of a function defined by a set of rules aka constraints. The function has variables whose values are not known, but can be defined to be within a given range.

These variables can be real/floating point values or integers and, in general, real variable solutions are easier to find than integer ones. In most most cases, for integer or "mixed integer" where some of the values are integers, there's no closed form solution. Constraint progamming is NP-Hard. https://en.wikipedia.org/wiki/Integer_programming#Heuristic_methods

There are many applicable techniques you may have seen in a CS algorithms class: Dijkstra's algorithm, A*, recursion/induction, A/B pruning, branch-and-bound, fail first, breadth first search, depth first search, simplex, gradient descent, satisfiability solver, memoization/tabling or just brute force.

A solution is found through iteration and any efficiency over brute force is through careful selection of which potential solution to try next, when to give up and try a different one, and identification of when a given search location is the same as a previous one. This last item is also known as symmetry breaking, that is, if a state is identical/symmetrical to a previous one, don't do the calculations all over again.

These algorithms can reduce the time to find a solution from hours to fractions of a second, but learning all of them and implementing them for a specific problem can be challenging. Enter Picat!

Picat has multiple built-in solvers: Boolean Satisfiability (SAT), Mixed-Integer Programming (MIP), Satisfiability Modulo Theories (SMT), Finite Domain (FD) Constraint Programming (CP), and a Planner for shortest path/minimum cost. 

Incredibly, the interface to all of them is essentially the same. This lets you switch between CP and SAT, for example by just changing and `import` statement. 

### Fibonacci and Tabling

Let's take a the example every dynamic programming text begins with, Fibonacci numbers. $F_n = F_{n-1} + F_{n-2}$. In Picat:

```
fib(0) = 1.
fib(1) = 1.
fib(N) = fib(N-1)+fib(N-2).
```
Run this with and we quickly see a problem with compute time.
```
main =>
    time(println(fib(5))),
    time(println(fib(30))),
    time(println(fib(40))).
---------------------------------
8 CPU time 0.0 seconds.
1346269 CPU time 0.042 seconds.
165580141 CPU time 4.755 seconds.
---------------------------------
```
`fib` is recalculating every value of `N-1` and `N-2` for each `N`. At `fib(40)` that's 331,160,281 recursive calls. https://cboard.cprogramming.com/c-programming/168662-fibonacci-how-long-would-take.html

The solution is to memoize the values into a hash table after they are computed so that they can be looked up rather than brute forced on subsequent calls.

Picat (and Prolog) make this ridiculously easy. Here's the revised code.

```
main =>
    time(println(fib(5))),
    time(println(fib(30))),
    time(println(fib(40))),
    time(println(fib(4000))).

table
fib(0) = 1.
fib(1) = 1.
fib(N) = fib(N-1)+fib(N-2).
---------------------------------
8 CPU time 0.0 seconds.
1346269 CPU time 0.0 seconds.
165580141 CPU time 0.0 seconds.
64574884490948173531376949015369595644413900640151342708407577598177210359034088914449477807287241743760741523783818897499227009742183152482019062763550798743704275106856470216307593623057388506776767202069670477506088895294300509291166023947866841763853953813982281703936665369922709095308006821399524780721049955829191407029943622087779296459174012610148659520381170452591141331949336080577141708645783606636081941915217355115810993973945783493983844592749672661361548061615756595818944317619922097369917676974058206341892088144549337974422952140132621568340701016273422727827762726153066303093052982051757444742428033107522419466219655780413101759505231617222578292486081002391218785189299675757766920269402348733644662725774717740924068828300186439425921761082545463164628807702653752619616157324434040342057336683279284098590801501
CPU time 0.013 seconds.
---------------------------------

```
### Constraint Example: Advent of Code 2016 Day 15

https://adventofcode.com/2016/day/15
>Part of the sculpture is even interactive! When a button is pressed, a capsule is dropped and tries to fall through slots in a set of rotating discs to finally go through a little hole at the bottom and come out of the sculpture. If any of the slots aren't aligned with the capsule as it passes, the capsule bounces off the disc and soars away. You feel compelled to get one of those capsules.
>
>The discs pause their motion each second and come in different sizes; they seem to each have a fixed number of positions at which they stop. You decide to call the position with the slot 0, and count up for each position it reaches next.
>
>Furthermore, the discs are spaced out so that after you push the button, one second elapses before the first disc is reached, and one second elapses as the capsule passes from one disc to the one below it. So, if you push the button at time=100, then the capsule reaches the top disc at time=101, the second disc at time=102, the third disc at time=103, and so on.
>
>The button will only drop a capsule at an integer time - no fractional seconds allowed.
>
>For example, at time=0, suppose you see the following arrangement:
>
>Disc #1 has 5 positions; at time=0, it is at position 4.
Disc #2 has 2 positions; at time=0, it is at position 1.
>
>If you press the button exactly at time=0, the capsule would start to fall; it would reach the first disc at time=1. Since the first disc was at position 4 at time=0, by time=1 it has ticked one position forward. As a five-position disc, the next position is 0, and the capsule falls through the slot.
>
>Then, at time=2, the capsule reaches the second disc. The second disc has ticked forward two positions at this point: it started at position 1, then continued to position 0, and finally ended up at position 1 again. Because there's only a slot at position 0, the capsule bounces away.
>
>If, however, you wait until time=5 to push the button, then when the capsule reaches each disc, the first disc will have ticked forward 5+1 = 6 times (to position 0), and the second disc will have ticked forward 5+2 = 7 times (also to position 0). In this case, the capsule would fall through the discs and come out of the machine.
>
>However, your situation has more than two discs; you've noted their positions in your puzzle input. What is the first time you can press the button to get a capsule?
>
>--- Part Two ---
>
>After getting the first capsule (it contained a star! what great fortune!), the machine detects your success and begins to rearrange itself.
>
>When it's done, the discs are back in their original configuration as if it were time=0 again, but a new disc with 11 positions and starting at position 0 has appeared exactly one second below the previously-bottom disc.
>
>With this new disc, and counting again starting from time=0 with the configuration in your puzzle input, what is the first time you can press the button to get another capsule?

This puzzle is perfect for Picat. Some things to notice:

- Look how long the description is versus the code!
- The use of parallel lists to encode the problem variables. This is a common approach.
- As the capsule falls, to solve for $T_{start}$ for a given $Disc$ the equation is $(T_{start}+ Distance + Disc_{time-zero}) \mod Disc_{num-positions} = 0$.
- We could code this as a `foreach` loop.
    ```
    foreach (D in 1..DPos.len)
        0 #= (T+D+DTZ[D]) mod DPos[D]
    end,
   ```
- But because each is zero, the sum is zero, and one line of code looks cooler than three. So we used `sum(...) #= 0`.
- We aren't given a range of times, so the program sets the range of the solution T to between 0 and `maxint_small()`, which is not very small. It's 72,057,594,037,927,935.
- `solve` is a predicate and unifies `T` with the solution.
- If you wanted all the solutions, `solve_all` is a function and looks like `Sols = solve_all(T).`
- On this problem the `cp` solver is the fastest, but that's not always the case.

    | Solver | Part1 | Part2 |
    |--------|-------|-------|
    | cp     | 0.04  | 0.25  |
    | sat    | DNF   | DNF   |
    | mip    | 0.4   | 2.4   |

```
import cp. 

main =>
    time(Part1 = go([17,3,19,13,7,5],
                    [15,2,4,2,2,0])), %0.04 sec
    printf("Answer Part 1: %w\n",Part1), % 400589

    time(Part2 = go([17,3,19,13,7,5,11],
                    [15,2,4,2,2,0,0])), % 0.25 sec
    printf("Answer Part 2: %w\n",Part2). % 3045959

go(DPos,DTZ) = T =>
    %(Time + Dist + T Zero Position) mod Disc Positions
    T :: 0..maxint_small(), % 72057594037927935
    sum([(T+D+DTZ[D]) mod DPos[D] : D in 1..DPos.len]) #=0,
    solve(T).
```


### Constraint Example: Jane Street Bug

https://www.janestreet.com/bug-byte/


> Bug Byte
> 
>Fill in the edge weights in the graph below with the numbers 1 through 24, using each number exactly once. Labeled nodes provide some additional constraints:
>
>The sum of all edges directly connected to this node is M.
>
>There exists a non-self-intersecting path starting from this node where N is the sum of the weights of the edges on that path. Multiple numbers indicate multiple paths that may overlap.
>
>Once the graph is filled, find the shortest (weighted) path from to and convert it to letters (1=A, 2=B, etc.) to find a secret message.

![Bug byte](janestreet_bug.png)

In Picat, here's a solution. The nodes and edges are defined and then the constraints about the known values of nodes and possibly values for the edge weights. Each constraint reduces the search space.

A constraint in this program are `all_distinct`, `#=`, `#^` and `#<=`.
`all_distinct` means that the values of the list of edges are all different from each other. When combined with the previous line that puts them in the range of 1 to 24, that means the edges will contain every possible value between 1 and 24. 

The `#=` constraint syntax means that a valid solution has the left and right side equal. This is different from unify, `=`, where the values have to be the same at the time of comparisson. The constraint tells the solver module (in this case `cp`) to try to find a set of values of left and right that can be unified.

`#<=` is less than or equal and `#^` is the logical XOR.

```
import cp.

main =>
    % Define the graph with nodes and edges
    Nodes = ['A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J', 'K', 'L', 'M', 'N', 'O', 'P', 'Q', 'S'],
    Edges = [('A', 'B'), ('A', 'D'),
             ('B', 'S'),
             ('C', 'H'),
             ('D', 'S'), ('D', 'H'),
             ('S', 'I'),
             ('F', 'I'),
             ('G', 'J'),
             ('H', 'J'), ('H', 'K'),
             ('I', 'J'), ('I', 'L'),
             ('J', 'K'), ('J', 'L'),
             ('K', 'M'), ('K', 'P'), ('K', 'N'),
             ('L', 'O'),
             ('M', 'N'), ('M', 'O'),
             ('N', 'E'),
             ('O', 'E'),
             ('Q', 'L')],

    % Edges are tuples of nodes and weights in range 1..24
    EdgeVars = [(Edge, V) : Edge in Edges, V :: 1..24],
    
    % Unique edge weights
    % Reduce search space to 24! = 6,204,484,903,168,028,160
    all_distinct([V : (_, V) in EdgeVars]),

    % Constraint to enforce known edge weights 
    % Reduce search space to 19! = 121,645,100,408,832,000
    KnownWeights = [(('D','S'), 12),
                    (('J', 'K'), 24),
                    (('K', 'N'), 7),
                    (('I', 'L'), 20)],
    foreach((Edge, Weight) in KnownWeights),
        member((Edge, Weight), EdgeVars)
    end,
    
    % Constraint to enforce known sum of weights for nodes
    % Reduce search space to 48
    KnownSums = [('A', 17), 
                 ('B', 3),
                 ('H', 54),
                 ('I', 49),
                 ('J', 60),
                 ('K', 79),
                 ('L', 75),
                 ('M', 29),
                 ('N', 39),
                 ('O', 25)],
    foreach((Node, Sum) in KnownSums),
        NodeEdges = [(N1, N2) : (N1, N2) in Edges, (N1 = Node ; N2 = Node)],
        NodeVars = [V : (Edge, V) in EdgeVars, Edge in NodeEdges],
        sum(NodeVars) #= Sum
    end,

    % Constraint that sum of weight on an edge = KnownPathSum OR 
    % sum of weight plus min edge on neighbor is <= to KnownPathSums
    % only need C, G, and F to reduce search space to 2
    KnownPathSums = [
                    ('C', 31),
                    %  ('D', 19), 
                    %  ('D', 23),
                     ('G', 6), 
                    %  ('G', 9), 
                    %  ('G', 16),
                     ('F', 8)],
    foreach((Node, Sum) in KnownPathSums),
        NodeEdges = [(N1, N2) : (N1, N2) in Edges, N1 = Node],
        NodeVars = [V : (Edge, V) in EdgeVars, Edge in NodeEdges],
        Neighbors = [N2 : (N1, N2) in Edges, N1 = Node],
        NodeOneHopEdges = [(N1, N2) : (N1, N2) in Edges, N1 in Neighbors],
        NodeOneHopVars = [V : (Edge, V) in EdgeVars, Edge in NodeOneHopEdges],
        (sum(NodeVars) #= Sum) #^ (sum(NodeVars) + min(NodeOneHopVars) #=< Sum)
    end,

    Solutions = solve_all([ffc], EdgeVars),
    printf("Number of solutions: %d\n", length(Solutions)),
    foreach(Solution in Solutions)
        AllEdges = Solution ++ [((Y,X), Weight) : ((X,Y), Weight) in Solution],
        sp(AllEdges, 'S', 'E', Path, _),
        Message = [chr(W+64) : ((X,Y), W) in Path],
        println('====================='),
        println(Path),
        println("Message is: "++Message),
    end,
    save_solution(Solutions),
    println("Done!").

% shortest path adapted from Picat Manual
table (+,+,+,-,min)
sp(Graph,X,Y,Path,WL) ?=>
    Path = [((X,Y),Wxy)],
    WL = Wxy,
    member(((X,Y), Wxy), Graph).

sp(Graph,X,Y,Path,WL) =>
    Path = [((X,Z), Wxz)|Path1],
    member(((X,Z), Wxz), Graph),
    sp(Graph,Z,Y,Path1,WL1),
    WL1 = Wzy,
    WL = Wxz+Wzy.

% Save to file for use with Python to make a graph
save_solution(Solutions) =>
    Out = open("solutions.txt", write),
    foreach(Solution in Solutions)
        println(Out, Solution)
    end,
    close(Out).
```

The above outputs two potential solutions.
```
Number of solutions: 2
=====================
[((S,I),18),((I,J),3),((J,L),17),((L,O),16),((O,E),4)]
Message is: RCQPD
=====================
[((S,D),12),((D,H),9),((H,K),14),((K,M),11),((M,O),5),((O,E),4)]
Message is: LINKED
Done!
```

### The Planner

The `planner` module is, as far as I know, unique to Picat. It lets you define a starting state, action to create the next state, the final/goal state, and then just solve it.

Planner acts as high-level interface to the underlying solver and mechanics of tabling and goal state checking.

### A Planner Example: Blocks World

Here's an example from the documentation for the programming language Curry, which combines functional and logic paradigms. https://curry-lang.org/docs/tutorial/html/curry-tutorial.Ch6.S1.html#SS3


>The “blocks world” consists of 3 possibly empty piles, labeled p, q and r, of unique blocks labeled A, B, C, etc. “Start” and “Final” below are two examples from [blocks world](https://www.d.umn.edu/~gshute/cs2511/projects/Java/assignment6/blocks/blocks.xhtml).

![](blocksworld1.png)

![](blocksworld2.png)

>A blocks world “problem” consists of two worlds, like Start and Final above. Its solution consists in the moves that produce the second world from the first one. A “move” transfers the block on top of a pile to the top of another pile. No other blocks are affected by the move. 

Here's a solution with the Picat `planner`.

NOTE: A neat trick here is the use of `()` and `;` (meaning "or") to create the equivalent of a `case` statement in another language. 

```
import planner.

main =>
    problem(S),
    best_plan(S,Plan), % solves the problem S
    println(S[1]), % prints the first state
    foreach (Step in Plan)
        println(Step) % prints each state
    end,
    printf("Solution cost: %w\n", length(Plan)).
 
% uncomment an S to run it
problem(S) => 
    % S = [[[a,b],[],[]], [[],[a,b],[]]]. % simple
    % S = [[[a,b],[],[]], [[],[b,a],[]]].  % simple
    S = [[[a,b,c,d,e],[],[]], [[],[c,b,a,d,e],[]]]. % difficult

% the goal state
final(State), State[1] == State[2] => true.

% action predicate defines how to go to the next state: NextS
% Action variable is used to write the chosen step to the log

action([[S1,S2,S3],G],NextS,Action,Cost) =>

    % pattern match on all the possible moves
    % this is essentially a case statement
    % Goal is G and is passed through as part of the state

    (
    S1 = [H1|T1], 
        NewS1 = T1,      
        NewS2 = [H1|S2], 
        NewS3 = S3,      
        Action = $("1->2",NewS1,NewS2,NewS3);

    S1 = [H1|T1], 
        NewS1 = T1,      
        NewS2 = S2,      
        NewS3 = [H1|S3], 
        Action = $("1->3",NewS1,NewS2,NewS3);

    S2 = [H2|T2], 
        NewS1 = S1,      
        NewS2 = T2,      
        NewS3 = [H2|S3], 
        Action = $("2->3",NewS1,NewS2,NewS3);

    S2 = [H2|T2], 
        NewS1 = [H2|S1], 
        NewS2 = T2,      
        NewS3 = S3,      
        Action = $("2->1",NewS1,NewS2,NewS3);

    S3 = [H3|T3], 
        NewS1 = [H3|S1], 
        NewS2 = S2,      
        NewS3 = T3,      
        Action = $("3->1",NewS1,NewS2,NewS3);

    S3 = [H3|T3], 
        NewS1 = S1,      
        NewS2 = [H3|S2], 
        NewS3 = T3, 
        Action = $("3->2",NewS1,NewS2,NewS3) 
    ),
    Cost = 1, % the cost for a given step
    NextS = [[NewS1,NewS2,NewS3],G]. % the next state.

```
This outputs:

```
[abcde,[],[]]
(1->2,bcde,a,[])
(1->2,cde,ba,[])
(1->3,de,ba,c)
(2->3,de,a,bc)
(2->3,de,[],abc)
(1->3,e,[],dabc)
(1->2,[],e,dabc)
(3->2,[],de,abc)
(3->2,[],ade,bc)
(3->2,[],bade,c)
(3->2,[],cbade,[])
Solution cost: 11
```

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

Unification results either in success/true/yes or fail/false/no. The slash here indicates these are the synonyms. The evaluation results in success or failure, the logical value of the evaluation for each respectively is true/false and the Picat interpreter will output `yes` or `no` to indicate this.

Key point: a successful unification results in an unbound variable being bound. Let's look at some examples.

**VERY IMPORTANT:** The explict and central use of unification is the main difference between Prolog and almost all other programming languages. 

Picat makes it easier for people coming from non-logic programming backgrounds by including functions (see below), `foreach` loops and list comprehensions. 

However, Picat is still a member of the Prolog family and as such, understanding how unification works is required for anything but the simplest program.

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

Picat programs consist of statements which can be combined into longer clauses inside procedures or functions. Picat statements are either rules or facts. This is more correct than thinking of them as statements because all of the rules and facts are stored in a database of the program. This is also how Prolog works.

Fun fact: Because a program is a database, you can alter the rules and facts on the fly with `cl_facts`. See the later of this section for a perfectly legitimate abuse of this.


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

#### Predicates vs. Functions 

A key difference of Picat vs. Prolog is the inclusion of functions. In Prolog, everything is a predicate and there's no direct concept of a "returned value". 

The classic example of this is Prolog's `append` predicate, which Picat also has and it let's you join two lists. It takes the form `append(L1,L2,L3)`. Note the standard use of terse variable names. 

```
append([1,2],[3,4],L3). % L3 = [1,2,3,4].
```

But `L1`, `L2`, and `L3` are not the "output" of the predicate. You can also write this:

```
append(L1,[3,4],[1,2,3,4]). % L1 = [1,2].
```

OK, that's not like Python at all. And it gets weirder. Type this into the Picat interpreter.

```
Picat> append(L1,L2,[1,2,3,4]).
L1 = '[]'
L2 = [1,2,3,4] ?

```
This means `[1,2,3,4]` can be formed by an empty `L1` and `L2` being the same as `L3`. And then there's this `?`. Picat is asking if you want more. If you enter `;`, which means "or", the output continues with all possibilities until there are no more, which is indicated with `no`.


```
Picat> append(L1,L2,[1,2,3,4]).
L1 = '[]'
L2 = [1,2,3,4] ?;
L1 = [1]
L2 = [2,3,4] ?;
L1 = [1,2]
L2 = [3,4] ?;
L1 = [1,2,3]
L2 = [4] ?;
L1 = [1,2,3,4]
L2 = '[]' ?;

no
```

Technically what is happening here is unification, which we discussed earlier. Unification allows for non-deterministic behavior, which we will go into futher later. But the key point here is what would it be like to write a program with nothing but predicates that work this way? 

Picat has all of this Prolog-stype behavior, but with functions it's more explicit what the input and output of a called object are. You can use `append` or, if you just want to join two lists, you can do this:

```
L3 = L1 ++ L2.
L3 = [1,2] ++ [3,4]. % L3 = [1,2,3,4].
[1,2] ++ [3,4] = L3. % L3 = [1,2,3,4].
L3 := [1,2] ++ [3,4]. % L3 = [1,2,3,4].

```
This is deterministic and unifies or explicitly assigns `L1` followed by `L2` to `L3`. Again note that unification is bidirectional.

***Key point:*** The Picat manual identifies functions with the notation `= Val`, `= ResList` or similar. If you try to call a predicate like a function or visa-versa you will get an error an `undefined procedure` or a fail that you didn't expect.

For example:
```
A = append(1,2,3). % *** Undefined procedure: append/3
[3,4] = [1,2] ++ L2. println(L2). % Fails/false/no.
```

These seem like simple errors to avoid, but the distinction between a function that returns a value versus a predicate that unifies one or more of its arguments can be subtle. It's important to read the manual. 

For example to get a single solution or all solutions to a constraint programming problem the manual says:

- solve(Vars): This predicate ... a single solution
- solve_all(Vars) = Solutions ... all solutions

Calling `solver(MyVariable)` will unify `MyVariable` with the solution. In other words, you can `println(MyVariable)` to see the result.

But `solve_all(MyVariable)` gives a `** Error  : function_used_as_predicate:import(cp,solve_all / 2)`. You need to write something like `Sols = solve_all(MyVariable)`.

Note the reason for the difference is that before calling `solve`, `Vars` is a domain variable that has the shape of the solution. Solving binds `Vars` to the solution, so it is a predicate. All the solutions is a list, which is not the same shape as a single solution, and therefore needs to be assigned to a new variable. 

Don't worry if this doesn't make perfect sense yet. Personally this took me quite some time to understand.


#### Function calls: `()` and `.`

Functions can be called by placing arguments in parenthesis or by using dot notation. This is just syntactic sugar. The `.` notation makes the code a little shorter (1 character versus 2), and looks more like functional programming (or Rust) as opposed to lots of parentheses. You may find it easier to follow the logic with the dot notation, and you can mix-and-match the notations however you like.

For example:

```
% parentheses
Steps = map(my_parser,map(split,read_file_lines("day.txt"))),
init_bots(Steps,Bots,Bins),
Answer = prod(map(dec,take(3,(to_list(step([S : S in Steps, S[1]=$gives],Bots,Bins))))),

% dot notation
Steps = ("day.txt").read_file_lines.map(split).map(my_parser),
init_bots(Steps,Bots,Bins),
Answer = step([S : S in Steps, S[1]=$gives],Bots,Bins).to_list.take(3).map(dec).prod,

% mixed
Steps = read_file_lines("day.txt").map(split).map(my_parser),
% etc.

```

#### Statement delimiters

All statements have to end in `,`, `;` or `.` `,` means "and", `;` means "or" and `.` means "end of statement". 

`()` are used to mark a block of code as separate from the code around it. They are often optional, but using them will help make sure the compiler and you don't get confused.

```
A=2, B=3.
Name.length > 0; Name := "I'll just call you Larry.".
```

#### Control flow: `if` and `foreach`

`if ... then` is exactly what you think it is, but there's required syntax that took me a while to get used to.

`if` requires an `end`. The statement just before this `end` doesn't need a `,`. Picat used to enforce this syntax, but doesn't anymore. 
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

Picat also allow Prolog style if statements with `->` and `;`. The syntax is $if -> then ; else$.

```
(A>5 -> println("Big"); println("small")).

```

`foreach` is a loop structure like any other programming language. Picat also has `while` and `do ... while`. The condition in `foreach` has to be enclosed in `()`. And, like `if`, you need an `end` at the end.

```                                                    
% multiple iterators are allowed, they are treated like nested loops
foreach(X in 1..10, Y in 1..10) 
   	A[X,Y] := my_hash_function(X,Y) % <- line end comma optional
end.
```

#### Control flow: `;` operator

`if ... then ... else` are familiar control flow for almost all programming languages. Prolog/Picat can also use the concept of "or". There's an example of this in [Blocks World](#a-planner-example-blocks-world).

The structure is essentially a case statement in other languages: `(A; B; C; D)`, which reads as perform `A`, if it fails, perform `B`, etc. Enclose these in `()` to make sure that the case statement doesn't get mixed up with other parts of the predicate or function.

#### `cond` and `compare_terms`

Another way to do an `if` is with `cond`. While it's not listed in the Picat manual index, it does define the function in this example:

> The fib function can also be defined as follows:
>
>  `fib(N) = cond((N = 0; N = 1), 1, fib(N-1)+fib(N-2)).`
>
>   The conditional expression returns 1 if the condition `(N = 0; N = 1)` is true, and the value of `fib(N-1)+fib(N-2)` if the condition is false.

Another option is `compare_terms`. It outputs -1, 0, or 1 if the first term is less than, equal to, or greater than the second term. For example.

```
A = compare_terms(5,2). % A = 1
B = compare_terms(2,2). % B = 0
C = compare_terms(2,5). % C = -1
```



### Helper functions for accumlating results

....Haskell fold....


### Global fact = Global state
The below code recursively parses parenthensis but has different requirements for part 1 and part 2. To do this it uses a global fact: `part(n)` to change the behavior of `parse1` function. The fact is changed from `part(1).` to `part(2).` with the `cl_facts()` command that updates the global fact dictionary.

Probably unsafe, but quite neat!

```
% Advent of code 2016, day 9

main =>
    % Data = "A(1x5)BC",
    % Data = "A(2x2)BCD(2x2)EFG",
    % Data = "(25x3)(3x3)ABC(2x3)XY(5x2)PQRSTX(18x9)(3x2)TWO(5x7)SEVEN",
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

#### Example: Dynamic Dispatch with `apply` and `call`

While Picat doesn't have lambda expressions, it does allow for code execution based on the value of a variable.

Here's an example that simulates a simple assembly language. It invokes the correct operation based on the parsed input strings via the `apply` function. `apply` and `call` are able to transfer operation to another function or predicate that is passed as an argument. 

`call` and `apply` perform the same action, but `apply` is a function and returns a value.

The interesting thing here is that the name of the function is identical to the string in the input. The *cpy* command is performed by the `cpy` function.

The code also makes use of a hash map to store the value of registers and passes state back and forth via unification. Note how `S` isn't explictly returned. It's not a global variable. It's unified with itself resulting in updates meaning it is both input and output.

The program counter, by contrast, is explictly returned and updated using the function syntax and `:=`.

One more thing to notice here: the code for converting strings to integers. You may find it useful when parsing input yourself.

From the Advent of Code website:

https://adventofcode.com/2016/day/12

- cpy x y copies x (either an integer or the value of a register) into register y.
- inc x increases the value of register x by one.
- dec x decreases the value of register x by one.
- jnz x y jumps to an instruction y away (positive means forward; negative means backward), but only if x is not zero.

Here's a couple of possible programs:

    | Program 1| Program 2|
    |----------|----------|
    | cpy 41 a | cpy 1 a  |
    | cpy a b  | cpy 1 b  |
    | inc a    | cpy 26 d |
    | inc a    | jnz c 2  |
    | dec a    | jnz 1 5  |
    | jnz a 2  | cpy 7 c  |
    | dec a    | inc d    |
    |          | dec c    |
    |          | jnz c -2 |
    |          | cpy a c  |
    |          | inc a    |
    |          | dec b    |
    |          | jnz b -2 |
    |          | cpy c b  |
    |          | dec d    |
    |          | jnz d -6 |
    |          | cpy 13 c |
    |          | cpy 14 d |
    |          | inc a    |
    |          | dec d    |
    |          | jnz d -2 |
    |          | dec c    |
    |          | jnz c -5 |

And here's the code.

```
% Advent of Code 2016 Day 12

import util.

main => 
    % Program = read_file_lines("test").map(split).map(parse),
    Program = read_file_lines("day.txt").map(split).map(parse),

    Part1 = run(Program,new_map(["a"=0,"b"=0,"c"=0,"d"=0]),1),
    printf("Answer Part 1: %w\n",get(Part1, "a")),

    Part2 = run(Program,new_map(["a"=0,"b"=0,"c"=1,"d"=0]),1),
    printf("Answer Part 2: %w\n",get(Part2, "a")).

run(Program,S,PC) = NewS =>
    while (between(1,Program.len,PC))
        [Op,Args] = Program[PC],
        PC := apply(Op,Args,S,PC),
    end,
    NewS = S.

% extract the op code as an atom 
% convert numbers in the argument into the correct int type.
parse([H|T]) = [(H).to_atom,T.map(parse_n)].
parse_n(X) = Xn => if between(97,122,ord(X[1])) then Xn = X else Xn = X.to_int end.

cpy([X,Y],S,PC) = NPC=> NPC = PC+1,
    if number(X) then put(S,Y,X) else V = get(S,X), put(S,Y,V) end.

inc([X],S,PC) = NPC => NPC = PC+1, V = get(S,X), put(S,X,V+1).

dec([X],S,PC) = NPC => NPC = PC+1, V = get(S,X), put(S,X,V-1).

jnz([X,Y],S,PC) = NPC => 
    if number(X) then Xn = X else Xn = get(S,X) end,
    if Xn != 0 then NPC = PC+Y else NPC = PC+1 end.

```

## Non-determinism

A key feature of logic programming languages is implicit backtracking from failure to try and achieve success. This is also why they lend themselve to constraint programming. 

#### Lists and Arrays


#### `?=>`


### Errors I Always Make and How I Compensate

#### Unification vs Assignment

Invariably I make an error where I use `=` when I need to use `:=`. This happens when I am editing code and moving things around and lose track of the first time I bind a variable versus when I either test it or mutate it. 

You could try to only ever use `:=` to do assignment and `==` to test equality. But without unification Picat is hobbled and there's no non-deterministic binding.


### Forgetting a comma or a period (or having an extra one)

Some people like to put the `,`, `;` and `.` at the start of lines. 

```
main =>
      A = 5
    , B = 10
    , if A > B 
        then println("Yoo")
        else println("Hoo")
      end
    .  

```
This can make it easier to move lines around, but I think it looks weird and it doesn't work in all cases.

Invariably, I add a new line of code and forget the comma or I accidentally add a period. Picat is improving in its ability to locate the error, but it can be vague and give a large range of possible lines to check.

I compensate by adding only a few lines at a time and always saving and rerunning so that I don't have far to hunt for the most recent edit that broke syntax rules.

### Print and Printf are Your Friends

Picat lets you put a `println` anywhere, and I make extensive use of this when debugging.

println one variable
use of $

printf simplest format

XXXXXXXXXXXXXXXXXXXXX



# TODO LIST

- Lists are usually decomposed using H and T as in `max([H|T])=max(H,max(T)).`
- printf is your friend or print([])

- #= domain variable
- => versus =
- when to use a comma (and not to)
- type error string/int
- accumulator for base case
- length is not a constraint, but sum [1: X in …] is

- $ means literal
- =>? opposite of prolog !, but we also have ! (!)
- cond not really in the manual
- -> not really in the manual
- instead of if, sign function
- term vs string
- “” vs ‘’ string (check this one)
- using @
- 2d array notation. link to rosetta code
- time and time2







## Resources

- Picat website https://picat-lang.org/ 
- The Manual https://picat-lang.org/download/picat_guide.pdf 
- Constraint Solving and Planning with Picat book https://picat-lang.org/picatbook2015/constraint_solving_and_planning_with_picat.pdf
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

To use Picat with an autograder, the student code can be imported as a module into the grader. For example, students could be given the magic square problem from the Picat Constraint Solving book. 

Here's a sample assignment.

A magic square is one where the columns, rows and diagonals sum to the same number. https://en.wikipedia.org/wiki/Magic_square You are to code a constrain solving solution in Picat that has that sum be the "magic sum" of N*(N*N+1)//2. https://en.wikipedia.org/wiki/Magic_constant Be sure to not change the code that says `module student.` at the top.

Use this code as your template:
```
module student. 
import cp.

magic(N) = Square =>
    NN = N*N,
    Sum = N*(NN+1)//2, % ensures consistency of solve
    Square = new_array(N,N),
    Square :: 1..NN,
    % your code here
    solve(Square)

```


Here's a potential full solution, this is from the Picat book. https://picat-lang.org/picatbook2015/constraint_solving_and_planning_with_picat.pdf

```
module student. 
import cp.

magic(N) = Square =>
    NN = N*N,
    Sum = N*(NN+1)//2,
    Square = new_array(N,N),
    Square :: 1..NN,
    all_different(Square.vars()), 
    foreach (I in 1..N)
        Sum #= sum([Square[I,J] : J in 1..N]), % rows
        Sum #= sum([Square[J,I] : J in 1..N]) % columns
    end,
    % diagonal sums
    Sum #= sum([Square[I,I] : I in 1..N]),
    Sum #= sum([Square[I,N-I+1] : I in 1..N]).
```

And the associated grader that puts the result into a file. Consistency in grading is ensured by using the combination of the magic sum and the set solve search method, in this caes: `[ffd,down]`. (Fail first with variable ordered by degree and down variables from largest to smallest.)

```
% grader.pi
import student.
import cp.

main =>
    Writer = open("grade_file.txt", write),
    Square = magic(5),
    solve([ffd,down],Square), % tell students the solve method
    if 
        Square == {{3,9,14,15,24},{10,20,22,2,11},{19,17,1,21,7},{8,13,16,23,5},{25,6,12,4,18}}
        then println(Writer,"Correct!") % or write to a file
        else println(Writer,"Wrong!")
    end.
```

## Appendix:  Things I Still Don't Fully Understand

The Picat manual can be terse and doesn't provide examples for everything. Here's some commands I don't know how to use or where I felt the documentation could have been better.

- `reduce` I think this is a functional `fold`, but have not been able to make it work.
- `acyclic_term` "This predicate is true if Term is acyclic, meaning that Term does not contain itself." I don't know what this means or when I would use it.
- `list_to_and(List) = Conj` I understand that this turns a list into a conjunction of facts separated by and (`,`). For example: 

    ```
    M = [A=5,B=3,A!=B],
    C = list_to_and(M).
    -------
    M = [_13558 = 5,_13570 = 3,_13558 != _13570]
    C = (A = 5,B = 3,A != B)
    yes
    ```
    Which is neat, and kind of like building up an expression that can then be dynamically evaluated, but how do I evaluate `C`? I have no idea. This is the only use of `Conj` as an output value in the entire manual.
    
- `->` can be used for `if` `then` as in (A>5 -> X=yes; X=no). It's in a footnote (pg 6) that says, "Picat also accepts Prolog-style if-then-else in the form (If -> Then; Else) and mandates the presence of the else-part." 
    
    `->` is used in one example (pg 93), and I had to have someone point out the footnote and example after ChatGPT insisted that `->` is the prefered method for Picat `if` `then`. (It is not.)

- Picat also has `cond`, but it's only mentioned in an example about Fibonacci and isn't in the index. It was ChatGPT who told me about it.

- `-->` The manual says this syntax supports DCG (Definite Clause Grammar) rules. I don't know much about these and the Prolog manual talks about the `phrase` predicate for processing them, which Picat doesn't seem to have. And without some examples, I'm not sure what I'd use them for. Would they make better/easier parsers for LL, LR or CFG grammars? I do not know.












