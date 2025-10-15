---
title: "Finding race conditions in Git with Coderrect"
date: 2021-09-05
permalink: /posts/2021-09-05/finding-race-conditions-in-git-with-coderrect/
categories:
  - Blog
tags:
  - Blog
excerpt: "As you read this article, about 3000 threads are running on your device simultaneously to give you a fast and real-time experience using…"
canonical_url: https://medium.com/@seyyedaliayati/finding-race-conditions-in-git-with-coderrect-b57f5fe5abb4
---

### Finding race conditions in Git with Coderrect

![](https://cdn-images-1.medium.com/max/800/1*OmkCEugB_Bp3AiwHcNOydg.png)Image from <https://devopedia.org>

As you read this article, about 3000 threads are running on your device simultaneously to give you a fast and real-time experience using applications, including your browser! Today’s software must make decisions fast in numerous applications. Parallel programming in C/C++ and multi-threading are the best ways to accomplish this.

In today’s story, we will get familiar with race conditions, their challenges, and **a fantastic tool called Coderrect, which helps software developers and engineers to find and locate those in their codes quickly.**

### Race Conditions and Their Challenges

Almost all programs and web sites today use “multi-threaded” processing, which allows them to do numerous activities at the same time. While this allows applications to run much faster, it also increases the risk of mistakes if multiple processes (or “threads”) attempt to access the same data at the same time.

A race condition occurs when two or more threads have access to the same data and try to update it simultaneously. You don’t know the order in which the threads will attempt to access the shared data because the thread scheduling algorithm can switch between threads at any time. As a result, the outcome of the data change is determined by the thread scheduling method, which means that both threads are “racing” to access/alter the data. The following code snippet in C++ illustrates this concept very well:

Simple pthread race condition in C++

The global variable `x`is accessed with two threads. The final value of `x` can be 1 or 2 based on the ordering of threads. You may think this is very easy to detect, but what if this code snippet exists in a large project with more than 100 files and 1000 lines of codes. Can you find it easily?

### Coderrect: An Advanced Analyzer for Race Condition

[Coderrect ](https://coderrect.com/)is an advanced static analyzer for race conditions in C/C++. It is both fast and scalable, which is suitable for complex software. This command-line tool runs on Linux-based operating systems, and installing it is pretty straightforward.
    
    
    $ wget https://public-installer-pkg.s3.us-east-2.amazonaws.com/coderrect-linux-1.1.3.tar.gz  
    $ tar xf coderrect-linux-1.1.3.tar.gz  
    $ export PATH=$PWD/coderrect-linux-1.1.3/bin:$PATH

Consider the mentioned example `pthread-race.cc` , To start the analysis on a single file like this, run the following command:
    
    
    coderrect -t g++ pthread-race.cc -lpthread

The `-t` option makes coderrect to provide a summary of results in the console, and the `g++ pthread-race.cc -lpthread` is the command that compiles the source code and generates a `a.out` file. After executing the command, we get:
    
    
    Coderrect 1.1.3 build 1630604567
    
    
    Analyzing /data/a.out ...  
    Linking a.out.bc 100% |████████████████████████████████| (3/3, 59 it/s)  
     - ✔ [00m:00s] Loading IR From File                      
     - ✔ [00m:00s] Running Compiler Optimization Passes                                      
     - ✔ [00m:00s] Running Pointer Analysis                          
     - ✔ [00m:00s] Building Static Happens-Before Graph                                      
     - ▖ [00m:00s] Detecting Races            
    ==== Found a race between:   
    line 12, column 5 in Downloads/pthread-race.cc AND line 37, column 37 in Downloads/pthread-race.cc  
    Static variable:   
    x at line 7 of Downloads/pthread-race.cc  
     7|int x = 0;  
    Thread 1 (write):   
     10|    long tid;  
     11|    tid = (long)threadid;  
    >12|    x++;  
     13|    cout << "Hello World! Thread ID, " << tid << endl;  
     14|    pthread_exit(NULL);  
    >>>Stack Trace:  
    >>>main  
    >>>  load_data_in_thread [Downloads/pthread-race.cc:34]  
    >>>    pthread_create [Downloads/pthread-race.cc:20]  
    >>>      PrintHello [Downloads/pthread-race.cc:20]  
    Thread 2 (read):   
     35|    pthread_join(thread1, 0);  
     36|    // pthread_join(thread2,0);  
    >37|    cout << "Final value of x: " << x << endl;  
     38|}  
    >>>Stack Trace:  
    >>>main  
     - ✔ [00m:00s] Detecting Races                 
     - ✔ [00m:00s] Scanning for additional OpenMP Regions
    
    
    ----------------------------The summary of races in a.out------------------------
    
    
    1 shared data races
    
    
    To check the race report in your browser, run "browse /data/.coderrect/report/index.html"
    
    
    Any feedback? please send them to [feedback@coderrect.com](mailto:feedback@coderrect.com), thank you!

Let’s check the generated report:

![](https://cdn-images-1.medium.com/max/800/1*Pnkysk3Y_gSV0PpWoBRNng.png)Generated report by Coderrect

As you can see, the Coderrect exactly tells the developer where to find the race condition.

### Run Coderrect on a Real-World Project: Git

[Git](https://github.com/git/git/) is a fast, scalable, distributed revision control system with a vibrant command set providing high-level operations and full access to internals.

Try to clone the repository and run `make` to compile and build the project. When everything is ready, run the following command to start analysis:
    
    
    coderrect make

It can take up to 5 minutes for compiling and 2 minutes for finishing the analysis. You can find the report at `<project_dir>/.coderrect/report/` .

### Are All Results Reliable?

Although this tool gives you the information for tracing back, checking all detected race conditions one by one is tedious. After reviewing and tracing back one of the results randomly, it is conclusive that both threads access this unprotected shared variable.

![](https://cdn-images-1.medium.com/max/800/1*g5TX6lV1M944dzhnnNI5Qg.png)Generated report by Coderrect on Git

For answering this question, it is much better to find out how Coderrect works because if its algorithms are valid, we can conclude that detected race conditions are correct.

Coderrect builds LLVM bitcode (BC) files as an intermediate representation of your source code and then does advanced static analyses on them to discover potential race situations.**Check out the references for further information.**

### Conclusion

The speed, depth, and accuracy are the best features of Coderrect, but reliability is a concern as this tool uses static analysis. Because there is no understanding of the developer’s intent, and there is no knowledge of the program’s flow in static analysis; however, I recommend using this meanwhile you code or before pushing your codes on the production server.

### References

  * <https://coderrect.com/>
  * <https://coderrect.com/olddocs/quick-start/>
  * <https://coderrect.com/tutorials/>
  * Bozhen Liu and Jeff Huang. D4: Fast Concurrency Debugging with Parallel Differential Analysis, D4: Fast Concurrency Debugging with Parallel Differential Analysis, Proceedings of the ACM SIGPLAN Conference on Programming Language Design and Implementation (PLDI), pages 359–373, 2018.
  * Bozhen Liu, Jeff Huang, and Lawrence Rauchwerger. Rethinking Incremental and Parallel Pointer Analysis, ACM Transactions on Programming Languages and Systems (TOPLAS), vol. 41, no. 1, pages 6:1–6:31, 2019.
  * Bradley Swain, Yanze Li, Peiming Liu, Ignacio Laguna, Giorgis Georgakoudis, and Jeff Huang. OMPRacer: A Scalable and Precise Static RaceDetector for OpenMP Programs, Proceedings of the International Conference for High Performance Computing, Networking, Storage, and Analysis (SC), 2020.