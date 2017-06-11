# Batteries - BatOptParse module
## Introduction

The motivation is, that I wanted to better understand OCaml and esp. the Batteries library and its optParser. Personally, the best way how to understand and comprehend this topic for me is to try it out and write about it. Do not take my words as normative, I am learning as probably as you are. It's possible you have much more experience.

Additional resources are here:
 - Documentation for Batteries: [Module BatOptParse](https://ocaml-batteries-team.github.io/batteries-included/hdoc2/BatOptParse.html)
 - [OCaml : Parsing a program’s arguments with the Arg module](http://scylardor.fr/2013/10/14/ocaml-parsing-a-programs-arguments-with-the-arg-module/)

 The goal is to create simple application which will be modular, and will provide structure and idea how to parse arguments with usage of optParser. I like to do something usefull even for educational purposes, so I decided, I will create very simple and naive implementation of scaffolding app, for generating basic project structure including `_oasis`, `.merlin`, `src` and it should start `oasis setup` command as well.

 User will need to provide options, so scaffolding app will help with generating new project.
 
 The source code of project is located on my github repository: **[Oscaffolder](https://github.com/martinjirku/oscaffolder)**.

## BatOptParse

BatOptParse contains modules for GNU getopt(3)-style command line parsing written by Bardur Arantsson. This module contains 4 other:

 - **_Opt_** - contains helpers for working with options
 - **_StdOpt_** - contains various standard options
 - **_Formatter_** - some formatting for custom usage message
 - **_OptParser_** - contains option parser itself.

I will divide my notes into few steps, I would like to follow in my investigation:
 1. Create working parser, with basic functionality of the `getopt(3)`
 2. Create options such as project name, skip-merling,...
 3. Refactor code into more functional form
 4. Creation of interactive guide, which will try to lead you so, your project will be successfully created
 5. Generate of the project based on data application gethered from user

# Parser in a application

 In JavaScript world, I am used to practical examples. It seems, I need to learn better read oCaml type system and understand it by looking. Truth is, type system is mostly you need. So let's look on OptParser we are going to use.

 `Module BatOptParse.OptParser` [documentation](https://ocaml-batteries-team.github.io/batteries-included/hdoc2/BatOptParse.OptParser.html) is nicely structured into few sections: 1.) exceptions, 2.) Types (important!), 2.) Creation, 3.) Output and error handling 4.) Option parsing.

 First, we need to create parser, so let's just focus on Creation section. Documentation is obvious here. To craete working parser we will use [`make`](https://ocaml-batteries-team.github.io/batteries-included/hdoc2/BatOptParse.OptParser.html#VALmake) function.

I will publish this project on my github account, it's going to be called **oscaffolder**. The code for the creation looks like this:

```ocaml
open BatOptParse

let () =
  let options = OptParser.make
    ~prog:"Oscaffolder turbo 2000"
    ~usage:"Oscaffolder helps you to generate new project by specifying project name"
    ~version:"Oscaffolder v0.0.1" ()
  in OptParser.parse options BatSys.argv |> List.iter print_endline;
```

Function `make` has few optional attributes. You can read about them in the documentation. However in our application we used
  - `prog` which specifies the program name
  - `version` we need to specify version of the application, it's usefull when user want to find out version by `oscaffolder.native --version`
  - `usage` - very short explanation what you want to provide to user, when user uses `oscaffolder.native -h` or `oscaffolder.native --help`

With this simple code we get for free pretty nice command line possibilities:

```
/Users/martinjirku/dev/ocaml/oscaffolder $ oscaffolder.native -h

usage: Oscaffolder helps you to generate new project by specifying project name

options:

  --version             show program's version and exit
  -h, --help            show this help message and exit
```

or nice version information:

```
/Users/martinjirku/dev/ocaml/oscaffolder $ oscaffolder.native --version

Oscaffolder v0.0.1
```

Let's add new options to our application

# Adding new Options

If you were reading documentation properly, you noticed, that after `make` is function we are interested in called `add`. Let's read its [documentation](https://ocaml-batteries-team.github.io/batteries-included/hdoc2/BatOptParse.OptParser.html#VALadd) more closely:

```ocaml
val add : t ->
  ?group: group ->
  ?help: string ->
  ?hide: bool ->
  ?short_name: char ->
  ?short_names: char list ->
  ?long_name: string ->
  ?long_names:string list->
  'a BatOptParse.Opt.t -> unit
```

Function `add` expects few named optional parameters and two required. As first parameter we need to specify the  parser of type `OptParser.t`. As last parameter, it expects type `BatOptParse.Opt.t`.
Interesting fact! you can see, that function `add` returns `unit`, which is basically nothing - void. Function _add_ is mutating OptParser instance. When we will implement core of this application, I need to think about this, and be carefull, not to introduce unpure functions into code.

To add new `BatOptParse.Opt` we need to create it first. So let's read closely documentation for [BatOptParse.Opt](https://ocaml-batteries-team.github.io/batteries-included/hdoc2/BatOptParse.Opt.html). Again, this module is divided into few sections, slightly different as we read in _BatOptParse.OptParser_: 1.) exceptions, 2.) Types (important!), 2.) Value retrieval, 3.) Creation, 4.) Output and error handling. One usable function could be `value_option`. With this function, you can create new BatOptParse.Opt, however good news is, that we already have all common Opt defined. Where is it? You asked. BatOptParse contains more submodules, one of the is: `StdOpt`. If you remember, this module contains various standard options. Again, let's look into [documentation](https://ocaml-batteries-team.github.io/batteries-included/hdoc2/BatOptParse.StdOpt.html).

We will use "Value options". For string options we can use: `str_option`. Type signature for this function is straighforward. 
```ocaml
val str_option : ?default:string -> ?metavar:string -> unit -> string BatOptParse.Opt.t
```

All arguments are optional, so we need just call this function to create new Opt. However, I would like to use both optional argumetns we will specify _default_ and _metavar_ value. We can find much more types of Opt available for you. Such as float_option, str_option, int_option. I chose this one `store_false`.

And the code looks like this:

```ocaml
open BatOptParse
open BatOptParse.Opt

let () =
  let options = OptParser.make
    ~prog:"Oscaffolder turbo 2000"
    ~usage:"Oscaffolder helps you to generate new project by specifying project name"
    ~version:"Oscaffolder v0.0.1" ()
  in
    OptParser.add options ~short_name:'n' ~long_name:"name" ~help:"Specify project name" @@ StdOpt.str_option ~default:("/") ~metavar:"" ();
    OptParser.add options ~short_name:'m' ~long_name:"skip-merlin" ~help:"Generate project name without Merlin" @@ StdOpt.store_false ();
    OptParser.parse options BatSys.argv |> fun (s) -> ();
```

When you compile application, now we have more options available in command line, these options are already documented for free:

```
usage: Oscaffolder helps you to generate new project by specifying project name

options:

  --version             show program's version and exit
  -h, --help            show this help message and exit
  -n, --name            Specify project name
  -m, --skip-merlin     Generate project name without Merlin
```

# Refactoring and cleaning

Let's look closer on the code. We should refactor it, decouple logic for getting and parsing options from the main part of the application. First step is to create a function which will be responsible for parsing these things. I call it `get_command_options`.

```ocaml
let get_command_options () = 
  let project_name = StdOpt.str_option ~default:("/") ~metavar:"" () in
  let skip_merlin = StdOpt.store_false () in
  let options = OptParser.make
    ~prog:"Oscaffolder turbo 2000"
    ~usage:"Oscaffolder helps you to generate new project by specifying project name"
    ~version:"Oscaffolder v0.0.1" ()
  in
    OptParser.add options ~short_name:'n' ~long_name:"name" ~help:"Specify project name" project_name;
    OptParser.add options ~short_name:'m' ~long_name:"skip-merlin" ~help:"Generate project name without Merlin" skip_merlin;
```

It was pretty straightforward task. More or less like *copy+paste* type of solution. Current implementation just initialize options and parser. Unfortunatelly, this is not sufficient! We want `get_command_options` to return settings object.

Let's define structure of settings object:
```ocaml
type settings = {
  project_name: string;
  skip_merlin: bool;
}
```

In `get_command_options` we need to add *the parsing* part:

```ocaml
BatOptParse.OptParser.parse_argv options
```

The problem is, that we're getting this warning from Merlin:
```
[merlin] Warning 10: this expression should have type unit.
```
Even this is not error, I do not like warnings, if I decide to leave code with warning, I better understand what's going on. So, what's going on here? The problem is, we do not assign the result of `BatOptParse.OptParser.parse_argv` function to enything, result is type of `string list`, so Merlin would like to have this value assign. My personal opinion, the one disadvantage of `BatOptParse` is that it's not pure functional solution and we depend on side effects done by implementation. When I dig deeper, I found the simple solution for these kind of situation. There is a function in OCaml core library with type signature: `type _ = 'a -> unit`. That's what we need! Function has self explaining name `ignore`;

In OCaml, I like the power of infix operators, one of these operators is `@@` provided by *Batteries*. This is used for the function application according [documentation](https://ocaml-batteries-team.github.io/batteries-included/hdoc2/BatPervasives.html#VAL(@@)): 

> Function application. `f @@ x` is equivalent to `f x`. However, it binds less tightly (between `::` and `=`,`<`,`>`, etc) and is right-associative, which makes it useful for composing sequences of function calls without too many parentheses. It is similar to Haskell's $. Note that it replaces pre-2.0 `**>` and `<|`.

Let's use the power of this Function application over here, to get rid of parentheses:

```ocaml
    ignore @@ BatOptParse.OptParser.parse_argv options;
```

At the end we want to return setting object, so we need to add little code for creation of the settings, let's see my final code:

```ocaml
type settings = {
  project_name: string;
  skip_merlin: bool;
}

let get_command_options () = 
  let open BatOptParse in
  let open BatOptParse.Opt in
  let project_name = StdOpt.str_option ~default:("/") ~metavar:"" () in
  let skip_merlin = StdOpt.store_false () in
  let options = OptParser.make
    ~prog:"Oscaffolder turbo 2000"
    ~usage:"Oscaffolder helps you to generate new project by specifying project name"
    ~version:"Oscaffolder v0.0.1" ()
  in
    OptParser.add options ~short_name:'n' ~long_name:"name" ~help:"Specify project name" project_name;
    OptParser.add options ~short_name:'m' ~long_name:"skip-merlin" ~help:"Generate project name without Merlin" skip_merlin;
    ignore @@ BatOptParse.OptParser.parse_argv options;
    {
      project_name = project_name.option_get () |> (function | None -> "default_name"; | Some(x) -> x);
      skip_merlin = skip_merlin.option_get () |> (function | None -> true; | Some(m) -> m);
    }

let () =
  let settings = get_command_options () in print_endline settings.project_name;
```

You can noticed I moved opening of `BatOptParse` and `BatOptParse.Opt` into `get_command_options` function, so global scope won't be polluted.

I've used the pattern matching to get rid of `Optional` type.

```project_name.option_get () |> (function | None -> "default_name"; | Some(x) -> x)```

Now we have access to settings object in main function. The result of the current code is:

```
/Users/martinjirku/dev/ocaml/oscaffolder $ ./oscaffolder.native --name test
test
```
