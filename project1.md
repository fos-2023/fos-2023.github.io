---
title: Project 1 - The NB Language
layout: default
start: 01 Jan 2023, 00:00 (Europe/Zurich)
index: 4
---

# Project 1: The NB Language

**Hand in:** 11 Oct 2023, 23:59 (Europe/Zurich)

**Project template:** [1-arithmetic.zip](projects/1-arithmetic.zip)

Note: This assignment is not graded.

The cryptic acronym stands for Numbers and Booleans and comes from the course book.
This simple language is defined in Chapter 3 of the the TAPL book.

    t  ::= "true"                   terms
         | "false"
         | "if" t "then" t "else" t
         | numericLiteral
         | "succ" t
         | "pred" t
         | "iszero" t

    v  ::= "true"                   values
         | "false"
         | nv

    nv ::= 0                        numeric values
         | "succ" nv

This language has three syntactic forms: terms, which is the most general form, and two
kinds of values: numeric values, and boolean values. We have extended the syntax by
allowing numeric literals. They are syntactic sugar and have to be transformed during
parsing to their equivalent value `succ succ .. 0`. The language is completely defined by
the production `t`, for terms. Values are a subset of terms, and for simplicity they are
defined using a BNF notation, but they need not be parsed as such.

## Small step semantics

The evaluation rules are as follows.

             if true then t1 else t2 → t1

             if false then t1 else t2 → t2

                  isZero zero → true

                isZero succ nv → false

                    pred zero → zero

                   pred succ nv → nv

                        t1 → t1'
     ——————————————————————————————————————————————
     if t1 then t2 else t3 → if t1' then t2 else t3

                         t → t'
                  ————————————————————
                  isZero t → isZero t'

                         t → t'
                    ————————————————
                    pred t → pred t'

                         t → t'
                    ————————————————
                    succ t → succ t'

## Big step semantics

The other style of operational semantics commonly in use is called big step sematics.
Instead of defining evaluation in terms of a single step reduction, it formulates the
notion of a term that evaluates to a final value, written "t ⇓ v". Here is how the big
step evaluation rules would look for this language:


               v ⇓ v            (B-VALUE)

      t1 ⇓ true     t2 ⇓ v2
    ——————————————————————————  (B-IFTRUE)
    if t1 then t2 else t3 ⇓ v2

      t1 ⇓ false     t3 ⇓ v3
    ——————————————————————————  (B-IFFALSE)
    if t1 then t2 else t3 ⇓ v3

             t1 ⇓ nv1
        ——————————————————      (B-SUCC)
        succ t1 ⇓ succ nv1

            t1 ⇓ 0
          ———————————           (B-PREDZERO)
          pred t1 ⇓ 0

         t1 ⇓ succ nv1
         —————————————          (B-PREDSUCC)
         pred t1 ⇓ nv1

            t1 ⇓ 0
        ————————————————        (B-ISZEROZERO)
        iszero t1 ⇓ true

         t1 ⇓ succ nv1
       —————————————————        (B-ISZEROSUCC)
       iszero t1 ⇓ false

## What you have to do

1. Implement `reduce` method which performs one step of the evaluation, according to the rules
   of the small step semantics. If none of the rules apply it should throw `NoReductionPossible`
   exception containing the smallest corresponding irreducible term.

1. Implement `eval` method which implements a big step evaluator (one which evaluates a term
   down to a value, or it gets stuck when no rule applies). This method should implement
   the big step semantics defined above, and not call reduce. If evaluation is not possible
   it should throw `TermIsStuck` exception containing the smallest corresponding stuck term.

1. Implement the `programVSmallerThan3` method. It must return a (constant) string that
   represents a program. The program receives `v` as "input", and must evaluate to `True`
   if `v < 3` and `False` otherwise. `v` is guaranteed to be a numeric value.

1. (for further thought:) can you implement a program that tests whether `v` is even?
   If not, why not?

All of these are given with stubbed `???` body. `???` is used to indicated unimplemented
parts of code in Scala.

## Development & debugging

We provide a simple runnner for your application that lets you quickly debug your current
implementation. When you implement `reduce` and `eval` implementations it should
produce results like the following examples.

In extension to the above syntax, a limited form of binding with `"declare" ident "=" term ("," ident "=" term)* "in" term`
is available.
For now, bindings are directly inlined into references during parsing, and later bindings
can only reference bindings defined above them.

**Example 1**:

input:

    if iszero pred pred 2 then if iszero 0 then true else false else false

output:

    If(IsZero(Pred(Pred(Succ(Succ(Zero))))),If(IsZero(Zero),True,False),False)
    If(IsZero(Pred(Succ(Zero))),If(IsZero(Zero),True,False),False)
    If(IsZero(Zero),If(IsZero(Zero),True,False),False)
    If(True,If(IsZero(Zero),True,False),False)
    If(IsZero(Zero),True,False)
    If(True,True,False)
    True
    Big step: True

**Example 2**:

input:

    declare
        x = succ succ false,
        y = succ x
    in
    pred y

output:

    Pred(Succ(Succ(Succ(False))))
    Big step: Stuck term: Succ(False)