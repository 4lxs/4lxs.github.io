# compiler

## ltr syntax

```hs
-- haskell is generally read right to left
print . foldr (+) 0 . filter odd $ [1..10]
-- first filter odd, then sum, then print
-- this is because this is how function application works in general
print(fold(...))

-- would it be better to reverse this?
[1..10] |> filter odd |> foldr (+) 0 |> print
-- imo, this is more readable at first glance
-- apparently, it's common in f#
-- it seems to be called forward/backward composition
```

## live coding/debugging

1. repl
2. changing code while running (hot reloading)
## naming

capitalized types

### conventions

[haskell naming conventions](https://kowainik.github.io/posts/naming-conventions)

* underscore postfix for functions that loop and don't return (haskell `until_`)
* prefix question mark or underscore for typed holes (idris, haskell)

  it prevents double-naming (i.e. some may use isSame, some same (like contains
  instead of doesContain). same?, contains? read better than any other)

  i really like this, but may clash with infix operators if ? can be used. i.e.
  `a?=b` may be interpreted as `a ?= b` or `a? = b`
  option is just not allow ? in infix operators/start of them

  maybe prefer result (i.e. `find : ... -> Result Index`) instead of predicates
  and use ? to convert result to bool (i.e. `cointains = find?`)

* postfix question mark for predicates (lisp)

## flow typing

```hs
(/) : Int -> Int -> Result Int of
  _, 0 -> error DivByZero
  x, y -> builtin_div x y

-- usage

x / y -- : Result Int
x / 10 -- : Int. compiler detects first branch is unreachable
-- if you know something compiler doesn't, you can use assert or assume. use
-- sparingly
assume y != 0 in x / y -- : Int

digitToInt : Char -> Result Int of
  x | isDigit x -> (ord x) - '0'
    | _ -> error NotDigit

intOrZero : Char -> Int of
  x | isDigit x -> digitToInt x -- : Int. x is constant and isDigit is pure
    | _ -> 0

-- note that these are essentially just refinement types
-- we would also like a way to do a check for the output

toChar : Int -> Result Char
  ensures isDigit result
of
  | x | x >= 0 && x < 10 -> chr $ x + ord '0'
  | _ -> error NotDigit

digitToInt $ toChar 5 -- : Int
-- 5 >= 0 && 5 < 10, meaning toChar 5 : Char and because toChar ensures isDigit,
-- digitToInt $ toChar : Int. the compiler also needs to check that the result
-- of toChar is always a digit

```

## no match expr

```hs
-- use equation style instead

-- problem. duplicated params
f : Env -> Params -> SumType -> Result of
  env, params, A => ...
  env, params, B => ...
  env, params, C => ...
  env, params, D => ...

-- with match, this would be much better
f : Env -> Params -> SumType -> Result of
  env, params, sumty = case sumty of
    A => ...
    B => ...
    C => ...
    D => ...

-- alternative, without match, allow of within of
f : Env -> Params -> SumType -> Result of
  env, params of
    A => ...
    B => ...
    C => ...
    D => ...
```

```

## unused fields

```
struct vec { data: [i32], len: i32 }

// different syntax
type dataonly = @skip(len) vec

b : dataonly = ...

// you can only use functions that don't use the len field
```

## explicit currying

```
a : (a: i32, b: i32) -> i32
  = a + b

a5 : (a: i32) -> i32 = a(5, $1)

fn add3(a, b, c) -> i32 = a + b + c

fn a5(a) = add3(3, 2, a)
a5 = add3(3, 2, $1)
a25 = add3(5, $1, $2)
a25 = add3(5, $..)

// alternative. use named arguments
a5 = add3 ~a=3 ~b=2

// alternative. use ! after function name
// explicitly show that it's currying
a5 = add! 3

// idea. explicitly need to enable currying
fn add(a, b) ...
add5 = add 5 // not allowed

fn add(~a, b, c)
add5 = add ~a=5 // allowed (int->int->int)
add53 = add ~a=5 ~b=3 // not allowed. b doesn't allow currying
```

## unnamed arguments

```
// only one letter argument names don't need to be named
fn add(a, b) = a + b
add 5 3 // ok

fn map(fn, data) = ...
map(fn=add 5, data=...)
map(add 5, ...) // not allowed. fn is not single letter
```

## interface change should always result in a compile error

variable type/function signature are considered their respective interface

## infix functions

```hs
-- 2-parameter functions in haskell can be infix
mod 5 2 == 5 `mod` 2

-- maybe better to explicitly declare a function infix
-- e.g. a function starting with `
mod :: Int -> Int -> Int -- not infix
5 mod 2 -- error
`mod :: Int -> Int -> Int -- infix
`mod 5 2 -- error
5 `mod 2 -- ok
-- curry
(`mod 5) 2 == 2 `mod 5
(5 `mod) 2 == 5 `mod 2
```

```

## named parameters

```ml
(* named parameters considered bad and are usually a crutch for bad api design
   contrived example, but illustrates the point *)
add : float -> float -> float -> float -> float -> float -> float -> float -> float

(* preferred *)
alias Point = { x: float, y: float, z: float }
add : Point -> Point -> Point

(* possible keyword parameters ala ocaml *)
add : ~name: String = "unnamed" -> Int -> Item
   of x = ...
(* usage *)
add ~name="foo" 5
```

## idk anymore

```haskell
module Vector
  Item : Type
  len : Int
  data : [Item]

let of_list : [Item] -> Vector

len : Self -> Int
    | self = self.len

concat : Self -> Self -> Self
       | self, other = { len = self.len + other.len }
```

## module parameters

```ml
(* file: random.x *)
module Random(seed: Int)

(* seed can be used as if it's a val *)
let state = seed ref
```

```ml
(* main gets the env argument *)
module Main(env : Env)

let () =
  Stream(env.out).printf "Hello, world\n"
```

## type annotations instead of gemerics

type annotations are basically type functions

```
type fn option(t) = (
  valid : bool
  data : t
)

let y : option int
```

## haskell-style type syntax

```ml
:: Int, Int -> Int
fn add(a, b) = a + b


(* maybe?? *)
let add : Int -> Int -> Int =
          a,     b,  = a + b

let plus : Nat -> Nat -> Nat of
         | Z  , n -> n
         | S m, n -> S (plus m n)
```

## names from types

```ml
fn insert(Ord, BTree)

(* equivalent to *)
fn insert(ord: Ord, bTree: BTree)

(* not allowed when ambiguous *)
fn add(a: Int, b: Int)
```

## optional struct features

```ml
struct list<bool double_ended?, bool sized?>(
  next: Self,
  @if double_ended?
  prev: Self,
  @if sized?
  length: i32,
)

fn len(list: list<sized? = true>) = list.length

// in rust, it would be a wrapper (ex. peekable)
struct sized(list: list, length: i32)
```

## pipe scope

```
// piping some type brings into scope the module in which it is defined

module A {
  type B = i32;
  let add5 x = x + 5
}

A.B |> add5
// equivalent to
A.B |> A.add5
```

## statement = expr consumer

```
fn a() = expr

fn b()
  ; expr
  = expr
```

## functions as pattern matchers

```
// first argument must be a list of two elements
fn something([a, b], c: i32) = a + b + c

// overloading
fn a(arg: f32) = ...
fn a(arg1, arg2) = ...
fn a(arg: i32) = ...

a something
// is equivalent to
match something
| (arg: f32) => // call a(arg: f32)
| (arg1, arg2) => // call a(arg1, arg2)
| (arg: i32) => // call a(arg: i32)
```

## macro system

[nim]: https://nim-lang.org/docs/tut3.html

```
macro if {$(condition: expr) => $(then: expr) else $(else: expr)} =>
  match ${condition} | true => ${then} | false => ${else}
```

another idea is to have alternative lisp frontend and to call lisp functions
from the language

## no-bracket syntax

```
fn a(a: i32) -> i32 = a + 5;;

fn a(a: i32) = print a;;;

struct Point(x: i32, y: i32)

choice A =
  | A(i32)
  | B(f32)

// => always denotes a block start. ;; block end
=>
  let x = 5;
  x + 5
;;

// let with block
let x : i32 =>
  let y = 5;
  y
;;

fn a(a: i32) -> i32 => a + 5;;

for i in 0..10 => print i;;

let lbtype: () -> void;;


let lambda = (x: i32) -> f64 => x + 5;;
let lambda = (x: i32) => x + 5;;

=> print 5;;

// block is either a signgle expression or list of statements taht require
// return
fn a() => 5 + 3;;
fn a() =>
  let x = 5 + 3;
  return x
;;

if a < 5 => print a;;

if a < 5 => a; else 5; let x = 5; x;

fn a => 5

// same as
fn a() -> i32 => 5

if a < 5 => a else 5

assert a < 5

// expression based. ';' joins expressions

// significant indentation

=>
  if x < 5 =>
    print x;
    x = 5;
  x = 3; // not part of if block
    5 // misleading indentation error

=>
  match y =>
    | ... => match x
      | 5 => print x
      | 3 => print 3
    | _ => print y

// lambda
()=> 5

// problem

print(x) => 5 // print(x)(=> 5)
print (x)=> 5 // print((x) => 5)
print (x)=> 5 () // print((x) => 5)() ?
```

## enum

```
// enum is a type sum
type Enum = i32 | f32;
// Enum is either i32 or f32

// there are on-fly types
type Enum =
  | A(i32)
  | B(f32)

// equivalent to
type A = i32;
type B = f32;
type Enum = | A | B;

// there are also implicit void types
type Enum = A | B | C
// equivalent to
type Enum = A() | B() | C()

```

## name-first syntax

```

a : i32 = 5;
b := 5;

lmb : (i32) -> i32 = { it + 5 }
lmb := { it + 5 }

// problem
fn a() { ... } // implies void return
a : () = { ...; 5 } // implies deduced return type
```

## module arguments

```
module Map {
  struct T { }
  fn get(map: T, key: String) -> Val { map.get(key); }
}
```

## destructibles

```
struct unique_ptr(item: *i32);

fn drop(self: unique_ptr) { free self.item; }

// unique_ptr is destructible. you cannot assign it
let a = unique_ptr(malloc(int)); // not allowed

// you can only create it with 'with'
with a = unique_ptr(malloc(int));

// with calls drop at the end of the block
```

## local returns

```
// syntax
// exit [label] [value] ['if' condition]
// if label is not provided, it exits the inner-most block
fn a() {
  exit fn 10 if true;
  exit a

  a := {
    exit 5
  } // => 5

  loop {
    exit a if a > 10;
    a += 2;
  } // => 11
}
```

## first-class fsm (finite state machine)

see: [reddit](https://www.reddit.com/r/ProgrammingLanguages/comments/i9uuzo/comment/g1k0829/?utm_source=share&utm_medium=web3x&utm_name=web3xcss&utm_term=1&utm_content=share_button)
```
while True:
  print "always"
with state_1:
  print "state 1"
  continue state_2
with state_2:
  print "state 2"
  continue state_1

// outputs
// always, state 1, always, state 2, always, state 1, ...
  
```

## auto-generated function types

```
// you write
fn add(a, b) = a + b;

// compiler adds types with capitalized names upon succesful compilation
:: (A: Addition, A) -> A
fn add(a, b) = a + b;
```

## literal variants

```
fn a() -> | Yes | No {
  if true { Yes } else { No }
}

assert(a() == a::Yes)
```

## extern

```
// body replaced with #foreign
fn atoi(s: str) -> i32 #foreign;
```

## assignment sugar

```
a = b
// this is just sugar for
a.set(b)

a .= add(b) // a = a.add(b)
```

## automatic lift for collections

```
fn add5(a: i32) -> i32 = a + 5;;

add5(10); // -> 15
add5([10, 20, 30]) // -> [15, 25, 35]
```

## dependent types and first class modules

```
use sys.Os().Out;

fn Os() -> module {
  if Linux { return sys.linux; }
  err UnsupportedOs
}
```

## struct modules / primary structures

```
module Program {

// because Scanner has data, it's a struct module. that means, there is an
// implicit struct Scanner(data: [i32]);
module Scanner {

type Enum = | A | B | C;

let data: [i32];

// constructor
fn(data: [i32]) =>
  // we need to initialize all fields
  data = data;

fn get() -> i32 => data.pop()

}

Scanner([1, 2, 3]).pop() // -> 3

}
```

## non-optional subtyping

```
/// note b is option
struct A(a: i32, b: i32?)

type A2(a: i32, b: i32) : A;
```

## ufcs

```
fn a(this: i32, that: i32) -> i32 = this + that;;

a(5, 6) == 5.a(6)


fn add(this: i32, that: i32) -> i32 { this + that }

```

## struct

```
struct<A: Integral> Int: A(

)

fn Int::a(i)

// primary constructor
type Int(a: i32) = struct(
  x: i32 = a,
  y: i32 = 5,
)

// if primary constructor is not provided, imlicit one will be created
// implicit constructor requires all named fields.

// you can also overload a constructor
// note. no return type. constructor always returns self
// you assign fields in self
fn Int::Int(self: Int) {
  self.x = 5;
  self.y = 5;
}
```

## module classes

```
module Vector;

data: *i32;

constructor() {
  data = malloc(5 * sizeof(i32));
}
```

```
module vector;

fn dump(stream: Stream = std::out) {
  stream.write(data);
  loop items {
    stream.write(it);
  }
}

fn dumpitem(stream: Stream = std::out) {
  stream.write(data);
}

module dump {
  
  fn dump() {
    stream.write(data);
  }
}



}
```

## constructors


```
struct Vec(x: i32, y: i32, z: i32)

// constructors are separated from other functions by capitalized name
fn Vec.Zero() { Vec(0, 0, 0) }

// function with same name as struct is a constructor. it implicitly returns the
// struct
fn Vec() { Vec(0, 0, 0) }


// option 2.
fn Vec.Vec() { }

Vec() // sugar for Vec.Vec()

```

## signatures

```
sig Map {
  type Key;
  type Value;

  fn get(key: Key) -> Value;
}

sig IntMap : Map {
  type Key = i32;
}

struct IntHashMap = HashMap : IntMap;

// note that : Map is completely optional. it just tells the compiler to emit an
// error if it's not a subtype of Map. it signs Map even without it
struct<
  type Key
  type Value
  type UnderlyingContainer : Stack<Key> = Vec<Key>
> HashMap : Map(type Value = V) {
  let value: UnderlyingContainer;
}

struct HashMap<Key, Value>

fn HashMap::get(key: Key) -> Value {
  ...
}

// this just checks that HashMap is a subtype of Map
// equivalent to HashMap : Map in definition
struct _ = HashMap : Map;

let a = HashMap<i32, i32>();
let b = IntMap<i32>();

struct Something()

struct Something : Map{type Key = i32; type Value = i32}

fn Something::get(i32) -> i32 {}

```

## program stages

```
@stage(1)
static x; // x can only be modified in stage 1 or earlier

// a can only be called by functions in stage 1 or earlier
@stage(1)
fn a() {
  x = 10;
}

// b can not call a, but a can call b
@stage(2)
fn b() {

}

// concurrency cannot be used in any stage

fn main() {
  // by default, main doesn't have a stage
  // the first statement needs to be @stage if you want to use stages

  // transition. cannot have code before this
  @stage 1
}
```

## lambdas

```
// normal syntax
let a = (x: i32) -> i32 { x + 5 }

// trailing lambda syntax
a.map { it + 5 }

// lambda blocks
let a = { (x: i32) -> i32 => x + 5 }
let a = { x => x + 5 }

// lambda blocks differ from normal blocks by =>
{ => 5 } // lambda
{ 5 } // block
```

## loops

```
// while loop
while a < 5 { }

// iteration loops with lambdas
loop vec(5, 3, 2, 1) {
  print it
}

// inifinite loop
// note that with loops, block is implicitly a lambda, so you can skip =>
loop { // => optional
}
```

## enum traits

```
enum trait Color {
  fn r() -> u8;
  fn g() -> u8;
  fn b() -> u8;
}

// all variants of Color must be in same module
struct Red : Color();
fn Red::r() -> u8 { 255 }
fn Red::g() -> u8 { 0 }
fn Red::b() -> u8 { 0 }
```

## enum

```
enum Color {
  Red: i32,
  Green: i32,
}
```

## optional/extension fields

```
struct Player(has_hitbox: bool) {
  hitbox?: Hitbox = has_hitbox? Hitbox(),
}
// hitbox is an optional field. if has_hitbox is false, the field will not even
// be allocated upon construction
// this mimics llvm's TrailingObjects class
// you cannot create Hitbox after construction
```

## monads

```
fn addone(a: i32) -> i32? { if a < 5 { a + 1 } else { none } }

let x = addone(4)
     => addone(it) // 5
     => addone(it) // not run
     => addone(it)

assert(!x?)
```

## idea

a way to encapsulate states in a type and only expose the states that are valid
at the time

e.g. ast -> typedast

```
struct VarDecl {
  substruct Typed {
    name: String,
  }
  type: Type*, // when initially creating ast, we may not know the type
}

fn analyze(Typed decl) -> VarDecl {
  // decl.type has uninitialized state

  // function figures out the type
  update decl {
    type = ...
  };
  // note that updating decl does not resize/move the struct. Typed has the same
  // size as VarDecl, but only exposes the fields that are valid in that
  // substruct

  return decl; // decl is now VarDecl and decl.type is valid
}

// this will also help creating new structs
stuct A { XKnown { x: i32 }, y: i32 }
let a = XKnown { x: 5 }
fn figureOutY(a: XKnown) -> i32 { 10 }
let y = figureOutY(a)
update a { y = y };
```

## errors

see [duffy-error-model]

error return function annotation

```
error SomeError
error AnotherError ""
error MsgError(i32) "" // you can have data in error

// function that returns an error
// throwing a ParseError will throw AnotherError
@throws(SomeError "something happened", // all errors reqire documentation
        AnotherError < ParseError, // any instance of ParseError will be thrown
                                   // as AnotherError
        DataError(i32) "" // you can attach data to errors. may be removed for
                          // performance
)
fn root(x: i32) -> f64 {
  if x > 0 { (f64 x).sqrt() } else {
    err ParseError // this will throw AnotherError
    // probably won't allow this. you can only directly throw SomeError but you
    // can rethrow ParseError
  }
}

// caller needs to handle the errors somehow
root(0) err // rethrow
root(0) catch e {
  // e is an enum of possible errors
  print("error: {e}")
  // trace e allows you to print the stack trace. this is only allowed in
  // prototype mode as they are expensive to generate
  trace e
}
// this will assume root doesn't error. in debug moe, this will abort the
// program
let x = assert root(9)

// everything that can error, does. like arithmetic, array access, ...
// and you need to handle it
let x = assert a + b
// there are unchecked versions of these operators
let x = a +! b // may overflow
// there is also compiler flow analysis that will allow you to skip checking
assert a < 5
let x = a + 5 // no error. compiler knows it can't overflow
// assert checks in release mode and panics if false.
// for debug only checks, you use assume
assume a < 5 // checks in debug. in release, it's a no-op
```

## contract idea

see [duffy-error-model]

```
@error SqrtError "can't sqrt negative number"
@requires x >= 0 else SqrtError
@debug @requires x < 1000 // maybe
@ensures result >= 0 // result is a special keyword in ensures clause
fn sqrt(x: f64) -> f64
{
  // x is guaranteed to be >= 0
  x.sqrt()
}

// if you can guarantee the contract, you can skip the error handling
assert a == 4
sqrt(a) // no error
assert b == -1
sqrt(b) // need to handle error
```

## traits and concepts

```
trait Number : Add + Sub + Mul + Div {
  fn abs(self) -> Self;
}

concept<T> Numeric {
};

fn Numeric::abs()
```

## generics

```
struct Vec<T>()

fn Vec::push(T t) {
  // T is available
}

fn<A> Vec<A>::push(A a) {
  // in case you need T from outer scope?
}
```

## nominal vs structural typing

```
trait Iterator {
  fn next(self) -> T;
}

struct A();

impl Iterator for A {
  ...
}

struct B();

// you can use Int as Iterator implicitly
// but need to cast B to iterator explicitly

// probably going to use nominal typing and structural subtyping of
// traits/contracts. that is, you don't need to impl Trait for a type to use it.
// it just needs to be in scope

// if it walks like a duck and talks like a duck, then it's a duck
contract Duck {
  fn talk();
  fn walk();
}

// a duck can fly
fn Duck::fly() { ... }

// Pig walks and talks
struct Pig();
fn Pig::talk() { print("oink") }
fn Pig::walk() { ... }

// ergo, Pigs can fly
Pig().fly();
```

## dafault initializations

nothing is default initialized. you need to explicitly initialize everything you
use. there is however late initialization

## prototype mode

you can enable prototype mode in the project file.
this will allow you to use some features that are nice to have, but shouldn't
be used in production code. e.g. stack traces.
in this mode, the compiler will try to recover from syntax errors and continue
compiling and just print a warning of what it assumed you meant.
some compiler errors will also be warnings or disabled in this mode. e.g. unused
variables, unreachable code, unhandled errors, etc.

## unexpected annotation

```
if a < 5 {
  doSomething
} else {
  // in debug mode, this will abort the program with an error message
  // in release mode, the compiler will assume this is unreachable
  @unexpected("a shouldn't be less than 5")
}
```

## (idea) indent-based

indent-based blocks that end with two semicolons. statements end with one

```
fn add(a: i32, b: i32) = a + b;;
fn add(a: i32, b: i32) =
  let c = a + b;
  c
;;

```

## (idea) function syntax

```
fn add a: i32 -> b: i32 -> i32 = a + b
// equivalent with lambda notation
let add = fn a: i32 -> b: i32 -> i32 { a + b }
// no parameters
let no_args = fn -> () { () }
// ==
let no_args = fn () -> () { () }
```

## other languages interop

you declare modules for other languages in the project file

```
// declerations from terrain.hpp are in terrain module
include(cpp) "terrain.hpp" in terrain
// declerations from stdio are in cppio module
include(c) <stdio.h> in cio
```

then in your code, you can define and use them

```
module terrain // you need to be in the same module to define functions

// assume terrain.h has following contents
// int get_height(int x, int y);

// we define the function here
let get_height = (x: i32, y: i32) -> i32 {

  // we can also call functions from other modules like this
  cio::printf("hello world\n");

  10 // return value
}
```

## ideas

invalid states, which stack
let a = 5. / 0.; // inf. invalid state
a += 5.; // valid statement. returns f64? with invalid state inf

you can define invalid states on your own types
impl Invalid for f64 {
  Infinity { i == inf }
  NaN { I == nan }
}

. | pipes output of a function to another function
a | b
but |. pipes output to a member function

ability to split module interface (like header files). outside of module you can
only access interface structures

```
interface module ::json
fn parse() { ... }

fn to_string();

module // ::json

fn to_string() { ... }
```

## variables

[best-ml-way](https://maxheiber.medium.com/the-best-way-for-a-programming-language-to-do-variables-so-far-the-ml-way-76d853d2e27c)

```
// should mutability be part of type or variable (rust) or both (c++)?
mut x = 5;
// vs
let x: mut i32 = 5;

// difference is allowing internal immutability

// type mutability
struct Point (x: mut i32, y: i32);
let p: mut Point = Point(x: 1, y: 2);
p.x = 5; // allowed
p.y = 5; // not allowed
let r: Point = Point(x: 1, y: 2);
r.x = 5; // not allowed

// variable mutability doesn't allow this
struct Point (x: i32, y: i32);
let mut p = Point(x: 1, y: 2);
p.x = 5; // allowed
p.y = 5; // allowed
let p = Point(x: 1, y: 2);
p.x = 5; // not allowed
p.y = 5; // not allowed

//===
// shadowing
//===
// allowing shadowing in same scope may have some problems
fn a(x: i32) {
  // shadowing is nice for sanitizing
  let x = if x < 0 { -x } else { x };

  // but others may expect that x is what is passed in. especially if you work
  // at end of a long function
  
  // ... use x expecting it to be the input x
}

// not allowing shadowing will have worse problems
let state = getstate();
let state2 = processstate(state);
let state3 = reprocessstate(state2);
usestate(state2) // bugprone

// this can be solved with shadowing

// both can be solved by having mutability of type and variable
let state = getstate();
let state = processstate(state); // error

mut state = getstate(); // state value is constant, but i can reassign the
                        // variable
mut state = mut processstate(state); // valid. i can reassign and change state
// don't like it
```

## (idea) capitalization of types

this way you know if you are dealing with a type or a variable
at scanning time

## language

```
// you specify module before the code in the module. allows you to
// move the file around without breaking the code
module somemodule

// for now, you can only define one module per file
// but idea is to allow multiple modules in a file like this. if we want it
// // this code is part of module somemodule
// module somemodule::a
// // this code is part of somemodule::a

// you may only define a module once

// empty module is the current project module, so
// use ::b
// is the b module in current project, but
// use b
// is the a project's default module

// expression-based language. everything is an expression. block evaluates to
// the value of the last expression
fn add(a: i32, b: i32) { a + b }

// available types are:
// primitives: i32, f32, bool, char...
type Int = i32;;

// structures
type Point = (x: i32, y: i32);;
// you create a struct like this
let point: Point = Point(x: 1, y: 2)
// you can skip the field names if you pass variable with same name as field
let point: Point = Point(x, y)
// you have the unit struct
type Unit = ();;
// single values are also structs. you can skip the field name with them
type Int = (i: i32);; // same as Int = i32 and Int = (i32)
// single field structs can be created with just the value
let i: Int = 5
let j: Int = Int(5)
let k: Int = Int(i: 5)

// optionals.
type OptInt = Int?;;
let opt: OptInt = 5
// you can skip the value to create a null optional
let noInt: OptInt
// you can check if an optional is null and the compiler will allow you to
// access the value (type flow analysis)
if opt { opt } else { 2 } // 5
if noInt { noInt } else { 2 } // 2
// they allow pointers to be null
type NullableIntPtr = Int\*?;;

// pointers. they are not nullable. if you move the value, the type becomes
// IntPtr? (nullable) with value null. that means, they are like c++ unique_ptr
type IntPtr = Int\*;;

// functions
type Succ = i32 -> i32;;
// you can create a function like this (lambda notation)
let succ: Succ = ||x: i32 { x + 1 }
// they only take exactly one argument. to pass multiple, use a struct
type Add = (a: i32, b: i32) -> i32;;
let add: Add = (a: i32, b: i32) -> { a + b }
// to pass no arguments, or return nothing, use the unit struct
type NoArgs = () -> ();;
let no_args: NoArgs = () -> { () }
// you call functions like this
succ 5 // equivalent to succ(5)
add(1, 2)
no_args()

// function receivers. you can define a function that will allow you to call it
// as a member
let Int::add = ||(i, a: int) { i + a }
let Point::x = ||(i) { i.x }
// i is a special parameter name that binds the A structure so you can call add
// as a member. it must be the first parameter and must not have a type
let num: Int = 5
num.add(5) // -> 10
// fuck can still not access private members of A (unless part of same module)

// + is a special function name. you must pass exactly two arguments and return
// exactly one
// the two arguments and return types must match
fn +(a: i32, b: i32) -> i32 {
a.add(b)
}
// similar for other operators

```

[duffy-error-model]: https://joeduffyblog.com/2016/02/07/the-error-model/

## docs

- invariant based programming with assert and assume

- encoding invariants into the type system

- bytecode interpreter and runtime analysis
    - performance
    it analyzes the program and finds likely if path, hot code, ... can detect where memory flow and can transform rc to manual
    

    - safety
     can detect every place a specific variable gets assigned
     detects memory errors (dbl free, ...)
 - gc
   only used in self-ref structures, otherwise rc

### docsite ideas

optional sidebars for additional stuff. something like detail hiding [here](https://tomasp.net/coeffects/).

docs should be written like this

```
hey there
here is some text that is shown in all cases

>!theory
> here is some text that is shown only when
> theory mode is enabled

>!history
> historical notes. why it was done this way, which languages/language features
> inspired it, and link to initial discussion about this feature

>!imperative
> how it's done in other languages. i.e. mode for beginners coming from c++

>!implementation
> implementation details for this specific feature

>!example
> this is where examples should go. if people are looking for something
> specific, examples may get in the way. they should be shown by default
```

the user should be able to enable or disable these modes at the top by the title

## choosing the right idea matters

naming things matters because it gives the right abstraction for reader

this means even if I provide the same idea with different syntaxes it will affect the readability of programs

## name types not values

```
// bad
let env: string value map

// good
type Env = string value map
let env: Env
```

idea: values from types

```
let env'
// equal to
let env: Env
```
