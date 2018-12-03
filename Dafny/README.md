# Dafny Learning Notes 

<!-- markdown-toc start - Don't edit this section. Run M-x markdown-toc-generate-toc again -->
**Table of Contents**

- [Dafny Learning Notes](#dafny-learning-notes)
- [Basic Types](#basic-types)
- [`predicate` and `function`](#predicate-and-function)
- [Framing](#framing)
    - [``(`)`` back-quote / back-tick operator](#-back-quote--back-tick-operator)
    - [`reads` clause](#reads-clause)
    - [`modifies` clause](#modifies-clause)
    - [`!= null` warning](#-null-warning)
- [Troubleshooting Guide](#troubleshooting-guide)
    - [Tips for better specifications](#tips-for-better-specifications)
    - [Common Error Messages and how to deal with them](#common-error-messages-and-how-to-deal-with-them)
- [References](#references)

<!-- markdown-toc end -->



# Basic Types 
There are TWO distinctive type groups in Dafny: (1) the Value types and (2) the Reference types. 

1. Value types include normal values like integers, booleans PLUS _tuples, sets, sequences, and multisets_  
2. Reference types include _Arrays and Objects_  

# `predicate` and `function`
`predicate` IS `function`; the only difference is that `predicate` only returns a single `bool` as its return type.  
The practical reason why we need `predicate` rather than using more general `function`?  
Ans: you can omitting the return type and a few keystrokes :)  


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

## `!= null` warning
First of all, what is `null`?  
`null` is a literal residing in every reference type!  

> The special value null is part of every reference type. (Section 5.1 Reference Type - Dafny Lang. Ref.)

It is a VALUE and therefore can be used as one.  

If you use newer version (since Dec 2017 verson 2.0.1) of Dafny (including the web instance), chances are you will come across this warning while copy-pasting some old Dafny code: 

> the type of the other operand is a non-null type, so this comparison with 'null' will always return 'true' (to make it possible for variable 'a' to have the value 'null', declare its type to be 'array?<int>')	

This is the implementation of first footnote in Dafny official language reference, "non-nullable types" extension. This extension further seperates reference types into 2 groups. 

In short, if you have a type `C` in Dafny, you have 2 versions of this type:  
	1. C which is non-nullable (you can NEVER point a null to objects of this type)
	2. C? potentially nullable type
	
Notice `C?` is the _name_ of the nullable type variant! (`?` is NOT an operator!)  

So what does the warning really say?  
In previous Dafny, it is the obligation of the programmer to make sure certain references are not null to begin/end with methods working on these references, resulting `!= null` comparison all over the place.  
In the new Dafny, this "sanity check of non-null" is lifted to the tool itself, so you dont have to do `!= null` check anymore in normal usages!  

Now, if you DO want to accomodate potentially null pointing cases, make sure you use the `C?` type!  



# Troubleshooting Guide 
This section is contributed by [Simon Robillard](http://cse.chalmers.se/~simrob/), a huge thank you for his work and sharing :) 
Original guide can be found [here](http://www.cse.chalmers.se/edu/year/2017/course/TDA567_Testing_debugging_and_verification/dafny_troubleshooting.pdf)

## Tips for better specifications 
Remeber, writing specifications is just like writing the code. Decent coding practices can get you a long way down the road while bad coding habbits can occationally bite you in the butt. Now, the whole point of writing specifications and verification is to prevent these hidden bugs hence the verification process is designed to report early with a much strick checking. Therefore, a wrongly specified spec SHOULD not get to you walk at all, let along walking the through the valley of the shadown of bugs! 

1. KISS (keep it small and simple) 
   + Keeping things simple is useful when coding, but even more important for specification. Your specification should be complete, but if it is complex and unreadable it is not likely to be useful (or correct).
   + Keep the `modifies` minimal! In the modifies clause, include only what is actually modified and nothing else. Avoid modifies this as it means that any field of this object can be modified. Instead detail exactly the fields that are modified, and only those.

2. USe *Data types* to your aid 
   + Use the built-in (value) types such as: *`sets, multi-sets, sequences, maps`*. Don't only stick to `arrays`
   + Notice these built-in types and their associated properties can help you in saving some specs! (e.g. uniqueness, order) 
   + Choosing the right data type not only makes the specification much easier to read, but data types are in themselves a form of specification!
   + Algebaric data types (Tuples and Enumerations) can help with the code and spec :) 

3. Boolean values and boolean operators are first-class citizen in Verification
   + Boolean values are, well, Boolean values. So you can use them directly (i.e. avoid writing `b == true` or `b == false`, just write `b` or `!b`
   + Dont forget `<==>` (reads "If and ONLY IF") operator (when you start to write a lot of `==>`s) 
   + If the method must satisfy different post-conditions based on the input value, use an implication to describe those conditional behaviors:
   ```
	   ensures P ==> Q1
	   ensures !P ==> Q2
   ```
   The condition `P` should be based on values before the execution (and therefore use `old`), otherwise these assertions might not mean what you think they do.


## Common Error Messages and how to deal with them
+ `index out of range`

+ `assertion violation`

+ `call may violate context's modifies clause`

+ `array may be null` ONLY in old Dafny 1.9x, see the [`!= null`](#-null-warning)

# References
[Dafny language ref](https://github.com/Microsoft/dafny/blob/master/Docs/DafnyRef/out/DafnyRef.pdf)
