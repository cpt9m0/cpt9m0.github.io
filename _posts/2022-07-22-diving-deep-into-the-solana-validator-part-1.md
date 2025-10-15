---
title: "Diving Deep into the Solana Validator — Part 1"
subtitle: "Exploring Solana validator source code and architecture — Part 1"
date: 2022-07-22
permalink: /posts/2022-07-22/diving-deep-into-the-solana-validator-part-1/
categories:
  - Blockchain
    - Solana
tags:
  - Blockchain
    - Solana
    - Validator
excerpt: "Exploring Solana validator source code and architecture in depth."
---

### Diving Deep into the Solana Validator — Part 1

#### Reading the source code of Solana validator (Part 1).

![](https://cdn-images-1.medium.com/max/800/1*lffPx7NM_R1p-58sIEQrxw.png)Pipelining in TPU. Image belongs to [the solana labs](https://medium.com/solana-labs/pipelining-in-solana-the-transaction-processing-unit-2bb01dbd2d8f).

[In the previous article](https://medium.com/@seyyedaliayati/running-a-solana-validator-on-aws-bb86162eaf29), we talked about Solana validator and running it on a cloud platform like AWS. In this article, we will get deeper into the source code of the Solana validator[1] and see what each thread does precisely.

> **Note:** This is a technical article, not an investment advise!

Speaking of source code, Solana is written in the _Rust_ programming language. _Rust_ is designed for performance and safety, exceptionally safe concurrency, and memory management. Rust is used in many companies worldwide, including Solana, Mozilla, Dropbox, and more. As many developers recommend, the best way to learn Rust is to read the tutorial book written by the core developers of Rust [2].

> **Note:** Having a general understanding of the Rust programming language is recommended.


### Getting Familiar with Multi-threading in Rust

A thread is created by calling `std::thread::spawn`. It is a function that takes a _closure_. A _closure_ is an anonymous function that can be saved in a variable or passed as an argument to other functions. Note that when the main thread of a Rust program completes, all spawned threads are shut down, whether or not they have finished running.

Calling `std::thread::spawn` returns a `JoinHandle` instance that has a `join `method. By calling that method the current thread will wait until the spawned thread is closed and then continue executing. Here is an example of threads and joining them [3]:

Gist 1. An example of threads and join in Rust

By executing this script your output will be something similar to the following output:
    
    
    hi number 1 from the main thread!  
    hi number 2 from the main thread!  
    hi number 1 from the spawned thread!  
    hi number 3 from the main thread!  
    hi number 2 from the spawned thread!  
    hi number 4 from the main thread!  
    hi number 3 from the spawned thread!  
    hi number 4 from the spawned thread!  
    hi number 5 from the spawned thread!  
    hi number 6 from the spawned thread!  
    hi number 7 from the spawned thread!  
    hi number 8 from the spawned thread!  
    hi number 9 from the spawned thread!

So, if you put `join` function before the for loop, the main thread will wait for other threads to finish their job and join the main thread. As a result, you will see the “from the main thread!” lines after “from the spawned thread!” lines.
    
    
    hi number 1 from the spawned thread!  
    hi number 2 from the spawned thread!  
    hi number 3 from the spawned thread!  
    hi number 4 from the spawned thread!  
    hi number 5 from the spawned thread!  
    hi number 6 from the spawned thread!  
    hi number 7 from the spawned thread!  
    hi number 8 from the spawned thread!  
    hi number 9 from the spawned thread!  
    hi number 1 from the main thread!  
    hi number 2 from the main thread!  
    hi number 3 from the main thread!  
    hi number 4 from the main thread!

### Multi-threading in Solana Validator

Solana is mainly known for high-speed and low fees. Solana is now able to process 60,000 transactions per second (TPS). While reading this sentence, about 150,000 transactions were processed by Solana’s validators around the world (WoW!). Moreover, Solana claims that theoretically, it is possible to reach 710,000 TPS, making Solana extremely fast [4].

If you want to estimate how many threads a Solana validator runs, you will need to run one on your system. Instead of getting our hands dirty by running an actual validator, we can just use the test validator provided by the Solana team.

> During early stage development, it is often convenient to target a cluster with fewer restrictions and more configuration options than the public offerings provide. This is easily achieved with the `solana-test-validator` binary, which starts a full-featured, single-node cluster on the developer’s workstation.

Run the test validator (Read docs [here](https://docs.solana.com/developing/test-validator)):
    
    
    solana-test-validator

Open a new terminal and grap the PID (Process ID) for the test validator:
    
    
    ps ax | grep solana-test-validator

Find the number of light weight processes (a.k.a threads):
    
    
    ps -o nlwp <PID of the test validator>
    
    
    // Output:
    
    
    NLWP  
     201

In my system, the output(number of threads) is about 200. However, what are these threads and what do they do?

I believe Solana owes its speed to the PoH (i.e., Proof of History) system and the programming language (i.e., Rust), and their software architecture. In the next section we are going to dive deep into the TPU source code and see what is the job of each thread.

#### Transaction Processing Unit (TPU)

In the previous article, it is declared that the Solana validator uses a CPU design technique called _pipeline_. A _pipeline_ is a set of data processing elements connected in series, where the output of one element is the input of the next one.

> Solana validator has two pipeline processes: TVU and TPU. TPU stands for Transaction Processing Unit. It creates ledger entries, and it is used in leader mode, and TVU is for validating entries.

![](https://cdn-images-1.medium.com/max/800/1*S-nvh5w27HvO81cXK-gYTw.png)Fig 1. The anatomy of TPU [5]

The main responsibility of a TPU is _block production_. It contains a multi-stage process including Fetch, SigVerify, Banking, and Broadcast stages.

Gist 2. Solana TPU Structure

#### TPU: Fetch Stage

> The fetch stage allocates packet memory and reads the packet data from the network socket and applies some coalescing of packets received at the same time.

Gist 3. Fetch stage in Solana TPU [6]

The fetch stage includes three main categories of threads: Receiving packets, receiving forwarded packets, and receiving votes. There are also two other threads in the fetch stage. One is for handling forwarded packets, and another is for reporting metrics and stats.

#### TPU: SigVerify Stage

> The sigverify stage de-duplicates packets and applies some load-shedding to remove excessive packets before then filtering packets with invalid signatures by setting the packet’s discard flag.

The SigVerify stage actually is two stages. One is for verifying the signatures of transactions and the other one is for verifying votes.

Gist 4. SigVerify stagesGist 5. SigVerify stage structure

As you can see in Gist 5, The process of verifying packets are done in batches in a single thread. The main process happens in the `verifier` function [7]. Here are the steps:

  1. `process_received_packet`
  2. `discard_excess_packets`
  3. `verify_batches`
  4. `process_passed_sigverify_packet`
  5. `send_packets`

#### TPU: Banking Stage

> The banking stage decides whether to forward, hold or process packets received. Once it detects the node is the block producer it processes held packets and newly received packets with a Bank at the tip slot.

In this stage, at least three threads will be created (since `MIN_TOTAL_THREADS `is 3) . Based on the system specs and user input it can create more threads.
    
    
    const NUM_VOTE_PROCESSING_THREADS: u32 = 2;
    
    
    const MIN_THREADS_BANKING: u32 = 1;
    
    
    const MIN_TOTAL_THREADS: u32 = NUM_VOTE_PROCESSING_THREADS + MIN_THREADS_BANKING;

Gist 6. TPU Banking Stage

These threads process transactions in parallel. They talk to `poh_service` and broadcasts the entries once they have been recorded and once an entry has been recorded, its block-hash is registered with the bank.

#### TPU: Broadcasting Stage

> The broadcast stage receives the valid transactions formed into Entry’s from banking stage and packages them into shreds to send to network peers through the turbine tree structure. Serializes, signs, and generates erasure codes before sending the packets to the appropriate network peer.

There are four types of broadcasting. The only difference is the runner that the stage will run:
    
    
    pub enum BroadcastStageType {  
        Standard,  
        FailEntryVerification,  
        BroadcastFakeShreds,  
        BroadcastDuplicates(BroadcastDuplicatesConfig),  
    }

The first thread is the **sender** socket and calls the runner of the broadcast type. The second group of threads is broadcaster **transmit** sockets. The number of threads is equal to the number of UDP input sockets. The third group of threads is broadcast **recorders**. The number of threads in this group is equal to the number of insert threads (`NUM_INSERT_THREADS`=2). Furthermore, the final thread is the broadcaster **re-transmit** thread. In Gist 7, you can see each of these groups.

Gist 7. Banking stage threads

### References

All links were valid on the date of publishing this article. (July 22, 2022)

  * [1] <https://github.com/solana-labs/solana>
  * [2] <https://doc.rust-lang.org/book/title-page.html>
  * [3] <https://doc.rust-lang.org/book/ch16-01-threads.html>
  * [4] <https://www.nansen.ai/research/solana-scalability-through-speed>
  * [5] <https://docs.solana.com/validator/tpu>
  * [6] <https://github.com/solana-labs/solana/blob/master/core/src/tpu.rs>
  * [7] <https://github.com/solana-labs/solana/blob/2acb6c37a52b4d36a2798cdace10ffff43e4d49c/core/src/sigverify_stage.rs#L292>