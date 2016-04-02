# Rust: Memory Safety in Programming Languages

This document is a proposal for a student-led half semester course.
Loosely speaking the class would be titled _Rust: Memory Safety in Programming
Languages_.

## Course Vision

### Course Zen

The course essentially aims to guide students through the process of
discovering, navigating, and articulating the tension between writing programs
that are highly performant, easy to understand, and provably memory safe.

In this way the course will promote students discovering obstacles to memory
safety organically, as they try to write programs to perform specific tasks.
The course also aims to help students develop their ability to articulate these
sometimes technical problems in writing and in presentation.

### Learning Outcomes

Students will hopefully learn about the following things:
   * Memory use in systems languages
   * Memory safety and attempts to guarantee memory safety
   * How to use the Rust programming language
   * The relationship between memory safety and concurrency

### Course Skills

The course will promote the following skills:
   * Writing programs which directly handle memory
   * Writing programs with functional programming patterns
   * Writing in a language with powerful static analysis built-in
   * Articulating technical problems

## Course Format

The course will consist of weekly meeting and assignments. The assignments will
be designed to bring students into contact with interesting problems concerning
memory safety and/or the design of Rust. The meetings will serve as an avenue
to introduce the assignments and a venue for student presentations about the
problems they've encountered.

Through the course students might:
   * Do a lot of programming in Rust
   * Work on problems where memory safety is non-trivial
   * Articulate their problems and solutions through presentation
   * Review the code and APIs of others

## Course Outline

### Week 0/1: Warm-Up

The first week is intended to get people comfortable with some of the basics of
the Rust language. This includes the following:
   * Syntax of functions, structs, enums, traits, impls, and statements
   * Type inference
   * Generics
   * Closures, iterator interface / functional programming
   * Mutable and immutable variables
   * [simple] borrows and mutable borrows, but only on a surface level
   * Modules, crates?
   * Existence of macros

For the most part I envision this week's assignment to be very lab-like, in
which students write a lot of small bits of code which cover a broad base of
language features, rather than wrestling with some really tough problems.

Perhaps as a closing problem they could write some sort of generic function
composition function, although this might be a bit too far out there.

This also may end up being a week and a half, or two weeks.

### Week 2: A Simple Linked List

This week is about interacting with the borrow checker for the first time. In
particular, how do we write data structures using the ownership rules?
Hopefully over the course of the week students will run afoul of the borrow
checker a bunch, and gain some more intuition for what safe borrows are.

The pre-assignment lecture will begin with the ownership/borrow rules. It will
then cover some of the standard library's API around ownership. In particular
it will cover `mem::swap`, `Option::take`, and `Box`.

We'll then discuss the API for the linked list that will be implemented, paying
particular attention the ownership properties of the API and when `Option` is
used.

The linked list will need to correctly implement the following trait:

```rust
trait StackQueue<T> {
   fn new() -> Self;
   fn push_back(&mut self, item: T);
   fn push_front(&mut self, item: T);
   fn pop_front(&mut self) -> Option<T>;
   fn peek_front(&self) -> Option<&T>;
   fn size(&self) -> usize;
}
```

Some presentation prompts:

> `pop_front` can be implemented in multiple ways. One way uses `size` and
> `Option::unwrap`. Another does not. Articulate the difference in the
> implementations.


> In this assignment we implemented a singly linked list using `Box`. What
> about doubly linked lists?


> In Haskell, the following is valid:
>
> ```haskell
> data List a = Cons a (List a) | None
> ```
>
> What about this rust `enum`:
> ```rust
> enum List<T> { Cons(T,List<T>), None }
> ```

### Week 3: Trees

This week is about taking that knowledge of the ownership system and trying to
solve a problem which is slightly more complicated from a memory perspective.
Specifically tree rotations involve some pointer swaps which it is difficult
for the compiler to verify. Over the course of the week students will learn how
to solve this problem using safe abstractions provided by the standard library.

### Week 4: Macros, Nom, Parsing

Now we shift gears a bit. This week students will use a Rust library (nom) to
write a parser for simple arithmetic expressions. This library is macro-based,
so they should gain a lot of experience with the macro system in the process.
However, while it may not be obvious, the problem they're being asked to solve
actually has some memory safety components to it. There's some flexibility here
as to whether to alert them to that fact so they more quickly recognize it or
to let them discover it for themselves.

The assignment will be to take the example parser for nom (which 'parses' an
expression into its value) and turn it into an actually parser, which produces
an AST for the expression.

### Week 5: Lifetimes

By this point they should have a reasonable amount of experience dealing with
ownership, both when it is explicit (building data structures) and when it
sneaks up on them (in parsing). Now its time to take this knowledge to the next
level by exploring lifetimes. Roughly speaking this assignment will involve
using data structures which hold references into other data structures, perhaps
in pursuit of avoiding extra copies in performance-sensitive situations.

### Week 6: Unsafe

Now we venture even deeper into Rust, this week exploring `unsafe`, the feature
which allows certain compiler checks to be elided on code blocks. In the first
part of the assignment students will write a data structure which requires the
use of `unsafe`, such as a growable array. The students will then trade code
and attempt to break the data structure in various ways: first by changing code
in unsafe blocks, then by changing safe blocks, and then by not changing the
data structure code at all (the data structure should not be breakable in this
way).

In some sense the end goal of this week is to get some sense of what `unsafe`
means in Rust, and how it interacts with the rest of the language (hint:
`unsafe` taints things, but ultimately privacy saves you).

### Week 7: Graphs

In our final assignment we tackle a more open question: implementing a graph.
Doing so involves a non-trivial amount of coding, but also some very
non-obvious design decisions around the API. Values such as ease-of-use,
performance, and safety interact in interesting ways, and I'm not sure it's
possible to get all 3. An emphasis will be placed on writing a graph with an
API which allows for easy, safe, and efficient implementations of common graph
algorithms.

Students may also do an activity involving taking a look at existing graph
libraries (such as `petgraph`) and critiquing them - seeing what they value.

### Miscellaneous Ideas

I have a few loose ends to chase down, maybe they become assignments, maybe
not.
   * Drop
   * Non-lexical borrow
   * Final Project?
   * Rc, RefCell, Cell, etc.

I'll also be spending the summer working with Rust, and will probably come up
with new ideas along the way.
