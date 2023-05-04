# Generics

Move language  supports the use of generics, which allow developers to write code that can be used with different types of data without having to rewrite the code for each specific type.

Generics in Move are similar to generics in other programming languages such as Java and C#. They allow developers to write reusable code that can work with any type that meets a specific set of requirements. This can help reduce code duplication and improve code readability and maintainability.

In Move, we will often use the term generics interchangeably with _type parameters_ and _type arguments_.

**Declaring Type Parameters**

Both functions and structs can take a list of type parameters in their signatures, enclosed by a pair of angle brackets `<...>`.

**Generic Functions**

Type parameters for functions are placed after the function name and before the (value) parameter list. The following code defines a generic identity function that takes a value of any type and returns that value unchanged.

Once defined, the type parameter `T` can be used in parameter types, return types, and inside the function body.

```rust
module my_addrx::Generics
{
    //a generic identity function that takes a value of any type and returns that value unchanged
    fun example<T>(num: T): T {
        num
    }

    #[test]
    fun testing()
    {
        let x:u64 = example<u64>(8);
        let y:bool = example<bool>(true);

        assert!(x==8,1);
        assert!(y==true,1);

    }

}
```

**Generic Structs**

Type parameters for structs are placed after the struct name, and can be used to name the types of the fields.

```rust
struct Foo<T> has copy, drop { x: T }

struct Bar<T1, T2> has copy, drop {
    x: T1,
    y: vector<T2>,
}
```
