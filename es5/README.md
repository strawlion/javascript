# JavaScript Style Guide() {

*A mostly reasonable approach to JavaScript*


## Table of Contents

  1. [Types](#types)
  1. [Objects](#objects)
  1. [Arrays](#arrays)
  1. [Strings](#strings)
  1. [Functions](#functions)
  1. [Properties](#properties)
  1. [Variables](#variables)
  1. [Hoisting](#hoisting)
  1. [Comparison Operators & Equality](#comparison-operators--equality)
  1. [Blocks](#blocks)
  1. [Comments](#comments)
  1. [Whitespace](#whitespace)
  1. [Commas](#commas)
  1. [Semicolons](#semicolons)
  1. [Type Casting & Coercion](#type-casting--coercion)
  1. [Naming Conventions](#naming-conventions)
  1. [Constructors](#constructors)
  1. [Events](#events)
  1. [Modules](#modules)
  1. [jQuery](#jquery)
  1. [ECMAScript 5 Compatibility](#ecmascript-5-compatibility)
  1. [Testing](#testing)
  1. [Performance](#performance)
  1. [Resources](#resources)

## Types

  - **Primitives**: When you access a primitive type you work directly on its value.
  
    + `string`
    + `number`
    + `boolean`
    + `null`
    + `undefined`

    ```javascript
    var foo = 1;
    var bar = foo;

    bar = 9;

    console.log(foo, bar); // => 1, 9
    ```
  - **Complex**: When you access a complex type you work on a reference to its value.

    + `object`
    + `array`
    + `function`

    ```javascript
    var foo = [1, 2];
    var bar = foo;

    bar[0] = 9;

    console.log(foo[0], bar[0]); // => 9, 9
    ```

**[⬆ back to top](#table-of-contents)**

## Objects

  - Use the literal syntax for object creation.

    ```javascript
    // bad
    var item = new Object();

    // good
    var item = {};
    ```

**[⬆ back to top](#table-of-contents)**

## Arrays

  - Use the literal syntax for array creation.

    ```javascript
    // bad
    var items = new Array();

    // good
    var items = [];
    ```

  - Use Array#push instead of direct assignment to add items to an array.

    ```javascript
    var someStack = [];

    // bad
    someStack[someStack.length] = 'abracadabra';

    // good
    someStack.push('abracadabra');
    ```
    
  - Use higher order Array functions over imperative for loops. An exception can be made for
  any performance sensitive code
    
    ```javascript
    var values = [0, 1, 2, 3, 4, 5];
    
    // bad
    var valuesGreaterThan2 = [];
    for (var i = 0; i < values.length; i++) {
      var value = values[i];
      if (value > 2) { 
        valuesGreaterThan2.push(value); 
      }
    }
    
    // good
    values.filter(isGreaterThan2);
    
    // bad
    var squaredValues = [];
    for (var i = 0; i < values.length; i++) {
      var value = values[i];
      squaredValues.push(value * value);
    }
    
    // good
    values.map(toSquaredValue);
    
    // bad
    var doesValuesContain7 = false;
    for (var i = 0; i < values.length; i++) {
      var value = values[i];
      
      if (value === 7) {
        doesValuesContain7 = true;
        break;
      }
    }
    
    // good
    values.some(equals7);
    
    // bad
    var areAllValuesPositive = false;
    for (var i = 0; i < values.length; i++) {
      var value = values[i];
      
      if (value < 0) {
        areAllValuesPositive = false;
        break;
      }
    }
    
    // good
    values.every(isPositive);
    ```

  - Use reduce when creating a value from an array
    
    ```javascript
    var values = [5, 6, 1, 3];
    
    // bad
    var sum = 0;
    values.forEach(addToSum);
    
    // good
    var sum = values.reduce(toSum, 0);
    
    // bad
    var idToObject = {};
    values.forEach(addToMap);
    
    // good
    var idToObject = values.reduce(toMap, {});
    ```

  - When you need to copy an array use Array#slice. [jsPerf](http://jsperf.com/converting-arguments-to-an-array/7)

    ```javascript
    var len = items.length;
    var itemsCopy = [];
    var i;

    // bad
    for (i = 0; i < len; i++) {
      itemsCopy[i] = items[i];
    }

    // good
    itemsCopy = items.slice();
    ```

  - To convert an array-like object to an array, use Array#slice.

    ```javascript
    function trigger() {
      var args = Array.prototype.slice.call(arguments);
      ...
    }
    ```

**[⬆ back to top](#table-of-contents)**


## Strings

  - Use single quotes `''` for strings.

    ```javascript
    // bad
    var name = "Bob Parr";

    // good
    var name = 'Bob Parr';

    // bad
    var fullName = "Bob " + this.lastName;

    // good
    var fullName = 'Bob ' + this.lastName;
    ```

  - When programmatically building up a string, use Array#join instead of string concatenation. Mostly for IE: [jsPerf](http://jsperf.com/string-vs-array-concat/2).

    ```javascript
    var items;
    var messages;
    var length;
    var i;

    messages = [{
      state: 'success',
      message: 'This one worked.'
    }, {
      state: 'success',
      message: 'This one worked as well.'
    }, {
      state: 'error',
      message: 'This one did not work.'
    }];

    length = messages.length;

    // bad
    function inbox(messages) {
      items = '<ul>';

      for (i = 0; i < length; i++) {
        items += '<li>' + messages[i].message + '</li>';
      }

      return items + '</ul>';
    }

    // good
    function inbox(messages) {
      items = [];

      for (i = 0; i < length; i++) {
        // use direct assignment in this case because we're micro-optimizing.
        items[i] = '<li>' + messages[i].message + '</li>';
      }

      return '<ul>' + items.join('') + '</ul>';
    }
    ```

**[⬆ back to top](#table-of-contents)**


## Functions

  - Function expressions:

    ```javascript
    // anonymous function expression
    var anonymous = function() {
      return true;
    };

    // named function expression
    var named = function named() {
      return true;
    };

    // immediately-invoked function expression (IIFE)
    (function() {
      console.log('Welcome to the Internet. Please follow me.');
    })();
    ```
    
  - Function declarations:
  
    ```javascript
    function name() {
      return true;
    }
    ```
    

  - Never declare a function in a non-function block (if, while, etc). Browsers will allow you to do it, but they all interpret it differently, which is bad news bears.
  Declare the function at the bottom of the first valid containing block, or use a function expression if micro optimization is necessary.
  - **Note:** ECMA-262 defines a `block` as a list of statements. A function declaration is not a statement. [Read ECMA-262's note on this issue](http://www.ecma-international.org/publications/files/ECMA-ST/Ecma-262.pdf#page=97).

    ```javascript
    // bad
    if (currentUser) {
      test();
      function test() {
        console.log('Nope.');
      }
    }

    // better
    if (currentUser) {
      var test = function test() {
        console.log('Meh.');
      };
      test();
    }
    
    // best
    if (currentUser) {
      test();
    }
    
    function test() {
      console.log('Yup.');
    }
    ```
    
  - Declare inner functions below the return value. 
  
   > Why? The high level logic for a given function should be up front and clear.
   The implementation details of individual steps are not important unless there is an 
   issue with the parent function, in which case the user can "opt in" to reading them.
  
    ```javascript
    // bad
    function areAllDatesWithinRange(dates, range) {
      
      function toMoment(date) {
        return moment(date);
      }
      
      function isWithinRange(moment) {
        return moment.isBetween(range.start, range.end);
      }
      
      return dates.map(toMoment)
                  .every(isWithinRange);
    }
    
    // good
    function areAllDatesWithinRange(dates, range) {
      
      return dates.map(toMoment)
                  .every(isWithinRange);
                  
      function toMoment(date) {
        return moment(date);
      }
      
      function isWithinRange(moment) {
        return moment.isBetween(range.start, range.end);
      }
    }
    ```

  - Avoid use of unnecessary function "wrappers".

    ```javascript
    
    function logResult(result) {
      console.log(result);
    }
    
    // bad
    doSomething.then(function(result) {
      logResult(result);
    });
    
    // good
    doSomething.then(logResult);
    ```
    

  - Never name a parameter `arguments`. This will take precedence over the `arguments` object that is given to every function scope.

    ```javascript
    // bad
    function nope(name, options, arguments) {
      // ...stuff...
    }

    // good
    function yup(name, options, args) {
      // ...stuff...
    }
    ```

**[⬆ back to top](#table-of-contents)**



## Properties

  - Use dot notation when accessing properties.

    ```javascript
    var luke = {
      isJedi: true,
      age: 28
    };

    // bad
    var isJedi = luke['isJedi'];

    // good
    var isJedi = luke.isJedi;
    ```

  - Use subscript notation `[]` when accessing properties with a variable.

    ```javascript
    var luke = {
      isJedi: true,
      age: 28
    };

    function getProp(prop) {
      return luke[prop];
    }

    var isJedi = getProp('isJedi');
    ```

**[⬆ back to top](#table-of-contents)**


## Variables

  - Always use `var` to declare variables. Not doing so will result in global variables. We want to avoid polluting the global namespace. Captain Planet warned us of that.

    ```javascript
    // bad
    superPower = new SuperPower();

    // good
    var superPower = new SuperPower();
    ```

  - Use one `var` declaration per variable.
    It's easier to add new variable declarations this way, and you never have
    to worry about swapping out a `;` for a `,` or introducing punctuation-only
    diffs.

    ```javascript
    // bad
    var items = getItems(),
        goSportsTeam = true,
        dragonball = 'z';

    // bad
    // (compare to above, and try to spot the mistake)
    var items = getItems(),
        goSportsTeam = true;
        dragonball = 'z';

    // good
    var items = getItems();
    var goSportsTeam = true;
    var dragonball = 'z';
    ```

  - Declare unassigned variables last. This is helpful when later on you might need to assign a variable depending on one of the previous assigned variables.

    ```javascript
    // bad
    var i, len, dragonball,
        items = getItems(),
        goSportsTeam = true;

    // bad
    var i;
    var items = getItems();
    var dragonball;
    var goSportsTeam = true;
    var len;

    // good
    var items = getItems();
    var goSportsTeam = true;
    var dragonball;
    var length;
    var i;
    ```

  - Assign variables nearest to their use, but be aware of hoisting.

    ```javascript
    // bad
    function() {
      var name = getName();

      test();
      console.log('doing stuff..');

      //..other stuff..

      if (name === 'test') {
        return false;
      }

      return name;
    }
    
    // bad
    function() {
      var name;

      if (!arguments.length) {
        return false;
      }

      name = getName();
      this.setFirstName(name);

      return true;
    }
    
    // good
    function() {
      
      test();
      console.log('doing stuff..');

      //..other stuff..

      var name = getName();
      if (name === 'test') {
        return false;
      }

      return name;
    }
    ```

  - Initializing default values for unsupplied arguments should be done at the top of the function body.

    ```javascript
    // bad
    function(arg1, arg2) {
      
      arg1 = arg1 || arg1Default;
      doSomeStuff(arg1);
      
      arg2 = arg2 || arg2Default;
      doSomeOtherStuff(arg2);
    }
    
    // good
    function(arg1, arg2) {
      arg1 = arg1 || arg1Default;
      arg2 = arg2 || arg2Default;
      
      doSomeStuff(arg1);
      doSomeOtherStuff(arg2);
    }
    ```

**[⬆ back to top](#table-of-contents)**


## Hoisting

  - Variable declarations get hoisted to the top of their scope, but their assignment does not.

    ```javascript
    // we know this wouldn't work (assuming there
    // is no notDefined global variable)
    function example() {
      console.log(notDefined); // => throws a ReferenceError
    }

    // creating a variable declaration after you
    // reference the variable will work due to
    // variable hoisting. Note: the assignment
    // value of `true` is not hoisted.
    function example() {
      console.log(declaredButNotAssigned); // => undefined
      var declaredButNotAssigned = true;
    }

    // The interpreter is hoisting the variable
    // declaration to the top of the scope,
    // which means our example could be rewritten as:
    function example() {
      var declaredButNotAssigned;
      console.log(declaredButNotAssigned); // => undefined
      declaredButNotAssigned = true;
    }
    ```

  - Anonymous function expressions hoist their variable name, but not the function assignment.

    ```javascript
    function example() {
      console.log(anonymous); // => undefined

      anonymous(); // => TypeError anonymous is not a function

      var anonymous = function() {
        console.log('anonymous function expression');
      };
    }
    ```

  - Named function expressions hoist the variable name, not the function name or the function body.

    ```javascript
    function example() {
      console.log(named); // => undefined

      named(); // => TypeError named is not a function

      superPower(); // => ReferenceError superPower is not defined

      var named = function superPower() {
        console.log('Flying');
      };
    }

    // the same is true when the function name
    // is the same as the variable name.
    function example() {
      console.log(named); // => undefined

      named(); // => TypeError named is not a function

      var named = function named() {
        console.log('named');
      }
    }
    ```

  - Function declarations hoist their name and the function body.

    ```javascript
    function example() {
      superPower(); // => Flying

      function superPower() {
        console.log('Flying');
      }
    }
    ```

  - For more information refer to [JavaScript Scoping & Hoisting](http://www.adequatelygood.com/2010/2/JavaScript-Scoping-and-Hoisting) by [Ben Cherry](http://www.adequatelygood.com/).

**[⬆ back to top](#table-of-contents)**



## Comparison Operators & Equality

  - Use `===` and `!==` over `==` and `!=`.
  - Conditional statements such as the `if` statement evaluate their expression using coercion with the `ToBoolean` abstract method and always follow these simple rules:

    + **Objects** evaluate to **true**
    + **Undefined** evaluates to **false**
    + **Null** evaluates to **false**
    + **Booleans** evaluate to **the value of the boolean**
    + **Numbers** evaluate to **false** if **+0, -0, or NaN**, otherwise **true**
    + **Strings** evaluate to **false** if an empty string `''`, otherwise **true**

    ```javascript
    if ([0]) {
      // true
      // An array is an object, objects evaluate to true
    }
    ```

  - Use shortcuts.

    ```javascript
    // bad
    if (name !== '') {
      // ...stuff...
    }

    // good
    if (name) {
      // ...stuff...
    }

    // bad
    if (collection.length > 0) {
      // ...stuff...
    }

    // good
    if (collection.length) {
      // ...stuff...
    }
    ```
    
  - Use `==` and `!=` if both a null and undefined check is necessary.

    ```javascript
    // bad
    if (name !== null && name !== undefined) {
      // ...stuff...
    }

    // good
    if (name != null) {
      // ...stuff...
    }
    ```

  - For more information see [Truth Equality and JavaScript](http://javascriptweblog.wordpress.com/2011/02/07/truth-equality-and-javascript/#more-2108) by Angus Croll.

**[⬆ back to top](#table-of-contents)**


## Blocks

  - Use braces with all blocks.

    ```javascript
    // bad
    if (test)
      return false;

    // bad
    if (test) return false;

    // good
    if (test) {
      return false;
    }
    
    // good
    if (test) { return false; }
    ```

  - If you're using multi-line blocks with `if` and `else`, put `else` on the next line from the
    `if` block's closing brace.

    ```javascript

    // bad
    if (test) {
      thing1();
    } else if (test2) {
      thing2();
    } else {
      thing3();
    }
    
    // good
    if (test) {
      thing1();
    }
    else if (test2) {
      thing2();
    }
    else {
      thing3();
    }
    ```


**[⬆ back to top](#table-of-contents)**


## Comments

  - Put an empty line before the comment.

    ```javascript
    // bad
    function getType() {
      console.log('fetching type...');
      // set the default type to 'no type'
      var type = this._type || 'no type';

      return type;
    }

    // good
    function getType() {
      console.log('fetching type...');

      // set the default type to 'no type'
      var type = this._type || 'no type';

      return type;
    }
    ```

  - Prefixing your comments with `TODO` helps other developers quickly understand if you're pointing out a problem that needs to be revisited, or if you're suggesting a solution to the problem that needs to be implemented. These are different than regular comments because they are actionable. The action is `TODO -- need to implement or fix something`.

  - Use `// TODO:` to annotate problems.

    ```javascript
    function Calculator() {

      // TODO: shouldn't use a global here
      total = 0;

      return this;
    }
    ```

  - Or to annotate solutions to problems.

    ```javascript
    function Calculator() {

      // TODO: total should be configurable by an options param
      this.total = 0;

      return this;
    }
    ```

**[⬆ back to top](#table-of-contents)**


## Whitespace

  - Use soft tabs set to 4 spaces.

    ```javascript
    
    // bad
    function() {
    ∙var name;
    }

    // bad
    function() {
    ∙∙var name;
    }
    
    // good
    function() {
    ∙∙∙∙var name;
    }
    ```

  - Place 1 space before the leading brace.

    ```javascript
    // bad
    function test(){
      console.log('test');
    }

    // good
    function test() {
      console.log('test');
    }

    // bad
    dog.set('attr',{
      age: '1 year',
      breed: 'Bernese Mountain Dog'
    });

    // good
    dog.set('attr', {
      age: '1 year',
      breed: 'Bernese Mountain Dog'
    });
    ```

  - Place 1 space before the opening parenthesis in control statements (`if`, `while` etc.). Place no space before the argument list in function calls and declarations.

    ```javascript
    // bad
    if(isJedi) {
      fight ();
    }

    // good
    if (isJedi) {
      fight();
    }
    
    // bad
    function () {
      console.log('Swooosh!');
    }
    
    // good
    function() {
      console.log('Swooosh!');
    }
    
    // bad
    function fight () {
      console.log ('Swooosh!');
    }

    // good
    function fight() {
      console.log('Swooosh!');
    }
    ```

  - Set off operators with spaces.

    ```javascript
    // bad
    var x=y+5;

    // good
    var x = y + 5;
    ```

  - Use indentation when making long method chains. Use a leading dot, which
    emphasizes that the line is a method call, not a new statement.

    ```javascript
    // bad
    $('#items').find('.selected').highlight().end().find('.open').updateCount();

    // bad
    $('#items').
      find('.selected').
        highlight().
        end().
      find('.open').
        updateCount();

    // good
    $('#items')
      .find('.selected')
        .highlight()
        .end()
      .find('.open')
        .updateCount();
    ```
   
  - When used in a variable assignment, align chained methods to the righthand side of the assignment operator (=)
    
    > Why? The high level logic for a given function should be easily decipherable. 
    The variable name should describe the method chain's intended output. As such, these 
    implementation details are generally not useful for this high level understanding, 
    and should be "opt-in".
    
    ```javascript
    // bad
    var leds = stage.selectAll('.led')
      .data(data)
    .enter().append('svg:svg')
      .classed('led', true)
      .attr('width', (radius + margin) * 2)
    .append('svg:g')
      .attr('transform', 'translate(' + (radius + margin) + ',' + (radius + margin) + ')')
      .call(tron.led);

    // good
    var leds = stage.selectAll('.led')
                    .data(data)
                  .enter().append('svg:svg')
                    .classed('led', true)
                    .attr('width', (radius + margin) * 2)
                  .append('svg:g')
                    .attr('transform', 'translate(' + (radius + margin) + ',' + (radius + margin) + ')')
                    .call(tron.led);
    ```

**[⬆ back to top](#table-of-contents)**

## Commas

  - Leading commas: **Nope.**

    ```javascript
    // bad
    var story = [
        once
      , upon
      , aTime
    ];

    // good
    var story = [
      once,
      upon,
      aTime
    ];

    // bad
    var hero = {
        firstName: 'Bob'
      , lastName: 'Parr'
      , heroName: 'Mr. Incredible'
      , superPower: 'strength'
    };

    // good
    var hero = {
      firstName: 'Bob',
      lastName: 'Parr',
      heroName: 'Mr. Incredible',
      superPower: 'strength'
    };
    ```

**[⬆ back to top](#table-of-contents)**


## Semicolons

  - **Yup.**

    ```javascript
    // bad
    (function() {
      var name = 'Skywalker'
      return name
    })()

    // good
    (function() {
      var name = 'Skywalker';
      return name;
    })();
    ```

**[⬆ back to top](#table-of-contents)**


## Type Casting & Coercion

  - Perform type coercion at the beginning of the statement.
  - Strings:

    ```javascript
    //  => this.reviewScore = 9;

    // bad
    var totalScore = this.reviewScore + '';

    // good
    var totalScore = '' + this.reviewScore;

    // bad
    var totalScore = '' + this.reviewScore + ' total score';

    // good
    var totalScore = this.reviewScore + ' total score';
    ```

  - Use the `+` operator for numbers.

    ```javascript
    var inputValue = '4';

    // bad
    var val = new Number(inputValue);

    // bad
    var val = inputValue >> 0;
    
    // bad
    var val = parseInt(inputValue);

    // bad
    var val = parseInt(inputValue, 10);

    // bad
    var val = Number(inputValue);
    
    // good
    var val = +inputValue;

    ```

  - If for whatever reason you are doing something wild and `+` is your bottleneck and need to use Bitshift for [performance reasons](http://jsperf.com/coercion-vs-casting/3), leave a comment explaining why and what you're doing.

    ```javascript
    // good
    /**
     * + was the reason my code was slow.
     * Bitshifting the String to coerce it to a
     * Number made it a lot faster.
     */
    var val = inputValue >> 0;
    ```

  - **Note:** Be careful when using bitshift operations. Numbers are represented as [64-bit values](http://es5.github.io/#x4.3.19), but Bitshift operations always return a 32-bit integer ([source](http://es5.github.io/#x11.7)). Bitshift can lead to unexpected behavior for integer values larger than 32 bits. [Discussion](https://github.com/airbnb/javascript/issues/109). Largest signed 32-bit Int is 2,147,483,647:

    ```javascript
    2147483647 >> 0 //=> 2147483647
    2147483648 >> 0 //=> -2147483648
    2147483649 >> 0 //=> -2147483647
    ```

  - Booleans:

    ```javascript
    var age = 0;

    // bad
    var hasAge = new Boolean(age);

    // bad
    var hasAge = Boolean(age);

    // good
    var hasAge = !!age;
    ```

**[⬆ back to top](#table-of-contents)**


## Naming Conventions

  - Avoid single letter names. Your names should accurately describe the 
    underlying behavior without expecting the reader to make inferences
    from context (within reason).

    > Why? The more inferences the reader has to keep in (their) memory, 
    the more likely they are to forget something. This can lead to error prone code.
    Accurate naming also makes bugs easier to spot by providing clear intent.
    
    ```javascript
    // bad
    function f(number) {
      return number < 5;
    }

    // bad - Is there an error here? What is it?
    function isValid(number) {
      return number < 4;
    }
    
    // good - The error here is obvious
    function isLessThan5(number) {
      return number < 4;
    }
    
    // bad
    var filteredNumbers = numbers.filter(isValid);
    
    // good
    var validNumbers = numbers.filter(isLessThan5);
    
    ```

  - Sometimes behavior cannot be fully expressed through naming
  due to prohibitive length or optimization constraints. In these 
  cases a more general name is fine. Comments should be used if the 
  behavior is hard to discern from the implementation.

    ```javascript

    // bad
    function someComplexAlgorithm(values) {
      for (var i = 0; i < values.length; i++) {
        // complex operations, imperative looping, recursion etc...
      }
    }
    
    // good
    // Comment describing high level flow of the algorithm
    function someComplexAlgorithm(values) {
      for (var i = 0; i < values.length; i++) {
        // complex operations, nested imperative looping, recursion etc...
      }
    }
    
    // good - well named functions make high level flow easy to discern
    function someComplexAlgorithm(values) {
      values.filter(isMeantForStepOne)
            .forEach(doStepOne);
            
      values.map(toStepTwoObject)
            .forEach(doStepTwo);
            
      return values.reduce(toResult);
    }
    ```

  - Use camelCase when naming objects, functions, and instances.

    ```javascript
    // bad
    var OBJEcttsssss = {};
    var this_is_my_object = {};
    var o = {};
    function c() {}

    // good
    var thisIsMyObject = {};
    function thisIsMyFunction() {}
    ```

  - Use PascalCase when naming constructors or classes.

    ```javascript
    // bad
    function user(options) {
      this.name = options.name;
    }

    var bad = new user({
      name: 'nope'
    });

    // good
    function User(options) {
      this.name = options.name;
    }

    var good = new User({
      name: 'yup'
    });
    ```
    
  - Array names should always be plural.
  
  > Why? We should make it easy for the reader to infer the type of a variable
  from its name. Using the plural form reads more naturally than suffixing with 
  something like "List"

    ```javascript
    // bad
    var number = [1, 2, 3];
    
    // bad
    var numberList = [1, 2, 3];
    
    // good
    var numbers = [1, 2, 3];
    ```
    
  - Objects used like maps should follow the convention `keyToValue`. An exception can be made for cases
  where the name would otherwise be prohibitively long.

  > Why? The reader does not have to reference the object's initialization logic to understand how to use it.
  It also provides for an easy way to differentiate from arrays.
  
    ```javascript
    // bad
    var users = {
      bob: userOne,
      alice: userTwo,
      jim: userThree,
    };
    
    // bad
    var userMap = {
      bob: userOne,
      alice: userTwo,
      jim: userThree,
    };
     
     // good
     var nameToUser = {
       bob: userOne,
       alice: userTwo,
       jim: userThree,
     };
    ```

  - When saving a reference to `this` use `self`.

    ```javascript
    // bad
    function() {
      var that = this;
      return function() {
        console.log(that);
      };
    }

    // bad
    function() {
      var _this = this;
      return function() {
        console.log(_this);
      };
    }
    
    // good
    function() {
      var self = this;
      return function() {
        console.log(self);
      };
    }
    ```

  - Name your functions. This is helpful for stack traces. Prefer function declarations over 
  function expressions.
  
  > Why? If we can accurately express the function's behavior through its name, the reader does
  not have to reference its implementation. When an error exists within a given function, it 
  becomes easy to isolate the steps that could be causing issues.

    ```javascript
    // bad
    var log = function(msg) {
      console.log(msg);
    };

    // ok
    var log = function log(msg) {
      console.log(msg);
    };
    
    // good
    function log(msg) {
      console.log(msg);
    }
    ```
    
  - Function names should be verbs. An exception can be made for patterns where 
  getter/setter hybrids are prominent.
  
  > Why? This makes them easy to distinguish and removes ambiguity about their behavior. 
  The consumer should only have to reference the implementation as a last resort. 
  
  
  ```javascript
    // bad
    function lastName(person) { 
      return person.lastName;
    }

    // good
    function getLastName(person) {
      return person.lastName;
     }
    
    // bad
    function monday() {
      // ...
    }
    
    // good
    function isMonday() {
      // ...
    }
    ```

  - Use appropriately named function declarations for any non trivial logic.
  
    > Why? The reader can immediately grasp the high level flow, and intention thereof,
    of a given function.
    
    ```javascript
    // bad
    messages.filter(function(message) {
      if (message && message.length) { return; }
      
      var words = splitIntoWords(message);

      for (var i = 0; i < words.length; i++) {
        var word = words[i];
        if (word.toLowerCase() === 'cat') {
          return true;
        }
      }
    })
    .forEach(function(message) {
      var emailService = getEmailService(config.token);
      emailService.send(message, config.email.addresses.catLady, config.email.oAuthToken);
    });

    // good
    messages.filter(containsTheWordCat)
            .forEach(sendToCatLady);
    
    function containsTheWordCat(text) {
      if (text && text.length) { return; }
      var words = splitIntoWords(text);

      for (var i = 0; i < words.length; i++) {
        var word = words[i];
        if (word.toLowerCase() === 'cat') {
          return true;
        }
      }
    }

    function sendToCatLady(message) {
      var emailService = getEmailService(config.token);
      emailService.send(message, config.email.addresses.catLady, config.email.oAuthToken);
    }
    ```

**[⬆ back to top](#table-of-contents)**

## Constructors

  - Prefer object literals or object creation functions over "newable" constructors. 
  
    > Why? In the majority of cases an object's "lifetime" is rather short. 
    Unless used on a large scale, the overhead of "class" declaration often 
    detracts, rather than contributes to understanding of code.
    The use of `this` generally makes code more difficult to comprehend and 
    can lead to surprises when used improperly.

    ```javascript
    // bad
    function Jedi(someProperty) {
      this.someProperty = someProperty;
    }
    
    Jedi.prototype.fight = function fight() {
      console.log('fighting');
    };

    Jedi.prototype.block = function block() {
      console.log('blocking');
    };
    
    // good 
    var jedi = {
      someProperty: someProperty,
      fight: fight,
      block: block,
    };
    
    // good
    function createJedi(someProperty) {
      return {
        someProperty: someProperty,
        fight: fight,
        block: block,
      };
    }
    
    function fight() {
      console.log('fighting');
    }
    
    function block() {
      console.log('blocking');
    }
    ```

**[⬆ back to top](#table-of-contents)**

## Modules

  - The file should be named with spinal-case, and match the name of the single export.
  - Always declare `'use strict';` at the top of the module.

    ```javascript
    // fancy-input.js

    function(global) {
      'use strict';

      var previousFancyInput = global.FancyInput;

      function FancyInput(options) {
        this.options = options || {};
      }

      FancyInput.noConflict = function noConflict() {
        global.FancyInput = previousFancyInput;
        return FancyInput;
      };

      global.FancyInput = FancyInput;
    }(this);
    ```

**[⬆ back to top](#table-of-contents)**


## jQuery

  - Prefix jQuery object variables with a `$`.

    ```javascript
    // bad
    var sidebar = $('.sidebar');

    // good
    var $sidebar = $('.sidebar');
    ```

  - Cache jQuery lookups.

    ```javascript
    // bad
    function setSidebar() {
      $('.sidebar').hide();

      // ...stuff...

      $('.sidebar').css({
        'background-color': 'pink'
      });
    }

    // good
    function setSidebar() {
      var $sidebar = $('.sidebar');
      $sidebar.hide();

      // ...stuff...

      $sidebar.css({
        'background-color': 'pink'
      });
    }
    ```

  - For DOM queries use Cascading `$('.sidebar ul')` or parent > child `$('.sidebar > ul')`. [jsPerf](http://jsperf.com/jquery-find-vs-context-sel/16)
  - Use `find` with scoped jQuery object queries.

    ```javascript
    // bad
    $('ul', '.sidebar').hide();

    // bad
    $('.sidebar').find('ul').hide();

    // good
    $('.sidebar ul').hide();

    // good
    $('.sidebar > ul').hide();

    // good
    $sidebar.find('ul').hide();
    ```

**[⬆ back to top](#table-of-contents)**


## ECMAScript 5 Compatibility

  - Refer to [Kangax](https://twitter.com/kangax/)'s ES5 [compatibility table](http://kangax.github.com/es5-compat-table/).

**[⬆ back to top](#table-of-contents)**


## Testing

  - **Yup.** We use the [Jasmine](http://jasmine.github.io/2.0/introduction.html) framework for testing.
    
    ```javascript
    describe('someModule', function() {
      
      describe('someMethod', function() {
        
        it('should do something', function() {
          expect(true).toBe(true);
        });
        
      });
      
    });
    ```
    
  - Test folder hierarchy should mirror the source code hierarchy. Files should be suffixed with `.spec`.
  
    ```
    js/
      file-one.js
      file-two.js
    
    test/
      js/
        file-one.spec.js
        file-two.spec.js
    ```
    
  - Describe blocks should be organized two levels deep. Module being tested -> function being tested.
    
    ```javascript
    describe('someModule', function() {
      
      describe('someModuleMethod', function() {
        it('should do something');
        it('should do something else');
      });
      
      describe('someOtherModuleMethod', function() {
        it('should do something');
        it('should do something else');
      });
      
    });
    ```
    
  - Any widely used, particularly complex, or application critical functions must have supporting tests. 
  Tests not only allow us to make enhancements worry free, but act as documentation to someone unfamiliar 
  with the code.
  
  - If a bug is encountered in production for one of these cases, a new test should be written to cover that 
  issue. We should not make the same mistake twice.

**[⬆ back to top](#table-of-contents)**


## Performance

  - [On Layout & Web Performance](http://kellegous.com/j/2013/01/26/layout-performance/)
  - [String vs Array Concat](http://jsperf.com/string-vs-array-concat/2)
  - [Try/Catch Cost In a Loop](http://jsperf.com/try-catch-in-loop-cost)
  - [Bang Function](http://jsperf.com/bang-function)
  - [jQuery Find vs Context, Selector](http://jsperf.com/jquery-find-vs-context-sel/13)
  - [innerHTML vs textContent for script text](http://jsperf.com/innerhtml-vs-textcontent-for-script-text)
  - [Long String Concatenation](http://jsperf.com/ya-string-concat)
  - Loading...

**[⬆ back to top](#table-of-contents)**


## Resources


**Read This**

  - [Annotated ECMAScript 5.1](http://es5.github.com/)

**Tools**

  <!--- 
  TODO - Code Style Linters
    + [JSHint](http://www.jshint.com/) - [Airbnb Style .jshintrc](https://github.com/airbnb/javascript/blob/master/linters/jshintrc)
    + [JSCS](https://github.com/jscs-dev/node-jscs) - [Airbnb Style Preset](https://github.com/jscs-dev/node-jscs/blob/master/presets/airbnb.json)
  -->

**Other Style Guides**

  - [Google JavaScript Style Guide](http://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml)
  - [jQuery Core Style Guidelines](http://docs.jquery.com/JQuery_Core_Style_Guidelines)
  - [Principles of Writing Consistent, Idiomatic JavaScript](https://github.com/rwldrn/idiomatic.js/)
  - [JavaScript Standard Style](https://github.com/feross/standard)

**Other Styles**

  - [Naming this in nested functions](https://gist.github.com/4135065) - Christian Johansen
  - [Conditional Callbacks](https://github.com/airbnb/javascript/issues/52) - Ross Allen
  - [Popular JavaScript Coding Conventions on Github](http://sideeffect.kr/popularconvention/#javascript) - JeongHoon Byun
  - [Multiple var statements in JavaScript, not superfluous](http://benalman.com/news/2012/05/multiple-var-statements-javascript/) - Ben Alman

**Further Reading**

  - [Understanding JavaScript Closures](http://javascriptweblog.wordpress.com/2010/10/25/understanding-javascript-closures/) - Angus Croll
  - [Basic JavaScript for the impatient programmer](http://www.2ality.com/2013/06/basic-javascript.html) - Dr. Axel Rauschmayer
  - [You Might Not Need jQuery](http://youmightnotneedjquery.com/) - Zack Bloom & Adam Schwartz
  - [ES6 Features](https://github.com/lukehoban/es6features) - Luke Hoban
  - [Frontend Guidelines](https://github.com/bendc/frontend-guidelines) - Benjamin De Cock

**Books**

  - [JavaScript: The Good Parts](http://www.amazon.com/JavaScript-Good-Parts-Douglas-Crockford/dp/0596517742) - Douglas Crockford
  - [JavaScript Patterns](http://www.amazon.com/JavaScript-Patterns-Stoyan-Stefanov/dp/0596806752) - Stoyan Stefanov
  - [Pro JavaScript Design Patterns](http://www.amazon.com/JavaScript-Design-Patterns-Recipes-Problem-Solution/dp/159059908X)  - Ross Harmes and Dustin Diaz
  - [High Performance Web Sites: Essential Knowledge for Front-End Engineers](http://www.amazon.com/High-Performance-Web-Sites-Essential/dp/0596529309) - Steve Souders
  - [Maintainable JavaScript](http://www.amazon.com/Maintainable-JavaScript-Nicholas-C-Zakas/dp/1449327680) - Nicholas C. Zakas
  - [JavaScript Web Applications](http://www.amazon.com/JavaScript-Web-Applications-Alex-MacCaw/dp/144930351X) - Alex MacCaw
  - [Pro JavaScript Techniques](http://www.amazon.com/Pro-JavaScript-Techniques-John-Resig/dp/1590597273) - John Resig
  - [Smashing Node.js: JavaScript Everywhere](http://www.amazon.com/Smashing-Node-js-JavaScript-Everywhere-Magazine/dp/1119962595) - Guillermo Rauch
  - [Secrets of the JavaScript Ninja](http://www.amazon.com/Secrets-JavaScript-Ninja-John-Resig/dp/193398869X) - John Resig and Bear Bibeault
  - [Human JavaScript](http://humanjavascript.com/) - Henrik Joreteg
  - [Superhero.js](http://superherojs.com/) - Kim Joar Bekkelund, Mads Mobæk, & Olav Bjorkoy
  - [JSBooks](http://jsbooks.revolunet.com/) - Julien Bouquillon
  - [Third Party JavaScript](http://manning.com/vinegar/) - Ben Vinegar and Anton Kovalyov
  - [Effective JavaScript: 68 Specific Ways to Harness the Power of JavaScript](http://amzn.com/0321812182) - David Herman
  - [Eloquent JavaScript](http://eloquentjavascript.net) - Marijn Haverbeke
  - [You Don't Know JS](https://github.com/getify/You-Dont-Know-JS) - Kyle Simpson

**Blogs**

  - [DailyJS](http://dailyjs.com/)
  - [JavaScript Weekly](http://javascriptweekly.com/)
  - [JavaScript, JavaScript...](http://javascriptweblog.wordpress.com/)
  - [Bocoup Weblog](http://weblog.bocoup.com/)
  - [Adequately Good](http://www.adequatelygood.com/)
  - [NCZOnline](http://www.nczonline.net/)
  - [Perfection Kills](http://perfectionkills.com/)
  - [Ben Alman](http://benalman.com/)
  - [Dmitry Baranovskiy](http://dmitry.baranovskiy.com/)
  - [Dustin Diaz](http://dustindiaz.com/)
  - [nettuts](http://net.tutsplus.com/?s=javascript)

**Podcasts**

  - [JavaScript Jabber](http://devchat.tv/js-jabber/)


**[⬆ back to top](#table-of-contents)**

# };
