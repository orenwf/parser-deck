---
marp: true
---
# LL(k) (top-down) vs. LR(k) (bottom-up) Parsers:
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
# Top-down aka Recursive Descent Parsing
![w:700](https://ruslanspivak.com/lsbasi-part7/lsbasi_part7_parsetree_01.png)


---
# Top-Down aka Recursive Descent Parsing
- you don't have to think hard to see how it works
- mimics generating a context free string
- take a start symbol, try productions down the parse tree until they termintate
- Given a string of non-terminals, take the leftmost and apply the production
- match your parse tree leaves with the input string
- if it doesn't match, rewind the subtree and try a different derivation
- Left-recursive grammars can't be handled
- Worst case complexity $O(k^n)$

---
# Top-Down parsing with look-ahead
- Naive backtracking recursive descent is potentially so expensive
- $LL(k)$ parsers use $k$-symbol look-ahead to remove ambiguity
1. The stack-top `T` is the start symbol and $0<i = k$
2. `Input` is the next $k-i+1$ text symbols
  - If `T == Input`, pop the stack top and eat the input
3. Otherwise look up `(T, Input)` pair in a rule table
  - If a rule exists such that `(T, Input) -> P`, stack top `T = P` and `i = k`
  - Otherwise, `i = i-1; GOTO 2`
### Still possible to have exponential complexity with blow-up in look up table size (think about an adversarially designed input string)
[Reference](https://en.wikipedia.org/wiki/LL_parser#Concrete_example)

---
# Bottom Up parsing ( $LR(k)$ )
- We saw this in Hopcroft 7.4: the Cocke, Younger & Kasami algorithm
- The parser scans the entire input string in one forward pass
- The stack begins empty and Non-terminals are added that correspond to the current input
- Unlike in top-down (LL) parsing, decisions are made not just based on the stack-top/input pair, but on the **entire** stack and the input
- At each step, the stack symbols are reduced down to keep the stack size small
- This process is known as "shift-reduce" and it is essentially what CYK does
- Lookahead of up to $k$ symbols is also used to handle grammatical ambiguity
- [Reference](https://en.wikipedia.org/wiki/LR_parser#LR_parse_steps_for_example_A*2_+_1)
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