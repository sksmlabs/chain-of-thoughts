# Rust Notes

- cargo
- serde
- toml
- tokio
- macros
- attribute
- trait
- struct
- mod
- fn
- use
- impl
- let
- mut
- const
- parse

https://doc.rust-lang.org/book/ch03-02-data-types.html

---
#### Intro
- `rust` is an ahead-of-time compiled language, meaning you can compile a program and give the executable to someone else, and they can run it even without having Rust installed
- Rust is an expression based language.

#### Cargo
- `cargo` is Rust’s build system and package manager.
- `TOML` (Tom’s Obvious, Minimal Language) format, which is Cargo’s configuration format.
- [package] [dependencies]
- `cargo build` builds and creates an executable file in `target/debug/`
- `cargo run` to compile the code and then run the executable file
- `cargo checks` quickly checks if code compiles but skips producing executable
- `cargo build --realease` for building for release

#### Rust Variables and Constants
- `variables` by default are immutable, called using `let`
- `mut` in front of variable name, allows its mutability.
- `const` are always immutable, `mut` is not allowed with constants. 
- `const` name convention is UPPERCASE_WITH_UNDERSCORES
- `const` may be set only to a constant expression, not the result of a value
- `const` type of the value must be annotated
- `shadowing` in owner scope
- `let` and `shadowing` together allows using same variable name.
- `let` and `shadowing` allows changing type of the value
- unused variables should be named starting _ (underscore).

#### Rust Data Types
- `rust` is statically typed language, which means it much know types of all variables at compile time.
- Compiler usually inferes what type we want to use
- Mandatory to provide type annotation if converting type like using parse
- `Scaler types:` integers, floats, booleans, characters
- integers: i8,u8,i16,u16,i32,u32,i64,u64,i128,u128,isize,usize
    - i8 stores numbers which equals −128 to 127
    - u8 stores numbers which equals 0 to 255
    - isize depends on size of computer architecture
    - interger overflow
        - panics in development
        - wraps to start in release
        - `wrapping_*`
        - `checked_*` returns None
        - `overflowing_*` returns value and boolean
        - `saturating_*`
- number literals: Decimal, Hex, Octal, Binary, Byte
- floating-point: f32, f64 are numbers with decimal points.
    - The default type is f64
    - All floating-point types are signed.
- numeric operations:
- boolean:
    - Booleans are one byte in size
    - The Boolean type in Rust is specified using `bool`
- characters:
    - char literals are specified with single quotes as opposed to string literals which use double quotes.
- `Compound Types`: group multiple values into one type; types: typles, arrays
- tuples:
    - fixed length: once declared, they cannot grow or shrink in size.
    - To get the individual values out of a tuple, we can use pattern matching to destructure a tuple value
    - The tuple without any values has a special name, unit. This value and its corresponding type are both written () and represent an empty value or an empty return type
- arrays: 
    - every element of an array must have the same type.
    - arrays in Rust have a fixed length.
    - Arrays are useful when you want your data allocated on the stack instead of heap.
    -  An array isn’t as flexible as the vector type, though. A vector is a similar collection type provided by the standard library that is allowed to grow or shrink in size. 

#### Rust Functions
- `fn` keyword allows to declare new functions
- `snake case` to declare functions and variables
- curly brackets tell the compiler where function begines and ends. 
- Rust doesn't care where the function is defined; only that they are defined somewhere in a scope for caller. 
- In a function signature; you must declare type of each parameter.
- `statements` are instructions that perform some action and do not return a value.
    - functions definition are statement, but calling function is not a statement.
- `expressions` evaluate to a resultant value.
    - expressions can be part of statement.
    - calling function or macro is an expression
    - A new scope created with curly brackets is an expression
    - Expressions do not include ending semicolons. If you add a semicolon to the end of an expression, you turn it into a statement, and it will then not return a value.
- `return` value
    - must declare their type after an arrow (->)
    - the return value is synonymous with the value of the final expression in the block of the body of a function
    - return early from a function by using the `return` keyword and specifying a value

#### Rust Control Logic
- `if`
    - condition in `if` expression should always be bool
    - Blocks of code associated with the conditions in `if` expressions are sometimes called arms
    - `else` `elseif
    - Because `if` is an expression, we can use it on the right side of a let statement to assign the outcome to a variable`
- `loops`: loop, for, while
    - loops are expressions
    - place the `break` keyword within the loop to tell the program when to stop executing the loop
    - `continue` in a loop tells the program to skip over any remaining code in this iteration of the loop and go to the next iteration.
    - add the value you want returned after the break expression you use to stop the loop
    -  Loop labels must begin with a single quote
    -  `while` eliminates a lot of nesting that would be necessary if you used loop, if, else, and break, and it’s clearer
    -  when loop through array use 'for' and not 'while' because while index < number is slow because the compiler adds runtime code to perform the conditional check of whether the index is within the bounds of the array on every iteration through the loop.

#### Rust Macros
- Macros work on the abstract syntax tree (AST) level and are defined in separate crates.
- Types
    - Declerative Like: declare_id
    - Function Like: macros_rules! 
    - Attribute Like: #[program]
    - Derivative Like: #[derive()]

#### Rust Questions
1. Why variables in rust are purposely immutable? 
2. How to make variables in rust mutable? 
3. How are constants different from immutable variables?
4. How variables shadowing are different from mutable variables?
