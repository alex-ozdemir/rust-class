# Memory Safety in Programming Languages

This document is a proposal for a student-led half semester course.
Loosely speaking the class would be titled _Memory Safety in Programming
Languages_. The class will use the Rust programming language heavily.

## Course Vision

### Inspiration

A constant struggle in systems programming is writing programs that are highly
performant, memory safe, and easy to write/understand. The Rust programming
language seeks to solve this problem by using an elaborate type system to
statically verify memory safety. This solution is far from perfect, but is
ingenious nonetheless, and in exploring the Rust type system and the impact of
it on programming one can learn a lot about the problem of memory safety.

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
   * Syntax of functions, structs, enums, and statements
   * Type inference
   * Closures, iterator interface / functional programming
   * Mutable and immutable variables
   * Modules, crates?

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

The linked list will need to correctly implement the following trait such that
each function runs in worst case constant time:

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

#### Presentation Prompts

A reasonable one

> `pop_front` can be implemented in multiple ways. One way uses `size` and
> `Option::unwrap`. Another does not. Articulate the difference in the
> implementations.

An advanced one

> In this assignment we implemented a singly linked list using `Box`. What
> about doubly linked lists?

An easier one (probably too easy)

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

I'm somewhat unsure about this week. I wonder how much it would really help
after the linked-list week. The student probably won't use any new standard
library features - they would just be using them to solve very slightly more
complex memory problems.

It might be good to tear this week out and put in a week on some of the other
memory APIs (RC, Cell, RefCell, etc). I need to come up with an assignment for
this though. Also, the RC/Cell week would probably go later on, and the macros
assignment would get bumped up a week.

### Week 4: Macros, Nom, Parsing

Now we shift gears a bit. This week students will use a Rust library (nom) to
write a parser for simple arithmetic expressions. This library is macro-based,
so they should gain a lot of experience with the macro system in the process.
However, while it may not be obvious, the problem they're being asked to solve
actually has some memory safety components to it. There's some flexibility here
as to whether to alert them to that fact so they more quickly recognize it or
to let them discover it for themselves.

The assignment will be to take [the example
parser](http://rust.unhandledexpression.com/nom/#example) for nom (which
'parses' an expression into its value) and turn it into an actual parser, which
produces an AST for the expression. The design of the parser is pretty clever,
but also brings up some memory questions.

### Week 5: Lifetimes

By this point they should have a reasonable amount of experience dealing with
ownership, both when it is explicit (building data structures) and when it
sneaks up on them (in parsing). Now its time to take this knowledge to the next
level by exploring lifetimes. Roughly speaking this assignment will involve
using data structures which hold references into other data structures, perhaps
in pursuit of avoiding extra copies in performance-sensitive situations.

This can actually be done as an expansion upon the last assignment, by trying
to make a parser or AST processor that minimizes the number of copies that
occur. The only problem with this approach is that it is a bit wild-west-y.
The nature of lifetimes may shine more clearly through a more carefully
constructed assignment.

### Placeholder: Rc, Cell, RefCell

### Week 6: Unsafe

Now we venture even deeper into Rust, this week exploring `unsafe`, the feature
which allows certain compiler checks to be elided on code blocks. In the first
part of the assignment students will write a data structure which requires the
use of `unsafe`, such as a growable array. The students will then trade code
and attempt to break the data structure in various ways:

   1. by changing code in unsafe blocks
   1. by changing safe blocks
   1. by not changing the data structure code at all (the data structure should
      not be breakable in this way).

In some sense the end goal of this week is to get some sense of what `unsafe`
means in Rust, and how it interacts with the rest of the language (hint:
`unsafe` taints things, but ultimately privacy saves you).

A relevant resource for designing this week's assignment will be Alexis
Beingessner's book, the [Rustonomicon](https://doc.rust-lang.org/nomicon/),
which discusses `unsafe` in great deal and I believe also walks through an
implementation of `Vec`.

### Week 7: Graphs

In our final assignment we tackle a more open question: implementing a graph.
Doing so involves a non-trivial amount of coding, but also some very
non-obvious design decisions around the API. Values such as ease-of-use,
performance, and safety interact in interesting ways, and I'm not sure it's
possible to get all 3. An emphasis will be placed on writing a graph with an
API which allows for easy, safe, and efficient implementations of graph
algorithms.

Students may also do an activity involving taking a look at existing graph
libraries (such as
[`petgraph`](https://github.com/bluss/petulant-avenger-graphlibrary)) and
critiquing them - seeing what they value.

### Miscellaneous Ideas

I have a few loose ends to chase down, maybe they become assignments, maybe
not.
   * Drop
   * Non-lexical borrow
   * Final Project?
   * Rc, RefCell, Cell, etc.
   * Variance

#### Rc, RefCell, Cell

These are all the tools that allow programmers to break out of the strict
ownership rules. We should definitely take a look at them, but I actually
haven't use them much so I'm not sure how too yet.

I'll also be spending the summer working with Rust, and will probably come up
with new ideas along the way.

### Further Resources

#### Various Courses:

   * [U Iowa](http://homepage.cs.uiowa.edu/~achampion/teaching/plc/lectures.shtml)
   * [UPenn](http://cis198-2016s.github.io/schedule/)
   * [U Virginia OS](http://rust-class.org/index.html) - also a reflective
       [sub-page](http://rust-class.org/0/pages/using-rust-for-an-undergraduate-os-course.html)

#### Various Blogs:

   * [Huon Wilson](http://huonw.github.io/)
   * [Niko Matsakis](http://smallcultfollowing.com/babysteps/blog/archives/)

#### Various Posts
   * [Error Handling](http://blog.burntsushi.net/rust-error-handling/), by
       Andrew Gallant
   * [Rust via Core
       Values](http://designisrefactoring.com/2016/04/01/rust-via-its-core-values/)
