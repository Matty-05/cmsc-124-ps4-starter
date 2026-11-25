# The Search and the Stack

== trace P07 ==
Call: grandparent(tom,X)
  Call: parent(tom,_)
  Exit: parent(tom,liz)
  Call: parent(liz,X)
  Fail: parent(liz,X)
  Redo: parent(tom,liz)
  Exit: parent(tom,bob)
  Call: parent(bob,X)
  Exit: parent(bob,ann)
Exit: grandparent(tom,ann)
answer 1: X=ann
Redo: grandparent(tom,ann)
  Redo: parent(bob,ann)
  Exit: parent(bob,pat)
Exit: grandparent(tom,pat)
answer 2: X=pat
Redo: grandparent(tom,pat)
  Redo: parent(bob,pat)
  Fail: parent(bob,X)
  Redo: parent(tom,bob)
  Fail: parent(tom,_)
Fail: grandparent(tom,X)
== 2 answers ==

- Which of its lines push a frame onto Activity 15's runtime stack? Which
  lines pop one?

  It should be the function calls that are pushing a frame onto the runtime stack. Everytime there is a 'Call:' line, a new frame is pushed that needs to store the arguments for that specific frame. Returning a value should be what pops a frame and data is deleted, but Prolog has Redo, so even if an 'Exit' line returns a value, if the other choices that have been save are not exhausted yet through 'Redo', they don't actually pop the frame.

  The opening `Call: grandparent(tom,X)` would push the outer frame in Activity 15. The indented `Call: parent(liz,X)` would push a child frame, and `Fail: parent(liz,X)` shows that invocation ending without a result. The first `Exit: grandparent(tom,ann)` is like a successful return. In Prolog, the later `Redo: grandparent(tom,ann)` shows that the search still has alternatives after that return.

- What does a `Redo` line resume that a returned frame cannot?

  A returned frame means a popped frame, so that means that the stored data within that frame is already gone. A 'Redo' line goes back to a save point from before choosing the clause it is already on and resets bindings for the next clause. This makes it so that it is able to exhaust all the possible clauses and eventually exhaust all choices.

  That save point is a choice point. `Redo: parent(bob,ann)` resumes the search at Bob's next matching fact. It undoes the binding `X = ann` and tries `parent(bob,pat)`. The stack in Activity 15 has no record of a next alternative once an invocation returns.

- Why does swapping the two clauses of `reach/2` change whether the search
  ever answers, while swapping the two clauses of `ancestor/2` only changes
  the order of the answers?

  With the recursive `reach/2` clause first, Prolog calls `reach(X, Z)` before it checks an edge. The next call does the same thing, so the search can descend forever without trying the base clause. Putting the base clause first lets each call try a direct edge before recursing.
  For `reach(a,d)`, that makes it possible to find `a` to `b` to `c` to `d`. The cycle still allows the search to keep going on backtracking.

  The recursive `ancestor/2` clause follows a `parent/2` fact before it calls itself. Each call moves down one generation in the finite family tree, so either clause order eventually runs out of branches. With the base clause first, `ancestor(tom,D)` gives Tom's children first. With
  recursion first, it finds deeper descendants such as Sue first. Both orders find the same people.
