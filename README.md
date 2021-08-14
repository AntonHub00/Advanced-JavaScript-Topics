# Advanced JavaScript Topics

---

**Note**

This shouldn't be taken as an absolute truth, it can contain mistakes or some
things could be completly wrong.

The goal of this repository is to take notes of some important/advanced
JavaScript topics that I think could be useful, so feel free to make a pull
request if you want to add or correct something.

Also some references could be missing. My intention is not to steal all the
information that other people have gathered, but gather all that other people
gathered that I think is relevant to me, thus most of the information that can
be found in this repository it not from my own. If you find the links or the
sources this information came from, also feel free to make a pull request adding
them.

---

## JavaScript engine

Is what makes JS code run.

Contains (V8 at least):

- Parser.
- AST (Abstract Syntax Tree).
- Interpreter (generates bytecode).
- Profiler (monitor).
- Compiler (JIT compiler. Generates optimized code).
- Call stack.
- Memory heap.

One branch:

Parser -> Abstract Syntax Tree -> interpreter -> byte code.

or

Parser -> Abstract Syntax Tree -> Profiler -> compilation (JIT compilar) -> optimized code.

## Runtime

Contains:

- The JS engine (V8, SpiderMonkey, Chakra, etc.).
- Event loop.
- Callback stack.
- APIs (Web, Node).

## Execution context

It is the execution context that decides which code section has access to the
functions, variables, and objects used in the code. During the execution
context, the specific code gets parsed line by line then the variables and
functions are stored in the memory.

Types:

- Global Execution Context
- Function Execution Context
- Eval Execution Context

### Execution stack (Call Stack)

It carries all the execution contexts and tracks them.

### How an Execution Context is created

#### Creation stage

1. "arguments" object (if it is a Function Execution Context).
2. "VariableEnvironment" component is used for the initial storage for the
   variables, arguments and function declarations. The var declared variables are
   initialized with the value of undefined.
3. The value of "this" is determine.
4. "LexicalEnvironment" is just the copy of VariableEnvironment at this stage.
5. "Hoisting".

Example:

```javascript
// Execution context in ES5
ExecutionContext = {
  VariableEnvironment: { ... },
  ThisBinding: <this value>,
  LexicalEnvironment: { ... },
}
```

#### Execution stage

1. Values are assigned.
2. LexicalEnvironment is used to resolve the bindings.

### Lexical environment

A Lexical Environment is a specification type used to define the association of
identifiers to specific variables and functions based upon the lexical nesting
structure of ECMAScript code.

It is used for identifier resolution.

Lexical environment consists of two main components:

- The environment record.
- Reference to the outer (parent) lexical environment.

## IIFE (Immediately Invoked Function Expression)

A function that runs as soon as is defined.

It is a design pattern.

Contains 2 main parts:

1. The first is the anonymous function with lexical scope enclosed within the
   Grouping Operator ("()").
2. The second part creates the immediately invoked function expression ()
   through which the JavaScript engine will directly interpret the function.

```javascript
(function (params) {
  // statements...
})(params);
```

### Use cases

- Avoids polluting the global namespace (Good for code that won't be reused).
- Module pattern (to have private and public members).
- For loop with var before ES6 (avoids obtaining only the last value of a
  variable when we want the current value for some process).

## This keyword

Is the object that a function is property of.

If object "A" contains the function "B", then "this" (inside the function "B")
makes reference to the object "A".

## Types

### Primitives

- undefined
- Boolean
- Number
- String
- BigInt
- Symbol
- null

### Non primitives (data structures)

- Objects
  - Literal objects
  - Arrays
  - Functions
  - Any other

## Type coercion

Type coercion is the automatic or implicit conversion of values from one data
type to another.

Type conversion is only posible to the following types:

- String
- Boolean
- Number

### Primitives to primitives

#### String coercion

**Explicit conversion**:

- String()

**Implicit conversion**:

- Triggered by the binary "+" operator, when one of the operands is a string.

**Note**

- Symbols only can be converted explicitly (with String()).

**Examples**

Example 1:

```javascript
String(123); // explicit
123 + ""; // implicit
```

Example 2:

```javascript
// All primitive values are converted to strings naturally as you might expect:
String(123); // '123'
String(-12.3); // '-12.3'
String(null); // 'null'
String(undefined); // 'undefined'
String(true); // 'true'
String(false); // 'false'
```

Example 3:

```javascript
String(Symbol("my symbol")); // 'Symbol(my symbol)'
"" + Symbol("my symbol"); // TypeError is thrown
```

#### Boolean coercion

**Explicit conversion**:

- Boolean()

**Implicit conversion**:

- Triggered by logical context (if, for, while, switch, etc.).
- Triggered by logical operators (||, &&, !).

**Note**

- Logical operators such as || and && do boolean conversions internally, but
  actually return the value of original operands, even if they are not boolean.

**Examples**

```javascript
// List of all falsy values (any other value will return true):

Boolean(""); // false
Boolean(0); // false
Boolean(-0); // false
Boolean(NaN); // false
Boolean(null); // false
Boolean(undefined); // false
Boolean(false); // false
```

#### Numeric coercion

**Explicit conversion**:

- Number()

**Implicit conversion**:

- Triggered by comparison operators (>, <, <=,>=, ==, !=).
- Triggered by bitwise operators (|, &, ^, ~).
- Triggered by arithmetic operators (-, +, \*, /, % ).
- Triggered by "+" unary operator.

**Note**

- The binary operator "+" don't trigger coercion if one operand is a string (is
  already cover by string coercion).
- When converting a string to a number the engine first trims leading and
  trailing whitespace, \n, \t, etc., characters.
- Symbols cannot be converted to a number neither explicitly nor implicitly.
- The loose equality operators "==" and "!=" don't trigger coercion if:
  - Both operands are strings.
  - One operand is null (null is only equals to null and undefined).
  - One operand is undefined (undefined is only equals to undefined).
  - NaN is not equals to anything (not even itself).

**Examples**

```javascript
Number(null); // 0
Number(undefined); // NaN
Number(true); // 1
Number(false); // 0
Number(" 12 "); // 12
Number("-12.34"); // -12.34
Number("\n"); // 0
Number(" 12s "); // NaN
Number(123); // 123
```

### Objects to primitives

The same rules for primitives types (String, Boolean and Number) trigger type
coercion for objects. What changes is the logic about how objects are converted
into primitives types, before the final type.

#### Boolean coercion

As said before, because all that is not in the [list of falsy values](#boolean-coercion)
is true, all objects are converted to true (objects, arrays, functions, etc.).

#### String and Number coercion

Objects are converted to primitives via the internal [[ToPrimitive]] method,
which is responsible for both numeric and string conversion.

[[ToPrimitive]] is passed with an input value and preferred type of conversion:
Number or String. _preferredType_ is optional and this will be set to Number if
a number operation triggered the object coercion, and String for string
operations.

Both numeric and string conversion make use of two methods of the input object:
valueOf and toString. Both methods are declared on Object.prototype and thus
available for any derived types, such as Date, Array, etc.

**Logic for Object coercion to String**:

1. If input is already a primitive, return it.
2. Call toString().
3. If the result is primitive, return it.
4. Call valueOf().
5. If the result is primitive, return it.
6. Throw TypeError.

**Logic for Object coercion to Number**:

1. If input is already a primitive, return it.
2. Call valueOf().
3. If the result is primitive, return it.
4. Call toString().
5. If the result is primitive, return it.
6. Throw TypeError.

**Note**

- Most built-in types have "Number" as the default conversion mode.
- Date is an exception. It has "String" as default conversion mode.
- **Most built-in types** don't have valueOf() or its valueOf() return the
  object this itself (e.g. _myArray_.valueOf() returns the _myArray_), so it is
  ignored because it is not a primitive. That's why almost always toString() end
  up being called.
- The operators "==" (if aren't the same types (?)), "!=" (if aren't the same
  types (?)) and "+" (unary) trigger default conversion modes or equals to
  default. (preferredType is not especified or preferredType = default).
- Object coercion happens before the final type conversion, e.g. if a string
  operation triggered the object coercion, the [[toPrimitive]] method will try to
  convert the object to a primitive type and then it will be converted to String
  (which is what triggered the object coercion).

## Hoisting

---

Base concepts:

- Hoisting happens at the _creation_ stage when a new _execution context_ is
  created.
- Variables declared with the _var_ keyword are _function scoped_.
- Functions declared with the _function_ keyword are _function scoped_.
- Variables and functions declared with _const_ or _let_ have _block scope_ (if,
  while, for, function, etc., have blocks (delimited by "{" and "}")), so they're
  not hoisted.

---

The following elements are "Hoisted":

- Variables declared with the _var_ keyword.
- Function declared with the _function_ keyword.

Functions are completly hoisted, i.e. ("internally") the whole function
definition is "moved" to the begining of the scope.

Example:

```javascript
// Code as it is writen in the file. ///////////////////////////////////////////

function foo() {
  // Some statements (1)...

  function bar() {
    // Some statements (2)...
  }

  // Some statements (3)...

  function baz() {
    // Some statements (4)...
  }

  // Some statements (5)...
}

// Code as taken by the engine (hoisted) ///////////////////////////////////////

function foo() {
  function bar() {
    // Some statements (2)...
  }

  function baz() {
    // Some statements (4)...
  }

  // Some statements (1)...

  // Some statements (3)...

  // Some statements (5)...
}
```

Variables are partly hoisted, i.e. only the the declaration is hoisted, thus its
initial value is "undefined".

Example:

```javascript
// Code as it is writen in the file. ///////////////////////////////////////////

function foo() {
  // Some statements (1)...

  var bar = "bar";

  // Some statements (3)...

  var baz = "baz";

  // Some statements (5)...
}

// Code as taken by the engine (hoisted) ///////////////////////////////////////

function foo() {
  var bar = undefined;
  var baz = undefined;

  // Some statements (1)...

  bar = "bar";

  // Some statements (3)...

  baz = "baz";

  // Some statements (5)...
}
```

## References

Some info taken from:

- [JavaScript type coercion explained](https://www.freecodecamp.org/news/js-type-coercion-explained-27ba3d9a2839/)
- Some more probably missing.
