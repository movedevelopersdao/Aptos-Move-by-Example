# Uses and Aliases

The `use` syntax can be used to create aliases to members in other modules. `use` can be used to create aliases that last either for the entire module, or for a given expression block scope.

**Syntax:**

```rust
use <address>::<module name>;
use <address>::<module name> as <module alias name>;
use <address>::<module name>::<module member>;
use <address>::<module name>::<module member> as <member alias>;
use <address>::<module name>::{<module member>, <module member> as <member alias> ... };
```

Some examples:

```rust
use std::vector;
use std::debug as d;
use std::debug::print as p;
```

**Self:**

If you need to add an alias to the Module itself in addition to module members, you can do that in a single `use` using `Self`. `Self` is a member of sorts that refers to the module.

```rust
use std::simple_map::{Self, SimpleMap};
```

**Inside a module:**

Inside of a `module` all `use` declarations are usable regardless of the order of declaration.

```rust
module  my_addrx::example {
    use std::vector;

    fun example(): vector<u8> {
        let v = empty();
        vector::push_back(&mut v, 0);
        vector::push_back(&mut v, 10);
        v
    }

    use std::vector::empty;
}
```

The aliases declared by `use` in the module usable within that module.

**Inside an expression:**

You can add `use` declarations to the beginning of any expression block

<pre class="language-rust"><code class="lang-rust"><strong>module  my_addrx::example {
</strong>
    fun example(): vector&#x3C;u8> {
        use std::vector::{empty, push_back};

        let v = empty();
        push_back(&#x26;mut v, 0);
        push_back(&#x26;mut v, 10);
        v
    }
}
</code></pre>

As with `let`, the aliases introduced by `use` in an expression block are removed at the end of that block.

```rust
module  my_addrx::example {

    fun example(): vector<u8> {
        let result = {
            use std::vector::{empty, push_back};
            let v = empty();
            push_back(&mut v, 0);
            push_back(&mut v, 10);
            v
        };
        result
    }
}
```

Attempting to use the alias after the block ends will result in an error

```rust
fun example(): vector<u8> {
    let result = {
        use std::vector::{empty, push_back};
        let v = empty();
        push_back(&mut v, 0);
        push_back(&mut v, 10);
        v
    };
    let v2 = empty(); // ERROR!
//           ^^^^^ unbound function 'empty'
    result
}
```

Any `use` must be the first item in the block. If the `use` comes after any expression or `let`, it will result in a parsing error

```rust
{
    let x = 0;
    use std::vector; // ERROR!
    let v = vector::empty();
}
```

**Naming convention:**

Aliases must follow the same rules as other module members. This means that aliases to structs or constants must start with `A` to `Z`.

**Unused Use or Alias:**

An unused `use` will result in an error

```rust
module my_addrx::Example
{
    use std::debug; //ERROR -> Unused 'use' of alias 'debug'. Consider removing it

    fun printing()
    {
        //do nothing
    }   
}
```
