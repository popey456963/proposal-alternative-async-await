# Async/Await Proposal Syntax

### Examples

```javascript
// Our async function, note the special "async return".
async function asyncFunction() {
    someAsync('data', (err, data) => {
        async return [err, data]
    })
}

// We can make this function appear synchronous by using the "await" keyword.
function syncFunction() {
    let [err, data] = await asyncFunction()
    console.log(data)
}

// Or we can use the "async" keyword so that it returns a promise.
function promiseFunction() {
  let promise = async asyncFunction()
  promise.then((data => {
    console.log(data)
  }
}

// If we don't use either, we should error out.
function forgetfulFunction() {
    // Errors out, easily showing people if they forgot to use async/await.
    let [err, data] = asyncFunction()
}
```

### Motivation and Overview

The main reason for this proposal was because I was finding myself writing so many functions similar to:

```javascript
async function asyncFunction() {
    return new Promise((resolve, reject) => {
        someAsync('data', (err, data) => {
            if (err) {
                reject(err); return;
            }
        resolve(data);
        });
    }); 
}
```

Wouldn't the following be much easier to understand:

```javascript
async function asyncFunction() {
    someAsync('data', (err, data) => {
        async return [err, data]
    })
}
```

Hence, this proposal implements new behaviour for the "async" and "await" keywords as follows:

 - If a function has an async keyword attached to it, it is an asynchronous function.
 - An async function should contain one or more "async return" statements which can be used in order to either end a promise or return a value.
 - Any async function must be preceeded when it is being called by either "await" or "async".
 - Await will prevent this branch from continueing until a value is returned from the asynchronous function.
 - Async will return a promise instantly.

