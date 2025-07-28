Responses to Hakank

hakank: Why COIN-OR?

    I added this to the intro and removed the reference to COIN-OR in the "best of" section.

    My interest in optimization goes back to when I worked on the Optimization Subroutine Library at IBM Kingston in my first job out of college in 1989.
    They needed a technical writer for the manual who had a background in computing and math, and as a dual CS/Math major with a writing minor, who also happend to have a father who worked at IBM for 30 years, I was the perfect person.
    OSL was a cutting edge Simplex and interior-point barrier method linear optimimzation library of subroutines for IBM mainframes that worked with Fortran, APL, and hard-core 360 Assembly. 
    It could also take advantage of the "vector processing facility" to do multiple operations simultaneously. 

    This was software that cost in the hundreds of thousands of dollars and ran on machines that cost millions. Nowadays, you can just download for free any of a number of linear programming libraries and run them on your laptop with more compute than any 1990s mainframe.

    However, at the time, and still today, the math at the center of this is over my head, which is why I find Picat so appealling. It's a chance to learn about optimization with the linear algebra available, but not required.


    Fun fact: A couple the researchers from IBM who wrote much of OSL, John Forrest and John Tomlin, were and are instrumental in COIN-OR, an open source optimization software initative. John Forrest was not the friendliest to a young technical writer, but John Tomlin was as kind as possible.
    https://www.informs.org/Recognizing-Excellence/Award-Recipients/John-Forrest 
    https://www.coin-or.org/coinCup/coinCup2007Winner.html

hakank: Don't forget "global" variables using get_global_map() etc

    Are the global variables only system defined or can the programmer make their own global variables? For example, in much of my code I pass around large arrays. Should I be making those global and not passing to save passing around pointers? How would that work?

    Also, how does Picat pass variables? By reference or value? Or some mix?

hakank: I'm not sure if you have tested DCGs, Definite Clause Grammar. Unfortunately only mentioned at page 44.

    I noted later in the document that I don't understand how to use them in Picat. I would love an example or better yet two or three!

hakank: Using the "case" analogy is interesting, but is misleading since in Picat - as in Prolog - all alternatives are tried, not just the first one that "match".

    I did not know that. If stmtA, the first item in a list of statements: (stmtA);(stmtB);(stmtC), succeeds then the other two, stmtB and stmtC are still tried?


Another way to do an `if` is with `cond`. While it's not listed in the Picat manual index, it does define the function in this example:
hakank: Good catch about the omission from the index. I'll mention this to Neng-Fa.

    That index is my most used part of the manual!

hakank: You will get a small speedup (0.32s -> 0.2s) by using table on action/4.

    I thought planner automatically tables action and final. See figure 5.1 in the Picat manual.

hakank: I'm not sure I understand "unified with itself" and "is both input and output". The point of S as a map is rather that it's mutable (as maps usually are). 

    I thought you said that variables in Picat aren't mutable. :-)

`reduce` I think this is a functional `fold`, but have not been able to make it work.
hakank: Yes, reduce/2 and reduce/3 are the fold variants. Note the order of the arguments, especially reduce/3.
hakank: Picat> X=reduce(+,1..10)
hakank: X = 55
hakank: Picat> X=reduce(+,1..10,10)
hakank: X = 65
hakank: What did you try that didn't work?

    Once again, I failed to distinguish between a predicate and a function! I tried Picat> reduce(+,1..10), and when it didn't work, I didn't realize I needed to assign it to a value.

    If the error message said something like, "reduce is a function, not a predicate" it would help me a lot more than the current error message: "*** Undefined procedure: reduce/2"

    This happens to me so often, but I still forget.

    Also, now I know how to use `reduce` and I added some examples and I also really like your Haskell Prelude in Picat.