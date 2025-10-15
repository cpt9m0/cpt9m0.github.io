---
title: "A Real Example of Proving a Formal Specification with Move"
date: 2023-01-29
permalink: /posts/2023-01-29/a-real-example-of-proving-a-formal-specification-with-move/
categories:
  - Blockchain
tags:
  - Blockchain
excerpt: "Writing a formal specification by using the Move in Aptos wormhole smart contract and prove it using the Move Prover tool."
canonical_url: https://medium.com/@seyyedaliayati/a-real-example-of-proving-a-formal-specification-with-move-fe7d70bc2bf6
---

### A Real Example of Proving a Formal Specification with Move

#### Writing a formal specification by using the Move in Aptos wormhole smart contract and prove it using the Move Prover tool.

![](https://cdn-images-1.medium.com/max/800/0*iIPKLIYncOLFBNsV.png)Image Source: <https://www.certik.com>

### Aptos Blockchain and Wormhole Smart Contract

Web3 startup Aptos, commonly known as Aptos Labs, is developing a layer-1 blockchain. The business is using Move, a language created initially for the now-defunct blockchain project Diem. The Rust-based programming language that Meta separately created and the old Diem blockchain are both heavily utilized by Aptos. [1]

Wormhole is a smart contract platform built on top of the Aptos Blockchain. It allows developers to create and deploy smart contracts in a variety of programming languages, including Solidity, Vyper, and Move. These smart contracts can be used to create decentralized applications (dApps) and tokens, manage digital assets and identities, and automate business processes. The Wormhole smart contract platform also provides a web-based development environment, allowing developers to easily create and test their contracts. [2]

### What Is a Formal Specification (in Move)?

Formal specification in Move is a way to define the behavior and properties of smart contracts in a precise and mathematical language. It allows developers to express the desired behavior of their contracts using logical statements and mathematical notation, which can then be verified using formal verification tools (i.e. Move Prover). This allows for more accurate and reliable contracts, as the properties of the contract can be proven to be true or false before deployment. We already discussed about this in another Medium article titled “The ABC of Formal Verification with Move” [3].

### Installing Rust & Move on Your Machine

Before moving on to writing a simple specification, you may need to install Move and the Rust programming languages.

#### Installing the Rust Programming Language

To install Rust, use the following command: [4]
    
    
    curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

You can test the installation by running the following commands.
    
    
    rustc --help  
    cargo --help

#### Installing Move Programming Language

To install the Move programming language, it is necessary to build it from the source code. This can be done by downloading the source code from the Move language’s GitHub repository: [5]
    
    
    git clone https://github.com/move-language/move.git

After cloning the repository, it should be built with the “address32” feature enabled. This is necessary as the wormhole smart contract utilizes 32-bit addresses, whereas the default for Move is 16-bit addresses. The command for this is:
    
    
    cargo install --path move/language/tools/move-cli --features address32

### Writing a Real Specification

In the wormhole smart contract you can find the following function in `token_bridge/transfer_tokens.move`.
    
    
    public entry fun transfer_tokens_entry<CoinType>(  
            sender: &signer,  
            amount: u64,  
            recipient_chain: u64,  
            recipient: vector<u8>,  
            relayer_fee: u64,  
            nonce: u64  
            let coins = coin::withdraw<CoinType>(sender, amount);  
        subtitle: "Demonstrating formal verification in Move smart contracts with a real example"
            let wormhole_fee = wormhole::state::get_message_fee();  
            let wormhole_fee_coins = coin::withdraw<AptosCoin>(sender, wormhole_fee);  
            transfer_tokens<CoinType>(  
                coins,  
            - Move
            - Smart Contracts
                wormhole_fee_coins,  
                u16::from_u64(recipient_chain),  
            - Move
            - Smart Contracts
            - Formal Verification
        excerpt: "Step-by-step demonstration of formal verification in Move smart contracts."
                nonce  
            );  
        }

The `transfer_tokens_entry` is a publicly accessible function on the smart contract, making it a potential target for attackers. Formal verification can ensure that it operates correctly.
    
    
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

The following statements are checked in sequence by the preconditions of this specification:

  1. A registered sender account is required.
  2. It is inappropriate to freeze the sender coin store.
  3. The initial balance of the sender must be higher than the amount being transferred. (Preventing a deficit)
  4. The relayer fee need to be equal to or less than the amount being transferred. (Preventing a deficit)

And we anticipate that this function will operate as intended; it must send a specific number of coins to the token bridge. Thus, these are the post-conditions: (Sender’s post balance is equal to Sender’s balance following the completion of the function.)

  1. The post balance of the sender must be positive.
  2. Sender’s post-transaction balance must be lower than or equal to Sender’s initial-transaction balance. **Why** not just say less than instead of less than or equal? (Be sure to read the next part.)
  3. The amount being sent and the message fee should be deducted from the sender’s initial balance, which should be greater than or equal to that amount. **Why** not just say greater than instead of greater than or equal? (Be sure to read the next part.)

To run the Move Prover tool on specifications related to the `transfer_tokens` module, we can filter it using the `-t` argument.
    
    
    move prove -t transfer_token

The result would look like:
    
    
    [INFO] preparing module 0x84a::transfer_tokens  
    [INFO] transforming bytecode  
    [INFO] generating verification conditions  
    [INFO] 8 verification conditions  
    [INFO] running solver  
    [INFO] 4.087s build, 1.118s trafo, 0.173s gen, 3.198s verify, total 8.575s

#### Steps to Write The Specification

> This part can be skipped if you know the answers to the above **why** questions.

Before starting to write a specification, we should be aware of what the function does exactly and what is the desired behavior. So, the first and foremost step is to read the source code carefully and trace every possible path. OK, let’s do it:

In the first line of the function’s code, it calls a function from the coin module of Aptos framework to withdraw amount from sender. Let’s take a look at this function: (<https://github.com/aptos-labs/aptos-core/blob/main/aptos-move/framework/aptos-framework/sources/coin.move>)
    
    
    /// Withdraw specifed `amount` of coin `CoinType` from the signing account.  
        public fun withdraw<CoinType>(  
            account: &signer,  
            amount: u64,  
        ): Coin<CoinType> acquires CoinStore {  
            let account_addr = signer::address_of(account);  
            assert!(  
                is_account_registered<CoinType>(account_addr),  
                error::not_found(ECOIN_STORE_NOT_PUBLISHED),  
            );  
      
            let coin_store = borrow_global_mut<CoinStore<CoinType>>(account_addr);  
            assert!(  
                !coin_store.frozen,  
                error::permission_denied(EFROZEN),  
            );  
      
            event::emit_event<WithdrawEvent>(  
                &mut coin_store.withdraw_events,  
                WithdrawEvent { amount },  
            );  
      
            extract(&mut coin_store.coin, amount)  
        }

As it is clear, there is an `assert` call in the beginning of the function to make sure that sender is a registered account. So, this is our first pre-condition.
    
    
    assert!(  
                is_account_registered<CoinType>(account_addr),  
                error::not_found(ECOIN_STORE_NOT_PUBLISHED),  
            );

If we move on, we can see another `assert` statement to make sure that the sender’s account is not frozen. That is our second pre-condition.
    
    
    assert!(  
                !coin_store.frozen,  
                error::permission_denied(EFROZEN),  
            );

Finally it calls the `extract` function and returns its output. Let’s explore this function too.
    
    
    /// Extracts `amount` from the passed-in `coin`, where the original token is modified in place.  
    public fun extract<CoinType>(coin: &mut Coin<CoinType>, amount: u64): Coin<CoinType>  
      assert!(coin.value >= amount, error::invalid_argument(EINSUFFICIENT_BALANCE));  
      coin.value = coin.value - amount;  
      Coin { value: amount }  
    }

There is an `assert` call in this function to make sure that the sender’s balance is sufficient enough which implies the third pre-condition.
    
    
    assert!(coin.value >= amount, error::invalid_argument(EINSUFFICIENT_BALANCE));

Finally it returns the transferred amount wrapped in a `Coin` structure. After this function it tries again to withdraw the wormhole message fee and the call another function named `transfer_tokens`.
    
    
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

The `transfer_tokens` function immediately calls another function named `transfer_tokens_internal`. Let’s take a look at this one:
    
    
    // transfer a native or wraped token from sender to token_bridge  
        fun transfer_tokens_internal<CoinType>(  
            coins: Coin<CoinType>,  
            relayer_fee: u64,  
        ): TransferResult {  
      
            // transfer coin to token_bridge  
            if (!coin::is_account_registered<CoinType>(@token_bridge)) {  
                coin::register<CoinType>(&state::token_bridge_signer());  
            };  
            if (!coin::is_account_registered<AptosCoin>(@token_bridge)) {  
                coin::register<AptosCoin>(&state::token_bridge_signer());  
            };  
      
            let amount = coin::value<CoinType>(&coins);  
            assert!(relayer_fee <= amount, E_TOO_MUCH_RELAYER_FEE);  
      
            if (state::is_wrapped_asset<CoinType>()) {  
                // now we burn the wrapped coins to remove them from circulation  
                wrapped::burn<CoinType>(coins);  
            } else {  
                coin::deposit<CoinType>(@token_bridge, coins);  
                // if we're seeing this native token for the first time, store its  
                // type info  
                if (!state::is_registered_native_asset<CoinType>()) {  
                    state::set_native_asset_type_info<CoinType>();  
                };  
            };  
      
            let origin_info = state::origin_info<CoinType>();  
            let token_chain = state::get_origin_info_token_chain(&origin_info);  
            let token_address = state::get_origin_info_token_address(&origin_info);  
      
            let decimals_token = coin::decimals<CoinType>();  
      
            let normalized_amount = normalized_amount::normalize(amount, decimals_token);  
            let normalized_relayer_fee = normalized_amount::normalize(relayer_fee, decimals_token);  
      
            let transfer_result: TransferResult = transfer_result::create(  
                token_chain,  
                token_address,  
                normalized_amount,  
                normalized_relayer_fee,  
            );  
            transfer_result  
        }

There is an interesting `if` statement in this function. The answers to the “why” questions can be found here: The coins (i.e. amount) **can be burned or transferred** based on the blockchain state. **So, that’s why we used the greater equal and less than equal sign to cover both paths!**
    
    
     if (state::is_wrapped_asset<CoinType>()) {  
                // now we burn the wrapped coins to remove them from circulation  
                wrapped::burn<CoinType>(coins);  
            } else {  
                coin::deposit<CoinType>(@token_bridge, coins);  
                // if we're seeing this native token for the first time, store its  
                // type info  
                if (!state::is_registered_native_asset<CoinType>()) {  
                    state::set_native_asset_type_info<CoinType>();  
                };  
            };

### Move Prover Generates Counterexamples

When using the move prover tool to veify the specifications, it will generate a counter example if it couldn’t prove it. This feature is very helpful for the developers to fix their function or the related specification.

In the above example, it makes sense that `relayer_fee` is a positive number. (**negative fee may cause an increase in the recipient’s balance**) so if we write a **wrong** specification let’s see what happens when it is passed to the move prover tool:
    
    
      spec transfer_tokens_entry {  
            ensures relayer_fee < 0;  
        }

It generates a large amount of long log messages tracing function calls, but the counterexample generated by the move prover tool is visible at the beginning of the error messages. This counterexample provides values for the function arguments that violate the written specification. (in this case `relayer_fee=0`)
    
    
    at ./sources/structs/transfer_result.move:20: destroy  
       =         a =  
       =           transfer_result.TransferResult{  
       =             token_chain = u16.U16{number = 0},  
       =             token_address =  
       =               external_address.ExternalAddress{  
       =                 external_address = vector{(size): 2, default: 0u8}},  
       =             normalized_amount =  
       =               normalized_amount.NormalizedAmount{  
       =                 amount = 18446744073709551615},  
       =             normalized_relayer_fee =  
       =               normalized_amount.NormalizedAmount{amount = 0}}

### Global Specifications

Pre, post, and aborts conditions are supported by the Move programming language, as we have already shown. Additionally, it allows invariants over data structures like resources as well as the contents of **the global memory, which is the blockchain’s state**.

The global storage would be something like the following pseudocode: [6]
    
    
    struct GlobalStorage {  
      resources: Map<(address, ResourceType), ResourceValue>  
      modules: Map<(address, ModuleName), ModuleBytecode>  
    }

Global invariants are particularly significant in the move specification language because the accuracy of smart contracts partly depends on the correctness of the blockchain state. [7]

As **a very simple example** , we can make sure that all account addresses have non-negative balance.
    
    
    spec module {  
            invariant forall a: address where exists<AptosCoin>(a):  
                coin::balance<AptosCoin>(a) >= 0;  
        }

We can also use invariants for the `struct` data type to control the range of values. Here is an example from the Diem blockchain framework. [8]
    
    
    /// A resource specifying the account limits per-currency. There is a default  
    /// "unlimited" `LimitsDefinition` resource for accounts published at`@DiemRoot`, but   
    /// accounts may have different account limit definitons. In such cases, they will have a  
    /// `LimitsDefinition` published under their (root) account.  
    struct LimitsDefinition<phantom CoinType> has key {  
      /// The maximum inflow allowed during the specified time period.  
      max_inflow: u64,  
      /// The maximum outflow allowed during the specified time period.  
      max_outflow: u64,  
      /// Time period, specified in microseconds  
      time_period: u64,  
      /// The maximum amount that can be held  
      max_holding: u64,  
    }  
    spec LimitsDefinition {  
      invariant max_inflow > 0;  
      invariant max_outflow > 0;  
      invariant time_period > 0;  
      invariant max_holding > 0;  
    }

### Conclusion

In conclusion, Formal specification in Move is a technique used to define the behavior and properties of smart contracts in a precise and mathematical language, which can be verified using formal verification tools. The process of installing the necessary programming languages, Rust and Move, and writing a simple specification for the `transfer_tokens_entry` function on the smart contract is also discussed. The use of formal verification can ensure that the function operates correctly, providing added security and reliability for the smart contract.

Also, this could be a revolution in writing correct and secure codes, but there are some major problems to be discussed, including:

  * How we can ensure the specifications themselves are correct?
  * How to make the task of writing complete and correct specifications easier or even automatic?
  * How to make the proving process faster?
  * And how to manage and verify specifications while considering previous and future states of the program?

### References

> All links were valid at the time of publishing this article (i.e. Jan 19, 2023)

  1. <https://aptoslabs.com/>
  2. <https://wormhole.com/>
  3. <https://medium.com/@seyyedaliayati/the-abc-of-formal-verification-with-move-906ac1b2aaa0>
  4. <https://www.rust-lang.org/learn/get-started>
  5. <https://github.com/move-language/move>
  6. <https://move-language.github.io/move/global-storage-structure.html>
  7. D. Dill, W. Grieskamp, J. Park, S. Qadeer, M. Xu, and E. Zhong, “Fast and reliable formal verification of smart contracts with the Move prover,” in International Conference on Tools and Algorithms for the Construction and Analysis of Systems, 2022, pp. 183–200.
  8. <https://github.com/diem/diem>