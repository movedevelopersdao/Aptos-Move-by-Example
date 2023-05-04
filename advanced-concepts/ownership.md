# Ownership

In the Move programming language, ownership is a concept that is used to manage memory and resources. Ownership is based on the idea that each resource can only have one owner at a time, and ownership can be transferred from one owner to another. This allows Move to enforce memory safety, prevent data races, and enable efficient memory management.

In Move, ownership is enforced by the borrow checker, which checks that a resource is not used after it has been moved or borrowed. Move also has a linear type system, which means that resources can only be consumed once and must be moved or borrowed in a specific order.

Owner is a scope which _owns_ a variable. Variables either can be defined in this scope (e.g. with keyword `let`) or be passed into the scope as arguments. Since the only scope in Move is function's - there are no other ways to put variables into scope.

Each variable has only one owner, which means that when a variable is passed into function as argument, this function becomes the _new owner_, and the variable is no longer _owned_ by the first function. Or you may say that this other function _takes ownership_ of the variable.

**Move and Copy**

First, you need to understand how Move VM works, and what happens when you pass your value into a function. There are two bytecode instructions in VM: _MoveLoc_ and _CopyLoc_ - both of them can be manually used with keywords `move` and `copy` respectively.

When a variable is being passed into another function - it's being _moved_ and _MoveLoc_ OpCode is used. Let's see how our code would look if we've used keyword `move`:

```rust
script {
    use {{sender}}::M;

    fun main() {
        let a : Module::T = Module::create(10);

        M::value(move a); // variable a is moved

        // local a is dropped
    }
}

```

This is a valid Move code, however, knowing that value will still be moved you don't need to explicitly _move_ it. Now when it's clear we can get to _copy_.

**Keyword copy**

If you need to pass a value to the function (where it's being moved) and save a copy of your variable, you can use keyword `copy`.

```rust
script {
    use {{sender}}::M;

    fun main() {
        let a : Module::T = Module::create(10);

        // we use keyword copy to clone structure
        // can be used as `let a_copy = copy a`
        M::value(copy a);
        M::value(a); // won't fail, a is still here
    }
}
```

In this example we've passed a _copy_ of a variable (hence value) `a` into the first call of the method `value` and saved `a` in local scope to use it again in a second call.

By copying a value we've duplicated it and increased the memory size of our program, so it can be used - but when you copy huge data it may become pricey in terms of memory. In blockchains every byte counts and affects the price of execution, so using `copy` all the time may cost you a lot.
