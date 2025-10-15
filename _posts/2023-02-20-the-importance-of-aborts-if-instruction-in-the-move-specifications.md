---
title: "The Importance of aborts_if Instruction in The Move Specifications"
subtitle: "Enhancing safety and reliability in Move programming with aborts_if instruction"
date: 2023-02-20
permalink: /posts/2023-02-20/the-importance-of-aborts-if-instruction-in-the-move-specifications/
categories:
  - Blockchain
    - Move
    - Smart Contracts
tags:
  - Blockchain
    - Move
    - Smart Contracts
    - Formal Verification
excerpt: "How the aborts_if instruction improves safety and reliability in Move smart contracts."
---

### The Importance of aborts_if Instruction in The Move Specifications

#### Enhancing Safety and Reliability in Move Programming with aborts_if Instruction.

![](https://cdn-images-1.medium.com/max/800/1*KCPGB98qItvn50RWxtae9A.png)Figure 1: Syntax of the aborts_if instruction in Move programming language

Move is a programming language designed specifically for developing blockchain applications. It is a safe and secure language that is used to build smart contracts on the Diem blockchain. The language has a set of specifications that define its syntax and behavior, and one of the critical instructions in these specifications is the `aborts_if` instruction.

Check out the following useful resources to get familiar with the Move and Move Prover:

  * <https://move-language.github.io/move/introduction.html>
  * <https://medium.com/@seyyedaliayati/the-abc-of-formal-verification-with-move-906ac1b2aaa0>
  * <https://github.com/MystenLabs/awesome-move>

### A Brief Introduction to Data Invariant

Data invariants are an essential concept in programming, and they play a crucial role in ensuring the correctness and security of software systems. In the Move programming language, data invariants are particularly significant because they help ensure the safety and soundness of smart contracts.

A data invariant is a condition that must be true for a particular piece of data at all times. In other words, it is a constraint that ensures the validity of the data. In Move, data invariants are expressed using the `invariant` keyword, which is followed by a Boolean expression that describes the constraint.

Data invariants are enforced by the Move specifications, which checks the validity of the invariants every time the data is accessed or modified. If an invariant is violated, the Move Prover will reject the operation, preventing any further changes to the data until the issue is resolved. The following code snippet is written in the Move programming language and defines a struct named `Coin`.
    
    
    struct Coin has store, drop {  
       value: u64  
    }  
      
    spec Coin {  
       invariant value >= 10;  
    }

The above code defines a new struct named `Coin` that has one field called `value`. The `has store, drop` part of the code indicates that the struct has both storage and drop behavior. In Move, `store` means that the struct can be stored on the blockchain, and `drop` means that the struct can be deleted from the blockchain.

This specification ensures that any `Coin` structure created in a Move program will always have a value greater than or equal to 10, preventing any potential errors or vulnerabilities that could arise from having a `Coin` structure with a value less than 10.

### A Brief Introduction to Boogie IVL

![](https://cdn-images-1.medium.com/max/800/0*C2inSaJJUF6jWPwD.png)<https://www.microsoft.com/en-us/research/project/boogie-an-intermediate-verification-language/>

Boogie is an intermediate verification language (IVL) that is used to specify and verify software systems. It was developed at Microsoft Research in 2006 as a part of the Microsoft Research Boogie project.

Boogie is designed to be used with automated theorem provers (ATPs) and model checkers, which can use Boogie specifications to prove or disprove properties of software systems. Boogie has been used in a variety of applications, including program verification, compiler optimization, and security analysis.

Boogie provides a rich set of features for specifying software systems, including support for imperative programming constructs, such as loops, conditionals, and procedure calls. It also supports higher-order programming and reasoning about complex data structures, such as arrays, lists, and trees.

Here are a few examples of Boogie instructions:

`assume`: This instruction is used to specify assumptions about the values of variables in the program. For example, the following code specifies that the value of variable `x` is greater than zero:
    
    
    assume x > 0;

`assert`: This instruction is used to express properties that must hold at a certain point in the program. For example, the following code asserts that the value of variable `y` is less than or equal to the value of variable `x`:
    
    
    assert y <= x;

`goto`: This instruction is used to transfer control to a different location in the program. For example, the following code transfers control to the label `L1`:
    
    
    goto L1;

These are just a few examples of the instructions provided by Boogie. Boogie also provides support for many other instructions, such as `assign`, `call`, and `if-then-else`, among others. These instructions can be combined to specify the behavior of complex software systems and to express properties that need to be verified.

### Dive Deeper into Data Invariant

In this section we are going to dive deeper into the data invariants and see how it works. Let’s consider our previous `Coin` structure with its data invariant `value ≥ 10`.

To test this specification, let’s write a function that initializes the structure and passes a value less than ten (in this example 2+3 which is 5).
    
    
    module NamedAddr::Simple {  
      
        struct Coin has store, drop {  
            value: u64  
        }  
      
        spec Coin {  
            invariant value >= 10;  
        }  
      
        public fun run(): Coin{  
            let coin = Coin { value: 2 + 3};  
            coin  
        }  
    }

Let’s run the `move-prover` against this module:
    
    
    move prove -t Simple
    
    
    [INFO] preparing module 0xab27::Simple  
    [INFO] transforming bytecode  
    [INFO] generating verification conditions  
    [INFO] 1 verification conditions  
    [INFO] running solver  
    [INFO] 0.023s build, 0.001s trafo, 0.009s gen, 1.400s verify, total 1.432s  
    error: data invariant does not hold  
      ┌─ ./sources/Simple.move:8:9  
      │  
    8 │         invariant value >= 10;  
      │         ^^^^^^^^^^^^^^^^^^^^^^  
      │  
      =     at ./sources/Simple.move:12: run  
      =     at ./sources/Simple.move:8  
      
    Error: exiting with verification errors

As it is expected, it detected the anomaly and raises an error titled **data invariant does not hold** , and then it provides more information about the error such as the file path, row and column number, and etc… .

Let’s take a look at the translated boogie instructions of this specific example:
    
    
        // ...  
        $t0 := 5;  
        assume $IsValid'u64'($t0);  
      
      
        // $t1 := pack Simple::Coin($t0) at ./sources/Simple.move:15:20+18  
        $t1 := $ab27_Simple_Coin($t0);  
      
      
        // assert Gt(select Simple::Coin.value($t1), 10) at ./sources/Simple.move:10:9+21  
        // data invariant at ./sources/Simple.move:10:9+21  
        assume {:print "$at(2,136,157)"} true;  
        assert {:msg "assert_failed(2,136,157): data invariant does not hold"}  
          ($value#$ab27_Simple_Coin($t1) > 10);  
        // ...

As it seems, the move boogie translator, calculated the result of 2+3 which is 5 and stored it in a local variable called `$t0`. Then, there is an assert statement to check the data invariant for the property `value`.

Now, let’s modify our specification so that it can accept values larger than or equal to zero, and pass a negative value to `Coin`.
    
    
    module NamedAddr::Simple {  
      
        struct Coin has store, drop {  
            value: u64  
        }  
      
        spec Coin {  
            invariant value >= 0;  
        }  
      
        public fun run(): Coin{  
            let coin = Coin { value: 0 - 10};  
            coin  
        }  
    }

Run the `move-prover` again to get surprised! There is no error!
    
    
    move prove -t Simple
    
    
    [INFO] preparing module 0xab27::Simple  
    [INFO] transforming bytecode  
    [INFO] generating verification conditions  
    [INFO] 1 verification conditions  
    [INFO] running solver  
    [INFO] 0.022s build, 0.001s trafo, 0.009s gen, 1.340s verify, total 1.372s

It verified successfully without giving us any error messages for our violated data invariant! **What’s happening?** Let’s take a look at the updated boogie instructions:
    
    
        call $t2 := $Sub($t0, $t1);  
        if ($abort_flag) {  
            assume {:print "$at(2,228,229)"} true;  
            $t3 := $abort_code;  
            assume {:print "$track_abort(0,0):", $t3} $t3 == $t3;  
            goto L2;  
        }  
      
      
        // $t4 := pack Simple::Coin($t2) at ./sources/Simple.move:15:20+20  
        $t4 := $ab27_Simple_Coin($t2);  
      
      
        // assert Ge(select Simple::Coin.value($t4), 0) at ./sources/Simple.move:10:9+21  
        // data invariant at ./sources/Simple.move:10:9+21  
        assume {:print "$at(2,136,157)"} true;  
        assert {:msg "assert_failed(2,136,157): data invariant does not hold"}  
          ($value#$ab27_Simple_Coin($t4) >= 0);

In the above boogie instructions, there is `Sub` procedure that takes two integer arguments (in this case they are 0 and 10), then it checks for `abort_flag`!

If the `abort_flag` is `true` then it prints some debug information and jumps to the `L2` location. In the following there is the instructions for `L2` that sets abort info and simply return from the procedure.
    
    
    L2:  
        // abort($t3) at ./sources/Simple.move:14:5+1  
        assume {:print "$at(2,228,229)"} true;  
        $abort_code := $t3;  
        $abort_flag := true;  
        return;

Let’s take a look at the `Sub` function to understand how the an abort occurs:
    
    
    procedure {:inline 1} $Sub(src1: int, src2: int) returns (dst: int)  
    {  
        if (src1 < src2) {  
            call $ExecFailureAbort();  
            return;  
        }  
        dst := src1 - src2;  
    }

It is crystal clear that if `src1` (i.e. first integer number) is less that `src2` (i.e. second integer number) then **an abort should occur!** This is the surprise! Based on the translated boogie instructions it is expected that the **Move Prover should abort the verification and give us some debug information about the failure; but it does not :(**

#### Why?

To be honest, at first, I thought there is a bug in the translation process or even its next steps (e.g. SMT Solver). **But the answer is much easier!** It is because the developer did not set the `aborts_if` flag, and by default the Move Prover skips the aborts and does not report it to the user. For doing so, we should write another spec for our function as follows:
    
    
    spec run {  
            aborts_if false; // This means that this function should never abort!  
        }

Now let’s run the Move Prover and see the debug info:
    
    
    [INFO] preparing module 0xab27::Simple  
    [INFO] transforming bytecode  
    [INFO] generating verification conditions  
    [INFO] 1 verification conditions  
    [INFO] running solver  
    [INFO] 0.022s build, 0.001s trafo, 0.009s gen, 1.388s verify, total 1.420s  
    error: abort not covered by any of the `aborts_if` clauses  
       ┌─ ./sources/Simple.move:16:5  
       │    
    12 │           let coin = Coin { value: 0 - 10};  
       │                                      - abort happened here with execution failure  
       ·    
    16 │ ╭     spec run {  
    17 │ │         aborts_if false; // This means that this function should never abort!  
    18 │ │     }  
       │ ╰─────^  
       │    
       =     at ./sources/Simple.move:12: run  
       =         ABORTED  
      
    Error: exiting with verification errors

Now the Move Prover reports the abort at line 12. Just one question remains. **Why the Move Prover ignores aborts by default and the developer should explicitly define another spec?**

The answer to this question should not be hard for you if you understand aborts and exceptions in programming. **Feel free to write down your thoughts in the comment section!**