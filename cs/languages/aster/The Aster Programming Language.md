# The Aster Programming Language

The **Aster** programming language is a *general-purposed*, *static*, and *strong-typed* language. I draft this language in the light of existing programming languages. **Aster** has a similar grammar with modern **C++**, a similar type system with **Haskell**, and a similar object management strategy like **Rust**. **Garbage Collection (GC)** widely used in many languages like **Java** and **Python** is welcomed to be standardized, but I will *not* enforce it.

Basically, I'm fascinated by **C++**'s flexible paradigms and powerful meta-programming with templates, but sometimes annoyed by the over-complicated language features, redundant grammars, and strange corner cases due to backward compatibility. That's why I want to design a new language, half as good as **C++** in terms of runtime performance and low-level correspondence, but much better with respect to: 1. easy to write safe codes; 2. easy to predict program behaviors (no *Undefined Behavior (UB)* of course); 3. more natural transitions between different paradigms.

In this article (probably will evolve into a tiny guide book after several iteration), I will try to use a casual tone and share all my thoughts and choices made during the consideration of language features. Occasionally, I will refer to some features appeared or proposed in other languages, like the ones I mentioned before.

Let's begin now. Have fun!

[TOC]



## Basics

We will have to be familiar with the following terms and concepts before moving foward, since they will be frequently used. Important words are *italicized*.

- A *source file* is a file that contains **Aster** *source code* to be *compiled* into a file of a *target language*, e.g. **C++**.
- A *module* is a collection of source files managed by a *header file* that contains *declarations* of *identifiers* as well as specifications of their visibility to other modules.
- **Aster** preserves ceratin permutations of characters, called *keywords*, to structure the source code. Some other patterns of character sequences are recognized as *literals* that represent *objects* of *integers*, *strings*, etc.
- All other words are *identifiers* that can be bound to **Aster** *entities*, including *objects*, *types*, *classes*, *templates*, *namespaces*, *macros*, *labels*, and *argument packs*. 
- The action of introducing *identifiers* is known as a *declaration*. The process of binding them to *entities* is called a *definition*. An identifier can only be bound to one entity within the same *scope level*, which rule is called the *One Definition Rule (ODR)*.
- Objects that are bound to identifiers are called *variables*. Though 
- A *scope* is either a *file scope*, restricted to a source file, or *block scope*, usually confined within a pair of braces, `{}`. Identifiers defined in a scope could be "redefined" in another scope, and cannot be referred to at outside of the scope where their definitions lie.
- Objects are *evaluable* entities that can be resolved to bound *values*. Some of them are known as *callables*, which can run *subroutines* if one or more *arguments* are provided.
- There is a special subroutine called *main function* that is by default configured as the entry of every program.
- A *subroutine* consists of commands, called *statements*, that are executed in sequence after compilation to executable files. Each statement involves *operations* that perform calculations or modifications on objects. The operations together with the operands are called *expressions*. An expression is also evaluable, just like an object.
- All *objects* and *expressions* are associated with a *storage type*, which reveals the underlying structure of the values they are evaluated to. A *type*, on the other hand, is an *alias* of a storage type that can concretize the same value with different meanings. *Member functions* are callables that are connected with bound *types*.
- A *type class* or simply *class* claims properties of an unknown group of *types*. A type *satisfies* a class if all claimed properties are met, and we also call it an *instance* of the class.

Besides, it should be helpful if I introduce the grammar to define a variable in **Aster**:

```cpp
auto i = 10;			// i : int
auto s = "abc";			// s : [const char]
auto arr = [ 1, 2, 3 ];	// arr : [int]
```

That's it. If you are familiar with **C++**, you can treat it as the auto type deduction (with minor differences we don't care so far). `i` is an identifier bound to an integer object whose value is `10`; `s` is an identifier bound to an array of constant characters who make up of a string `"abc"`; `arr` is an identifier bound to an array of integers whose values are `1`, `2`, and `3`.

Finally, let's get to know the abbreviations of some terms, which will be frequently used in illustration of grammar:

| **Abbreviation** | **Meaning** | **Abbreviation** | **Meaning** | **Abbreviation** | **Meaning** |
| ---------------- | ----------- | ---------------- | ----------- | ---------------- | ----------- |
| `id`             | identifier  | `expr`           | expression  | `stmt`           | statement   |
| `qual`           | qualifier   | `spec`           | specifier   | `attr`           | attribute   |
|                  |             |                  |             |                  |             |



## A Crash Course of Aster

### Statements

I wound like to start with the *statements* in **Aster**. It's rather similar to **C++**:

- Labeled Statements: A label structure followed by a colon.
  - `<id>: <stmt>`: A target for jump statements.
  - `case <pattern>: <stmt>`: A case in the match statement.
- Expression Statements: The trivial statements made up of an expression.
  - `?<attr> <expr>;`
- Compound Statements: A collection of statements put inside a block.
  - `{ ?<stmt>... }`
- Branch Statements: 
  - `if (<expr>) <stmt>`: 
  - `if (<expr>) <stmt> else <stmt>`:
  - `switch (<expr>) <stmt>`:
  - `match (<expr>) <stmt>`:
- Loop Statements:
  - `while (<expr>) <stmt>`:
  - `loop <stmt>`:
  - `loop <stmt> when (<expr>)`:
  - `for (<pattern> in <expr>) <stmt>`:
- Jump Statements:
  - `goto <id>`:
  - `break <id>`:
  - `continue <id>`:
  - `return ?<expr>`:
  - `return ?<expr> for <id>`: Return a value for a labeled `do` expression.
- Declaration Statements:
  - `<decl>`

