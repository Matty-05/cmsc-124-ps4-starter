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

Since 'ancestor_r' here goes into the recursive clause first, we go down bob then ann then sue giving us an X=sue first, then it goes down bob then pat then jim giving us X=jim second. Backtracking then gets us ann and pat, with the other clause giving us liz and bob so we also get 6 total.

## P10

Since we go into the first clause we get X=pat first with the only parent fact that has jim second. In the recursive clause we hit tom first going from top to bottom so that gives us X=tom as second answer in the query and then bob as third. Our count here becomes 3.

## P11

'reach(a,d)' here keeps going back into 'reach()' infinitely without ever getting to the 'edge()' part so all are unbounded here.

## P12

In 'reach_r(a,d)' we get an 'edge()' first and when it fails it goes into the recursion clause so we go through a to b to c to d and get true, then we backtrack to get c to a to b to c to d so we get a second true. After we get the 'edge(c,d)' it goes back into the recursion clause which gets us to 'edge(c,a)' so it eventually calls 'reach_r(a,d)' after all that and keeps going infinitely and we get an unbounded count.

## P13

'len[]' here keeps going until we hit an empty list so we take off each head 3 times where +1 happens to N, so we get N=3 with no second answer and a count of 1.

## P14

So we start with the first 'join' which matches X to [] and [1,2] to L, and since Y is matched to L as well, Y is matched to [1,2] which is our first answer. Then we go into the recursion 'join' where X is matched to [H|T], Y is matched to L again and [1,2] to [H|R] is unified so H is 1 and R is [2] which gives us an X matched to [1|T] which calls 'join(T, Y, [2])' which again goes to the first clause giving us X = [1] from the previous [1|T] and Y = [2] for the second answer. Then we go into the second clause for 'join(T, Y, [2])' and we match T with another [H|T] so since X is [1|T] we get an X = [1, H|T] and 2 becomes the H in the [H|R] so we get an X=[1,2|T] and then it goes 'join(T, Y, [])' and we get T=[] and Y=[] which goes back into the previous X so X is now [1,2] and Y is [], and then another recursion clause for 'join(T, Y, [])' but it fails since we cant split an empty list so that leaves us at count 3.

## P15

Here it does 'X > 2' first but an operator like '>' needs bounded arguments for both sides, and since there is no X yet, it results in an error so all fields here are error.

## P16

Since we are now actually binding X first, there is no error like in P15 so we go on with 'elem(X, [X|_])' which gets us X = 1 since we take the head from [1,2,3] and it fails so we go into the recursive 'elem()' that calls 'elem(X, [2,3])' which again fails in first 'elem()' since 2 is not greater than 2, so again it goes into the recursive one with now 'elem(X, [3])' which gives true, and now we try the next one with 'elem(X, [])' but since we cannot split an empty list the query ends with a false here.

