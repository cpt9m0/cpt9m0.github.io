---
title: "The ABC of Formal Verification with Move"
subtitle: "Introduction to formal verification in Move smart contracts"
date: 2023-01-11
permalink: /posts/2023-01-11/the-abc-of-formal-verification-with-move/
categories:
  - Blockchain
  - Move
  - Smart Contracts
tags:
  - Blockchain
  - Move
  - Smart Contracts
  - Formal Verification
excerpt: "Introductory guide to formal verification in Move smart contracts."
---

### The ABC of Formal Verification with Move

#### A Beginner’s Guide

![](https://cdn-images-1.medium.com/max/800/1*ofq_730TVD4obapS6XTVKw.png)An overview of software testing [1]

In recent years, the field of formal verification has been gaining traction as a way to mathematically prove that a system, such as a computer program or hardware design, behaves as intended. However, many people still find the concept of formal verification to be intimidating and difficult to understand. In this article, we’ll go over the basics of formal verification using Move, a programming language and framework originally developed by the Facebook to power the Diem blockchain.

### Tables of Contents

  * An Introduction to Formal Verification
  * An Introduction to Move Programming Language
  * Formal Verification with The Move Prover
  * Summary
  * References


### An Introduction to Formal Verification

Formal verification is a method of mathematically proving that a system, such as a computer program or hardware design, behaves as intended. This is accomplished by constructing a formal model of the system, and then using mathematical reasoning to prove that the model satisfies certain properties. Formal verification can be used to ensure that a system is free from certain types of bugs and errors, and can provide a high degree of confidence that the system will behave correctly in all possible scenarios.

There are several different approaches to formal verification, each with their own strengths and weaknesses. Some of the most common methods include:

  * **Model checking:** This method involves building a formal model of the system, and then using a computer program to systematically explore all possible states of the model. The program checks whether the model satisfies certain properties, such as safety properties (e.g., the system will never reach an unsafe state) or liveness properties (e.g., the system will eventually reach a desirable state).
  * **Theorem proving:** In this method, a set of mathematical proofs are constructed to establish that the system’s properties are true. This is a more manual approach, but can be more powerful for certain types of systems, such as those with complex logic or real-time constraints.
  * **Constraint solving:** It’s a technique where a set of constraints are defined in a mathematical language and the solver will check whether there exist any values of the variables that satisfy the given constraints.

#### Applications of Formal Verification

Formal verification is particularly well-suited for systems that are safety-critical, such as those used in aerospace, defense, and healthcare. Such systems must be highly reliable, as the consequences of failure can be severe. Formal verification can also be used in other industries, such as finance and telecommunications, where the costs of errors or downtime can be high.

#### Pros and Cons of Formal Verification

One of the biggest advantages of formal verification is that it can detect bugs and errors that may be difficult or impossible to find through testing alone. For example, a system may work correctly in all of the test cases that have been considered, but still contain errors that only occur under certain rare or hard-to-reproduce conditions. Formal verification can uncover these errors, allowing them to be fixed before the system is deployed.

However, formal verification can be a time-consuming and complex process, especially for large or highly complex systems. It also requires a significant investment in formal methods expertise, as well as tools and infrastructure to support the verification process.

### An Introduction to Move Programming Language

![](https://cdn-images-1.medium.com/max/800/1*JN6fyeAA6pWPFM4tfIaWuQ.jpeg)Move is going to be the JavaScript of Web3 [2]

Move is a new programming language originally developed by Facebook that is designed to enable a new class of secure, programmable digital assets. Move is built on the principle of resource-oriented programming, which allows developers to create and manage assets that are guaranteed to be unique and non-duplicable. This makes Move well-suited for a wide range of applications, including digital currencies, supply chain management, and identity verification.

One of the key features of Move is its strong type system. Move has a number of built-in types, including integers, booleans, and addresses, that are used to represent assets and other values. These types are designed to be highly expressive, making it easy for developers to create and manipulate assets in a safe and predictable way.

Another important feature of Move is its resource-oriented programming model. In Move, all assets are represented as “resources”, which are unique and non-duplicable. This means that once an asset has been created, it cannot be copied or duplicated. This property is enforced at the language level, making it impossible to accidentally duplicate an asset.

Here is an example of a simple Move program that creates and manipulates a digital currency asset:
    
    
    module MyCurrency {  
      // Define the resource for the currency.  
      resource Currency {  
        let balance: U64;  
      }  
      
      // Create a new currency with a starting balance of 100.  
      public fun create_currency(): Currency {  
        return Currency { balance: 100 };  
      }  
      
      // Transfer funds from one currency to another.  
      public fun transfer(from: &Currency, to: &Currency, amount: U64): bool {  
        if (from.balance >= amount) {  
          from.balance = from.balance - amount;  
          to.balance = to.balance + amount;  
          return true;  
        }  
        return false;  
      }  
    }

In this example, we define a new Move module called `MyCurrency`, which contains a resource type called `Currency`. The `Currency` resource has a single field, `balance`, which is used to store the balance of the currency. The module also contains two functions: `create_currency`, which creates a new instance of the `Currency` resource with a starting balance of 100, and `transfer`, which allows funds to be transferred from one currency to another.

You can see in `transfer` function, it's using a language level protection by using `&` before the variable name, it is guaranteed that the `transfer` function can not copy the currency, it can only reference to the original one and make the necessary changes. In this way, you can use Move to create and manage digital assets that are guaranteed to be unique and non-duplicable, making it well-suited for a wide range of applications.

Note that this is just an introductory example, and Move has much more powerful features that allows doing things like creating custom resource types, signature verification, and gas payouts to name a few.

### Formal Verification with The Move Prover

Move Prover is a tool that takes a Move program and a set of properties as input, and then uses mathematical reasoning to prove that the program satisfies those properties. The Move Prover can be used to check the properties of a Move program, such as ensuring that the program does not violate any security or correctness properties.

An example of using Move Prover to formally verify the properties of a system is checking that a digital wallet implemented in the Move language does not allow negative balance.

Let’s assume we have defined a resource type called “Balance” that represents the current balance of a user’s digital wallet. A user is able to deposit and withdraw funds from the wallet, which updates the balance accordingly. To ensure that a user cannot have a negative balance, we can use Move Prover to prove that the “withdraw” operation will always check the current balance before allowing the withdrawal, and will not allow the withdrawal if it would result in a negative balance.
    
    
    fun withdraw<CoinType>(addr: address, amount: u64) : Coin<CoinType> acquires Balance {  
            let balance = balance_of<CoinType>(addr);  
            assert!(balance >= amount, EINSUFFICIENT_BALANCE);  
            let balance_ref = &mut borrow_global_mut<Balance<CoinType>>(addr).coin.value;  
            *balance_ref = balance - amount;  
            Coin<CoinType> { value: amount }  
        }

Next, we can use the Move Prover to check that this operation always enforces the non-negative balance constraint by providing a property that balance must be non-negative after withdraw function is called. These constraints are called specification in Move [3].
    
    
    spec withdraw {  
            let balance = global<Balance<CoinType>>(addr).coin.value;  
      
            aborts_if !exists<Balance<CoinType>>(addr);  
            aborts_if balance < amount;  
      
            let post balance_post = global<Balance<CoinType>>(addr).coin.value;  
            ensures result == Coin<CoinType> { value: amount };  
            ensures balance_post == balance - amount;  
        }

With the following command we can run the prover:
    
    
    move prove  
      
    # The result would be:  
    [INFO] preparing module 0x12::Medium  
    [INFO] transforming bytecode  
    [INFO] generating verification conditions  
    [INFO] 6 verification conditions  
    [INFO] running solver  
    [INFO] 0.644s build, 0.005s trafo, 0.010s gen, 0.964s verify, total 1.624s

#### The Move Prover Architecture (i.e. How It Works)

Move Prover is built on top of the Z3 SMT (Satisfiability Modulo Theories) solver, which is an efficient and widely used tool in formal verification, providing a solid foundation of the implementation.

![](https://cdn-images-1.medium.com/max/800/1*TS_n5ERXUIPkGBjMY5OFFQ.png)The Move Prover Architecture [4]

The Move Prover takes annotated Move source code as input and verifies that the code meets the specifications provided. The process involves several steps including extracting the specifications, compiling the source code, removing stack operations and translating the code into a program in the **Boogie** intermediate verification language (IVL). This program is then checked by the Boogie verification system and an **SMT solver**. If the code meets the specifications, it is reported as such to the user. However, if the result is not satisfactory, the prover provides a diagnosis that the user can use to improve the code and repeat the process. The Move Prover is written in the Rust programming language and is available in the Move language repository on GitHub [5].

#### Generating Counterexamples

In the Move Prover, a counterexample is generated when the verification process determines that the code and specifications provided do not match. Specifically, when the SMT solver returns a result of SAT (which stands for “satisfiable”), this means that the SMT formula created by the Boogie verification system has at least one solution, or a set of values for the variables that make the formula true. This represents a violation of the specifications, and the counterexample is a set of values that demonstrate how the code behaves in a way that does not match the specifications.

Consider the following simple “add” function which passes the unit test successfully, but we know that it can behave incorrectly with specific input arguments.
    
    
    public fun add(a: u64, b: u64): u64 {  
            if (a * b == 40) {  
                return 0  
            };  
            a + b  
        }  
      
    #[test]  
    // This test passes  
    public fun test_add() {  
        assert!(add(10, 20) == 30, 0);  
    }

As previously explained, we can determine the function’s specifications and formally verify them with the Move Prover. The aim is to ensure that the function returns the sum of its inputs **in all possible scenarios.** After running the Move Prover, you will notice that it generates a counterexample (in this case, a = 1, b = 40) to guide the developer.
    
    
    spec add {  
            ensures result == a + b;  
    }
    
    
    error: post-condition does not hold  
       ┌─ ./sources/Medium.move:33:9  
       │  
    33 │         ensures result == a + b;  
       │         ^^^^^^^^^^^^^^^^^^^^^^^^  
       │  
       = Related Bindings:   
       =         a = 40  
       =         b = 1  
       =         result = 0  
       = Execution Trace:  
       =     at ./sources/Medium.move:25: add  
       =         a = 40  
       =         b = 1  
       =     at ./sources/Medium.move:26: add  
       =     at ./sources/Medium.move:27: add  
       =         result = 0  
       =     at ./sources/Medium.move:33: add (spec)  
       =         `ensures result == a + b;` = false  
      
    Error: exiting with verification errors

### Conclusion

In conclusion, formal verification is a powerful technique for mathematically proving that a system behaves as intended. This method can detect bugs and errors that may be difficult or impossible to find through testing alone, and is particularly well-suited for systems that are safety-critical such as those used in aerospace, defense, and healthcare.

Move is a new programming language and framework developed by Facebook, that enables a new class of secure, programmable digital assets by using the principle of resource-oriented programming.

Formal verification can be done with Move Prover, a formal verification tool for smart contracts written in Move, which allows developers to mathematically prove the correctness of their contracts, and can be used to check whether the implementation of a contract meets its desired properties, as well as to prove the absence of certain types of errors. The process of formal verification can be time-consuming and complex, but it can give developers a high degree of confidence that the system will behave correctly in all possible scenarios.

### References

> All links were valid at Jan 10, 2023.

  1. <https://blog.digitalasset.com/developers/what-is-formal-verification-and-what-it-means-for-daml>
  2. <https://github.com/MystenLabs/awesome-move>
  3. <https://github.com/move-language/move/blob/main/language/move-prover/doc/user/spec-lang.md>
  4. Zhong, J. E., Cheang, K., Qadeer, S., Grieskamp, W., Blackshear, S., Park, J., … & Dill, D. L. (2020, July). The move prover. In International Conference on Computer Aided Verification (pp. 137–150). Springer, Cham.
  5. <https://github.com/move-language/move/tree/main/language/move-prover>