---
title: "Writing Valid Specs IS NOT Easy!"
date: 2023-02-02
permalink: /posts/2023-02-02/writing-valid-specs-is-not-easy/
categories:
  - Blog
tags:
  - Blog
excerpt: "In this article, we try to debug our previous specification for Wormhole smart contract in the Aptos blockchain step by step and improve…"
canonical_url: https://medium.com/@seyyedaliayati/writing-valid-specs-is-not-easy-2df9736c0b02
---

### Writing Valid Specs IS NOT Easy!

#### In this article, we try to debug our previous specification for Wormhole smart contract in the Aptos blockchain step by step and improve it.

![](https://cdn-images-1.medium.com/max/800/1*841qFrFspTbz1TX3srgv1A.png)Image Credits: <https://dribbble.com/Monika_Lizak>

> This article is dependent on my previous article titled “[A Real Example of Proving a Formal Specification with Move](https://medium.com/@seyyedaliayati/a-real-example-of-proving-a-formal-specification-with-move-fe7d70bc2bf6)”, so it is highly recommended to take a look at it before reading this one.

In the mentioned article, a real specification for the Wormhole smart contract was written. In this article, we are going to **improve** it and provide **better answers** to the following questions:

**Q1:** Why there is a `<=` instead of `<`, in `ensures sender_post_balance <= sender_init_balance;`?

**Q2:** What is the reason of using `>=` in `ensures sender_post_balance >= sender_init_balance — amount — old(wormhole::state::get_message_fee());` ?

**Q3:** Is `ensures sender_post_balance >= 0;` necessary? Since the sender_post_balance is a type of `u64`.
    
    
    spec transfer_tokens_entry {  
            let sender_address = signer::address_of(sender);  
            let coin_store = borrow_global_mut<coin::CoinStore<AptosCoin>>(sender_address);  
      
            let sender_init_balance = coin_store.coin.value;  
            let post sender_post_balance = coin_store.coin.value;  
      
            requires coin::is_account_registered<AptosCoin>(sender_address) == true;  
            requires coin_store.frozen == false;  
            requires sender_init_balance >= amount;  
            requires amount >= relayer_fee;  
      
            ensures sender_post_balance >= 0;  
            ensures sender_post_balance <= sender_init_balance;  
            ensures sender_post_balance >= sender_init_balance - amount - old(wormhole::state::get_message_fee());  
        }

### Answer to Q1

For answering this specific question, the first step is to take a closer look at the `transfer_tokens_entry` function.
    
    
    public entry fun transfer_tokens_entry<CoinType>(  
            sender: &signer,  
            amount: u64,  
            recipient_chain: u64,  
            recipient: vector<u8>,  
            relayer_fee: u64,  
            nonce: u64  
        ) {  
            let coins = coin::withdraw<CoinType>(sender, amount);  
            let wormhole_fee = wormhole::state::get_message_fee();  
            let wormhole_fee_coins = coin::withdraw<AptosCoin>(sender, wormhole_fee);  
            transfer_tokens<CoinType>(  
                coins,  
                wormhole_fee_coins,  
                u16::from_u64(recipient_chain),  
                external_address::from_bytes(recipient),  
                relayer_fee,  
                nonce  
            );  
        }

As you can see in the source code of this function the `amount` and `wormhole_fee` is being withdrew from the sender. So, it makes sense that **the sender’s post balance should be less than the initial balance** , not less that or equal, specially if the amount is larger than zero. But, if we change the post-condition the move-prover cannot prove it. Let’s debug the specification and see what is going wrong!

#### Reading The Move-Prover Error Logs

By using the following command, move-prover will generate a set of error log messages.
    
    
    cd wormhole/aptos/token_bridge  
    move prove -d -t transfer_tokens

The `-d` flag tells the move-prover to use the development environment, and the `-t` flag filters move modules (in our case it will run the prover against `transfer_tokens` module). Unfortunately, it will generate a lot of log messages that your terminal windows may not be able to show the full history. So, with the following command we can redirect error messages to a text file. This technique helps us to store a full history of logs.
    
    
    move prove -d -t transfer_tokens 2> result.txt

If you need to see a more-detailed version of logs, you can pass the `-t `or `--trace` flag after the module name:
    
    
    move prove -d -t transfer_tokens -- -t 2> result.txt

> To see the list of all command line options, use `move prove -- --help` [1].

By reading the logs line by line, you will see all **the function calls, input arguments, and return results**. It can be seen, the both sender and receiver are the same that’s why a `>=` should be included instead of a `>`!
    
    
    at ./sources/transfer_tokens.move:20: transfer_tokens_entry  
                sender =  
                  signer{0x84a5f374d29fc77e370014dce4fd6a55b58ad608de8074b0be5571701724da31}  
                amount = 18446744073709551615  
                recipient_chain = 0  
                recipient = vector{}  
                relayer_fee = 18446744073709551614  
                nonce = 0  
                    ...  
    at /home/ali/.move/https___github_com_aptos-labs_aptos-core_git_mainnet/aptos-move/framework/aptos-framework/sources/coin.move:239: deposit  
                account_addr =  
                  0x84a5f374d29fc77e370014dce4fd6a55b58ad608de8074b0be5571701724da31  
                coin = coin.Coin{value = 18446744073709551615}

To fix this issue, we can define another pre-condition. This one makes sure that the sender address is not the token bridge itself!
    
    
    requires sender_address != @token_bridge;

This pre-condition won’t be enough. The sender initial balance should also be greater than amount and the wormhole fee.
    
    
    requires sender_init_balance >= amount + message_fee;

### Answer to Q2

As we already inspected the source code of the target function, there are two withdraw actions for the sender:
    
    
    let coins = coin::withdraw<CoinType>(sender, amount);  
    let wormhole_fee = wormhole::state::get_message_fee();  
    let wormhole_fee_coins = coin::withdraw<AptosCoin>(sender, wormhole_fee);

So, the final balance of the sender **should not be less than** `sender_init_balance — amount -wormhole_fee`. It means:
    
    
    ensures !(sender_post_balance < sender_init_balance - amount - message_fee);

With **simple boolean algebra rules** , the above condition is equivalent to the following one:
    
    
    ensures sender_post_balance >= sender_init_balance - amount - message_fee;

### Answer to Q3

Let’s take a look at the move specification language official documentation [2]:

> There are two types of encodings for integer types: `num` and `bv` (bit vector). If an integer (either a constant or a variable) does not involve in any bitwise operations directly or indirectly, regardless of its type in Move (`u8`, `u16`, `u32`, `u64`, `u128` and `u256`), **it is treated as the same type**. In specifications, this type is called `num`, which is an arbitrary precision _signed_ integer type…

Based on the provided explanation, **it is better to include this post-condition**. However, removing it will not affect the final results because the **compiler can handle type checking and raise runtime errors**.

### An Improved Version of Our Specification

To sum it up, the debugged (improved) version of the previous specification is as follows:
    
    
    spec transfer_tokens_entry {  
            let sender_address = signer::address_of(sender);  
            let coin_store = borrow_global_mut<coin::CoinStore<CoinType>>(sender_address);  
            let sender_init_balance = coin::balance<CoinType>(sender_address);  
            let post sender_post_balance = coin::balance<CoinType>(sender_address);  
            let message_fee = wormhole::state::get_message_fee(); // new  
      
            requires sender_address != @token_bridge; // new  
            requires sender_init_balance >= amount + message_fee; // new  
            requires coin::is_account_registered<CoinType>(sender_address) == true;  
            requires coin::is_account_registered<CoinType>(@token_bridge) == true; // new  
            requires coin_store.frozen == false;  
            requires amount >= relayer_fee;  
            requires amount > 0;  
      
            ensures sender_post_balance >= 0;  
            ensures sender_post_balance < sender_init_balance; // modified  
            ensures !(sender_post_balance < sender_init_balance - amount - message_fee); // new  
            ensures sender_post_balance >= sender_init_balance - amount - message_fee; // equivalent to above  
              
        }

### References

  1. <https://github.com/move-language/move/blob/main/language/move-prover/doc/user/prover-guide.md>
  2. <https://github.com/move-language/move/blob/main/language/move-prover/doc/user/spec-lang.md#type-system>