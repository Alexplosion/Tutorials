# Functional JavaScript

## Overview
Introduction to functional programming in JavaScript. This guide covers the following topics:  

1. What is functional programming?  
2. Higher order functions
3. Applicative programming (map, filter, reduce)  
4. Recursion  

## 1. What is functional programming and why should I care?
Functional programming is often introduced as a programming style which makes use of some of the following features:
- **Higher-order functions:** functions which take other functions as arguments and/ or return other functions
- **Composability:** functions built from other functions to encourage reusability (includes currying)
- **Pure functions:** functions which always return the same value when given the same argument
- **Immutability:** functions which return new values without mutating/ changing the state of the argument passed
- **Recursion:** functions that call themselves until they don't

**BUT!** I find this jargon overwhelming and a difficult way to understand functional programming for a JavaScript newbie like myself...

I find examples helpful and I will attempt to compare a simple function whose purpose is to capitalize a name it is given using three approaches:  
- **A. Imperative** - logically solving the task in one capitalize function  
- **B. Object-oriented** - encapsulating the capitalize method in a Person object
- **C. Functional** - breaking down the solution into multiple smaller and reusable functions

### A. Imperative
Imperative approach often follows how we would logically solve the problem. In our example of capitalizing a name we might approach a solution as follows:
1. Split the name string into an array of words
2. Loop through the array and transform each word by uppercasing the first letter and adding the remaining letters
3. Join the array of words into a new string and return the string

Let's look at this in code
```javascript
var capitalize = function(str){
  var arr = str.split(' ');
  for (var i = 0; i < arr.length; i++) {
    arr[i] = arr[i][0].toUpperCase() + arr[i].substr(1);
  }
  return arr.join(' ');
};

capitalize('clark jerome kent');
// => 'Clark Jerome Kent'
```

Now while there is nothing wrong with this approach we can point out a few issues which may occur as our codebase scales. Firstly, this function serves a very specific purpose and is not composable, meaning we may find ourselves writing a new function to uppercase the second letter in the string (why? I dunno). Secondly, there could be a risk of naming conflicts if later on we assign a different value to capitalize.

### B. Object-Oriented
An object oriented approach may look to encapsulate the capitalize name method in a Person object to prevent naming conflicts. While this example may be a stretch/overkill it illustrates how we can take an object-oriented approach and encapsulate the function.

We might create a Person object which includes a name property and a method for capitalizing its own name. You may notice that the capitalize method in the Person object is similar to the imperative example above; it is important to note that you shouldn't feel obliged to follow any one style dogmatically. Use what is best for readability and debugging.

More code
```javascript
var Person = {
  addName: function(name){
    this.name = name;
  },
  capitalize: function(){
    var arr = this.name.split(' ');
    for (var i = 0; i < arr.length; i++){
      arr[i] = arr[i][0].toUpperCase() + arr[i].substr(1);
    }
    this.name = arr.join(' ');
  }
};

var superman = Object.create(Person);
superman.addName('clark jerome kent');
superman.capitalize();
superman.name;
// => 'Clark Jerome Kent'
```

### C. Functional
Finally we can compare an imperative and object-oriented approach to a functional approach. Here we will create two functions, the first capitalizeWord whose job as it suggests will be to capitalize one word and another function mapString who will take two arguments being a function and string to iterate through. The mapString function will then split the string, iterate through and call on the function it was given.

Let's see this in code
```javascript
var capitalizeWord = function(word){
  return word[0].toUpperCase() + word.substr(1);
};

var mapString = function(string, fun){
  return string.split(' ').map(fun).join(' ');  // Refer sections below for an explanation of the map, filter and reduce functions
};

mapString('clark jerome kent', capitalizeWord);
// => 'Clark Jerome Kent'
```

We can also refactor this code into some ES6 syntactic sugar. While it may only be syntax different, it in fact promotes the use of simple one line functions and encourages chaining.
```javascript
var capitalizeWord = (word) => word[0].toUpperCase() + word.substr(1);
var mapString = (string, fun) => string.split(' ').map(fun).join(' ');

mapString('bruce wayne', capitalizeWord);
// => 'Bruce Wayne'
```

Our mapString function is now composable and can accept any functions. Here we create a new function called endY which as its name suggests adds a Y to the end of the word
```javascript
// mapString function is now composable accepting any function
var endY = (word) => word + 'y';

mapString('bruce wayne', endY);
// => 'brucey wayney'
```

Key observations in above functional example:
- We broke aspects of the solution into smaller solutions. 1) One function was to transform one element; and (2) Another function was to split a string into an array and iterate through passing a function
- Our functions were composable. The mapString function accepted other function arguments, in this example adding a y to each letter to make brucey wayney
- Our functions promoted immutability, meaning we did not transform/ destroy any of the arguments but returned a new value
- We used chaining which works well with immutable functions that return new values. In the mapString example we chained a split map join methods
- Finally the biggest thing to remember is that with JavaScript we can accept functions as arguments (e.g. capitalize and endY for our mapString function) and we should be encouraged to do so

## 2. Higher-order functions
Functional programming also makes use of higher-order functions which can take a function as a first parameter and then return another function which await a parameter. In the capitalize name example above we used a higher-order function mapString which accepted another function as an argument. In this example we will write a function which returns another function. Functions which return other functions can be thought of as template functions which allow us to create new custom functions.

Code example should be clearer:

We will write a findAndReplace function which takes two arguments (regex, strReplace) and returns a new function which awaits a text string which will match the previously passed in regex and replace with the strReplace.

```javascript
// Create a higher-order function findAndReplace which is used as a template return new custom functions
var findAndReplace = function(regex, strReplace){
  return function(text){
    return text.replace(regex, strReplace);
  };
};

// Create a custom function passing in a custom regex and word to replace it with
var correctJS = findAndReplace(/javascript/g, 'JavaScript');
// correctJS will now be a new function awaiting a text argument which it will then replace with the regex /javascript/g with correct spelling 'JavaScript'
correctJS('I love javascript and spelling javascript');
// => 'I love JavaScript and spelling JavaScript'

// We can compose a new function with the findAndReplace higher-order function template
var correctRails = findAndReplace(/rails/g, 'Ruby on Rails');
correctRails('I am learning rails');
// => 'I am learning Ruby on Rails'
```

Remember that higher-order functions can be thought of as templates/ blueprints to construct other functions. We firstly pass in arguments to create a custom function variable; Then we can call the function by passing in a new argument which will execute against the previously passed in arguments which created the function.

## 3. Applicative programming
*In progress*

## 4. Recursion
Recursive functions are ones that call themselves from inside the function until a condition is reached and they stop. Recursive functions can be used as an alternative to for loops, and in particular if the bounds are unknown.

Let's start with a simple example of writing a countdown timer in an imperative vs. recursive approach:

```javascript
// Imperative approach
var countDown = function(num) {
  for (var i = num; i > 0; i--){
    console.log(i);
  }  
};
countDown(10);
// => 10, 9 ... 1

// Recursive approach
var countDownRecursive = function(num){
  if (num === 0) { return; }
  console.log(num);
  countDownRecursive(num - 1);
};
countDownRecursive(10);
// => 10, 9 ... 1
```

A recursive function should include three patterns: 1) a condition for when to stop; 2) take one step; 3) smaller problem for function to solve. Let's compare another example to find the length of an array (I know you can do this normally):

```javascript
// Define an array
var arr = [1,2,3,4,5,6];

// Imperative approach
var countLen = function(arr){
  var count = 0;
  var length = arr.length;
  var i;
  for (i = 0; i < length; i++){
    count++;
  }
  return count;
};
countLen(arr);
// => 6

// Recursive approach
var countLenRecursive = function(arr){
  // 1) condition to stop when length === 0 (I know we are using length here but hey its just an example)
  if (arr.length === 0) { return 0;}
  // 2) take one step by adding 1 to previous function return and slicing the array
  // 3) smaller problem to solve by passing in a smaller array
  return 1 + countLenRecursive(arr.slice(1));
};
countLenRecursive(arr);
// => 6
```

Let's do one more example: find the factorial (e.g. factorial of 6 is 6 x 5 x 4 x 3 x 2 x 1). I know maths sucks but lets do it anyway:

```javascript
// Imperative approach
var factorial = function(num){
  var sumProduct = 1;
  for (var i = 2; i <= num; i++){
    sumProduct = sumProduct * i;
  };
  return sumProduct;
};
factorial(6);
// => 720

// Recursive approach
var factorialRecursive = function(num){
  // 1) Stop when: Terminate by returning 1 if number is less than 0;
  if (num === 0) {return 1;}
  // 2) Take one-step (num *)
  // 3) Smaller problem (num -1)
  return num * factorialRecursive(num -1);
};
factorial(6);
// => 720

```
