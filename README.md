# NARLY: Not A Real Language Yet

*This is a sketch for a language I'd like to create.*

NARLY is a gradually typed language environment that aims to be superior to
both dynamically typed languages like Ruby and statically typed languages like
C++ for a broad array of use cases.

## NARLY is a better Ruby

* Write most of your code in a dynamically typed garbage-collected Ruby-like language, but make hot code paths run at native speed by adding type annotations and/or using explicit memory management.

## NARLY is a better C++

* Write your library in a type-checked, performant C++-like language, while allowing the users of your library to use it either from either a statically-typed environment (for performance) or a dynamically-typed environment (for faster prototyping). To achieve this, in the statically-typed case we generate specializations at compile time, while in the dynamically-typed case we emit slower dependently-typed machine code that is run-time polymorphic over all possible type parameters.

* Support for object lifetimes, allowing Rust-like memory management.

* Made for generic programming from the ground up:

    * Type classes ("concepts" in C++).

    * The compile-time environment is not just turing-complete, but identical to the run-time environment -- we can instantiate our types and run our functions at compile time! This allows for powerful code generation logic, like parser generators.

* Well-specified semantics allow the same code to be compiled for different CPUs and platforms. No more "What width is my int?".

* An error-signaling/exception-handling system that people actually want to use: No performance penalty, and no weird edge cases in destructors.

## NARLY is a better Haskell

* NARLY allows us to write provably side-effect-free functions in a functional programming style.

    * Side-effect-free functions may perform mutation on local state. This is useful for performance-sensitive code, and allows us to mix imperative-style (`for`) and functional-style (`map`) code in our side-effect-free functions.

    * NARLY has a notion of a function being side-effect-free up to some operation. For example, imagine a function that uses a Rust-like memory-allocator to create objects on the heap and free them before it returns. The `malloc` and `free` syscalls are side effects, but we can still say that this function is side-effect-free up to allocation. Similarly, we might want a function to be side-effect-free up to temporary file creation and deletion, or up to database reads.

* Side effects in NARLY are modeled with monad-like structures behind the scenes, but are sugared like in C++ or Ruby.

    * Rather than doing all I/O through a single `IO` type, we differentiate between different I/O objects, allowing us to say that a function reads from the clock and sends data through a socket, but doesn't have access to the filesystem.

    * We treat mutation of objects (global or passed by reference) as sugar for argument-passing followed by reassignment of a return value.

## Bonus

* Sandboxed usage: Compile and run untrusted code that has only access to types and I/O objects you provide, and provably cannot have any other side effects, while still running at native speed.

    * Wrap your compilation in a sandbox. If it compiles locally, it's guaranteed to compile on CI, and produce a byte-for-byte identical binary.

* One type environment, multiple languages. We supply a C++-like StaticNARLY language with syntax optimized for mostly-statically-typed usage, and a Ruby-like DynamicNARLY language for mostly-dynamically-typed usage. More NARLY languages can be added and co-exist in the same code-base. Think LLVM, but higher-level. Think .NET or JVM, but without the pesky runtime and with normal binaries. In StaticNARLY, a "Hello World" binary still comes out as small as its C equivalent.

* The type-system is platform-agnostic, and primitive types are not hard-coded. Want to use NARLY to generate JavaScript, without going through asm.js? Swap your x86 primitives (`int64`, `char`, `pointer`) for JS primitives (`number`, `string`, `object`), define translation rules for the JS primitives, and you're in business!

    * Generate FFI code for non-C languages. Say we want to use a third-party C++ library, and the library uses C++ features like templating, so we don't want to funnel everything through a C FFI. To do this, we might write some custom adapter types in NARLY that upon instantiation emit C++ code to call the third-party library.

* The type system is implemented using a general-purpose proof checker, which allows us to express and prove other properties besides typing if desired, like commutativity on `+`, transitivity on `<`, or bounds-safety on `[]`.

    * Write specs in a formal language, then write implementations that provably conform to the spec. No more RFCs describing things in English!

## "What is this about? Why document a language that doesn't exist?"

I've been thinking about making a better programming language for a while.
There's too many aspects to the problem to hold in my head at once, so this
README serves to put my current thoughts in order. Also, when people ask me
what I'm trying to build, I can now point them at this.

Making a language is a pretty hard problem and it probably won't be my next
project, but it might well be the one after. In the meantime, I'm reading up
on various languages and language theory to prepare.

I'm also trying to figure out some questions:

* I *think* that everything I wrote down is implementable with theory we have today. Is this true, or do we lack theory for some things? Is some of this even impossible to do for fundamental reasons?

* Which parts are surprisingly easy, and which are surprisingly hard?

* If I break down the problem, which constituent parts should I try to solve first? Can I get something practically useful out of solving some subset of the problem in isolation?

If you have any ideas, pointers, insights, or just want to warn me that
something I'm trying to do here is a Bad Idea, I'd love to hear from you!
Tweet me at [@jo_liss](https://twitter.com/jo_liss) or email me at
joliss42@gmail.com.
