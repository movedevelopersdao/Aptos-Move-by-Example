# Loops

**Loops:**

Move provides two ways for defining loops:

* using `while`
* using infinite `loop`

```rust
module my_addrx::Loops
{
    use std::debug::print;
    //lets find the sum of first N natural numbers using while and infinite loop

    //using while loop
    fun sum_using_while(n:u64) :u64
    {
        let sum=0;
        let i:u64=1;  //setting counter to 1
        while(i <= n) //This Loop will run until the expression inside the round brackets is valid
        {
            sum=sum+i;
            i=i+1;  //incrementing the counter
        }; //while is an expression therefore it should be end with a semicolon.
        sum //you can also return in functions like this.
    }
    
    //using infinite loop
    fun sum_using_loop(n:u64) :u64
    {
        let sum=0;
        let i:u64=1;
        loop
        {
            sum=sum+i;
            i=i+1;
            if(i>n)
                break;   //break statement terminates the loops 
        };
        sum
    }



    #[test]
    fun testing()
    {
        let sum = sum_using_while(10);
        print(&sum);
        let sum = sum_using_loop(10);
        print(&sum);
    }
}
```

{% hint style="info" %}
Similar to `break` statement there is `continue` statement which forces the program to execute next iteration by skipping the current one.
{% endhint %}
