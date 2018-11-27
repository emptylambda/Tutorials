# Dafny Learning Notes 

## `reads` clause 
The intended usage of `reads` in Dafny is for pure functions. 
*Methods are allowed to read everything from the memory without explicit `reads`.*
While these pure functions cannot cause any side effects by definition, sometimes user must provide certain _invariants_ in order to express framing context (for example, a function never reads into the modified memory location). 

If a function is to be given reading access to all memory location, you can specify using `reads *` (wildcard). 
Notice this will result in Dafny "forgetting" everything about the framing context in next step, making certain style of function impossible (i.e. recursive function calls) 

Multiple `reads` clauses are treated using _set union_. 

## `modifies` clause 
While `reads` is designed for the pure functions, `modifies` is designed for the side-effectful methods.
Again, *Methods are allowed to read everything from the memory without explicit `reads`*



Multiple `modifies` clauses are treated using _set union_. 

# References
[Dafny language ref]()
