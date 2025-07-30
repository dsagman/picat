Responses to Hakank

hakank2: My comments to your comments are tagged as "hakank2".
david2: And my comments back.

hakank: Don't forget "global" variables using get_global_map() etc

    Are the global variables only system defined or can the programmer make their own global variables? For example, in much of my code I pass around large arrays. Should I be making those global and not passing to save passing around pointers? How would that work?

hakank2: The "global variables" is probably confusing term, and sorry about that. In Picat (and Prolog) "plain" variables can not be made global. What I mean was the maps that live though different predicate calls (and perhaps also through backtracking) such as get_global_map(). Here's a simple example of such a global map.
hakank2:   Map = get_global_map(),
hakank2:   Map.put(counter,0)
hakank2:   Map.put(a_huge_list,[1,2,3,4,5,6,7])
hakank2: In another predicate/call, one can then add/delete/change this via the usual map operations.
hakank2:   Map2 = get_global_map(),
hakank2:   Map2.put(counter,Map2,get(counter)+1),
hakank2:   Map2.put(a_huge_list,Map2,get(a_huge_list)++[8])
hakank2:
hakank2: You might want to benchmark between the performance between this approach and when you simply add the things via an extra parameter to the predicate/function.
hakank2: There are other type of global maps, see get_heap_map(), get_table_map() which has some different behavior regarding backtracking etc.

david2: updates made for global maps

hakank2: Also, in Prolog/Picat there are no pointers (at the Prolog/Picat level); "pointers" are the wrong abstraction.

    Also, how does Picat pass variables? By reference or value? Or some mix?

hakank2: I'm not sure that they can be described as neither, or perhaps it's both. Variables in Picat are not like variables in (imperative) programming languages, they are more placeholders which are "filled" with values by unification (or reassigned with :=/2). A variable that have been unified with a value (say 3), might probably be described as passed by value, but a value that hasn't been unified at the call might be described as passed by reference. So perhaps "pass by reference|value" should not be mentioned at all.

david2: at some point the compiled code results in variables being stored in memory and access when referenced in a function or predicate body. this must be to either a single memory location or a copy of a memory location. I understand that it's under the hood and that Picat doesn't expose this like C, but there's got to be a pointer or copy when a variable is passed to a function/predicate. 


hakank: I'm not sure if you have tested DCGs, Definite Clause Grammar. Unfortunately only mentioned at page 44.

    I noted later in the document that I don't understand how to use them in Picat. I would love an example or better yet two or three!

hakank2: I agree that The Picat Guide should give some example of DCGs. I gave you some links in my first comment round.

david2: I added DCG information in. Somewhere I thought I noted that this reminds me of Parsec in Haskell and that I enjoyed your advent of code problem that uses DCGs and I also have trouble with \n and \n\n in Parsec.


&&&& here&&&&&

hakank: Using the "case" analogy is interesting, but is misleading since in Picat - as in Prolog - all alternatives are tried, not just the first one that "match".

    I did not know that. If stmtA, the first item in a list of statements: (stmtA);(stmtB);(stmtC), succeeds then the other two, stmtB and stmtC are still tried?

hakank2: Well, it depends on the context. If stmtA is in a context of failure, the other will also be tried.
hakank2: Here is a - contrieved - example showing this. All statements have a fail, and thus the statemaets following them  will be all tested.
hakank2:   Picat> (3==4,println(first),fail) ; (1 == 1,println(second),fail) ; (3 == 4,println(third),fail) ; (9==9, println(fourth),fail)  
hakank2:   second
hakank2:   fourth


hakank2: I usually do simple text searches in the PDF. But a correct index is important.

hakank: You will get a small speedup (0.32s -> 0.2s) by using table on action/4.

    I thought planner automatically tables action and final. See figure 5.1 in the Picat manual.

hakank2: Tabling action/4 can give a huge boost, or slowing it down (e.g. caused by too many clauses), or making no discerible different. But I have to get back to you on this to give you a good answer.


hakank: I'm not sure I understand "unified with itself" and "is both input and output". The point of S as a map is rather that it's mutable (as maps usually are). 

    I thought you said that variables in Picat aren't mutable. :-)

hakank2: A "normal" variable is not mutable (via unification). This will fail:
hakank2:   X = 3, X = X + 1
hakank2: The exceptions are when using :=/2 
hakank2:     X = 3, X := X + 1
hakank2: Though I would not use "mutable" since that's indicates the wrong metaphor. I usually use the term "reassignment" and that's the power of :=/2.
hakank2:
hakank2: Another case is when adding/updatng a slot in a map; I think it's fairly OK to say that maps are mutable (or at least updatable):
hakank2:   Picat> Map = new_map([a=1,b=2,c=3]), Map.put(a,2), Map.put(b,Map.get(b,0)+1),Map.put(d,99)
hakank2:   Map = (map)[d = 99,c = 3,b = 3,a = 2]

david2: I think I get it. Mutuability to me means that a variable's old value is no longer accessible. But with backtracking variables sometimes do get their "old" value. So this is re-assignment which can be, sometimes, undone.



    Once again, I failed to distinguish between a predicate and a function! I tried Picat> reduce(+,1..10), and when it didn't work, I didn't realize I needed to assign it to a value.

    If the error message said something like, "reduce is a function, not a predicate" it would help me a lot more than the current error message: "*** Undefined procedure: reduce/2"

hakank2: Are you using the -log parameter when running Picat? Then the call:
hakank2:    reduce(1..4,4)
hakank2: Gives the following _warnings_ (but not errors):
hakank2:   undefined_assumed_global_predicate: reduce / 2
hakank2:   defined_as_function_in(basic): reduce / 2
hakank2: Which might give a hint what's wrong.
hakank2:
hakank2: Interestingly, when one mix predicates as functions (and vice versa) then
hakank2: the _errors are thrown (a_function/1 is a function, and a _predicate/2 is a predicate)
hakank2:    ** Error  : function_used_as_predicate:a_function / 1
hakank2:    ** Error  : predicate_used_as_function:a_predicate / 2
hakank2: The -log parameter gives warnings about singletons (variable name that are only used once) which helps to catch typos.
hakank2: Note that the "type" (function or predicate) is shown in the index (and in the function/predicate list in the main text):
hakank2:   reduce(Func,List) = Res
hakank2: which indicates that it returns something.

david2: I think these log level errors should be shown by default with the option to turn them off. They are valuable for the learner and it's not obvious from the manual that this will help the way you show it will.
david2: Regardless, I made edits to the document to explain this.

