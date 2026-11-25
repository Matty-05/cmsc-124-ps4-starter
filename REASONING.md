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

'parent(tom, X)' here scans facts from from top to bottom. We see liz first then we see bob. Since there are no other children for tom, we get a count of 2.

## P02

Here we check if 'parent(tom, bob)' is true and since it is, there is no second so we get count 1.

## P03

'parent(X, tom)' here looks for a parent fact that has tom second. Since there is none found, it is false and we get count 0.

## P04

Unification binds X to f(b,a) and then Y to b. Count is 1 since we get X=f(b,a),Y=b and no other answer.

## P05

Here unification binds the terms positionally so X binds to a and Y binds to b. No second answer so count is 1.

## P06

Since a and b are distinct, they cannot unify. The query fails and we get false with 0 count.

## P07

When looking at the 'grandparent(G,C)' rule, we first find P whose parent is G, then we look at those who have parent P. There are no parent rules with liz as first so nothing there. We find 2 children through bob so first is ann and second is pat. No other parent facts are true so count is 2.

## P08

'ancestor' here has 2 clauses so we first get those whose parent is tom, then we go into the second clause where we go into bob's children's children which are ann and pat who also have a child each being jim and sue. We get a count of 6 total.

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

