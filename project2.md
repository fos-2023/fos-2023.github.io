---
title: Project 2 - Untyped Lambda Calculus
layout: default
start: 11 Oct 2023, 00:00 (Europe/Zurich)
index: 5
---

# Project 2: Untyped Lambda Calculus

**Hand in:** 25 Oct 2023, 23:59 (Europe/Zurich)

**Project template:** [2-untyped.zip](projects/2-untyped.zip)

In this exercise, you'll be studying untyped λ-calculus. This classic language is defined in Chapter 5 of the TAPL book.

    t ::= ident              terms
        | "\" ident "." t
        | t t
        | "(" t ")"

    v ::= "\" ident "." t    values

We use standard syntax to express λ-terms, with lambdas replaced by backslashes. As a reminder, note that the bodies of abstractions are taken to extend as far to the right as possible, so that, for example, `λx. λy. x y x` stands for the same tree as `λx. (λy. ((x y) x))` (see TAPL, p54).

## Evaluation rules

The evaluation rules for full beta-reduction are as follows.

       t1 → t1'
    --------------
    t1 t2 → t1' t2

       t2 → t2'
    --------------
    t1 t2 → t1 t2'

        t1 → t1'
    ----------------
    λx. t1 → λx. t1'

    (λx. t1) t2 → [x → t2] t1

The last rule uses substitution, whose definition we reproduce here (see TAPL, p71):

| Input              | Output                    | Condition                                                         |
|--------------------|---------------------------|-------------------------------------------------------------------|
| `[x → s] x`        | `s`                       |                                                                   |
| `[x → s] y`        | `y`                       | if `y ≠ x`                                                        |
| `[x → s] (λy. t1)` | `λy . t1`                 | if `y = x`                                                        |
|                    | `λy . [x → s] t1`         | if `y ≠ x` and `y ∉ FV(s)` **<span style="color:red">(*)</span>** |
| `[x → s] (t1 t2)`  | `([x → s] t1 [x → s] t2)` |                                                                   |

The part marked with an **<span style="color:red">(*)</span>** doesn't handle the case where `y ∈ FV(s)`. So what shall we do then? We first use of alpha-conversion for consistently renaming a bound variable in a term - actually a lambda abstraction - and then continue to apply the substitution rules. To rename a bound variable in a lambda abstraction `λx. t1`, one chooses a fresh name `x1` for bound variable `x`, and consistently renames all free occurrences of `x` in the body `t1`. Note: you will need to define a function that "freshens" variable names. You can rely on variable names in the input term containing only characters, never any numbers.

We use the following rules to test if a variable is free in some term:

| Input        | Output            |
|--------------|-------------------|
| `FV(x)`      | `{x}`             |
| `FV(λx. t1)` | `FV(t1) \ {x}`    |
| `FV(t1 t2)`  | `FV(t1) ∪ FV(t2)` |

## Evaluation strategies

TAPL p56 presents several evaluation strategies for the λ-calculus:

  * Under **full beta-reduction** any redex may be reduced at any time. This is the strategy described by the evaluation rules listed above, but this is too unrestricted in practice: we need a deterministic way to choose a certain redex, when more than one is available.

  * Under **normal order** strategy, the leftmost, outermost redex is always reduced first. This strategy allows to reduce inside unapplied lambda terms.

  * The **call-by-name** strategy is yet more restrictive, allowing no reductions inside lambda abstractions. Arguments are not reduced before being substituted in the body of lambda terms when applied.

      Haskell uses an optimized version known as call-by-need that, instead of re-evaluating an argument each time it is used, overwrites all occurrences of the argument with its value the first time it is evaluated, avoiding the need for subsequent re-evaluation.

  * Most languages use a **call-by-value** strategy, in which only outermost redexes are reduced and where a redex is reduced only when its right-hand side has already been reduced to a value (a function).

      The call-by-value strategy is strict, in the sense that the arguments to functions are always evaluated, whether or not they are used by the body of the function. In contrast, lazy strategies such a call-by-name and call-by-need evaluate only the arguments that are actually used.

## What you have to do

  1. Implement `reduceNormalOrder` method that uses the normal-order strategy, which applies alpha-conversion and term substitution following the above reduction rules. If none of the rules apply it should throw `NoReductionPossible` exception containing corresponding irreducible term.

  1. Implement `reduceCallByValue` method that uses the call-by-value evaluation strategy. For that you need to define a new set of evaluation rules, similar to the ones given above. Again, if none of the rules apply it should throw `NoReductionPossible` exception containing corresponding irreducible term.

1. Implement the `programGCD` method.
   It must return a (constant) string that represents a program.
   The program must evaluate to an abstraction value.
   When applied to two Church numerals `a` and `b` as arguments, that function must return another Church numeral that represents the greatest common divisor (GCD) of `a` and `b`.
   For example, if applied to `c4` and `c6`, it must return a Church numeral that behaves like `c2`.
   Use the functional variant of the Euclidian algorithm to compute `gcd(a, b)`: it is defined as `gcd(a, b) = if b == 0 then a else gcd(b, mod(a, b))`.
   We give you a template with all the "standard" lambda-terms that you already used in the exercise last week; feel free to define additional helpers.

      Since we speak about values, we need to define what a value is. We will follow the book in saying that the only values are lambda abstractions. Does it simplify the substitution operation? What would happen if we add variables to the set of values (do not implement, these are self-check questions)? Compare the output of the two reducers, and try to understand why it is different.

## Development & debugging

We provide a simple runnner for your application that lets you quickly debug your current implementation. When you implement `term`, `reduceNormalOrder` and `reduceCallByValue` implementations it should produce results like:

input:

    \y. ((\x. x) y)

output:

    normal order:
    Abs(y,App(Abs(x,Var(x)),Var(y)))
    Abs(y,Var(y))
    call-by-value:
    Abs(y,App(Abs(x,Var(x)),Var(y)))

### Parser and `declare .. in` syntax

Similar to P1, the provided parser supports additional syntax as follows:
```
    tl       ::= term                          top-level expressions
               | "declare" bindings "in" tl

    bindings ::=                               simple bindings
               | binding "," bindings
    binding  ::= ident "=" term                single binding
```
where the **top-level** expression is allowed to have simple expression bindings that will be inlined into the resulting expression.
Similar to P1, only later bindings (and the term after `in`) are allowed to refer to earlier bindings.

Note that like P1, cascading `declare`s are allowed:
```
declare
    zero = \f. \x. x
  , succ = \n. \f. \x. f (n x)
in declare
    one = succ zero
in succ one
```
but unlike P1, `declare`s are not allowed within any expression (other than the top-level expression). So the following code will **not** parse:
```
declare
    zero = (declare x = \t. t in \f. x)
in zero
```


Also, be careful with shadowing bindings inside lambdas!
```
declare x = \t. t in \x. x // parses into Abs(x, Var(x))
declare x = \t. t in \y. x // parses into Abs(y, Abs(t, Var(t)))
```