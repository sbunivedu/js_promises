# JavaScript Promises

## Create and Consume a Promise
```javascript
// ES5: create promise

var isMomHappy = false;

// Promise
var willIGetNewPhone = new Promise(
  function (resolve, reject) {
    if (isMomHappy) {
      var phone = {
        brand: 'Samsung',
        color: 'black'
      };
      resolve(phone); // fulfilled
    } else {
      var reason = new Error('mom is not happy');
      reject(reason); // reject
    }
  }
);

// ES5: consume promise
var askMom = function () {
  willIGetNewPhone
    .then(function (fulfilled) {
      // yay, you got a new phone
      console.log(fulfilled);
      // output: { brand: 'Samsung', color: 'black' }
    })
    .catch(function (error) {
      // oops, mom don't buy it
      console.log(error.message);
      // output: 'mom is not happy'
    });
};

askMom();
```
live code https://jsbin.com/nifocu/1/edit?js,console

## Chaining Promises
```javascript
const isMomHappy = true;

// Promise
const willIGetNewPhone = new Promise(
  (resolve, reject) => { // fat arrow
    if (isMomHappy) {
      const phone = {
        brand: 'Samsung',
        color: 'black'
      };
      resolve(phone);
    } else {
      const reason = new Error('mom is not happy');
      reject(reason);
    }

  }
);

// 2nd promise
var showOff1 = function (phone) {
  return new Promise(
    function (resolve, reject) {
      var message = 'Hey friend, I have a new ' +
        phone.color + ' ' + phone.brand + ' phone';
      resolve(message);
    }
  );
};

const showOff = function (phone) {
  const message = 'Hey friend, I have a new ' +
    phone.color + ' ' + phone.brand + ' phone';
  return Promise.resolve(message);
};

// call our promise
const askMom = function () {
  willIGetNewPhone
    .then(showOff)
    .then(fulfilled => console.log(fulfilled)) // fat arrow
    .catch(error => console.log(error.message)); // fat arrow
};

askMom();
```
## Callback
```javascript
// add two numbers remotely
// get the result by calling an API

function addAsync (num1, num2, callback) {
  // use the famous jQuery getJSON callback API
  return $.getJSON('http://www.example.com', {
    num1: num1,
    num2: num2
  }, callback);
}

addAsync(1, 2, success => {
  // callback
  const result = success; // you get result = 3 here
});
```
## Subsequent Callbacks
Synchronous version:
```javascript
// add two numbers normally

let resultA, resultB, resultC;

function add (num1, num2) {
  return num1 + num2;
}

resultA = add(1, 2); // you get resultA = 3 immediately
resultB = add(resultA, 3); // you get resultB = 6 immediately
resultC = add(resultB, 4); // you get resultC = 10 immediately

console.log('total' + resultC);
console.log(resultA, resultB, resultC);
```

Async version:
```javascript
// add two numbers remotely
// get the result by calling an API

let resultA, resultB, resultC;

function addAsync (num1, num2, callback) {
  // use the famous jQuery getJSON callback API
  // https://api.jquery.com/jQuery.getJSON/
  return $.getJSON('http://www.example.com', {
    num1: num1,
    num2: num2
  }, callback);
}

addAsync(1, 2, success => {
  // callback 1
  resultA = success; // you get result = 3 here

  addAsync(resultA, 3, success => {
    // callback 2
    resultB = success; // you get result = 6 here

    addAsync(resultB, 4, success => {
      // callback 3
      resultC = success; // you get result = 10 here

      console.log('total' + resultC);
      console.log(resultA, resultB, resultC);
    });
  });
});
```
live code https://jsbin.com/barimo/edit?js,console

## Escape from Callback Hell
```javascript
// add two numbers remotely using observable

let resultA, resultB, resultC;

function addAsync(num1, num2) {
  // use ES6 fetch API, which return a promise
  // What is .json()? https://developer.mozilla.org/en-US/docs/Web/API/Body/json
  return fetch(`http://www.example.com?num1=${num1}&num2=${num2}`)
    .then(x => x.json());
}

addAsync(1, 2)
  .then(success => {
    resultA = success;
    return resultA;
  })
  .then(success => addAsync(success, 3))
  .then(success => {
    resultB = success;
    return resultB;
  })
  .then(success => addAsync(success, 4))
  .then(success => {
    resultC = success;
    return resultC;
  })
  .then(success => {
    console.log('total: ' + success)
    console.log(resultA, resultB, resultC)
  });
```
live code https://jsbin.com/qafane/edit?js,console

Sources:
* https://www.digitalocean.com/community/tutorials/javascript-promises-for-dummies
* https://api.jquery.com/jquery.callbacks/
