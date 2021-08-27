# TypeInferenceAlgorithm
This folder consists of the files necessary to perform type inference on Prolog programs.

-- Normalize.pl --

Performs normalization of programs. A program is normalized if there is only one clause defining each predicate and the body of the clause is the ``or'' of the bodies of each separate clause in the non-normalized program. The head contains only variables, which are the only variables that can be shared by different or-bodies.

Example:

add(zero,X,X).
add(s(X),Y,s(Z)) :- add(X,Y,Z).

add(X1,X2,X3) :- (X1 = zero, X2 = X, X3 = X ; X1 = s(X'), X2 = Y, X3 = s(Z), X4 = X', X5 = Y, X6 = Z, add(X4,X5,X6)).

To run normalize:
1) term_expander((clause),_). where clause is the clause to normalize.
2) repeat for all clauses.
3) term_expander(end_of_file,_).
4) findall(X, clause(X), List).
5) List is the list of all normalized clauses.

This step is mandatory for the program to work, even if the predicates in a program already have only one clause, since the algorithm will assume that every occurence of terms inside predicate is a variable.


-- TypeInferenceAlgorithm --

There are several Prolog files inside this folder. TypedCompile.pl is the main one. When compiled ['TypedCompile.pl'], it compiles the other ones automatically.
The files:
- Generation.pl : corresponds to the constraint generation part of the algorithm. It generates the constraints for each clause. If a clause calls another predicate, it will need to solve the constraints generated by that predicate first before generating the constraints for the caller. This is because we assume a stratified program.
- Solver.pl : corresponds to the constraint solving part of the algorithm. Ti solves the equality and subtyping constraints makes the necessary steps, in order to get the final set of type definitions. This file includes the intersection algorithm, which calculates the interstion of two types; and the simplification algorithm that simplifies type definitions as necessary, to ``clean'' the output and assure that type definitions are deterministic.
- New_Better_Closure.pl : corresponds to the closure algorithm, that will be executed iff the programmer has asked for it or if the prorammer declared at least one data type.
- PP.pl : correspons to the pretty printer that prints the types for the predicates.


To run inference:
1) typed_compile(file). _where file is the file that contains the Prolog program to infer types for._
2) see printed output.

This inference algorithm was published in https://arxiv.org/abs/2108.06562, in LOPSTR 2021, and is based on the type system published in https://arxiv.org/abs/1909.08232, in ICLP 2019.
