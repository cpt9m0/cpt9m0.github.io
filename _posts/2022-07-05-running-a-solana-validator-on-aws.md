---
title: "Running a Solana Validator on AWS"
subtitle: "Step-by-step guide to deploying a Solana validator on Amazon Web Services (AWS)"
date: 2022-07-05
permalink: /posts/2022-07-05/running-a-solana-validator-on-aws/
categories:
  - Blockchain
    - Solana
tags:
  - Blockchain
    - Solana
    - Validator
    - AWS
excerpt: "Step-by-step guide to deploying a Solana validator on AWS for blockchain enthusiasts."
---

### Running a Solana Validator on AWS

#### An step-by-step guide to run a Solana validator instance in amazon web services a.k.a AWS

Solana is one of the fastest and underrated blockchains in the world, and its ecosystem is overgrowing in crypto areas such as Web3, Defi, NFTs, and more.

In this article, we will discuss the validator architecture in Solana. Moreover, we will get out hands dirty and run a Solana validator on an AWS EC2 instance.

**NOTE: This is a technical article and not an investing advise!**

#### How Does a Blockchain Works in Simple Words

Having minimum knowledge about blockchain is necessary for reading and using this article. If you are familiar with the concepts of a blockchain, you are free to skip this part.

![](https://cdn-images-1.medium.com/max/800/1*AuFgH78eqW8HUcPQ5Jz2DA.jpeg)Fig 1. How a blockchain works [1].

Suppose Ali wants to send some money to Sarah (see Fig 1). The data of this transaction is represented online as a block. The block is broadcast to every party in the network, and those in the network validate the transaction (validators). After the block is validated, it can be added to the chain (The money moves from Ali to Sarah). This chain provides an indelible and transparent record of the transaction. That’s why it is called blockchain (chain of blocks!).

The first concept to get familiar with is _transaction_. A transaction is a set of instructions signed by the client. A transaction is executed atomically with only two possible outcomes: success or failure.

A _ledger_ is like a bank or a database for keeping records of clients’ transactions. So a ledger is a list of entries containing transactions that the clients sign.

> New to trading? Try [crypto trading bots](https://medium.com/coinmonks/crypto-trading-bot-c2ffce8acb2a) or [copy trading](https://medium.com/coinmonks/top-10-crypto-copy-trading-platforms-for-beginners-d0c37c7d698c)

#### Solana Validator

As mentioned earlier, a validator is simply a computer instance that validates the transactions added to the ledger. A set of validators that maintain a ledger is called a cluster.

Solana validator uses _pipelining_ , an optimization standard in CPU design. It is the right tool when a stream of input data needs to be processed in a sequence of steps, and different hardware is responsible for each step [2].

![](https://cdn-images-1.medium.com/max/800/1*_Qf4rg9c1Uzq5D7BzZ_jPA.png)Fig 2. The anatomy of a validator [2].

Solana validator has two pipeline processes: TVU and TPU. TPU stands for Transaction Processing Unit. It creates ledger entries, and it is used in leader mode, and TVU is for validating entries.

#### Preparing Virtual Machine

You can install and run Solana validator on any bare server or virtual machine on a cloud platform like AWS. You will need a 12 cores CPU with 24 threads or more, at least 128 GB of RAM, Ubuntu 20.04, and a cuda-supported GPU [3].

Before you begin, ensure you have installed the Solana command-line tools. It can be installed via the following command on macOS or Linux [4]:
    
    
    sh -c “$(curl -sSfL https://release.solana.com/v1.10.29/install)"

The installation can be tested by running `solana --version`.

Since we are going to connect our validator to the Devnet cluster, it is required to set configs via the `--url` argument:
    
    
    solana config set — url <http://api.devnet.solana.com>

By fetching the transaction count via `solana transaction-count `we can make sure that the cluster is reachable.

#### Tuning The System

Before running the validator, it is necessary to adjust some system settings for optimization purposes (increasing OS UDP buffer and file mapping limits). The whole process can be done automatically by a daemon called `solana-sys-tuner` which is included in the Solana binary release [5].

To run it:
    
    
    sudo $(command -v solana-sys-tuner) --user $(whoami) > sys-tuner.log 2>&1 &

#### Generating Identities

Creating an identity keypair for the validator:
    
    
    solana-keygen new -o ~/validator-keypair.json

Your validator identity keypair uniquely identifies your validator within the network. **It is crucial to back-up this information.**

If you don’t back up this information, you WILL NOT BE ABLE TO RECOVER YOUR VALIDATOR if you lose access to it. If this happens, YOU WILL LOSE YOUR ALLOCATION OF SOL TOO.

To back-up your validator identify keypair, **back-up your “validator-keypair.json” file or your seed phrase to a secure location.**

So far so good, now it is time to set the Solana configuration to use the generated keypair:
    
    
    solana config set --keypair ~/validator-keypair.json

The output should be similar to the following:
    
    
    Config File: /home/solana/.config/solana/cli/config.yml  
    RPC URL: <http://api.devnet.solana.com>  
    WebSocket URL: ws://api.devnet.solana.com/ (computed)  
    Keypair Path: /home/solana/validator-keypair.json  
    Commitment: confirmed

Next step is to airdrop some SOLs for getting started. Note that this airdrop is only available on the Devnet and Testnet:
    
    
    solana airdrop 1

We also need a withdrawer and a vote account. The withdrawer account has the authority to withdraw from your vote account and it can change all aspects of your vote account. So it is very important!
    
    
    solana-keygen new -o ~/authorized-withdrawer-keypair.json

Vote account:
    
    
    solana-keygen new -o ~/vote-account-keypair.json

#### Connect and run the validator

Now we are ready to connect or validator to the cluster by:
    
    
    solana-validator \  
      --identity ~/validator-keypair.json \  
      --vote-account ~/vote-account-keypair.json \  
      --rpc-port 8899 \  
      --entrypoint entrypoint.devnet.solana.com:8001 \  
      --limit-ledger-size \  
      --log ~/solana-validator.log

The validator will automatically log to a file (solana-validator.log in this case).

If the validator is connected to the network its IP address and public key will appear in the following list:
    
    
    solana gossip

Finally, please do not stop here and make sure to read the official documentation for getting more options and accurate information [6].

#### References

*All links were valid at the time of publishing this article (July 05, 2022).

  * [1] <https://www.pinterest.com/pin/654218283351673725/>
  * [2] <https://docs.solana.com/validator/anatomy>
  * [3] <https://developer.nvidia.com/cuda-gpus>
  * [4] <https://docs.solana.com/cli/install-solana-cli-tools>
  * [5] <https://docs.solana.com/running-validator/validator-start#system-tuning>
  * [6] <https://docs.solana.com/running-validator/validator-start>