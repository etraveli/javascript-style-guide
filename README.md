# Javascript Style Guide

This is how we write Javascript code.

Other Style Guides

- [React](react/)
- [Testing](testing/)
- [CSS](css-in-javascript)
- [Linters](linters/)

## Table of Contents

1. [References](#references)
1. [Objects](#objects)
1. [Arrays](#arrays)
1. [Destructuring](#destructuring)
1. [Strings](#strings)
1. [Functions](#functions)
1. [Arrow Functions](#arrow-functions)
1. [Modules](#modules)
1. [Iterators and Generators](#iterators-and-generators)
1. [Properties](#properties)
1. [Variables](#variables)
1. [Comparison Operators & Equality](#comparison-operators--equality)
1. [Blocks](#blocks)
1. [Comments](#comments)
1. [Whitespace](#whitespace)
1. [Naming Conventions](#naming-conventions)

## References

<a name="references--prefer-const"></a><a name="1.1"></a>

- [1.1](#references--prefer-const) Use `const` for all of your references; avoid using `var`. eslint: [`prefer-const`](https://eslint.org/docs/rules/prefer-const.html), [`no-const-assign`](https://eslint.org/docs/rules/no-const-assign.html)

  > Why? This ensures that you can’t reassign your references, which can lead to bugs and difficult to comprehend code.

  ```javascript
  // bad
  var a = 1;
  var b = {};
  let c = 2;
  let d = [];

  // good
  const a = 1;
  const b = {};
  const c = [];
  ```

<a name="references--disallow-var"></a><a name="1.2"></a>

- [1.2](#references--disallow-var) If you must reassign references, use `let` instead of `var`. eslint: [`no-var`](https://eslint.org/docs/rules/no-var.html)

  > Why? `let` is block-scoped rather than function-scoped like `var`.

  ```javascript
  // bad
  var count = 1;
  if (true) {
    count += 1;
  }

  // good, use the let.
  let count = 1;
  if (true) {
    count += 1;
  }
  ```

**[⬆ back to top](#table-of-contents)**

## Objects

<a name="objects--no-new"></a><a name="2.1"></a>

- [2.1](#objects--no-new) Use the literal syntax for object creation. eslint: [`no-new-object`](https://eslint.org/docs/rules/no-new-object.html)

  ```javascript
  // bad
  const item = new Object();

  // good
  const item = {};
  ```

<a name="objects--grouped-shorthand"></a><a name="2.2"></a>

- [2.2](#objects--grouped-shorthand) Group your shorthand properties at the beginning of your object declaration. The rest should be in alphabetic order

  > Why? It’s easier to tell which properties are using the shorthand.

  ```javascript
  const anakinSkywalker = 'Anakin Skywalker';
  const lukeSkywalker = 'Luke Skywalker';

  // bad
  const obj = {
    episodeOne: 1,
    twoJediWalkIntoACantina: 2,
    lukeSkywalker,
    episodeThree: 3,
    mayTheFourth: 4,
    anakinSkywalker
  };

  // good
  const obj = {
    anakinSkywalker,
    lukeSkywalker,
    episodeOne: 1,
    episodeThree: 3,
    mayTheFourth: 4,
    twoJediWalkIntoACantina: 2
  };
  ```

  <a name="objects--quoted-props"></a><a name="2.3"></a>

- [2.3](#objects--quoted-props) Only quote properties that are invalid identifiers. eslint: [`quote-props`](https://eslint.org/docs/rules/quote-props.html)

  > Why? In general we consider it subjectively easier to read. It improves syntax highlighting, and is also more easily optimized by many JS engines.

  ```javascript
  // bad
  const bad = {
    'foo': 3,
    'bar': 4,
    'data-blah': 5
  };

  // good
  const good = {
    foo: 3,
    bar: 4,
    'data-blah': 5
  };
  ```

<a name="objects--rest-spread"></a><a name="2.4"></a>

- [2.4](#objects--rest-spread) Prefer the object spread operator over [`Object.assign`](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Object/assign) to shallow-copy objects. Use the object rest operator to get a new object with certain properties omitted.

  ```javascript
  // very bad
  const original = { a: 1, b: 2 };
  const copy = Object.assign(original, { c: 3 }); // this mutates `original` ಠ_ಠ
  delete copy.a; // so does this

  // bad
  const original = { a: 1, b: 2 };
  const copy = Object.assign({}, original, { c: 3 }); // copy => { a: 1, b: 2, c: 3 }

  // good
  const original = { a: 1, b: 2 };
  const copy = { ...original, c: 3 }; // copy => { a: 1, b: 2, c: 3 }

  const { a, ...noA } = copy; // noA => { b: 2, c: 3 }
  ```

**[⬆ back to top](#table-of-contents)**

## Arrays

<a name="arrays--literals"></a><a name="3.1"></a>

- [3.1](#arrays--literals) Use the literal syntax for array creation. eslint: [`no-array-constructor`](https://eslint.org/docs/rules/no-array-constructor.html)

  ```javascript
  // bad
  const items = new Array();

  // good
  const items = [];
  ```

<a name="arrays--push"></a><a name="3.2"></a>

- [3.2](#arrays--push) Use [Array#push](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Array/push) instead of direct assignment to add items to an array.

  ```javascript
  const someStack = [];

  // bad
  someStack[someStack.length] = 'abracadabra';

  // good
  someStack.push('abracadabra');
  ```

<a name="es6-array-spreads"></a><a name="3.3"></a>

- [3.3](#es6-array-spreads) Use array spreads `...` to copy arrays.

  ```javascript
  // bad
  const len = items.length;
  const itemsCopy = [];
  let i;

  for (i = 0; i < len; i += 1) {
    itemsCopy[i] = items[i];
  }

  // good
  const itemsCopy = [...items];
  ```

<a name="arrays--from"></a>
<a name="arrays--from-iterable"></a><a name="3.4"></a>

- [3.4](#arrays--from-iterable) To convert an iterable object to an array, use spreads `...` instead of [`Array.from`](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Array/from).

  ```javascript
  const foo = document.querySelectorAll('.foo');

  // good
  const nodes = Array.from(foo);
  const nodes = [...foo];
  ```

<a name="arrays--from-array-like"></a><a name="3.5"></a>

- [3.5](#arrays--from-array-like) Use [`Array.from`](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Array/from) for converting an array-like object to an array.

  ```javascript
  const arrLike = { 0: 'foo', 1: 'bar', 2: 'baz', length: 3 };

  // bad
  const arr = Array.prototype.slice.call(arrLike);

  // good
  const arr = Array.from(arrLike);
  ```

<a name="arrays--mapping"></a><a name="3.6"></a>

- [3.6](#arrays--mapping) Use [`Array.from`](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Array/from) instead of spread `...` for mapping over iterables, because it avoids creating an intermediate array.

  ```javascript
  // bad
  const baz = [...foo].map(bar);

  // good
  const baz = Array.from(foo, bar);
  ```

<a name="arrays--callback-return"></a><a name="3.7"></a>

- [3.7](#arrays--callback-return) Use return statements in array method callbacks. It’s ok to omit the return if the function body consists of a single statement returning an expression without side effects, following [8.2](#arrows--implicit-return). eslint: [`array-callback-return`](https://eslint.org/docs/rules/array-callback-return)

  ```javascript
  // good
  [1, 2, 3].map(x => {
    const y = x + 1;
    return x * y;
  });

  // good
  [1, 2, 3].map(x => x + 1);

  // bad - no returned value means `acc` becomes undefined after the first iteration
  [[0, 1], [2, 3], [4, 5]].reduce((acc, item, index) => {
    const flatten = acc.concat(item);
    acc[index] = flatten;
  });

  // good
  [[0, 1], [2, 3], [4, 5]].reduce((acc, item, index) => {
    const flatten = acc.concat(item);
    acc[index] = flatten;
    return flatten;
  });

  // bad
  inbox.filter(msg => {
    const { subject, author } = msg;
    if (subject === 'Mockingbird') {
      return author === 'Harper Lee';
    } else {
      return false;
    }
  });

  // good
  inbox.filter(({ subject, author }) => {
    if (subject === 'Mockingbird') {
      return author === 'Harper Lee';
    }

    return false;
  });

  // best
  inbox.filter(
    ({ subject, author }) =>
      subject === 'Mockingbird' ? author === 'Harper Lee' : false
  );
  ```

**[⬆ back to top](#table-of-contents)**

## Destructuring

<a name="destructuring--object"></a><a name="4.1"></a>

- [4.1](#destructuring--object) Use object destructuring when accessing and using multiple properties of an object. eslint: [`prefer-destructuring`](https://eslint.org/docs/rules/prefer-destructuring)

  > Why? Destructuring saves you from creating temporary references for those properties.

  ```javascript
  // bad
  const getFullName = user => {
    const firstName = user.firstName;
    const lastName = user.lastName;

    return `${firstName} ${lastName}`;
  };

  // good
  const getFullName = user => {
    const { firstName, lastName } = user;
    return `${firstName} ${lastName}`;
  };

  // best
  const getFullName = ({ firstName, lastName }) => `${firstName} ${lastName}`;
  ```

<a name="destructuring--array"></a><a name="4.2"></a>

- [4.2](#destructuring--array) Use array destructuring. eslint: [`prefer-destructuring`](https://eslint.org/docs/rules/prefer-destructuring)

  ```javascript
  const arr = [1, 2, 3, 4];

  // bad
  const first = arr[0];
  const second = arr[1];

  // good
  const [first, second] = arr;
  ```

**[⬆ back to top](#table-of-contents)**

## Strings

<a name="es6-template-literals"></a><a name="4.3"></a>

- [4.3](#es6-template-literals) When programmatically building up strings, use template strings instead of concatenation. eslint: [`prefer-template`](https://eslint.org/docs/rules/prefer-template.html) [`template-curly-spacing`](https://eslint.org/docs/rules/template-curly-spacing)

  > Why? Template strings give you a readable, concise syntax with proper newlines and string interpolation features.

  ```javascript
  // bad
  const sayHi = name => 'How are you, ' + name + '?';

  // bad
  const sayHi = name => ['How are you, ', name, '?'].join();

  // good
  const sayHi = name => `How are you, ${name}?`;
  ```

**[⬆ back to top](#table-of-contents)**

## Functions

<a name="es6-destructuring"></a><a name="4.4"></a>

- [4.4](#es6-destructuring) Always destructure if possbile

  ```javascript
  // bad
  const callMe = person =>
    `${person.name} wants you to call this number: ${person.number}`;

  // good
  const callMe = ({ name, number }) =>
    `${name} wants you to call this number: ${number}`;

  // good
  const callMe = person =>
    person
      ? `${person.name} wants you to call this number: ${person.number}`
      : null;
  ```

<a name="es6-rest"></a><a name="4.5"></a>

- [4.5](#es6-rest) Never use `arguments`, opt to use rest syntax `...` instead. eslint: [`prefer-rest-params`](https://eslint.org/docs/rules/prefer-rest-params)

  > Why? `...` is explicit about which arguments you want pulled. Plus, rest arguments are a real Array, and not merely Array-like like `arguments`.

  ```javascript
  // bad
  const concatenateAll = () => {
    const args = Array.prototype.slice.call(arguments);
    return args.join('');
  };

  // good
  const concatenateAll = (...args) => args.join('');
  ```

<a name="es6-default-parameters"></a><a name="4.6"></a>

- [4.6](#es6-default-parameters) Use default parameter syntax rather than mutating function arguments.

  ```javascript
  // really bad
  const handleThings = opts => {
    // No! We shouldn’t mutate function arguments.
    // Double bad: if opts is falsy it'll be set to an object which may
    // be what you want but it can introduce subtle bugs.
    opts = opts || {};
    // ...
  };

  // still bad
  const handleThings = opts => {
    if (opts === void 0) {
      opts = {};
    }
    // ...
  };

  // good
  const handleThings = (opts = {}) => {
    // ...
  };
  ```

<a name="functions--defaults-last"></a><a name="4.7"></a>

- [4.7](#functions--defaults-last) Always put default parameters last.

  ```javascript
  // bad
  const handleThings = (opts = {}, name) => {
    // ...
  }

  // good
  const handleThings = (name, opts = {}) => {
    // ...
  }
  ```

<a name="functions--constructor"></a><a name="4.8"></a>

- [4.8](#functions--constructor) Never use the Function constructor to create a new function. eslint: [`no-new-func`](https://eslint.org/docs/rules/no-new-func)

  > Why? Creating a function in this way evaluates a string similarly to `eval()`, which opens vulnerabilities.

  ```javascript
  // bad
  const add = new Function('a', 'b', 'return a + b');

  // still bad
  const subtract = Function('a', 'b', 'return a - b');
  
  // good
  const add = (a, b)  => a + b;
  const subtract = (a, b)  => a - b;
  ```

<a name="functions--reassign-params"></a><a name="4.9"></a>

- [4.9](#functions--reassign-params) Never reassign parameters. eslint: [`no-param-reassign`](https://eslint.org/docs/rules/no-param-reassign.html)

  > Why? Reassigning parameters can lead to unexpected behavior, especially when accessing the `arguments` object. It can also cause optimization issues, especially in V8.

  ```javascript
  // bad
  const f1 = a => {
    a = 1;
    // ...
  }

  const f2 = a => {
    if (!a) {
      a = 1;
    }
    // ...
  }

  // good
  const f3 = a => {
    const b = a || 1;
    // ...
  }

  const f4 = (a = 1) => {
    // ...
  }
  ```

**[⬆ back to top](#table-of-contents)**

## Arrow Functions

<a name="arrows--use-them"></a><a name="5.1"></a>

- [5.1](#arrows--use-them) Preferably use arrow functions over regular function expression, unless there is a specific need for it.

  ```javascript
  // bad
  function increase() {
    // ...
  }

  // good
  function increase() {
    this.counter++;
    // ...
  }

  // good
  const increase = () => {
    //...
  };
  ```

<a name="arrows--anonymous-use-them"></a><a name="5.2"></a>

- [5.2](#arrows--anonymous-use-them) When you must use an anonymous function (as when passing an inline callback), use arrow function notation. eslint: [`prefer-arrow-callback`](https://eslint.org/docs/rules/prefer-arrow-callback.html), [`arrow-spacing`](https://eslint.org/docs/rules/arrow-spacing.html)

  > Why? It creates a version of the function that executes in the context of `this`, which is usually what you want, and is a more concise syntax.

  > Why not? If you have a fairly complicated function, you might move that logic out into its own named function expression.

  ```javascript
  // bad
  [1, 2, 3].map(function(x) {
    const y = x + 1;
    return x * y;
  });

  // good
  [1, 2, 3].map(x => {
    const y = x + 1;
    return x * y;
  });
  ```

<a name="arrows--implicit-return"></a><a name="5.3"></a>

- [5.3](#arrows--implicit-return) If the function body consists of a single statement returning an [expression](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Expressions_and_Operators#Expressions) without side effects, omit the braces and use the implicit return. Otherwise, keep the braces and use a `return` statement. eslint: [`arrow-parens`](https://eslint.org/docs/rules/arrow-parens.html), [`arrow-body-style`](https://eslint.org/docs/rules/arrow-body-style.html)

  > Why? Syntactic sugar. It reads well when multiple functions are chained together.

  ```javascript
  // bad
  [1, 2, 3].map((number, index) => {
    return {
      [index]: number
    };
  });

  // good
  [1, 2, 3].map((number, index) => ({
    [index]: number
  }));

  // bad
  const getObject = (name, value) => {
    return {
      [name]: value
    };

  // good
  const getObject = (name, value) => ({
    [name]: value
  }));
  ```

  **[⬆ back to top](#table-of-contents)**

## Modules

<a name="modules--use-them"></a><a name="6.1"></a>

- [6.1](#modules--use-them) Always use modules (`import`/`export`) over a non-standard module system. You can always transpile to your preferred module system.

  > Why? Modules are the future, let’s start using the future now.

  ```javascript
  // bad
  const utils = require('./utils');
  module.exports = utils.es6;

  // ok
  import utils from './utils';
  export default utils.es6;

  // best
  import { es6 } from './utils';
  export default es6;
  ```

  <a name="modules--no-wildcard"></a><a name="6.2"></a>

- [6.2](#modules--no-wildcard) Do not use wildcard imports. Unless needed for specific cases.

  > Why? This makes sure you have a single default export.

  ```javascript
  // bad
  import * as utils from './utils';

  // good
  import utils from './utils';
  ```

  <a name="modules--no-duplicate-imports"></a><a name="6.3"></a>

- [6.3](#modules--no-duplicate-imports) Only import from a path in one place.
  eslint: [`no-duplicate-imports`](https://eslint.org/docs/rules/no-duplicate-imports)

  > Why? Having multiple lines that import from the same path can make code harder to maintain.

  ```javascript
  // bad
  import foo from 'foo';
  // … some other imports … //
  import { named1, named2 } from 'foo';

  // good
  import foo, { named1, named2 } from 'foo';
  ```

  <a name="modules--default-export"></a><a name="6.4"></a>

- [6.4](#modules-default-export) In modules where default export exist prefer a named function

  > Why? Function declarations are hoisted, which means that it’s easy - too easy - to reference the function before it is defined in the file. This harms readability and maintainability. If you find that a function’s definition is large or complex enough that it is interfering with understanding the rest of the file, then perhaps it’s time to extract it to its own module! Don’t forget to explicitly name the expression, regardless of whether or not the name is inferred from the containing variable (which is often the case in modern browsers or when using compilers such as Babel). This eliminates any assumptions made about the Error’s call stack. ([Discussion](https://github.com/airbnb/javascript/issues/794))

  ```javascript
    // bad
    export default function() {}
    export default () => {}

    // good
    const foo = () => {}
    export default foo;
  ```

<a name="modules--imports-first"></a><a name="6.5"></a>

- [6.5](#modules--imports-first) Put all `import`s above non-import statements.
  eslint: [`import/first`](https://github.com/benmosher/eslint-plugin-import/blob/master/docs/rules/first.md)

  > Why? Since `import`s are hoisted, keeping them all at the top prevents surprising behavior.

  ```javascript
  // bad
  import foo from 'foo';
  foo.init();

  import bar from 'bar';

  // good
  import foo from 'foo';
  import bar from 'bar';

  foo.init();
  ```

  **[⬆ back to top](#table-of-contents)**

## Iterators and Generators

<a name="iterators--nope"></a><a name="7.1"></a>

- [7.1](#iterators--nope) Don’t use iterators. Prefer JavaScript’s higher-order functions instead of loops like `for-in` or `for-of`. eslint: [`no-iterator`](https://eslint.org/docs/rules/no-iterator.html) [`no-restricted-syntax`](https://eslint.org/docs/rules/no-restricted-syntax)

  > Why? This enforces our immutable rule. Dealing with pure functions that return values is easier to reason about than side effects.

  > Use `map()` / `every()` / `filter()` / `find()` / `findIndex()` / `reduce()` / `some()` / ... to iterate over arrays, and `Object.keys()` / `Object.values()` / `Object.entries()` to produce arrays so you can iterate over objects.

  ```javascript
  const numbers = [1, 2, 3, 4, 5];

  // bad
  let sum = 0;
  for (let num of numbers) {
    sum += num;
  }
  sum === 15;

  // good
  let sum = 0;
  numbers.forEach(num => {
    sum += num;
  });
  sum === 15;

  // best (use the functional force)
  const sum = numbers.reduce((total, num) => total + num, 0);
  sum === 15;

  // bad
  const increasedByOne = [];
  for (let i = 0; i < numbers.length; i++) {
    increasedByOne.push(numbers[i] + 1);
  }

  // good
  const increasedByOne = [];
  numbers.forEach(num => {
    increasedByOne.push(num + 1);
  });

  // best (keeping it functional)
  const increasedByOne = numbers.map(num => num + 1);
  ```

  **[⬆ back to top](#table-of-contents)**

## Properties

<a name="properties--dot"></a><a name="8.1"></a>

- [8.1](#properties--dot) Use dot notation when accessing properties. eslint: [`dot-notation`](https://eslint.org/docs/rules/dot-notation.html)

  ```javascript
  const luke = {
    jedi: true,
    age: 28
  };

  // bad
  const isJedi = luke['jedi'];

  // good
  const isJedi = luke.jedi;

  // good
  const universe = {
    'the-force': true,
  }
  const hasForce = universe['the-force'];

  ```

## Variables

<a name="variables--define-where-used"></a><a name="9.1"></a>

- [9.1](#variables--define-where-used) Assign variables where you need them, but place them in a reasonable place.

  > Why? `let` and `const` are block scoped and not function scoped.

  ```javascript
  // bad - unnecessary function call
  const checkName = hasName => {
    const name = getName();

    if (hasName === 'test') {
      return false;
    }

    if (name === 'test') {
      this.setName('');
      return false;
    }

    return name;
  }

  // good
  const checkName = hasName => {
    if (hasName === 'test') {
      return false;
    }

    const name = getName();

    if (name === 'test') {
      this.setName('');
      return false;
    }

    return name;
  }
  ```

  **[⬆ back to top](#table-of-contents)**

## Comparison Operators & Equality

<a name="comparison--eqeqeq"></a><a name="10.1"></a>

- [10.1](#comparison--eqeqeq) Use `===` and `!==` over `==` and `!=`. eslint: [`eqeqeq`](https://eslint.org/docs/rules/eqeqeq.html)

  ```javascript
    // bad 
    if (value == 1) {
      // ...
    }

    // good 
    if (value === 1) {
      // ...
    }
  ```

<a name="comparison--eqeqeq"></a><a name="10.2"></a>
- [10.2](#comparison--Boolean) Use `Boolean` not double negative (`!!`), to cast a value to a boolean

  ```javascript
  // bad
  if (!!value) {
    //...
  }

  // good
  if (Boolean(value)) {
    //...
  }
  ```

<a name="comparison--shortcuts"></a><a name="10.3"></a>

- [10.3](#comparison--shortcuts) Use shortcuts for booleans, but explicit comparisons for strings and numbers.

  ```javascript
  // bad
  if (isValid === true) {
    // ...
  }

  // good
  if (isValid) {
    // ...
  }

  // bad
  if (name) {
    // ...
  }

  // good
  if (name !== '') {
    // ...
  }

  // bad
  if (collection.length) {
    // ...
  }

  // good
  if (collection.length > 0) {
    // ...
  }
  ```

**[⬆ back to top](#table-of-contents)**

## Blocks

<a name="blocks--braces"></a><a name="11.1"></a>

- [11.1](#blocks--braces) Use braces with all multi-line blocks. eslint: [`nonblock-statement-body-position`](https://eslint.org/docs/rules/nonblock-statement-body-position)

  ```javascript
  // bad
  if (test) return false;

  // bad
  if (test) { return false };

  // good
  if (test) {
    return false;
  }
  ```

**[⬆ back to top](#table-of-contents)**

## Comments

<a name="comments--todo"></a><a name="12.1"></a>

- [12.1](#comments--todo) Use `// TODO:` to annotate solutions to problems. It should be connected to a story in JIRA.

  ```javascript
  // good
  const calculator = () => {
    // TODO: add logic to calculate: ETIWEB-1234
    // or
    // TODO: add logic to calculate: http://jira.url.net/ETIWEB-1234
  };
  ```

**[⬆ back to top](#table-of-contents)**

## Whitespace

<a name="whitespace--after-blocks"></a><a name="12.1"></a>

- [12.1](#whitespace--after-blocks) Leave a blank line after blocks and before the next statement.

  ```javascript
  // bad
  if (foo) {
    return bar;
  }
  return baz;

  // good
  if (foo) {
    return bar;
  }

  return baz;

  // bad
  const obj = {
    foo() {
    },
    bar() {
    },
  };
  return obj;

  // good
  const obj = {
    foo() {
    },

    bar() {
    },
  };

  return obj;

  // bad
  const arr = [
    foo() {
    },
    bar() {
    },
  ];
  return arr;

  // good
  const arr = [
    foo() {
    },

    bar() {
    },
  ];

  return arr;
  ```

  **[⬆ back to top](#table-of-contents)**

## Naming Conventions

<a name="naming--descriptive"></a><a name="13.1"></a>

- [13.1](#naming--descriptive) Avoid single letter names. Be descriptive with your naming. eslint: [`id-length`](https://eslint.org/docs/rules/id-length)

  ```javascript
  // bad
  const q = () => {
    // ...
  };

  // good
  const query = () => {
    // ...
  };
  ```

  <a name="naming--camelCase"></a><a name="13.2"></a>

- [13.2](#naming--camelCase) Use camelCase when naming objects, functions, and instances. eslint: [`camelcase`](https://eslint.org/docs/rules/camelcase.html)

  ```javascript
  // bad
  const OBJEcttsssss = {};
  const this_is_my_object = {};
  const c = () => {}

  // good
  const thisIsMyObject = {};
  const thisIsMyFunction = () => {}
  ```

  <a name="naming--PascalCase"></a><a name="13.3"></a>

- [13.3](#naming--PascalCase) Use PascalCase only when naming constructors, classes or components. eslint: [`new-cap`](https://eslint.org/docs/rules/new-cap.html)

  ```javascript
  // bad
  function user(options) {
    this.name = options.name;
  }

  const bad = new user({
    name: 'nope'
  });

  const user = ({ name }) => <h1>{name}</h1>;

  // good
  class User {
    constructor(options) {
      this.name = options.name;
    }
  }

  const good = new User({
    name: 'yup'
  });

  const User = ({ name }) => <h1>{name}</h1>;
  ```

  <a name="naming--leading-underscore"></a><a name="13.4"></a>

- [13.4](#naming--leading-underscore) Do not use trailing or leading underscores. eslint: [`no-underscore-dangle`](https://eslint.org/docs/rules/no-underscore-dangle.html)

  > Why? JavaScript does not have the concept of privacy in terms of properties or methods. Although a leading underscore is a common convention to mean “private”, in fact, these properties are fully public, and as such, are part of your public API contract. This convention might lead developers to wrongly think that a change won’t count as breaking, or that tests aren’t needed. tl;dr: if you want something to be “private”, it must not be observably present.

  ```javascript
  // bad
  this.__firstName__ = 'Panda';
  this.firstName_ = 'Panda';
  this._firstName = 'Panda';

  // good
  this.firstName = 'Panda';

  // This is private
  const doStuff = x => x * 1;

  const myFunc = a => doStuff(a);

  export default myFunc
  ```

<a name="naming--self-this"></a><a name="13.5"></a>

- [13.5](#naming--self-this) Don’t save references to `this`. Use arrow functions or [Function#bind](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind).

  ```javascript
  // bad
  class {
    constructor() {
      document.querySelector('button').addEventListener('click', () => this.handleClick)
    }
    
    handleClick = () => {
      // ...
    };
  }

  // bad
  function foo() {
    const that = this;
    return function() {
      console.log(that);
    };
  }

  // good
  function foo() {
    return () => {
      console.log(this);
    };
  }
  ```

  <a name="naming--filename-matches-export"></a><a name="13.6"></a>
 - [13.6](#naming--filename-matches-export) A base filename should exactly match the name of its default export.
 
    ```javascript
    // file 1 contents
    class CheckBox {
      // ...
    }
    export default CheckBox;

    // file 2 contents
    const fortyTwo = () => 42;
    export default fortyTwo;

    // file 3 contents
    const insideDirectory = () => {}
    export default insideDirectory;

    // in some other file
    // bad
    import CheckBox from './checkBox'; // PascalCase import/export, camelCase filename
    import FortyTwo from './FortyTwo'; // PascalCase import/filename, camelCase export
    import InsideDirectory from './InsideDirectory'; // PascalCase import/filename, camelCase export

    // bad
    import CheckBox from './check_box'; // PascalCase import/export, snake_case filename
    import forty_two from './forty_two'; // snake_case import/filename, camelCase export
    import inside_directory from './inside_directory'; // snake_case import, camelCase export
    import index from './inside_directory/index'; // requiring the index file explicitly
    import insideDirectory from './insideDirectory/index'; // requiring the index file explicitly

    // good
    import CheckBox from './CheckBox'; // PascalCase export/import/filename
    import fortyTwo from './fortyTwo'; // camelCase export/import/filename
    import insideDirectory from './insideDirectory'; // camelCase export/import/directory name/implicit "index"
    // ^ supports both insideDirectory.js and insideDirectory/index.js
    ```

## License

(MIT License)

Copyright (c) 2018 Etraveli Technology

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

## Amendments

This style guide was heavily inspired by Airbnb's guide: https://github.com/airbnb/javascript

**[⬆ back to top](#table-of-contents)**
