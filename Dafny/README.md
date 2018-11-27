# Dafny Learning Notes 

<!-- markdown-toc start - Don't edit this section. Run M-x markdown-toc-generate-toc again -->
**Table of Contents**

- [Dafny Learning Notes](#dafny-learning-notes)
- [Basic Types](#basic-types)
- [Framing](#framing)
    - [``(`)`` back-quote / back-tick operator](#-back-quote--back-tick-operator)
    - [`reads` clause](#reads-clause)
    - [`modifies` clause](#modifies-clause)
- [References](#references)

<!-- markdown-toc end -->


# Basic Types 
There are TWO distinctive type groups in Dafny: (1) the Value types and (2) the Reference types. 

Value types include normal values like integers, booleans PLUS _sets, sequences, and multisets_  
Reference types include _Arrays and Objects_  

# Framing

## ``(`)`` back-quote / back-tick operator 
this operator is used to specify _a field_ of a class object! 
e.g. 
```
  modifies Student`score;
```

back-quote operator can also be used without any preceding expression, like such: 
```
  modifies `age; 
```
this clause means modifying CURRENT object!  
(think about the constructor or setter pattern in Java) 


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
Another implicit but important rule: *You can modify whatever you created* (newly allocated objects can always be modified).  

If no `modifies` clause is given, the method can only modify the objects it created.  

loops can also be given `modifies` clause! (ToDo)  

Multiple `modifies` clauses are treated using _set union_.  

# References
[Dafny language ref](https://github.com/Microsoft/dafny/blob/master/Docs/DafnyRef/out/DafnyRef.pdf)
