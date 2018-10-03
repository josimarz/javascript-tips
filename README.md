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
