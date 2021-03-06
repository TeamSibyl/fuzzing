# Generation

This page covers techniques for generating test cases.

## Test case generation

There are a few commonly used ways to generate test cases from scratch:

1. Keyword generation: For text based protocols, good test cases can be created by randomly combining keywords, perhaps with other random identifies. Useful for things like SQL injection attacks.
2. CFG: Many tools take this a step further and define an entire grammar to be used to generate the test cases.
If the given grammar is well thought out, this can be extremely performant for things like database connections that require queries to follow a specific syntax to be understood.
However, this technique may miss many bugs caused by a failure of the system to handle malformed input, or to properly handle edge cases, or to sanitize input.
3. Brute-force: The simplest method is to run a random string generator and feed its input to the program. This was how the first fuzzers worked, and it still works on an alarmingly large number of targets.

## Mutation

Once some testcases have been generated, further test cases can be generated by mutating them.
Once again, there are quite a few techniques for this:
1. For binary protocols, random bit flips works surprisingly well.
2. For text protocols, a single random character can be removed or inserted at a random position.
3. If the test cases are generated based on a grammar, a rule may be temporarily modified, and the test cases regenerated, or a random identifier may be swapped with another one.
4. Many more intelligent fuzzers work through genetic algorithms.
Here, each test case is represented by a set of genes, organized into a chromosome.
Each test case is executed, and then based on the results, some are removed, some are mutated, and some are recombined with other testcases.
This allows us to efficiently explore the solution space without the risk of getting stuck at a local maxima/minima.
