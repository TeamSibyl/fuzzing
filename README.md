# Fuzzing
A brief overview of fuzzing.

## What is fuzzing anyway?

>Fuzzing or fuzz testing is an automated software testing technique that involves
>providing invalid, unexpected, or random data as inputs to a computer program.
>
> -Wikipedia

Fuzzing is esseantially a brute-force technique that bombards the target program/system with malformed input with the goal of detecting an edge case, memory allocation issue, or other type of bug.


## Components

A fuzzer generally consists of three parts:

 ### Test case generator

The test case generator creates test cases for the engine to run. It is only concerned with the test case itself, and not in how it is executed.
Often, the test case generator has little to no information on the attack surface.

There are quite a few viable techniques for generating these test cases.
These techniques vary mostly in terms of intelligence; does the generator operate according to a ruleset/grammar/whatever, or does it simply generate random strings?

There are quite a few concerns to be aware of when creating test cases:

1. For many attack surfaces, the solution space is very large(practically infinite in many cases). In these cases, (dumber) brute-force approaches may not perform well.
2. Many communication protocols are quite stringent, and require exchanges in a certain format(such as key exchanges or handshakes), and they will kick (or ban) a client that refuses to comply. In these cases, the generator needs to follow some rules.
3. On the other hand, if the test cases conform _too_ stringently to the protocol, they may miss errors caused by the server being unable to correctly handle misbehaving clients.
4. Many attack targets depend on or are supported by other software. Usually, this software is either resistant to bugs, or is simply not of any interest.
This can complicate the process of tailoring test cases to focus on the target software, and in the detection of bugs.
For example, most websites run on top of Apache.
Even if you can get the website itself to crash(which is frighteningly easy in most cases), Apache itself will not crash. If your program detects errors by listening to the Apache logs, it may miss the failures entirely.

Test-case generators are usually paired with a mutation engine, which allows it to generate new test cases from previous ones.

## Detector (a.k.a Monitor)

The detector tries to detect if an error has occurred, after the execution of a test case.
In general, detectors are split into categories based on their awareness of the source code of the fuzz target:

- Black box: These detectors have no knowledge whatsoever of the structure of the target program/system. They can only detect bugs by either checking the program's logs and whether or not it crashed. These detectors tend to be fast, simple, and widely applicable.
- White box: These detectors use static analysis to better understand the program.
They can detect potentially troublesome parts of the code and automatically generate test cases meant to test those bits of code.
These detectors tend to have the highest code coverage, but are (prohibitively) slow and expensive to run.
- Grey box: These detectors function similar to black-box, but they watch the running program more closely.
One common technique is to run the program in a debugger.
This gives the detector access few useful tools such as watches and breakpoints.

Detectors also vary based on whether the target is running locally or remotely, and whether or not the target system/program has multiple processes/components.

## Fuzzing engine
The fuzzing engine is the core of the entire fuzzer.
It controls the entire process, and is primarily responsible for executing testcases and recording the results.
It decides whether the testcase succeed in finding a bug, based on the feedback from the detector, and then decides what to do with that result.
Some fuzzers will try to mutate the testcase and then analyze all the testcases that produced the bug to try to discover the pattern.
If the fuzzer is distributed, the engine coordinates all the different processes.
