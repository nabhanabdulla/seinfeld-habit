# JavaScript Promises - Best resource on net

## Questions answered

1. What are Promises?
1. Why should I use Promise?
1. What problem(s) does Promise solve?
1. How to use a Promise?
1. When to use a Promise?
1. Commmon pitfalls when using Promises
1. Promise alternatives / improvements
1. Chaining promises

## What are Promises?

- Promises in JS are objects which contain a soon to be completed operation

- A completed (or fulfilled) Promise can either return a value or throw an error

- Promises can be in one of the below states
  1. Pending
  2. Fulfilled -> Resolved
  3. Fulfilled -> Rejected


## What problem(s) does Promises solve?

### 1. Escape from Callback hell
There will be scenarios where we'll need to wait for an asynchronous operation to be executed only after another asynchronous operation has completed. For example, let's say a user needs to book an Uber. There'll be a set of steps that happen in the background to complete the booking process (respective *asynchronous* functions that does these are given in brackets)

1. Deduct ride amount from user's e-wallet (`deductUserBalance(userId, rideId)`)
1. Find a nearby Uber drive whose available for the ride (`findNearbyDriver(usersCurrentLocation)`) 
1. Send ride info to the nearby Uber driver (`sendRideInfoToDriver(rideId)`)
1. Give the user update on the Uber driver location (`confirmBooking(rideId, driverId)`)

Now, the 
<!-- Add callback hell code -->

### 2. Cleaner code

## How to use a Promise?

- We create a Promise using the `Promise` constructor

```
let promise = new Promise()
```

- The constructor takes in as argument, a callback function specifying the code to be executed asynchronously

```
let promiseCallback = function(resolve, reject) {
    // code to be executed asynchronously
}

let promise = new Promise(promiseCallback);
```

- To specify the promise code executed completely (i.e, resolved) we use the `resolve` parameter of the callback function

```
let promiseCallback = function(resolve, reject) {
    // code to be executed asynchronously

    // wave success flag
    resolve();
}

let promise = new Promise(promiseCallback);
```

- Similarly, calling `reject` specifies the promise was rejected

```
let promiseCallback = function(resolve, reject) {
    // code to be executed asynchronously

    // trigger failure
    reject();
}

let promise = new Promise(promiseCallback);
```

- To find out if a Promise was resolve or rejected, we utilise the `.then()`, `.catch()` methods of the Promise object.

    - Code in `.then()` block gets executed for a resolved Promise
    - Code in `.catch()` block gets executed for a rejected Promise

```
let promiseCallback = function(resolve, reject) {
    // code to be executed asynchronously

    // wave success flag
    resolve();
}

let promise = new Promise(promiseCallback);

promise
 .then(function(resolvedOutput) {
    console.log("Promise was resolved");
 })
 .catch(function(rejectedOutput) {
    console.log("Promise was rejected");
 })
```

- If you need to send back some value from the promise callback function, you can do so by passing it as a parameter to the `resolve`/`reject` functions
```
let promiseCallback = function(resolve, reject) {
    // code to be executed asynchronously

    // wave success flag
    resolve("Message from Promise");
}

let promise = new Promise(promiseCallback);

promise
 .then(function(resolvedOutput) {
    console.log("Promise resolution value: ", resolvedOutput);
 })
 .catch(function(rejectedOutput) {
    console.log("Promise rejection value: ", rejectedOutput);
 })
```


<hr>
Author notes

- Can be a series of resources to learn Promises in different ways based on learners's preference

    1. Plain text
    1. Plain text with learn by doing
    1. ...

- When learning to create videos/decks, the updates can still be added to repo for record