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

## Closures

Closures are posible due to the following things:

- Funtions are first class citizens (we can treat them as other types, i.e. can
  be assigned to variables, been passed as parameters or been returned from
  functions).
- Lexical scope/environment.

A closure means a function can access the variables from an enclosing
scope/environment even after it leaves the scoped in which it was declared. This
is posible because if a variable from the outside function is used in the inner
function, due to the inner function keeps a reference to the outer variable,
that variable is not destroyed (is not garbage collected).

```javascript
function foo() {
  const baz = "baz";

  function bar() {
    console.log(`I'm the ${baz} variable`);
  }

  // Returning the "bar" function creates a closure because it is making a
  // reference to the "baz" variable and then this function is leaving the scope
  // where it was created.
  return bar;
}

const newFunc = foo();

// This prints "I'm the baz variable" because a reference to the "baz" variable
// is still available (not detroyed/garbage collected) even if the "foo"
// function was already executed.
newFunc();
```

## Prototypes

Prototypes are the mechanism by which JavaScript objects inherit features from
one another. It is not classic inheritance like Java.

JavaScript is often described as a prototype-based language — to provide
inheritance, objects can have a prototype object, which acts as a template
object that it inherits methods and properties from.

An object's prototype object may also have a prototype object, which it inherits
methods and properties from, and so on. This is often referred to as a
**prototype chain**, and explains why different objects have properties and
methods defined on other objects available to them.

Every (and only) functions have a "protoype" property. This "property" object is
what other objects will inherit.

"Object" is a function (a constructor function). "Object.protoype" (is
called the base object) is what all objects inherit.

With "\_\_proto\_\_" and "Object.getPrototypeOf(obj)" we can get the constructor
function used to create that object, i.e points to the "prototype" property of
the constructor function:

```javascript
// Suppose Foobar is a constructor function.
const fooInstance = new Foobar();

// Then
Object.getPrototypeOf(fooInstance) === Foobar.prototype;
fooInstance.__proto__ === Foobar.prototype;
```

With the "new" operator, (after creating the object in memory and before
running function Foo() with this defined to it) the reference to the protoype
object is copied to the internally "[[prototype]]" of the new instance.

Example:

```javascript
const obj = new Foo(); // Being "Foo" a constructor function.

// "const obj = Foo();" Internally is somethig like this:
const obj = new Object();
obj[[protoype]] = Foo.prototype;
Foo.call(obj);
```

Example of prototyping with constructor function:

```javascript
// constructor function
function Person(name, age) {
  this.name = name;
  this.age = age;
  this.health = "healthy";

  this.greet = function () {
    console.log(`Hello I'm ${this.name} and I'm ${this.age}`);
  };
}

// Create objects
const person1 = new Person("John", 18);
const person2 = new Person("Tony", 40);

// Access properties
console.log(person1.name); // John
console.log(person1.greet()); // Tony

console.log(person2.name); // Hello I'm John and I'm 18
console.log(person2.greet()); // Hello I'm Tony and I'm 18
```

Example of prototyping by creating an object taking as a prototype other object:

```javascript
const human = {
  isMortal: true,
};

// A "safety" way to create a protoype (without modifying the "__proto__" property directly).
const person = Object.create(human);
person.age = 23; // This property only exists in the "person" object.

console.log("human is protoype of person: ", human.isPrototypeOf(person)); // true. "human" was used as prototype to create "person".

console.log("person: ", person); // {age: 23}.
console.log("person age: ", person.age); // 23.
console.log("person is mortal: ", person.isMortal); // true. Inherits the property from "human".

console.log("person.__proto__: ", person.__proto__); // {isMortal: true}. Makes reference to the prototype used to be created.
console.log("human.prototype: ", human.protoype); // undefined since is not a constructor function.

console.log(
  "person has own property isMortal: ",
  person.hasOwnProperty("isMortal")
); // false. "isMortal" only exists in "human".

console.log(
  "person.__proto__ has own property isMortal: ",
  person.__proto__.hasOwnProperty("isMortal")
); // true. The prototype("human") used to create the "person" object has defined the "isMortal" property.
```

## "new" operator (constructor functions)

The new operator lets developers create an instance of a user-defined object
type or of one of the built-in object types that has a constructor function.

The "new" keyword does the following things:

1. Creates a blank, plain JavaScript object.
2. Adds a property to the new object (\_\_proto\_\_) that links to the
   **constructor function's prototype** object.
3. Binds the newly created object instance as the **this** context (i.e. all
   references to **this** in the constructor function now refer to the object
   created in the first step).
4. Returns this if the function doesn't return an object.

**Note**:

Properties/objects added to the **construction function prototype** are
therefore accessible to all instances created from the constructor function
(using new).

Example:

```javascript
// This not exactly how it works, but is useful to ilustrate.

const obj = new Foo(arg1, arg2, ...); // Being "Foo" a constructor function.

// "const obj = Foo();" Internally is somethig like this:
const obj = new Object();
obj.__proto__ = Foo.prototype;
Foo.call(obj, arg1, arg2, ...); // "this" inside the constructor function takes the value of "obj".

```

## Classes (ES6)

Classes are a template for creating objects. They encapsulate data with code to
work on that data. Classes in JS are built on prototypes but also have some
syntax and semantics that are not shared with ES5 class-like semantics.

Classes are "special functions". Clases are just "syntactic sugar" because under
the hood is not using "classical inheritance", but "prototype-based
inheritance".

The body of a class is executed in strict mode.

The constructor method is a special method for creating and initializing an
object created with a class. There can only be one special method with the name
"constructor" in a class. A SyntaxError will be thrown if the class contains
more than one occurrence of a constructor method.

Classes can be writen as class declaration or class expressions (as same as
functons), but the difference is that classes declarations are not hoisted.

Example of class declaration and class expression:

```javascript
// Class declaration
class Rectangle {
  constructor(height, width) {
    this.height = height;
    this.width = width;
  }
}

// Class expression (unnamed)
let Rectangle = class {
  constructor(height, width) {
    this.height = height;
    this.width = width;
  }
};
console.log(Rectangle.name); // Rectangle

// Class expression (named)
let Rectangle = class Rectangle2 {
  constructor(height, width) {
    this.height = height;
    this.width = width;
  }
};
console.log(Rectangle.name); // Rectangle2
```

All **instance** methods and properties are public by default:

```javascript
class Foo {
  constructor(publicInstanceProperty) {
    this.publicInstanceProperty = publicInstanceProperty;
  }

  publicInstanceMethod() {
    return "I'm a public instance method";
  }
}

const fooInstance = new Foo("I'm a public instance property");

console.log(fooInstance.publicInstanceProperty); // "I'm a public instance property".
console.log(fooInstance.publicInstanceMethod()); //  "I'm a public instance method".
```

**Instance** properties can be defined inside or outside the constructor, but
**instance** properties defined outside of the constructor can have or not a
default value (useful when some instance data definition or initialization does
not required to be passed to the constructor):

```javascript
class Person {
  foo; // Instance property.
  bar = 10; // Instance property.

  constructor(name, age) {
    this.name = name; // Instance property.
    this.age = age; // Instance property.
  }
}

const personObj = new Person("John", 45);

console.log(personObj.foo); // undefined.
console.log(personObj.bar); // 10.
console.log(personObj.name); // 'John'.
console.log(personObj.age); // 45.
```

The "static" keyword defines a static method or property for a class. Static
members (properties and methods) are called without instantiating their class
and cannot be called through a class instance. Static methods are often used to
create utility functions for an application, whereas static properties are
useful for caches, fixed-configuration, or any other data you don't need to be
replicated across instances. A class with a static member can be sub-classed. In
order to call a static method or property within another static method of the
same class, you can use the this keyword.

Example of static members:

```javascript
class Point {
  constructor(x, y) {
    this.x = x;
    this.y = y;
  }

  static displayName = "Point";

  static distance(a, b) {
    const dx = a.x - b.x;
    const dy = a.y - b.y;

    return Math.hypot(dx, dy);
  }
}

const p1 = new Point(5, 5);
const p2 = new Point(10, 10);

console.log(p1.displayName); // undefined
console.log(p1.distance); // undefined
console.log(p2.displayName); // undefined
console.log(p2.distance); // undefined

console.log(Point.displayName); // "Point"
console.log(Point.distance(p1, p2)); // 7.0710678118654755
```

Private properties and methods can be writen or read within the class body.By
defining things that are not visible outside of the class, you ensure that your
classes's users can't depend on internals, which may change from version to
version:

```javascript
class AClass {
  // Private members are defined using "#".

  static #foo = 1;
  #bar = 10;
  #someVar;

  constructor(someVar) {
    this.#someVar = someVar;
  }

  #foobar() {
    return 'Im private "foobar" instance method';
  }

  static #foobaz() {
    return 'Im private static "foobaz" method';
  }

  printMembers() {
    console.log(AClass.#foo); // Static private members can only be use inside the class and with the class name.
    console.log(AClass.#foobaz()); // Static private members can only be use inside the class and with the class name.
    console.log(this.#bar); // Private members can only be use inside the class.
    console.log(this.#someVar); // Private members can only be use inside the class.
    console.log(this.#foobar()); // Private members can only be use inside the class.
  }
}

const aClassInstance = new AClass("someVar value");

console.log(AClass.#foo); // Error thrown.
console.log(AClass.#foobaz()); // Error thrown.
console.log(aClassInstance.#bar); // Error thrown.
console.log(aClassInstance.#foobar()); // Error thrown.
console.log(aClassInstance.printMembers());
```

It is a syntax error to refer to # names from out of scope. It is also a syntax
error to refer to private fields that were not declared before they were called,
or to attempt to remove declared fields with delete:

```javascript
class ClassWithPrivateField {
  #privateField;

  constructor() {
    this.#privateField = 42;
    delete this.#privateField;   // Syntax error
    this.#undeclaredField = 444; // Syntax error
  }
}

const instance = new ClassWithPrivateField()
instance.#privateField === 42;   // Syntax error

```

"this" in ES6 classes behaves the same as in constructor functions and object
methods, but the difference is that the value of "this" will be undefined if it
is called without a value for "this" (due to the body of a class is executed in
strict mode), instead of referencing something like the global object.

Example of binding "this" with "prototype" (class (?)) and static methods:

```javascript
class Animal {
  speak() {
    return this;
  }
  static eat() {
    return this;
  }
}

let obj = new Animal();
obj.speak(); // the Animal object
let speak = obj.speak;
speak(); // undefined (not the global object)

Animal.eat(); // class Animal
let eat = Animal.eat;
eat(); // undefined (not the global object)
```

## References

Some info taken from:

- [JavaScript type coercion explained](https://www.freecodecamp.org/news/js-type-coercion-explained-27ba3d9a2839/)
- [JavaScript Constructor Function](https://www.programiz.com/javascript/constructor-function)
- [Object prototypes](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Objects/Object_prototypes)
- [new operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/new)
- [Classes](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes)
- [Private class features](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes/Private_class_fields)
- Some more probably missing.
