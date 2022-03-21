---
title: Boa Constructor Introduction
date: 2022-03-21
description: 'An introduction to my thesis, Boa Constructor. Also provides links and references necessary to understand and work with it. 
'
---

## Background

Boa Constructor is an extension oriented compiler frontend for the Python programming language. It works primarily through the definition of translation grammars, which define the original and translated version of the language and can be composed easily. Boa Constructor is an additional frontend for Python that parses the file, outputs its translation, and then runs the translation through a standard Python interpreter. Boa Constructor builds a parse tree of the input file, translates each component node by node, and then outputs this code for execution. In order to work with this properly, I would suggest that you have some understanding of grammars and automata. If necessary, please take a moment to review the links in resources. 
## Translation Grammars

Translation grammars are styled in the same way the official Python grammar is and uses a mixture of EBNF and PEG features. Translation grammars can be left recursive, and each rule is formatted as: 
```
ruleName: ruleContents { translationDefinition } 
        | alternativeContents { translationDefinition }
```
Rule names are typically in camel case (although this is a style choice, not a requirement). The rule contents (and alternative contents) are rule definitions that can contain literal strings (":"), tokens (in all capital letters, defined in the Python standard library Tokenize package), and other rule names. As well as this, ruleContents can use positive and negative lookaheads (denoted with \& and !), optional blocks (either denoted by square brackets around some contents, or a question mark (?) following the optional contents), repetitions (with * denoting any number of repetitions, and + denoting more than 1 repetition), groups (some section of ruleContents wrapped in parentheses), and cuts (denoted with  ~) that tell the parser to commit to that rule even if it fails.
Translation definitions can include string literals, the special TABBED and ENDTABBED tokens (denoting that contents inside should be tabbed in a level), the STARTLOOP and ENDLOOP tokens (denoting that you should loop until any inner contents are empty), and the OPTIONAL and ENDOPTIONAL tokens (denoting that the section inside does not have to have contents). As well as these special tokens, the power in translations comes directly from accessing the inner children of that parse node, which is done by starting a variable name with \_ and following this with some letter in the English alphabet from a to z. This letter represents which variable in the parse nodes children you're accessing, with a representing the first child and z representing the 26th child. As well as this, you can index components of that child node by using [index, subindex] (specifically useful when you're accessing something that was part of a group). These translation definitions are quite powerful and can be used to build fully functioning translations from a parse node.
An example of a rule is as follows:
```
assignment:
    | NAME ':' expression ['=' annotated_rhs ] { _a ":" _b } 
    | single_target augassign ~ (yield_expr | star_expressions) { _a _b }
```
Another example of a rule with a translation follows:
```
list:
    | '[' [star_named_expressions] ']' { "[" OPTIONAL _a ENDOPTIONAL "]" }
```
In this example, the first alternative is the traditional Python 3.10 list, in which the resulting translation (and original syntax) is defined in the translation. The second alternative is a new extension that's been added, in which a list where two numbers are separated by the "..." string are actually translated to an instance of the range function. This represents adding an extension that provides a rust-style shorthand for defining ranges. 

## Rules For Translations

Vivamus sagittis diam et arcu posuere pharetra. Mauris malesuada mi vitae risus molestie, in pretium sapien scelerisque. Pellentesque aliquam gravida nisi in tincidunt. Nam ut mattis enim. Sed maximus ullamcorper pulvinar. Ut in elit eget tellus varius semper eu eu felis. Suspendisse tempor, ipsum et fermentum placerat, felis massa tempus nulla, in vehicula nisi ipsum a libero. Ut porttitor leo ut velit viverra, a viverra urna sollicitudin. Duis vel condimentum lacus, sed ultrices est. Quisque at metus bibendum, mollis sem a, mollis neque. Donec malesuada venenatis magna, eu tincidunt dolor semper quis. Maecenas luctus pharetra erat. Fusce egestas est sed nunc accumsan convallis. Interdum et malesuada fames ac ante ipsum primis in faucibus.

## Resources
Vivamus sagittis diam et arcu posuere pharetra. Mauris malesuada mi vitae risus molestie, in pretium sapien scelerisque. Pellentesque aliquam gravida nisi in tincidunt. Nam ut mattis enim. Sed maximus ullamcorper pulvinar. Ut in elit eget tellus varius semper eu eu felis. Suspendisse tempor, ipsum et fermentum placerat, felis massa tempus nulla, in vehicula nisi ipsum a libero. Ut porttitor leo ut velit viverra, a viverra urna sollicitudin. Duis vel condimentum lacus, sed ultrices est. Quisque at metus bibendum, mollis sem a, mollis neque. Donec malesuada venenatis magna, eu tincidunt dolor semper quis. Maecenas luctus pharetra erat. Fusce egestas est sed nunc accumsan convallis. Interdum et malesuada fames ac ante ipsum primis in faucibus.
