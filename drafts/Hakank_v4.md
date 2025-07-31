Responses to Hakank


- `list_to_and(List) = Conj` I understand that this turns a list into a conjunction of facts separated by and (`,`). For example: 

    ```
    M = [A=5,B=3,A!=B],
    C = list_to_and(M).
    -------
    M = [_13558 = 5,_13570 = 3,_13558 != _13570]
    C = (A = 5,B = 3,A != B)
    yes
    ```
    Which is neat, and kind of like building up an expression that can then be dynamically evaluated, but how do I evaluate `C` to get the logical value of `true`? I have no idea. This is also the only use of the term `Conj` as an output value in the entire manual.

hakank: I think that you are thinking in Haskell here. And I don't think that it's possible to do what you want.
hakank: But that depends on exactly you mean by "evaluating C".
david: I would like println(C) to output "true", because that's what would happen in the evaluation of those statements. In Python I would consider this an `eval`. If I had `C = (A=5,B=3,A==B),if C==false then println("No.") end.` Then I would expect `No.` to be printed.

hakank3: After playing with call(C) and cond/3 (which works, see below), I realized that you can do this quite simple by forcing the evaluation of an conjunction term with cond/3:
hakank3:   Picat> C=(A = 5,B = 3,A != B).cond(true,false),println([a=A,b=B,c=C])
hakank3:   [a = 5,b = 3,c = true]
hakank3:   C = true
hakank3:   A = 5
hakank3:   B = 3
hakank3:   yes
hakank3: Here's an example when it fails:
hakank3:   Picat> C = (A = 5,B = 3,A == B).cond(true,false)    
hakank3:   C = false
hakank3:
hakank3: Here's the version with call/N and cond/3 that I indicated above:
hakank3:   Picat> C = (A = 5,B = 3,A != B), Ret = cond(C.call,true,false)
hakank3:   C = (5 = 5,3 = 3,5 != 3)
hakank3:   A = 5
hakank3:   B = 3
hakank3:   Ret = true
hakank3:   yes
hakank3: The first version without call/N is arguably neater, but which version to choose depends on the specific use case.

david:3 see list_to_and.pi I can't make this work starting from a list of strings.


hakank3: Yeah, the trouble to handle "\n\n" "naturally" in Picat is a little annoying.
hakank3: And that was one of the reasons that I wrote the regex module: https://github.com/hakank/picat_regex
hakank3: See https://www.hakank.org/picat/#regex for some examples how to use this.
hakank3:
hakank3: Last AoC I wrote some utils modules:
hakank3:   https://hakank.org/advent-of-code-2024/aoc_utils.pi
hakank3: Using regex:
hakank3:   https://hakank.org/advent-of-code-2024/aoc_utils_regex.pi

david3: I will check these out.



hakank: You will get a small speedup (0.32s -> 0.2s) by using table on action/4.

    I thought planner automatically tables action and final. See figure 5.1 in the Picat manual.

hakank2: Tabling action/4 can give a huge boost, or slowing it down (e.g. caused by too many clauses), or making no discerible different. But I have to get back to you on this to give you a good answer.



hakank3: The explaination for -log in Picat Guide is:
hakank3: "-log: The option -log makes the system print log information and warning messages."
hakank3: But it would be better with some example showing that it's actual useful.
hakank3: By default I'm running a wrapper with the -log parameter, and then adding nolog/0 first in a predicate when tired of the SAT solver's or planner's (mildy annoying) log messages.

davids: I will have to set up a system alias for this. I use the fish shell, I think this should be straightforward.



