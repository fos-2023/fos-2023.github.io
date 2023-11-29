---
title: Project 5 - STLC Extensions
layout: default
start: 29 Nov 2023, 00:00 (Europe/Zurich)
index: 8
---

# Project 5: STLC Extensions

**Hand in:** 20 Dec 2023, 23:59 (Europe/Zurich)<br/>

**Project template:** [5-extensions.zip](projects/5-extensions.zip)

The project skeleton is very similar to the one from the last assignment.
You should download the template and copy your definitions from Project 3.
The final submission should contain all extensions, both from this assignment and from project 3.
In this assignment, you will extend your simply typed lambda calculus project from project 3 with several features.

There is a common base that everyone must implement:

* sum types, and
* recursive functions (the `fix` operator).

In addition, each group must *choose one* of the following "bundles" of features:

* imperative programming bundle: unit, sequencing and mutable state, or
* functional programming bundle: tuples, lists and universal types, or
* subtyping bundle: records and subtyping.

In any case, there is *no type reconstruction* in this project.

## Attention! No grader feedback for the extensions

The automatic tests that you receive from the bot will only test the common base.
For your chosen bundle, **you must write tests yourselves** to validate that you correctly implemented it.
Additional testing of the bundles will be done after your final submission.

## Grading for the bundles

The grade of this project will be 50% for the common base, and 50% for your bundle.

If you feel adventurous, you may implement *several* bundles.
In that case, we will take the *highest* grade you achieve among the 3 bundles.

## Built-in rules or derived forms

Unless otherwise specified, all the features must be built-in (as opposed to derived forms).

## Sum Types

Sum types are handy when one needs to handle heterogeneous collections of values. Imagine you
have a data type like a tree, where each element can be a leaf (containing a value) or an inner
node (containing two references to subtrees). A sum type is a type which draws its values from
exactly two other types. In other words, values of a sum type T1 + T2 can be either of type T1
or of type T2. We introduce now syntactic rules for sum types, together with evaluation and
typing rules.

    t ::= ...                                                terms
        | "inl" t "as" T                                     inject left
        | "inr" t "as" T                                     inject right
        | "case" t "of" "inl" x "=>" t "|" "inr" x "=>" t    case

    v ::= ...                                                values
        | "inl" v "as" T
        | "inr" v "as" T

    T ::= ...                                                types
        | T "+" T                                            sum type (right assoc.)


We create values of a sum type by injecting a value in the left or right "part". To deconstruct
such values, we use a case expression, similar to Scala’s match construct.

Sum types are right associative and `+` has the same precedence as `*`.

Evaluation rules for sum type extension:

    case (inl v0) of inl x1 => t1 | inr x2 => t2  →  [x1 → v0] t1

    case (inr v0) of inl x1 => t1 | inr x2 => t2  →  [x2 → v0] t2

                                       t   →   t'
    --------------------------------------------------------------------------------
    case t of inl x1 => t1 | inr x2 => t2  →  case t' of inl x1 => t1 | inr x2 => t2


        t  →  t'
    ----------------
    inl t  →  inl t'

        t  →  t'
    ----------------
    inr t  →  inr t'

And lastly new typing rules:

    Γ ⊢ t0: T1 + T2    Γ, x1: T1 ⊢ t1: T    Γ, x2: T2 ⊢ t2: T
    ---------------------------------------------------------
         Γ ⊢ case t0 of inl x1 => t1 | inr x2 => t2: T


              Γ ⊢ t1: T1
    -------------------------------
    Γ ⊢ inl t1 as T1 + T2 : T1 + T2

              Γ ⊢ t1: T2
    -------------------------------
    Γ ⊢ inr t1 as T1 + T2 : T1 + T2

Evaluation and typing rules are pretty straightforward, the only thing a bit out of
ordinary is the type adornment for inl and inr. This is necessary because the two
constructors can’t possibly know what is the type of the other component of the sum
type. By giving it explicitly, the type checker is able to proceed. Alternatives to
this decision will be explored in other exercises, when we know about type inference.

## The fix operator

To bring back the fun into the game, we need some way to write non terminating programs
(or at least, looping programs). Since types arrived, we were unable
to type the fixpoint operator, and indeed there’s a theorem which states that all well
typed terms in simply typed lambda calculus evaluate to a normal form. The only
alternative is to add it into the language.

    t ::= ...        terms
        | "fix" t    fixed point of t

New reduction rules:

    fix (λx: T1. t2)  →  [x → fix (λx: T1. t2)] t2

        t  →  t'
    ----------------
    fix t  →  fix t'

New typing rules:

    Γ ⊢ t1: T1 → T1
    ---------------
    Γ ⊢ fix t1: T1

In order to make the fixpoint operator easier to use, add the following derived form:

    letrec x: T1 = t1 in t2  ⇒  let x = fix (λx: T1. t1) in t2

Don’t forget that we implement let as a derived form as well, so the actual desugaring
is a bit more complex.

## Imperative programming bundle

The imperative programming bundle contains three features:

* The `unit` value and its `Unit` type, as defined in Section 11.2 (Figure 11-2)
* Sequencing as defined in Section 11.3 of the book
* Mutable state with references, as defined in Chapter 13

For mutable references, *do not introduce Σ in your `typeof` function*!
You do not need to give types to *locations*, since locations cannot be written in a source program.
Store typings are only necessary as an artifact of safety proofs, but we are not doing proofs here.

You must, however, introduce μ in your evaluation function, and hence you have to implement the overload of `reduce` that manipulates `Store`s.

Terms:

    t ::= ...
        | unit            the unit value
        | t; t            sequence
        | ref t           allocate new reference
        | !t              dereference
        | t := t          assignment
        | l               location (but not available in the source program, so not in the parser)

Types:

    T ::= ...
        | Unit
        | Ref T

Evaluation rules:

        t1 | μ  →  t1' | μ'
    ---------------------------
    t1; t2 | μ  →  t1'; t2 | μ'

    -----------------------
    unit; t2 | μ  →  t2 | μ

        t1 | μ  →  t1' | μ'
    ---------------------------
    ref t1 | μ  →  ref t1' | μ'

          l ∉ dom(μ)
    ----------------------------------
    ref v1 | μ  →  l | (μ, l -> v1)

     t1 | μ  →  t1' | μ'
    ---------------------
    !t1 | μ  →  !t1' | μ'

        μ(l) = v
    ----------------
    !l | μ  →  v | μ

          t1 | μ  →  t1' | μ'
    --------------------------------
    t1 := t2 | μ  →  !t1' := t2 | μ'

          t2 | μ  →  t2' | μ'
    --------------------------------
    v1 := t2 | μ  →  !v1 := t2' | μ'

    ---------------------------------
    l := v2 | μ  →  unit | [l -> v2]μ

Typing rules (note that there is no Σ, and no rule for locations):

    ---------------
    Γ ⊢ unit : Unit

    Γ ⊢ t1 : Unit     Γ ⊢ t2 : T2
    -----------------------------
        Γ ⊢ t1; t2 : T2

       Γ ⊢ t1 : T1
    -------------------
    Γ ⊢ ref t1 : Ref T1

    Γ ⊢ t1 : Ref T1
    ---------------
      Γ ⊢ !t1 : T1

    Γ ⊢ t1 : Ref T1    Γ ⊢ t2 : T1
    ------------------------------
        Γ ⊢ t1 := t2 : Unit

## Functional programming bundle

The function programming bundle contains two features:

* Explicit universal types, aka System F, as defined Section 23.3 (Figure 23-1)
* Records, as defined in Section 11.8 (Figure 11-7)
* Tuples, like defined in Section 11.7 (Figure 11-6), *but as a derived form*
* Lists, almost as defined in Section 11.12 (Figure 11-13)

Tuples *must* desugar into records whose labels are "integer strings", such as `"1"`, `"2"`, etc.

As shown by a suggested exercise in the book, `head` and `tail` are not sound.
Therefore, we make a few changes compared to the presention in Section 11.12:

* We remove `head` and `tail`
* We introduce instead a pattern matching construct
* We remove `isnil` because it is redundant

The pattern matching construct is similar to the one for sum types.
Despite the similarity of the surface syntax, it is truly a different, distinct term shape from the one for sum types.

Terms:

    t ::= ...
        | "/\" X "." t                                        type abstraction
        | t "[" T "]"                                         type application
        | "{" { l "=" t "," } "}"                             record
        | t "." l                                             record projection
        | "{" { t "," } "}"                                   tuple (to be desugared)
        | t "." i                                             tuple projection (to be desugared)
        | "nil "[" T "]"                                      empty list
        | "cons "[" T "]" t t                                 list constructor
        | "case" t "of" "nil" "=>" t "|" "cons" x y "=>" t    list case

We use `{ ... }` to denote repetition (0 to many times).
The last comma is required to simplify the notation of the term syntax here.

Types:

    T ::= ...
        | X                             type variable
        | "\/" X "." T                  universal type
        | "{" { l ":" T "," } "}"       type of records
        | "{" { Ti "," } "}"            type of tuples (to be desugard)
        | List T                        type of lists

Evaluation rules:

         t1  →  t1'
    --------------------
    t1 [T2]  →  t1' [T2]

    (λX: t1) [T2]  →  [X → T2]t1

                            tj  →  tj'
    ------------------------------------------------------------
    {li = vi, lj = tj, lk = tk}  →  {li = vi, lj = tj', lk = tk}
         with i ∈ 1..(j-1) and k ∈ (j+1)..n

      t1  →  t1'
    --------------
    t1.l  →  t1'.l

    {li = vi}.lj  →  vj

                t1  →  t1'
    ----------------------------------
    cons [T] t1 t2  →  cons [T] t1' t2

                t2  →  t2'
    ----------------------------------
    cons [T] v1 t2  →  cons [T] v1 t2'

                                      t1   →   t1'
    --------------------------------------------------------------------------------
    case t1 of nil => t2 | cons x y => t3  →  case t1' of nil => t2 | cons x y => t3

    case nil of nil => t2 | cons x y => t3  →  t2

    case (const v0 v1) of nil => t2 | cons x y => t3  →  [x → v0] [y → v1] t3

Typing rules:

      Γ, X ⊢ t2 : T2
    -----------------
    Γ ⊢ λX.t2 : ∀X.T2

         Γ ⊢ t1 : ∀X.T1
    ------------------------
    Γ ⊢ t1 [T2] : [X → T2]T1

            for each i  Γ ⊢ ti : Ti
    -----------------------------------------
    Γ ⊢ {l1=t1 ... ln=tn} : {l1:T1 ... ln:Tn}

    Γ ⊢ t1 : {l1:T1 ... ln:Tn}
    --------------------------
          Γ ⊢ t1.li : Ti

    Γ ⊢ nil [T] : List T

    Γ ⊢ t1 : T1     Γ ⊢ t2 : List T1
    --------------------------------
      Γ ⊢ cons [T] t1 t2 : List T2

    Γ ⊢ t1 : List T1   Γ ⊢ t2 : T   Γ, x: T1, y: List T1 ⊢ t3 : T
    -------------------------------------------------------------
          Γ ⊢ case t1 of nil => t2 | cons x y => t3 : T

Self-check: can you define a `map` function in your language?

Further thoughts: could we define `nil` and `cons` as regular terms with polymorphic types, as suggested in Section 23.4?
What would be difficult given the structure of our evaluation processor?

## Subtyping bundle

The subtyping bundle contains three features:

* Records, as defined in Section 11.8 (Figure 11-7)
* Subtyping for records, as defined in selected sections of Chapters 15 and 16 (Figures 15-1, 16-2 and 16-3)
* Ascription, as defined in Section 11.4 (Figure 11-3)

Note that we use *algorithmic subtyping and typing*, as presented in Chapter 16.
We have not seen that in the lectures at the time this project starts (we will see the coming week).
You may want to skim through Section 16.1 and 16.2, but the rules below recap everything.

The reason we introduce ascription is that it relieves us from having to think about Joins and Meets (see Section 16-3).
We can instead use ascription to force the two sides of an if-then-else to have the same type.

Terms:

    t ::= ...
        | "{" { l "=" t "," } "}"       record
        | t "." l                       projection
        | t "as" T                      ascription

We use `{ ... }` to denote repetition (0 to many times).
The last comma is required to simplify the notation of the term syntax here.

Types:

    T ::= ...
        | "{" { l ":" T "," } "}"       type of records

Evaluation rules:

                            tj  →  tj'
    ------------------------------------------------------------
    {li = vi, lj = tj, lk = tk}  →  {li = vi, lj = tj', lk = tk}
         with i ∈ 1..(j-1) and k ∈ (j+1)..n

      t1  →  t1'
    --------------
    t1.l  →  t1'.l

    {li = vi}.lj  →  vj

         t1  →  t1'
    --------------------
    t1 as T  →  t1' as T

    v1 as T  →  v1

Subtyping rules:

    Nat <: Nat

    Bool <: Bool

          {li...} ⊆ {kj...}   for i ∈ 1..n and j ∈ 1..m
                if kj = li then Sj <: Ti
    --------------------------------------------------------
       {kj: Sj...} <: {li: Ti...}  for i ∈ 1..n and j ∈ 1..m

    T1 <: S1     S2 <: T2
    ---------------------
      S1 → S2 <: T1 → T2

Typing rules:

    Γ ⊢ t1 : T1     T1 = T11 → T12
    Γ ⊢ t2 : T2     T2 <: T11
    ------------------------------
          Γ ⊢ t1 t2 : T12

            for each i  Γ ⊢ ti : Ti
    -----------------------------------------
    Γ ⊢ {l1=t1 ... ln=tn} : {l1:T1 ... ln:Tn}

    Γ ⊢ t1 : R1    R1 = {l1:T1 ... ln:Tn}
    -------------------------------------
              Γ ⊢ t1.li : Ti

    Γ ⊢ t1 : T1     T1 <: T
    -----------------------
        Γ ⊢ t1 as T : T

## What you need to do

1. Choose (at least) one bundle to implement. Add to it the sum types and the `fix` operator.

1. Implement `reduce` method which performs one step of the evaluation, according to the
rules of call-by-value reducer (small step). If none of the rules apply, it should throw
`NoRuleApplies` exception containing corresponding irreducible term.

1. Implement the `typeof` method which, given a term, finds its type. In the case of failure,
it should throw a `TypeError` exception containing the ill-typed term. The ill-typed term
is the leftmost innermost term that doesn't typecheck, i.e. that doesn't have any typing
rules that apply to it.

1. Do not forget to write tests for your chosen bundle.

**Hint**: re-using code from previous assignments might be a good idea.

## Development & debugging

Since the project skeleton is the same as in the previous assignment, everything from
the "Development & debugging" section in [project 3 description](project3.html)
applies to this project as well.