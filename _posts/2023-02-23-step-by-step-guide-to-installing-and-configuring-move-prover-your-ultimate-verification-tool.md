---
title: "Step-by-Step Guide to Installing and Configuring Move Prover: Your Ultimate Verification Tool"
date: 2023-02-23
permalink: /posts/2023-02-23/step-by-step-guide-to-installing-and-configuring-move-prover-your-ultimate-verification-tool/
categories:
  - Blockchain
  - Blockchain
subtitle: "Your Ultimate Verification Tool for Secure Blockchain Development"
excerpt: "Unlock the Full Potential of Move Prover for Robust and Secure Blockchain Development with This Comprehensive Installation and…"
canonical_url: https://medium.com/@seyyedaliayati/step-by-step-guide-to-installing-and-configuring-move-prover-your-ultimate-verification-tool-2bd807a837cb
categories:
  - Blockchain
  - Move
tags:
  - Move Prover
  - Smart Contracts
  - Formal Verification
excerpt: "Comprehensive guide to installing and configuring Move Prover for secure smart contract development."
---

As the demand for secure and trustworthy blockchain systems continues to grow, the need for effective tools to verify the correctness of smart contracts becomes increasingly important. Move Prover is a powerful tool that has gained widespread popularity for verifying the safety and correctness of smart contracts written in the Move programming language. This step-by-step guide aims to provide a comprehensive walk-through of the process of installing and configuring Move Prover, giving you the necessary knowledge and skills to utilize this ultimate verification tool to ensure the safety and reliability of your smart contract code.

### List of Contents

  * Installation
  * Configuration
  * Conclusion
  * References

### Step-by-Step Installation

  1. Install the Rust compiler:

    
    
    curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

2\. Clone Move source code:
    
    
    git clone https://github.com/move-language/move.git

3\. Install Move command line interface (a.k.a CLI):
    
    
    cargo install --path move/language/tools/move-cli

4\. [Optional] Enable features for the installation process:
    
    
    cargo install --path move/language/tools/move-cli --features "address32"

Here is a list of all available features:
    
    
    [features]  
    evm-backend = ["move-unit-test/evm-backend", "move-package/evm-backend"]  
    address20 = ["move-stdlib/address20"]  
    address32 = ["move-stdlib/address32"]  
    table-extension = ["move-table-extension", "move-unit-test/table-extension"]

If you need to install all features you can run the following command:
    
    
    cargo install --path move/language/tools/move-cli --all-features

5\. Test you installation:
    
    
    move --version

You should get a version like:
    
    
    move-cli 0.1.0

### Step-by-Step Configuration for Move Prover

There are two ways to apply a configuration.

  1. Pass the config arguments to the CLI:

    
    
    move prove -- --dump-cfg --dump-bytecode

This configuration tells the prover to keep intermediate byte-codes and control flow graphs.

2\. Write a Prover.toml file in the same directory as the Move.toml (i.e. the root directory), and just run `move prove:`
    
    
    [prover]  
    dump_bytecode=true  
    dump_cfg=true

There are two sets of configurations for the move prover: one for the prover itself, and the other for the back-end SMT solver engine (i.e. boogie).

Unfortunately, there is no official documentation that lists and explains these sets of configurations. To extract them, I had to dive into the `move-cli` source code and read the Rust code to figure out what the configurations are and what their default values are!

Let’s explore each sets of configurations respectively:

#### Move Prover Configurations

In the following GitHub gist you can find the prover options and their default values:

Configuration and default values for the Move Prover

#### Move Prover Back-End Configurations

In the following GitHub gist you can find the prover’s back-end (i.e. boogie) options and their default values:

#### How to Use The Above Gists

Let’s imagine that you want the Move Prover’s back-end keep the SMT files for you. You just need to add the following snippet to your `Prover.toml` file:
    
    
    [backend]  
    ...  
    generate_smt=true

### Conclusion

In conclusion, Move Prover is a powerful tool that can help developers ensure the safety and correctness of their smart contract code. Through this step-by-step guide, we have provided a comprehensive walk-through of the process of installing and configuring Move Prover, giving you the necessary knowledge and skills to start verifying your smart contracts with confidence.

By using Move Prover, you can minimize the risk of vulnerabilities and bugs in your smart contracts, which is crucial in building secure and trustworthy blockchain systems. As the blockchain ecosystem continues to evolve, having the right tools and skills is essential to stay ahead of the curve, and Move Prover is definitely a tool that should be in your arsenal. So, what are you waiting for? Install Move Prover and start verifying your smart contracts today!

### References

  * <https://www.rust-lang.org/tools/install>
  * <https://github.com/move-language/move>
  * <https://github.com/MystenLabs/awesome-move>
  * <https://gist.github.com/seyyedaliayati/f8347501a39f660edc85232f0a11a060>
  * <https://gist.github.com/seyyedaliayati/030c9857f99763b178b33e7b5487d697>