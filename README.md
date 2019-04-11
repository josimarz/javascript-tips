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

### Be careful using Object.assign

Probabily we had used the `Object.assign` function to create a clone of an object. But, what you propabily didn't know is that this function doesn't make a deep clone of an object. For a better explanation, look at this code snippet:

```js
let brazil = {
  name: 'Brazil',
  confederation: 'CBF',
  titles: {
    worldcup: 5,
    americacup: 8
  }
}
let argentina = Object.assign({}, brazil)
argentina.name = 'Argentina'
argentina.confederation: 'AFA'
argentina.titles.worldcup = 2
argentina.titles.americacup = 14
console.log(argentina.name) // will print 'Argentina'
console.log(brazil.name) // will print 'Brazi'
console.log(argentina.titles.worldcup) // will print '2'
console.log(brazil.titles.worldcup) // will print '2'! OMG! We lost three titles
console.log(argentina.titles.americacup) // will print '14'
console.log(brazil.titles.americacup) // will print '14'!
```

So, if you want to make a deep copy of an object, you should use another way. You can combine two JSON functions: `parse` and `stringify`. First convert the object to be cloned to string. After that, parse the string to JSON again. Let's recover our World Cup titles:

```js
let brazil = {
  name: 'Brazil',
  confederation: 'CBF',
  titles: {
    worldcup: 5,
    americacup: 8
  }
}
let argentina = JSON.parse(JSON.stringify(brazil))
argentina.name = 'Argentina'
argentina.confederation: 'AFA'
argentina.titles.worldcup = 2
argentina.titles.americacup = 14
```

Also, you can create your own clone function using the concept of recursion:

```js
const cloneObject = (obj) => {
  let clone = {};
  for(let i in obj) {
    if(obj[i] !== null && typeof obj[i] === 'object') {
      clone[i] = cloneObject(obj[i])
      continue
    }
    clone[i] = obj[i]
  }
  return clone
}

```

Other alternative is using the function `cloneDeep`, available by [lodash](https://lodash.com/docs/4.17.11#cloneDeep).
## Vue.js

### Using $nextTick built-in function

In the same day that I'm writing this tip, I faced a problem with Vue.js. For a better explanation, look at the Vue.js template below:

```vue
<div>
  <button v-on:click="onClick()">Click here</button>
  <div contenteditable="true" ref="myInput" v-if="showInput"></div>
</div>
```
My function `onClick` is something like this:
```js
onClick: function () {
  this.showInput = true // I want to show the input
  // the next line raises a exception
  this.$refs.myInput.focus() // I want to focus the input
}
```
The code above will raise the folow exception: "Cannot read property 'focus' of undefined". This happens because the element named `myInput` wasn't found, even though the property `showInput` was turned true. However the DOM wasn't updated yet.

To solve this, we can use the built-in function `$nextTick`. According to official Vue [documentation](https://vuejs.org/v2/api/#Vue-nextTick), this function receives a callback function that is executed after next DOM update cycle.

Therefore, we can change the above function like this:

```js
onClick: function () {
  this.showInput = true // I want to show the input
  // wait for next update cycle before focus the element
  this.$nextTick(() => {
    this.$refs.myInput.focus()
  })
}
```
