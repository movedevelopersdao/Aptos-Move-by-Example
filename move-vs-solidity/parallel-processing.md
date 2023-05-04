# Parallel Processing

Solidity and Move have different approaches to processing and concurrency.

In Solidity, the default execution model is sequential processing, meaning that the code is executed one step at a time, in the order that it appears in the code. This is because Solidity is a single-threaded language, which means that it can only execute one instruction at a time. However, Solidity does support asynchronous processing through the use of events and callbacks, which allow developers to write code that can execute in the background while the main thread is executing other code.

In contrast, Move has a parallel processing model, which allows for multiple threads to execute instructions simultaneously. This is achieved through the use of Move's resource model, which allows resources to be moved between different threads. This means that Move can execute multiple instructions at the same time, which can result in faster processing times and improved scalability.

The choice between sequential and parallel processing ultimately depends on the specific requirements of the application. If the application requires high-speed processing and scalability, a parallel processing model like Move may be more appropriate. However, if the application does not require parallel processing and is more focused on security and predictability, a sequential processing model like Solidity may be more appropriate.
