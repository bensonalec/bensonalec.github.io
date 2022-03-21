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
In this example, the first alternative is the traditional Python 3.10 list, in which the resulting translation (and original syntax) is defined in the translation. 
Actual extensions in Boa Constructor are Python files that contain a multiline string called "extension", that contains 
the actual rule contents, and a list of strings called keywords contains any necessary keywords for the extension. Keywords are any strings that are used directly as strings in a grammar rule, in the first example below these are true and false, as they need to be parsed differently from generic strings in a programming language. 
An example of a full extension is as follows:
```
extension = """
atom:
    | NAME { _a } 
    | 'True' { "True" } 
    | 'False' { "False" }
    | 'true' { "True" }
    | 'false' { "False" }
    | 'None' { "None" }
    | strings { _a } 
    | NUMBER { _a } 
    | (tuple | group | genexp) { _a } 
    | (list | listcomp) { _a[0] } 
    | (dict | set | dictcomp | setcomp) { _a } 
    | '...' { "..." }
"""

keywords = [
    "'true'",
    "'false'",
]
```
This extension allows a user to type true and false with entirely lowercase letters and Python to accept these as valid proxies for the True and False boolean keywords. 
Another example of a full extension:
```
extension = """
list:
    | '[' [star_named_expressions] ']' { "[" OPTIONAL _a ENDOPTIONAL "]" }
    | '[' NUMBER '::' NUMBER ']' { "[" _a "]" "*" _b }
"""

keywords = [
    "'::'",
]
```
This extension allows the user to type 
```
[3::4]
```
 which creates a list containing 4 integers with a value of 3. So, the translation takes the first returned value (the number, _a) and the second returned value (the number, _b) and translates this to 
```
[NUMBER] * NUMBER
```
 or in our example 
 ```
 [3] * 4
 ```
## Survery Setup
You will be provided with a link to a replit online programming environment, and a link to the form for this survey. We ask that you spend 30 minutes trying to implement an extension, which is an extension to the list rule in Python's regular grammar. This rule should take input in the form 
```
[1...10]
```
and convert this to 
```
range(1, 10)
```
Note that the numbers in this should be dynamic, and you will need to add a new keyword for the string "...".
## Resources
- Link 1
- Link 2
- Link 3