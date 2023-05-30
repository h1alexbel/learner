## Simplicity
We need to keep the code as simple as possible,
so it’s easier to understand and modify.
Developers spend far more time reading code than writing it, so that's what we should optimize for.

### Refactoring vs. Redesign
Refactoring is a "microtechnique" that is driven by finding small-scale improvements.
Our experience is that, applied rigorously and consistently,
its many small steps can lead to significant structural improvements.
Refactoring is not the same activity as redesign,
where the programmers take a conscious decision to change a large-scale structure.
That said, having taken a redesign decision,
a team can use refactoring techniques to get to the new design incrementally and safely.

### Failing Unit Tests
The unit tests help us maintain the quality of the code and should pass soon after they’ve been written.
Failing unit tests should never be committed to the source repository.

### e2e for Process
We prefer to have the end-to-end tests exercise 
both the system and the process by which it’s built and deployed.
An automated build, usually triggered by someone checking code into the source repository,
will: check out the latest version; compile and unit-test the code; integrate and package the system;
perform a production-like deployment into a realistic environment;
and, finally, exercise the system through its external access points.
So the end-to-end build cycle is an ideal candidate for automation.

### C&C
In software engineering terms,
that means that the code must be loosely **coupled** and highly **cohesive**, 
in other words, well-designed.

### Object powered
This lets us change the behavior of the system by changing the composition of
its objects—adding and removing instances,
plugging different combinations together—rather than writing procedural code.
It’s easier to change the system’s behavior 
because we can focus on what we want it to do, not how.