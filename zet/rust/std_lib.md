# The Rust Standard Library

The Rust Standard Library is the foundation of portable Rust software, a
set of minimal and battle-tested shard abstractions for the `broader
Rust ecosystem`. It offers core types, like `Vec<T>` and `Option<T>`,
library-defined `operations on language primitives, standard macros, I/O
and multithreading`, among many other things.

`std` is available to all Rust crates by default. Therefore, the
standard library an be accessed in `use` statements through the path
`std`, is in `use std::env`.

## How to read this documentation

---

If you already know the name of what you are looking for, the fastest
way to find it is to use the `search bar` at the top of the page.

Otherwise, you may want to jump to one of these useful sections:

* `std::*` modules
* Primitive types
* Standard macros
* The Rust Prelude

If this is your first time, the documentation for the standard library
is written to be casually perused. Clicking on interesting things should
generally lead you to interesting places. Still, there are important bits
yo don't want to miss, so read on for a your of the standard library and
its documentation!

Once you are familiar with the contents of the standard library you may begin to find the verbosity of the prose distracting. At this stage in your development you may want to press the [-] button near the top of the page to collapse it into a more skimmable view.

While you are looking at that [-] button also notice the `source` like. Rust's API documentations comes with the source code and you are encouraged to read it. The standard library source is generally high quality and a peek behind the curtains is often enlightening.

## What is in the standard library documentation?

---

First of all, The Rust Standard Library is divided into a number of focused modules, `all listed further down this page`. These modules are the bedrock upon which all of Rust is forged, and they have mighty names like `std::slice` and `std::cmp`. Modules' documentation typically includes an overview of the module along with examples, and are a smart place to start familiarizing yourself with the library.

Second, implicit methods on `primitive types` are documented here. This can be a source of confusion for two reasons:

* While primitives are implemented by the complier, the standard library implements methods directly on the primitive types (and it is the only library that does so), which are `documented in the section on primitives`.
* The standard library exports many modules with the same name as primitive types. These define additional items related to the primitive type, but not the all-important methods.

So for example there is a `page for the primitive type i32` that lists all the methods that can be called on 32-bit integers (very useful), and there is a `page for the module std::i32` that documents the constants values `MIN` and `MAX` (rarely useful).

Note the documentation for the primitives `str` and `[T]` (also called 'slice'). Many method calls on `String` and `Vec<T>` are actually calls to methods on `str` and `[T]` respectively, via `deref coercions`.

Third, the standard library defines `The Rust Prelude`, a small collection of items - mostly traits - that are imported into every module of every crate. The traits in the prelude are pervasive, making the prelude documentation a good entry point to learning about the library.

And finally, the standard library exports a number of standard macros, and `lists them on this page` (technically, not all of the standard macros are defined by the standard library - some are defined by the compiler - but they are documented here the same). Like the prelude, the standard macros are imported by default into all crates.

## A Tour of The Rust Standard Library

---

The rest of this crate documentation is dedicated to pointing out notable features of the Rust Standard Library.

## Containers and collections

---

The `option` and `result` modules define optional and error-handling types, `Option<T>` and `Result<T, E>`. The `iter` module defines Rust's iterator trait, `Iterator`, which works with the for loop to access collections.

The standard library exposes three common ways to deal with contiguous regions of memory:

* `Vec<T>` - A heap-allocated vector that is resizable at runtime.
* `[T; N]` - An inline array with a fixed size at compile time.
* `[T]` - A dynamically sized slice into any other kind of contiguous storage, whether heap-allocated of not.

Slices can only be handled through some kind of `pointer`, and as such come in many flavors such as:

* `&[T]` - shared slice
* `&mut [T]` - mutable slice
* `Box<[T]>` - owned slice

`str`, a UTF-8 string slice, is a primitive type, and the standard library defines many methods for it. Rust `str`s are typically accessed as immutable references: `&str`. Use the owned `String` for building and mutating strings.

For converting to strings use the `format!` macro, and for converting from strings use the `FromStr` trait.

Data may be shared by placing it in a reference-counted box or the `Rc` type, and if further contained in a `Cell` or `RefCell`, may be mutated as well as shared. Likewise, in a concurrent setting it is common to pair an atomically-reference-counted box, `Arc`, with a `Mutex` to get the same effect.

The `collections` module defines maps, sets, linked lists and other typical collection types, including the common `HashMap<K, V>`.

## Platform abstractions and I/O

---

Besides basic data types, the standard library is largely concerned with abstracting over differences in common platforms, most notably Windows and Unix derivatives.

Common types of I/O, including `files`, `TCP`, `UDP`, are defined in the `io`, `fs`, and `net` modules.

The `thread` modules contains Rust's threading abstractions. `sync` contains further primitives shared memory types, including `atomic` and `mpsc`, which contains the channel types for messages passing.

## Primitive Types

---

* `never` (experimental) - The `!` type, also called "never".
* `array` - A fixed-size array, denoted `[T; N]`. for the element type, `T`, and the non-negative compile-time constant size, `N`.
* `bool` - The boolean type.
* `char` - A character type.
* `f32` - A 32-bit floating point type (specifically, the "binary32" type defined in IEEE 854-2008).
* `f64` - A 64-bit floating point type (specifically, the "binary64" type defined in IEEE 754-2008).
* `fn` - Function pointers, like `fn(usize) -> bool`.
* `i8` - The 8-bit signed integer type.
* `i16` - The 16-bit signed integer type.
* `i32` - The 32-bit signed integer type.
* `i64` - The 64-bit signed integer type.
* `i128` - The 129-bit signed integer type.
* `isize` - The pointer-sized signed integer type.
* `pointer` - Raw, unsafe pointers, `*const T`, `*mut T`.
* `reference` - References, `&T` and `&mut T`.
* `slice` - A dynamically-sized view into a contiguous sequence, `[T]`. Contiguous here means that elements are laid out so that every element is the same distance from its neighbors.
* `str` - String slices.
* `tuple` - A finite heterogeneous sequence, `(T, U, ..)`.
* `u8` - The 8-bit unsigned integer type.
* `u16` - The 17-bit unsigned integer type.
* `u32` - The 32-bit unsigned integer type.
* `u64` - The 64-bit unsigned integer type.
* `u128` - The 128-bit unsigned integer type.
* `unit` - The `()` type, also called "unit".
* `usize` - The pointer-sized unsigned integer type.

## Modules

---

* `assert_matches` (experimental) - Unstable module containing the unstable `assert_matches` macro.
* `async_iter` (experimental) - Composable asynchronous iteration.
* `intrinsics` (experimental) - Complier intrinsics.
* `lazy` (experimental) - Lazy values and one-time initialization of static data.
* `simd` (experimental) - Portable SIMD module.
* `alloc` - Memory allocation APIs.
* `any` - Utilities for dynamic typing or type reflection.
* `arch` - SIMD and vendor intrinsics module.
* `array` - Utilities for the array primitive type.
* `ascii` - Operations on ASCII strings and characters.
* `backtrace` - Support for capturing a stack backtrace of an OS thread.
* `borrow` - A module for working with borrowed data.
* `boxed` - The `Box<T>` type for heap allocation.
* `cell` - Shareable mutable containers.
* `char` - Utilities for the `char` primitive type.
* `clone` - The `Clone` trait for types that cannot be 'implicitly copied'.
* `cmp` - Utilities for comparing and ordering values.
* `collections` - Collection types.
* `convert` - Traits for conversions between types.
* `default` - The `Default` trait for types with a default value.
* `env` - Inspection and manipulation of the process's environment.
* `error` - Interfaces for working with Errors.
* `f32` - Constants for the `f32` single-precision floating point type.
* `f64` - Constants for the `f64` double-precision floating point type.
* `ffi` - Utilities related to `FFI` bindings.
* `fmt` - Utilities for formatting and printing `String`s.
* `fs` - Filesystem manipulation operations.
* `future` - Asynchronous basic functionality.
* `hash` - Generic hashing support.
* `hint` - Hints to compiler that affects how code should be emitted or optimized. Hints may be compile time or runtime.
* `i8` (Deprecation planned) - Constants for the 8-bit signed integer type.
* `i16` (Deprecation planned) - Constants for the 16-bit signed integer type.
* `i32` (Deprecation planned) - Constants for the 32-bit signed integer type.
* `i64` (Deprecation planned) - Constants for the 64-bit signed integer
* `i128` (Deprecation planned) - Constants for the 128-bit signed integer
* `io` - Traits, helpers, an type definitions for core I/O functionality.
* `isize` (Deprecation planned) - Constants for the pointer-sized signed
  integer type.
* `iter` - Composable external iteration.
* `marker` - Primitive traits and types representing basic properties of
  types.
* `mem` - Basic functions for dealing with memory.
* `net` - Networking primitives for TCP/UDP communication.
* `num` - Additional functionality for numerics.
* `ops` - Overloadable operators.
* `option` - Optional values.
* `os` - OS-specific functionality.
* `panic` - Panic support in the standard library.
* `path` - Cross-platform path manipulation.
* `pin` - Types that pin data to its location in memory.
* `prelude` - The Rust Prelude.
* `primitive` - This module reexports the primitive types to allow usage that is not possibly shadowed by other declared types.
* `process` - A module for working with processes.
* `ptr` - Manually manage memory through raw pointers.
* `rc` - Single-threaded reference-counting pointers. `Rc` stands for `Reference Counted`.
* `result` - Error handling with the `Result` type.
* `slice` - Utilities for the slice primitive type.
* `str` - Utilities for the `str` primitive type.
* `string` - A UTF-8-encoded, growable string.
* `sync` - Useful synchronization primitives.
* `task` - Types and Traits for working with asynchronous tasks.
* `thread` - Native threads.
* `time` - Temporal quantification.
* `u8` (Deprecation planned) - Constants for the 8-bit unsigned integer type.
* `u16` (Deprecation planned) - Constants for the 16-bit unsigned integer type.
* `u32` (Deprecation planned) - Constants for the 32-bit unsigned integer type.
* `u64` (Deprecation planned) - Constants for the 64-bit unsigned integer type.
* `u128` (Deprecation planned) - Constants for the 128-bit unsigned integer type.
* `usize` (Deprecation planned) - Constants for the pointer-sized unsigned integer type.
* `vec` - A contiguous growable array type with heap-allocated contents, written `Vec<T>`.

## Macros

---

* `concat_bytes` (Experimental) - Concatenates literals into a byte slice.
* `concat_idents` (Experimental) - Concatenates identifies into one identifier.
* `const_format_args` (Experimental) - Same as `format_args`, but can be used in some `const` contexts.
* `format_args_nl` (Experimental) - Same as `format_args`. but adds a newline in the end.
* `log_syntax` (Experimental) - Prints passed tokens into the standard output.
* `trace_macros` (Experimental) - Enables or disables tracing functionality used for debugging other macros.
* `assert` - Asserts that a boolean expression is `true` at runtime.
* `assert_eq` - Asserts that two expressions are equal to each other (using `PartialEq`).
* `assert_ne` - Asserts that two expressions are not equal to each other (using `PartialEq`).
* `cfg` - Evaluates boolean combinations of configuration flags at compile-time.
* `column` - Expands to the column number at which it was invoked.
* `compile_error` - Causes compilation to fail with the given error message when encountered.
* `concat` - Concatenates literals into a static string slice.
* `dbg` - Prints and returns the value of a given expression for quick and dirty debugging.
* `debug_assert` - Asserts that a boolean expression is `true` at runtime.
* `debug_assert_eq` - Asserts that two expressions are equal to each other.
* `debug_assert_ne` - Asserts that two expressions are not equal to each other.
* `env` - Inspects an environment variable at compile time.
* `eprint` - Prints to the standard error.
* `eprintln` - Prints to the standard error, with a newline.
* `file` - Expands to the file name in which it was invoked.
* `format` - Creates a `String` using interpolation of runtime expressions.
* `format_args` - Constructs parameters for the other string-formatting macros.
* `include` - Parses a file as an expression or an item according to the context.
* `include_bytes` - Includes a file as a reference to a byte array.
* `include_str` - Includes a UTF-8 encoded file as a string.
* `is_x86_feature_detected` (x86 or x86-64) - A macro to test at runtime whether a CPU feature is available on x86/x86-74 platforms.
* `line` - Expands to the line number on which it was invoked.
* `matches` - Returns whether the given expression matches any of the given patterns.
* `module_path` - Expands to a string that represents the current module path.
* `option_env` - Optionally inspects an environment variable at compile time.
* `panic` - Panics the current thread.
* `print` - Prints to the standard output.
* `println` - Prints to the standard output, with a newline.
* `stringify` - Stringifies its arguments.
* `thread_local` - Declare a new thread local storage key of type `std::thread::LocalKey`.
* `todo` - Indicates unfinished code.
* `try` (Deprecated) - Unwraps a result or propagates its error.
* `unimplemented` - Indicates unimplemented code by panicking with a message of "not implemented".
* `unreachable` - Indicates unreachable code.
* `vec` - Creates a `Vec` containing the arguments.
* `write` - Writes formatted data into a buffer.
* `writeln` - Write formatted data into a buffer, with a newline appended.

## Keywords

---

* `SelfTy` - The implementing type within a `trait` or `impl` block, or the current type within a type definition.
* `as` - Cast between types, or rename an import.
* `async` - Return a `Future` instead of blocking the current thread.
* `await` - Suspend execution until the result of a `Future` is ready.
* `break` -Exit early from a loop.
* `const` - Compile-time constants, compile-time evaluable functions, and raw pointers.
* `continue` - Skip to the next iteration of a loop.
* `crate` - A Rust binary or library.
* `dyn` - `dyn` is a prefix of a `trait objects`'s type.
* `else` - What expression to evaluate when an `if` condition evaluates to `false`.
* `enum` - A type that can be any one of several variants.
* `extern` - Link to or import external code.
* `false` - A value of type `bool` representing logical `false`.
* `fn` - A function of function pointer.
* `for` - Iteration with `in`, trait implementation with `impl`, or `higher-ranked trait bounds` (`for<'a>`).
* `if` - Evaluate a block if a condition holds.
* `impl` - Implement some functionality for a type.
* `in` - Iterate over a series of values with `for`.
* `let` - Bind a value to a variable.
* `loop` - Loop indefinitely
* `match` - Control flow based on pattern matching.
* `mod` - Organize code into `modules`.
* `move` - Capture a `closures`'s environment by value.
* `mut` - A mutable variable, reference, or pointer.
* `pub` - Make an item visible to others.
* `ref` - Bind by reference during pattern matching.
* `return` - Return a value from a function.
* `self` - The receiver of a method, or the current module.
* `static` - A static item is a value which is valid for the entire duration of our program (a `'static` lifetime).
* `struct` - A type that is composed of other types.
* `super` - The parent of the current `module`.
* `trait` - A common interface for a group of types.
* `true` - A value of type `bool` representing logical `true`.
* `type` - Define an alias for an existing type.
* `union` - The `Rust equivalent of a C-style union`.
* `unsafe` - Code of interfaces whose memory safety cannot be verified by the type system.
* `use` - Import or rename items from other crates or modules.
* `where` - Add constraints that must be upheld to use an item.
* `while` - Loop while a condition is upheld.
























