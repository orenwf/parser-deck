---
marp: true
---
# LL (top-down) vs. LR (bottom-up) Parsers:

### What, when and why one should care.

## Oren Friedman

---
# TLDR:
- LL: top down, left recursive
- Worst case exponential complexity, but really NP
- LR: bottom up, right recursive, Polynomial
- you probably don't care unless you do A LOT of parsing
- but it's kind of interesting to understand when and why you might and why things are the way they are

---
# Overview of Top Down Parsing
## Worst case complexity $O(k^n)$
Exponential growth

---
# Overview of Bottom Up parsing
Polynomial growth in the worst case
## If CYK is $O(n^3)$ why do people write Top Down Parsers?

---
# Why are GCC, Clang, Javac etc all LL parsers?
Show some examples of the parsers here
- Almost all modern compiler infrastructures for popular industrial languages use LL parsing?
- This seems to fly in the face of what we have learned about computational complexity
- TLDR: It's because if you want to write a bottom up LR parser, it's too god damned hard to write it by hand

---
# First some background on LR parsers
- Why we need parser generators for practical LR parsers
- Overview of how they work

---
# Some examples of LR parser generator tools
Yacc, Bison, PLY, PGen, Lark, etc
- You literally would not ever see something like your lexer/parser that you wrote in your programming languages class in real life compilers
- you know:
`while(true) { switch(input) { case 'a': do_stuff(); ... }}`

---
# You feed it a EBNF or PEG grammar and it gives you the world
- These tools are amazeballs, but they have their limitations
- Hard to get good error messages
- Hard to reason about intuitively to do hand optimization which is sometimes the only way

---
# Therefore, we end up with LL parsing tools written by hand
- Top down parsing is easier to reason about
- That's good, if you're writing it by hand
- Writing it by hand lets you hand-roll optimizations
- Let's you really get down and dirty with edge cases
  - optimizations
  - compiler error messages (love tears god emoji)

---
# There also exist some LL parser generator tools
Antlr

---
# Mind Blown? There are also LR parser generator tools that fix the old problems
It's all kind of a moot point because...

---
# SEMANTIC ANALYSIS IS HARD
- typically also exponential in the size of the input?
- or at least probabilistically polynomial with a very high K while LL parsing can really be NP polynomial
- Semantic analysis is extremely important for your typical C style programming language
  - building your attribute grammar
  - checking type correctness
  - memory safety checking by the compiler (Rust)
  - garbage collection algorithms
## Who really gives a damn whether the parser is $O(n^3)$?

---
# So where does LL vs. LR parsing really matter?
- It probably doesn't in your life
- Maybe in small simple languages
  - query languages
  - DSL's
  - Markup/Down/Around languages
- Things that get processed repetitively over and over
- Not giant C++ programs that get distributed as binaries
- You get a fast computer, compile it and forget about it
- Who cares if it's $O(K^N)$?