# JavaScript Promises - Best resource on net

## Questions answered

- [x] What are Promises?
- [ ] (?) Why should I use Promise?
- [x] What problem(s) does Promise solve?
- [x] How to use a Promise?
- [ ] Promise alternatives / improvements
- [ ] Chaining promises
- [x] When to use a Promise?

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

1. Deduct ride amount from user's e-wallet (`deductUserBalance(userId, rideId, callbackFn)`)
1. Get the user's current location (`getUserLocation(userId, callbackFn)`)
1. Find a nearby Uber drive whose available for the ride (`findNearbyDriver(userLocation, callbackFn)`) 
1. Send ride info to the nearby Uber driver (`sendRideInfoToDriver(rideId, callbackFn)`)
1. Give the user update on the Uber driver location (`confirmBooking(rideId, callbackFn)`)

As all of these functions are asynchronous, we don't have a mechanism to return a value from these but only to make it accessible to the callback function when the value's ready.

Now, the implementation using callback functions will be somewhat like below
<!-- Add callback hell code -->
```javascript
deductUserBalance(userId, rideId, function(error, userId) {
    getUserLocation(userId, function(error, userLocation) {
        findNearbyDriver(userLocation, function(error, rideId) {
            sendRideInfoToDriver(rideId, function(error, rideId) {
                confirmBooking(rideId, function(err, rideId) {
                    console.log("Booking complete");
                })
            })
        })
    })
})
```
Even without error handling and no additional logic other than calling the functions, the implementation just got messy and hard to understand easily. This, ladies and gentleman, is what *callback hell* is!

## How to use a Promise?

- We create a Promise using the `Promise` constructor

```javascript
let promise = new Promise()
```

- The constructor takes in as argument, a callback function specifying the code to be executed asynchronously

```javascript
let promiseCallback = function(resolve, reject) {
    // code to be executed asynchronously
}

let promise = new Promise(promiseCallback);
```

- To specify the promise code executed completely (i.e, resolved) we use the `resolve` parameter of the callback function

```javascript
let promiseCallback = function(resolve, reject) {
    // code to be executed asynchronously

    // wave success flag
    resolve();
}

let promise = new Promise(promiseCallback);
```

- Similarly, calling `reject` specifies the promise was rejected

```javascript
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

```javascript
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
```javascript
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

## Commmon pitfalls when using Promises
1. Promise output is neither stored to the variable the Promise is assigned to, nor the `.then()` or `.catch()` blocks are assigned. It's because the value is available only later once the promise is fulfilled.
```javascript
let promise = new Promise (function (resolve, reject) {
    resolve("Promise output");
})

console.log(promise === "Promise output"); // prints false

let promiseThen = promise
 .then(function(resolveMsg) {
     console.log(resolveMsg === "Promise output"); // prints true
 })

console.log(promiseThen === "Promise output"); // prints false
``` 

2. If a value is returned from within `.then()` block, we'll need to use another `.then()` block to process it.
```

```
<!-- Note: Mention .then()/.catch() returns a Promise -->

3. The `.then()`, `.catch()` statements aren't synchronous as well
```javascript
console.log("Before promise creation");
let promise = new Promise (function (resolve, reject) {
    resolve("Promise output");
})
console.log("After promise creation");

console.log("Before then block");
promise
 .then(function(resolveMsg) {
     console.log(resolveMsg);
 })
console.log("After then block");
```
Above code snippet will print
```
Before promise creation
After promise creation
Before then block
After then block
Promise output
``` 


## Chaining Promises
Let's go back to the earlier Uber booking example. We'll start with the first two component functions
```javascript
deductUserBalance(userId, rideId, function(error, userId) {
    getUserLocation(userId, function(error, userLocation) {
        // more logic
    })
})
```
How do we translate this logic using callbacks to Promises?

To recall, 
* The `deductUserBalance()` function does some processing using the `userId` and `rideId` parameters

* A callback function is passed to it which in turn calls the `getUserLocation()` function

* The callback function will be called back inside the `deductUserBalance()` function

We'll use synchronous versions of these methods and then plug in asynchronousity using Promises.

```javascript
let deductUserBalancePromise = new Promise(resolve, reject) {
    let userId = deductUserBalanceSync(userId, rideId);
    resolve(userId);
}

let getUserLocationPromise = new Promise(resolve, reject) {
    let userLocation = getUserLocationSync(userId);
    resolve(userLocation);
}
```
Note: We assumed the callback function is an optional argument to the functions.

Now, how do we fulfill the `getUserLocationPromise` only after `deductUserBalancePromise` is resolved? Will the below code work?
```
let userId = deductUserBalancePromise
 .then(function (userId) {
     return userId;
 })
 .catch(function (err) {
     console.log("Err: ", err);
 });

 let userLocation = getUserLocationPromise
 .then(function (userLocation) {
     return userLocation;
 })
 .catch(function (err) {
     console.log("Err: ", err);
 });
```
This wouldn't work for two reasons
1. As we're having different `.then()`/`.catch()` blocks for both the Promises, these will be fulfilled independent of each other. We won't be able to ensure `getUserLocationPromise` is resolved only after `deductUserBalancePromise` always
2. Trying to store the output of the `.then()`/`.catch()` block wouldn't  work. You saw "why" in the "Commmon pitfalls when using Promises" section earlier

The correct way to do this will be to
1. Fulfil the `deductUserBalancePromise` promise
2. If resolved, return the dependent promise, `getUserLocationPromise`
3. Use another `.then()`/`.catch()` block to fulfil this promise now
```
deductUserBalancePromise
 .then(function (msg) {
     return getUserLocationPromise;
 })
 .then(function (msg) {
     console.log("userLocation: ", msg);
 });
```

If you have different promises and you need these to be fulfilled in a specific order, you can utilise chaining for this.

For example, 


### (Optional) Converting functions with callbacks to Promises
## When to use a Promise?
Promises are used in scenarios where we have some operation or set of operations which takes time to complete but can be run independent of the rest of the code. 

<hr>
Author notes

- Can be a series of resources to learn Promises in different ways based on learners's preference

    1. Plain text
    1. Plain text with learn by doing
    1. Full video
        1. Talking + 
        1. Animations
    1. Full video + activities
    1. ...

- When learning to create videos/decks, the updates can still be added to repo for record