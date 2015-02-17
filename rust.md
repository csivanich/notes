What I Shouldn't Forget About Rust
==========
Chris Sivanich ©2015

----------

### Ownership

- Single owner of objects
- Eliminate hanging pointer

```
box <var> to store on heap
```

```
&a to borrow
let a = Vec::new();
helper(&a);
```

```
String is heap allocated
```

### [References](http://static.rust-lang.org/doc/master/book/pointers.html#cheat-sheet)

t basic type of pointer that Rust has is called a reference. Rust references look like this:

    let x = 5;
    let y = &x;

    println!("{}", \*y);
    println!("{:p}", y);
    println!("{}", y);

We'd say "*y is a reference to x.*"
The first println! prints out the value of y's referent by using the dereference operator, \*.
The second one prints out the memory location that y points to, by using the pointer format string.
The third println! also prints out the value of y's referent, because println! will automatically dereference it for us.

Here's a function that takes a reference:

    fn succ(x: &i32) -> i32 { \*x + 1 }

You can also use & as an operator to create a reference, so we can call this function in two different ways:

    fn succ(x: &i32) -> i32 { \*x + 1 }

    fn main() {

        let x = 5;
        let y = &x;

        println!("{}", succ(y));
        println!("{}", succ(&x));
    }

Both of these println!s will print out 6.

Of course, if this were real code, we wouldn't bother with the reference, and just write:

    fn succ(x: i32) -> i32 { x + 1 }

**References are immutable by default**:

    let x = 5;
    let y = &x;

    \*y = 5; // error: cannot assign to immutable borrowed content `*y`

They can be made mutable with mut, but only if its referent is also mutable. This works:

    let mut x = 5;
    let y = &mut x;

This does not:

    let x = 5;
    let y = &mut x; // error: cannot borrow immutable local variable `x` as mutable

Immutable pointers are allowed to alias:

    let x = 5;
    let y = &x;
    let z = &x;

Mutable ones, however, are not:

    let mut x = 5;
    let y = &mut x;
    let z = &mut x; // error: cannot borrow `x` as mutable more than once at a time

Despite their complete safety, a reference's representation at runtime is the same as that of an ordinary pointer in a C program. They introduce zero overhead. The compiler does all safety checks at compile time. The theory that allows for this was originally called region pointers. Region pointers evolved into what we know today as lifetimes.

Here's the simple explanation: would you expect this code to compile?

    fn main() {
        println!("{}", x);
        let x = 5;
    }

Probably not. That's because you know that the name x is valid from where it's declared to when it goes out of scope. In this case, that's the end of the main function. So you know this code will cause an error. We call this duration a lifetime. Let's try a more complex example:

    fn main() {
        let x = &mut 5;

        if *x < 10 {
            let y = &x;

            println!("Oh no: {}", y);
            return;
        }

        *x -= 1;

        println!("Oh no: {}", x);
    }

Here, we're borrowing a pointer to x inside of the if. The compiler, however, is able to determine that that pointer will go out of scope without x being mutated, and therefore, lets us pass. This wouldn't work:

    fn main() {
        let x = &mut 5;

        if *x < 10 {
            let y = &x;
            *x -= 1;

            println!("Oh no: {}", y);
            return;
        }

        *x -= 1;

        println!("Oh no: {}", x);
    }

It gives this error:

    test.rs:5:8: 5:10 error: cannot assign to `*x` because it is borrowed
    test.rs:5         \*x -= 1;
    ^~
    test.rs:4:16: 4:18 note: borrow of `*x` occurs here
    test.rs:4         let y = &x;
    ^~

As you might guess, this kind of analysis is complex for a human, and therefore hard for a computer, too! There is an entire guide devoted to references, ownership, and lifetimes that goes into this topic in great detail, so if you want the full details, check that out.
