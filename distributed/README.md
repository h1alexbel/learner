> "In the messy reality of distributed systems, you have to be very careful with your assumptions."
<br>
@Martin Kleppmann

### Distributed Locking

If you need locks only on a best-effort basis
(as an efficiency optimization, not for correctness),
I would recommend sticking 
with the straightforward single-node locking algorithm for Redis
(conditional set-if-not-exists to obtain a lock, atomic delete-if-value-matches to release a lock),
and documenting very clearly in your code that the locks are only approximate and may occasionally fail.
Don’t bother with setting up a cluster of five Redis nodes.

On the other hand, if you need locks for correctness,
please don’t use `Redlock`.
Instead, please use a proper consensus system such as `ZooKeeper`,
probably via one of the Curator recipes that implements a lock.
(At the very least, use a database with reasonable transactional guarantees.)
And please enforce use of fencing tokens on all resource accesses under the lock.
