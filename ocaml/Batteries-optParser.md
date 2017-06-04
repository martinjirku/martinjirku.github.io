# Introduction

The motivation is, that I wanted to better understand OCaml and esp. the Batteries library and its optParser. Personally, the best way how to understand and comprehend this topic is to try it out and write about it.

## Resources

Additional resources are here:
 - Documentation for Batteries: [Module BatOptParse](https://ocaml-batteries-team.github.io/batteries-included/hdoc2/BatOptParse.html)
 - [OCaml : Parsing a program’s arguments with the Arg module](http://scylardor.fr/2013/10/14/ocaml-parsing-a-programs-arguments-with-the-arg-module/)

 The goal is to create simple application which will be modular, and will provide structure and idea how to parse arguments with usage of optParser. I like to do something usefull even for educational purposes, so I decided, I will create very simple and naive implementation of scaffolding app, for generating basic project structure including `_oasis`, `.merlin`, `src` and it should start `oasis setup` command as well.

 user will need to provide options, so scaffolding app will help with generating new project.

 