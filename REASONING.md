<!--no-pdf-->
# Why Each Prediction Reads the Way It Does

Write one entry per case, before you run anything. Name the rule behind each
predicted field. One to three sentences is enough. The grader checks the
structure of this file only. It does not judge how good the reasoning is, and
it sets no word minimum.

For every case you predicted wrongly, add a short correction after you run
it: what you predicted, what the language printed, and which rule you
misread. Corrections belong in the correction commits, not the prediction
commit.

Commit this file together with `predictions.tsv`, before any case runs:

```bash
git add predictions.tsv REASONING.md
```

The example below uses a query that is not one of the 16 cases, so it gives
no answer away. `parent(pat, X)` reads `parent(pat, jim)` because the `parent`
facts are tried top to bottom and only that one has `pat` as its first
argument:

```text
## parent(pat, X)

The goal scans the parent facts in source order. Only parent(pat, jim) has
pat first, so X binds to jim. No second fact matches, so the first ; finds
nothing and the search ends after one answer.
```

## P01

Prolog tries the `parent/2` facts in source order. The only two facts with Tom as the parent bind `X` to `liz` first and `bob` on backtracking.

## P02

The ground query matches `parent(tom,bob)` exactly, so it returns `true`. No other fact matches, so there is one answer.

## P03

No `parent/2` fact has `tom` as its second argument. The query returns `false` and has zero solutions.

## P04

Unification first binds `X` to `f(Y,a)`. The next goal binds the same `Y` to `b`, yielding `X=f(b,a),Y=b` as the only answer.

## P05

Both terms have the functor `f/2`, so unification matches the arguments by position. This binds `X=a` and `Y=b` in one solution.

## P06

The atoms `a` and `b` differ, so unification fails and the query has zero solutions.

## P07

`grandparent/2` looks for a child of Tom and then a child of that person. Liz has no children in the facts, so Prolog backtracks to Bob and finds his two children, Ann and Pat, in that order.

## P08

The base clause of `ancestor/2` gives Tom's direct children, Liz and Bob, first. The recursive clause finds Ann, Pat, Sue, and Jim through Bob, giving six answers in all.

## P09

`ancestor_r/2` tries recursion before the direct-parent clause. The depth-first search reaches Sue through Ann and then Jim through Pat before giving the shallower answers, for six total.

## P10

The base clause finds Jim's direct parent, Pat, first. The recursive clause tries Tom's parent facts before Bob's and finds the path from Tom through Bob and Pat to Jim, so Tom is second. Backtracking then returns Bob through Pat, giving three answers total.

## P11

The first clause of `reach/2` calls itself before checking any edge. The search keeps descending without reaching the base clause, so both answer slots and the count read `unbounded`.

## P12

`reach_r/2` follows edges before recursing and reaches `d` along `a -> b -> c -> d`. On backtracking it can go around `a -> b -> c -> a` any number of times before reaching `d` again, so the first two answers are `true` and the count is `unbounded`.

## P13

`len/2` gives the empty list length zero and adds one as each recursive call returns. The three elements give the single answer `N=3`.

## P14

The base clause of `join/3` gives the split `X=[],Y=[1,2]` first. Each recursive step adds one element to the prefix, giving `X=[1],Y=[2]` second and `X=[1,2],Y=[]` third.

## P15

Prolog evaluates `X > 2` before `elem/2` can bind `X`. The comparison raises an instantiation error, so the runner records `error` in all three fields.

## P16

`elem/2` binds `X` to each element before the comparison runs. Only `3` passes `X > 2`, so `X=3` is the sole answer.

