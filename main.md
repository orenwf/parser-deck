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
## A language of arithmetic expressions

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
- [We saw this in Hopcroft 7.4: the Cocke, Younger & Kasami algorithm](https://en.wikipedia.org/wiki/CYK_algorithm#/media/File:CYK_algorithm_animation_showing_every_step_of_a_sentence_parsing.gif)
![w:900](https://i.ytimg.com/vi/rrlaVty0_iQ/maxresdefault.jpg)

---
# LR(k) parsing
- The parser scans the entire input string in one forward pass
- The stack begins empty and Non-terminals are added that correspond to the current input
- Unlike in top-down (LL) parsing, decisions are made not just based on the stack-top/input pair, but on the **entire** stack and the input
- At each step, the stack symbols are reduced down to keep the stack size small
- This process is known as "shift-reduce" and it is essentially what CYK does
- Lookahead of up to $k$ symbols is also used to handle grammatical ambiguity
- [Reference](https://en.wikipedia.org/wiki/LR_parser#LR_parse_steps_for_example_A*2_+_1)
## If CYK is $O(n^3)$ why do people write Top Down Parsers?

---
# The plot so far:
- LR parsers always do their work in polynomial time.
- LL parsers are at best NP and at worst Exponential.
## So why do almost all modern compiler infrastructures for popular industrial languages use LL parsing?
- GCC (G++), Clang, Go, Javac, etc...
- GCC actually used to use a LR parser but [switched](https://gcc.gnu.org/wiki/New_C_Parser)
## This seems to fly in the face of what we have learned about computational complexity

---
# TLDR:  It's because if you want to write a bottom up LR parser, it's too god damned hard to write it by hand

- [Recursive Descent C Compiler](https://github.com/gcc-mirror/gcc/blob/ad9db543abb523cd97f1aa5af78a72188c01aa6e/gcc/c/c-parser.c#L2615-L2900)

### It's not that complicated, basically what you wrote in your PL class
- you know:
```
while(true) { 
    switch(input) { 
        case 'a': do_stuff(); ... 
    }
}
```
- [Compare that to a $LR(3)$ C compiler](https://github.com/gcc-mirror/gcc/blob/a02b08955f97eacca8261c2d57b08c7f5d287cc6/gcc/c-parse.c#L143-L3944)


---
# First some background on LR parsers
- Why we need parser generators for practical LR parsers
- Overview of how they work

---
# Some examples of LR parser generator tools
Yacc, Bison, PLY, PGen, Lark, etc

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

---
# References
- [LL and LR in Context Blog Post](http://blog.reverberate.org/2013/09/ll-and-lr-in-context-why-parsing-tools.html)