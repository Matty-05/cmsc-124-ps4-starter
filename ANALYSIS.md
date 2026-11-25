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

- What does a `Redo` line resume that a returned frame cannot?

  A returned frame means a popped frame, so that means that the stored data within that frame is already gone. A 'Redo' line goes back to a save point from before choosing the clause it is already on and resets bindings for the next clause. This makes it so that it is able to exhaust all the possible clauses and eventually exhaust all choices.

- Why does swapping the two clauses of `reach/2` change whether the search
  ever answers, while swapping the two clauses of `ancestor/2` only changes
  the order of the answers?
