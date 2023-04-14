
# Table of Contents

- [Huh?](#huh) - An introduction
- [Why?](#why) - The problems it solves and why
- [What?](#what) - The specification itself
  - [Pieces](#pieces) - The basic building blocks of the syntax
  - [In practice](#in-practice) - Examples and usage
  - [Technical Specification](#technical-specification) - Details on syntax and elements
  - [Notes](#notes) - Some miscellaneous notes
    - [On Nesting](#on-nesting)
    - [On Operator Precedence](#on-operator-precedence)
    - [On Extensibility](#on-extensibility)
    - [On Invalid Inputs](#on-invalid-inputs)
    - [On Whitespace](#on-whitespace)
    - [On Dice Trays](#on-dice-trays)
  - [Grammars](#grammars)
    - [Full Grammar](#full-grammar)
    - [Precursor Grammar](#precursor-grammar)
- ~~How? - The parser implementation.~~ \<\-\- Coming soon!

# Huh?

It's pronounced "[tishe](https://www.google.com/search?q=tyche+pronunciation)"(try saying "ties" with a lisp), like after the Greek goddess of fortune.

Here's the thing: 2D digital dice suck. They've sucked for a while. It seems like every form of digital dice was hacked together as part of a bigger project. "We're trying to make a `[CHARACTER SHEET / VIRTUAL TABLETOP / DISCORD BOT]`, just get it working." Even worse, it makes perfect sense—I don't blame them. No individual should let themselves descend into the delusion of believing they can make the next panacea. The _individual_ wants to get their work done—no more no less. Their job isn't to fix the system. The job entry they applied to didn't have the text "fix everybody's problems lol xD" at the bottom. Or, at the very least, I hope they're getting paid pretty damn well if it did.

And yet, I have made the lapse in judgement of falling into the aforementioned delusion.

In the tech world, the answer to the question "has anybody figured out a better way to do this?" is usually _yes_—they have and it's an open source project that'll wash your dishes and shine your shoes the first time you try it. It'll be like heaven on earth. Some smattering of humans came together to make, for free, something that _perfectly_ solves your particular problem. You smile at humanity's kindness and pure generosity, and type your `pip install`'s or `cargo add`'s or `pnpm i`'s.

And then you make the mistake of interacting with the real word, where things only get done if they _really_—and I do mean _really_—need to get done. Hazardous, confusing, sluggish, (and frankly broken) systems permeate the computers of every university, business(tech or otherwise), and individual. If the world of open source is a fragrant lavender, the real word pushes your nose into a three-year-old moldy sandwich with burnt hair in place of lettuce.

So sometimes, you just have to make your own damn sandwich.

# Why?

Once upon a time, I, a Dungeons & Dragons nerd through-and-through, decided to make a dice calculator. A little interface like the app that comes on every smartphone and desktop computer, the kind of interface that you'll get access to if you type "calculator" into Google—but for dice.

Then I realized I'd have to make a notation. Just like how calculators need a syntax for the order and form in which you input numbers and operations, so did my dice calculator.

And then I realized that there's a lot more the average user wants out of dice than just "roll $X$ dice with $Y$ sides(and maybe add or subtract a little)". You might want to keep the $X$ highest dice. Or the $ X $ lowest dice. Or reroll on an $ X $ result. Or, well, you get the idea. There are as many ways to roll dice as there are people in the pews. There exist a million and one tabletop roleplaying games that have made up their own stupid types of dice rolls. Which begs the question: why are current dice roll offerings so opinionated?

If you don't know the current state of the digital dice rolling world(which is the position of the vast majority of people), don't worry about it. However, if you do, please think to yourself: when using a dice bot on Discord, or a dice roller online, or anything of the sort, how quickly can you find something that it can't do—and not only _can't_ do, but _can't ever possibly do_. I'd venture pretty quickly.

Imagine you're a game designer. You want to make the _next big tabletop roleplaying game_. Now, immediately, you have two choices: alienate anybody who wants to roll dice in your game digitally and use a new, quirky system, _or_ make your game compatible with the basic D&D style of dice rolling. So, either sacrifice an avenue of potential creativity and innovation, or your audience.

As a game master for tabletop gaming myself, I strongly believe that your table is your own. There is a wonderful beauty to allowing each and every combination of people to infuse the hobby with whatever quirks and nuances they desire. As such, I believe every aspect of the hobby should facilitate this. Just as Matt Colville says, every DM becomes, is, and always was a game designer. Designers deserve flexible tools. Players, busy with jobs, studies, or responsibilities, deserve to have the tools they use to be as straightforward as possible, consistent across applications and platform. A standard—that's what we need. A way for humans and computers to both understand how the dice should be, will be, and have been tossed. A flexible and extensible standard that anyone can add to. A new dice notation for the digital age.

This, my friends, is where Tyche comes in. The Tyche Notation: one standard notation to rule them all—to serve every purpose and every game. Let's roll.

## What?

> # ⚠️ Here begins the **Tyche Notation Specification**
>
> This specification describes the syntax for the Tyche Notation, a notation for dice rolls that is human & machine readable, unopinionated, comprehensive, rigorous, flexible, and extensible. This is a a draft, and will be updated as lessons are learned during the implementation process.
>
> - `Draft Version: 0.1`
> - `Date: 2023-04-14`

Above all, Tyche follows a number of patterns:

- **Familiar** - Tyche takes inspiration from the dice notation you're already familiar with. If you've ever played a tabletop roleplaying game, you're already mostly familiar. We've just added some things to supercharge the complicated parts and make them easier to understand. We've also made it more consistent and reliable. Want to roll a dice and use the result as the amount of sides another die has? Tyche makes it easy.
- **Flexible** - A key component of Tyche is extensibility. If your implementation of Tyche isn't extensible, it's not Tyche. There's a basic subset of intended features inspired by [Sophie's Dice](https://sophiehoulden.com/dice/documentation/notation.html), but everything past that is up to you. Want to add your own functions, manipulators, and transformations? Simply write some Python and plug it in—that's the standard Tyche sets up.
- **Feature-filled** - Tyche is designed to cover all the bases. Strings and integers. Arithmetic and functions. Dice and manipulation of them. As best as we can, we've tried to make Tyche the most comprehensive standard possible.

Please also note that _Tyche is unimplemented_. This is a specification that defines the rules an implementation should follow. It, by consequence, defines the usage of the notation. At time of writing, an implementation is currently being written in the Rust programming language. A working implementation of a subset of Tyche's features(infinitely nestable dice rolls and arithmetic) is available at the top of this page. Or, if you're reading this standard on the Github repo for this project, at [mirth.cc/work/tyche](https://mirth.cc/work/tyche).

## Pieces

The atomic components of Tyche are as follows:

- **Dice** - A dice roll. `XdY`, where `X` is the number of dice to roll, and `Y` is the number of sides on each die.
  - **Manipulators** - Dice may also have manipulators, which present in the form `XdYMZ` or `XdYM{arg1, arg2, arg3, ...}`. `M` is the manipulator's identifier, and `Z` or `{arg1, arg2, arg3, ...}` are the arguments to the manipulator.
- **Numbers** - A decimal number, with no real limit on the number of digits or presence of a decimal point.
- **Strings** - A string of characters, enclosed in either single or double quotes.
- **Functions** - A function call. `function_name(arg1, arg2, arg3, ...)`, where `function_name` is the name of the function, and `arg1`, `arg2`, `arg3`, etc. are the arguments to the function.

The above can stand alone or be combined with **arithmetic operators—**`+`, `-`, `*`, `/`, `^`, `(`, and `)`—to form expressions. Expressions can be nested infinitely. Expressions can also be acted on by "**Transformers**", which take the form `expression.T(arg1, arg2, arg3, ...)`, where `expression` is the expression to be transformed, `T` is the transformer's identifier, and `arg1`, `arg2`, `arg3`, etc. are the arguments to the transformer.

## In practice

Below are some basic examples of Tyche expressions.

> `2d6 + 4 - 1` — arithmetic with dice
>
> `4d10 / 2 * 3` — division and multiplication
>
> `3d6 + (d4 / 2)` — parentheses for order of operations

Expressions can be combined in Tyche in the form of a "**Tychain**", a comma-separated list of expressions. A Tychain is the top-level unit that a Tyche parser works with. You give it a list of expressions, and it rolls them and returns the result.

Below are some more advanced examples of Tyche expressions.

> `2d6K3` — a dice roll with a manipulator
>
> `3d10D{<2, >8}` — a dice roll with a manipulator with parameters
>
> `cos(2d6)` — a function call
>
> `(d4)d6` - a dice roll with a dice roll as a parameter
>
> `2d6 + 4 - 1, 4d10 / 2 * 3, 3d6 + (d4 / 2), (d20/3) + (1d4, 1d6)` — a Tychain
>
> `(d20 + 1).R(3)` - an expression with a transformer

## Technical Specification

Here are the elements of the syntax(square brackets are used to represent variability):

| Item                                                                       | Identifier | Function                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| -------------------------------------------------------------------------- | ---------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| +                                                                          | ADD        | Addition                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| -                                                                          | SUB        | Subtraction, or used unarily to indicate a negative sign of a number                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| \*                                                                         | MULT       | Multiplication                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| /                                                                          | DIV        | Division — this will give a decimal output when warranted                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| //                                                                         | IDIV       | Integer division                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| %                                                                          | MODU       | Modulo, used to get remainder                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| "[some characters]"                                                        | STR        | A string of characters. Characters can be escaped using a backslash.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| p[n]                                                                       | PAR        | A string or number parameter. Will be written as "p1, p2, ..." in following examples.                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| ([EXPR])                                                                   | SEXP       | Parentheses can be used to enclose an expression.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| [X]d[Y]                                                                    | ROLL       | Rolls. Roll X number of Y-sided dice. X and Y can be numbers but they can also be parenthetical expressions(also called subexpressions), which evaluate to a number for the roll's input. X is optional. If X is not given, default to 1.                                                                                                                                                                                                                                                                                                                         |
| [ROLL]M\{p1, p2 ...\}<br />OR<br />[ROLL]Mp                                | MANI       | Manipulators. They always follow a roll. They can have one number param next to the Manipulator identifier, or they can have one or more string or number parameters (or expressions/subexpressions that evaluate to integers or strings) within curly braces. The names of manipulators must be 1 or 2 sequential characters or symbols. They evaluate to a "dice tray", meaning a modified version of the initial role. Manipulators can be chained, so that one can modify the output of the last. Once the last modifier is evaluated, the tray is summed up. |
| [EXPR].Trans\(p1, p2, p3\)<br />OR<br />[SEXP].Trans(p1, p2, ...)          | TRAN       | Transformers. They always follow an expression or subexpression. The syntax is expr/sexp, a period, then the name of the transformer, followed by parentheses, which either do or don't contain a number of string or integer parameters (or expressions/subexpressions that evaluate to integers or strings). The names of transformers must be ASCII alphanumeric + underscores, and cannot begin with a number or underscore. They evaluate to numbers or strings.                                                                                             |
| Func(p1, p2, ...)                                                          | FUNC       | Functions. They stand alone. The syntax is the name of the function followed by parentheses, which either do or don't contain a number of string or integer parameters (or expressions/subexpressions that evaluate to integers or strings). The names of functions must be ASCII alphanumeric + underscores, and cannot begin with a number or underscore. They evaluate to numbers or strings.                                                                                                                                                                  |
| [X]d\<p1, p2, ...>                                                         | CROL       | Custom dice. X is optional. If X is not given, default to 1. When rolled, chooses from one of the given string/int parameters at random. 1 or more parameters are required.                                                                                                                                                                                                                                                                                                                                                                                       |
| [EXPR], [EXPR], ...                                                        | TYCH       | This is the top-level component of the notation. Each input of dice notation is one or more comma-separated arithmetic dice expressions.                                                                                                                                                                                                                                                                                                                                                                                                                          |
| # [Any text]<br />OR<br />###<br />[Any text]<br />[Across lines]<br />### | COM        | Comments. Either single line or multiline.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |

## Notes

### On Nesting

Nesting is allowed and prioritized. Since functions, manipulators, and transformers all evaluate to values that can be used as input to other functions, manipulators, and transformers, there can be nested calls. Subexpressions can be nested in other subexpressions. Subexpressions can contain FUNC/MANI/TRAN and complex arithmetical operations, which are then nested within other operations, ad nauseaum.

### On Operator Precedence

Arithmetic operators follow the same precedence as Python. If same precedence, left-to-right evaluation is followed.The deepest nested FUNC/MANI/TRAN/EXPR/SEXP gets evaluated first.

### On Extensibility

The goal of Tyche is to be _extensible_. This is done through FUNC, MANI, and TRAN. Developers can write their own FUNC, MANI, and TRAN in a scripting language(to be decided on later), and then use them as plugins.

You'll notice I haven't defined a strict set of FUNC/MANI/TRAN. That's on purpose. The parser will assume that any valid FUNC/MANI/TRAN name is valid, and then if it _isn't_, it'll be found out during runtime.

### On Invalid Inputs

Since extensibility is the goal, a lot of unpredictability is introduced. What happens when a string input is given to something that expects a number, or vice versa? Answer: an error is thrown during runtime, giving a detailed output of where the error happened. String vs. number inputs aren't educated in the grammar itself.

Since the parser cannot know all valid FUNC/MANI/TRAN names, it should parse them as generic identifiers following the specified naming rules. During the evaluation phase, the system will attempt to resolve these identifiers to actual implementations. If an identifier cannot be resolved to a valid FUNC/MANI/TRAN, an error will be reported at runtime.

### On Whitespace

Unless a part of the notation explicitly states that something must "immediately follow" another piece of notation, whitespace is allowed as much as you want, and promptly ignored.

### On Dice Trays

Dice trays are an internal data structure used during the evaluation of rolls and manipulators. They represent the intermediate state of a set of dice as they undergo transformations by manipulators. Dice trays are not part of the parsing process, as they are only used in the evaluation phase after the input has been successfully parsed.

## Grammars

### Full Grammar

```ebnf
tyche_notation  ::= tych (',' tych)*
tych            ::= expr (operator expr)*
expr            ::= roll | crol | func | texp | number | sexp | string
sexp           ::= '(' expr ')'
roll            ::= [number | sexp]? 'd' (number | sexp) [mani]*
crol            ::= [number | sexp]? 'd<' par (',' par)+ '>'
mani            ::= 'M' ('{' par (',' par)* '}' | number)
texp            ::= expr tran
tran            ::= '.' identifier '(' [par (',' par)*]? ')'
func            ::= identifier '(' [par (',' par)*]? ')'
par             ::= number | string | expr
number          ::= ['-']? digit+ ['.' digit+]?
string          ::= '"' (character | '\')* '"'
identifier      ::= letter (letter | digit | '_')*
operator        ::= '+' | '-' | '*' | '/' | '//' | '%'
letter          ::= 'A'..'Z' | 'a'..'z'
digit           ::= '0'..'9'
character       ::= any printable character
```

### Precursor Grammar

```ebnf
tyche_notation  ::= tych (',' tych)*
tych            ::= expr (operator expr)*
expr            ::= roll | number | sexp | string
sexp           ::= '(' expr ')'
roll            ::= [number | sexp]? 'd' (number | sexp)
number          ::= ['-']? digit+ ['.' digit+]?
string          ::= '"' (character | '\')* '"'
identifier      ::= letter (letter | digit | '_')*
operator        ::= '+' | '-' | '*' | '/' | '//' | '%'
letter          ::= 'A'..'Z' | 'a'..'z'
digit           ::= '0'..'9'
character       ::= any printable character
```

## More examples

- Basic arithmetic: `3+4`, `5-2`, `4*3`, `8/4`, `8//3`, `7%3`
- Unary negation: `-3+2`
- Parentheses: `(3+2)*4`
- Basic rolls: `d6`, `2d6`, `(3+1)d6`
- Custom dice: `d<"A","B">`, `2d<"A","B","C">`, `4d<2, 4, 8, 10>`
- Manipulators: `2d6M{2}`, `4d6K3`, `10d8D{"<", 4}`
- Transformers: `(2+3).T1(1)`, `3d6.T2("A")`
- Functions: `F1(3, 4)`, `F2("A", 3)`
- Complex expressions: `(2+3*4)d6M{1}.T1("A", 2)+F1(4,5)`
- Comments: `# A single line comment`, `###A multiline comment###`
