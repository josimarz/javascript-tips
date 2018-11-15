# JavaScript Tips and Snippets
A space to write tips and snippets for JavaScript

## Implementing a sleep function
While using AngularJS, Vue.js or Ajax requests, I like to show a UI element for the user - like a spinner - to notice him that a HTTP request is happening. But, when testing in the development environment the request are so fast that isn't possible see the spinner. So, to simulate a HTTP request in the production environment I put a sleep function before request.

Unfortunately, JavaScript doesn't provide a native sleep function. After some researches I found a way to implement a sleep function. The result is a mix of the function `setTimeout` and promises. `setTimeout` is a asynchronous function, it means that the next command will not wait for `setTimeout` ends to execute itself.

To solve this, we can use a promise. The best way to explain is coding. See below:

```js
function sleep(delay) {
  return new Promise(resolve => setTimeout(resolve, delay));
}

async function testSleep() {
  await sleep(5000); // sleep for 5 seconds
  console.log("We're a thousands miles from confort...");
}

testSleep();
```
If you execute the example, the string `"We're thousands miles from confort..."` will be executed after 5 seconds. Notice that we used the `await` operator. It means that the script execution will stopped until `sleep` function ends. To use the `await` operator, the function where the operator is used should be declared with `async` operator.

If you like arrow function and write less you'll like the next example:

```js
let sleep = (delay) => new Promise(resolve => setTimeout(resolve, delay));

async function testSleep() {
  await sleep(5000); // sleep for 5 seconds
  console.log("We're a thousands miles from confort...");
}

testSleep();
```

Or you can use the next snippet inside your code, just changing the time:

```js
await ((delay) => new Promise(resolve => setTimeout(resolve, delay)))(5000);
```

## How the or operator (`||`) works

Probably you already seen something like this:

```js
let userName = 'Jo';

let me = {
  name: userName || null
}
```

Or like this:

```js
let regex = /(#[\w_\-\.]+)|(@[\w_\-\.]+)/g;
let m;

while ((m = regex.exec(input || null)) !== null) {
  // ...
}
```

Also, probably you already learnt that `||` is a logical operator to execute the operation `or`. But, what you would probably didn't know is that the result of a operation that uses the operator `||` will not be necessarily a boolean value like `true` or `false`.

For a better understanding, execute the code below:

```js
let userName = 'Jo';

let me = {
  name: userName || null
}

console.log(me);
```
You will see the content of the object `me`:
```js
{
  name: 'Jo'
}
```
Because we used the operator `||`, JavaScript has evaluated the expression from left to right and when it founds something true, it stopped the evaluation and returned the first true found value. Confusing? Let's write other example:

```js
let userName = false;
let name = 'Josimar';

let me = {
  name: userName || name || null
}

console.log(me);
```
For that code the result is:
```js
{
  name: 'Josimar'
}
```
But, if we make a bit change...
```js
let userName = false;
let name = ''; // a empty string

let me = {
  name: userName || name || null
}

console.log(me);
```
The resul will be:
```js
{
  name: null
}
```
That's because when a empty string is evaluated in a boolean expression it will result `false`. So, the last value at the expression will be assigned to the attribute: `null`.

## Searching and aggregate on MongoDB collection using local date

Recently, I had a problem to aggregate by date a set of documents in a MongoDB. The search returned 948 documents that were created at November 5 2018. The documents were created by a user from Brazil. So, the documents were saved with UTC time. Four documents
