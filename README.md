# Objectives
1. Gain experience writing and executing web and non-web server Node.js JavaScript code using VSCode
2. Gain experience using Fastify with the GET verb, routes, and query parameters

# Technologies Used
- Terminal
- VSCode
- Postman

# Part 1
Complete [Lab 3](https://pozawa1.github.io/cit281-lab3/) and [Lab 4](https://pozawa1.github.io/cit281-lab4/)

# Part 2
Create the following files:
- p3-module.js : Code module that exports the coinage functionality
- p3-server.js: Coinage web server 
- index.html : Main coin testing web page that issues requests to the web server
- package.json : Node.js configuration file
- .gitignore : Git intentional file ignore file

# Part 3
Initialize the cit281/p3 folder as a git repo. Add/commit the empty p3-module.js, p3-server.js, and index.html files.

# Part 4
The code module file, p3-module.js, will contain the following four functions:

(1) validDenomination(coin): Returns true if the coin function parameter is a valid coin value of either 1, 5, 10, 25, 50, or 100

```
function validDenomination(coin) {return ([1, 5, 10, 25, 50, 100].indexOf(coin) !== -1) ? true : false};
```

(2) valueFromCoinObject(obj): Returns the calculated value of a single coin object from the obj function parameter

```
function valueFromCoinObject(obj) {
    const { denom = 0, count = 0 } = obj;
    return count * denom;
}
```

(3) valueFromArray(arr): Iterates through an array of coin objects and returns the final calculated value of all coin objects

``` 
function valueFromArray(arr) {
    if(Array.isArray(arr[0])) {
        arr =arr[0];
    }
       return arr.reduce((acc, val) => {
        return acc + valueFromCoinObject({denom: val.denom, count: val.count});
    }, 0);
}
```

(4) coinCount(...coinage): Calls and returns the result of valueFromArray() function, which will be the value of all coin objects with the coinage array function parameter

```
function coinCount(...coinage) {
    return valueFromArray(coinage);
}
```

# Part 5
Create the file index.html, which will be displayed in your browser when navigating to the IP and port for the project web server.

```
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <title>Coinage</title>

  <style>
  </style>
</head>

<body>
    <h1>Welcome to Coinage!</h1>
    <ul>
        <li><a href="/coin?denom=25&count=3">3 x 25 coin = 75</a></li>
        <li><a href="/coins?option=1">Option 1 = 35</a></li>
        <li><a href="/coins?option=2">Option 2 = 57</a></li>
        <li><a href="/coins?option=3">Option 3 = 57 (Extra Credit)</a></li>
    </ul>
</body>
</html>
```

# Part 6
Initialize the project folder as a Node.js folder, and install Fastify.

# Part 7
The server code file, p3-server.js, will import the coinCount code module function, and will contain the Node.js and Fastify web server code. 

```
const fs = require("fs");
const fastify = require("fastify")();
const { coinCount } = require('./p3-module.js')

// Start server and listen to requests using Fastify
const listenIP = "localhost";
const listenPort = 8080;
fastify.listen(listenPort, listenIP, (err, address) => {
    if (err) {
        console.log(err);
        process.exit(1);
    }
    console.log(`Server listening on ${address}`);
});
```

# Part 8
Add a GET route for / that reads and returns index.html using the readFile() fs function.

```
fastify.get("/", (request, reply) => {
    fs.readFile(`${__dirname}/index.html`, (err,data) => {
        if (err) {
            reply
            .code(500)
            .header('Content-Type', 'text/html; charset=utf-8')
            .send('<h1>Error processing request</h1>')
        } else {
            reply
            .code(200)
            .header('Content-Type', 'text/html; charset=utf-8')
            .send(data)
        }
    });
});
```

# Part 9
Add an additional GET route, /coin, that will accept two query parameters, denom and count, and return the correct coin value.

```
fastify.get("/coin", (request, reply) => {
    const {denom = 0, count = 0} = request.query; 
    let coinValue = coinCount({denom, count});
    reply
    .code(200)
    .header("Content-Type", "text/html; charset=utf-8")
    .send(`<h2>Value of ${count} of ${denom} is ${coinValue}</h2><br /><a href="/">Home</a>`)
});
```

# Part 10
Add another GET route, /coins, that will accept a single query parameters, option, and will return the correct coin value based on calling coinValue() with the earlier test data values, depending on the value of option.

```
fastify.get("/coins", (request, reply) => {
    const {option} = request.query;
    const coins = [{denom: 25, count: 2}, {denom: 1, count: 7}]; 
    let coinValue;
    switch (option) {
        case "1":
            coinValue = coinCount({ denom: 5, count: 3 }, { denom: 10, count: 2});
            break;
        case "2":
            coinValue = coinCount(...coins);
            break;
        case "3":
            coinValue = coinCount(coins);
            break;
        default:
            coinValue = 0;
    };
    console.log(coinValue);
    reply
    .code(200)
    .header("Content-Type", "text/html; charset=utf-8")
    .send(`<h2>Option ${option} value is ${coinValue}</h2><br /><a href="/">Home</a>`)
})
```

[Source Code](https://github.com/pozawa1/cit281-p3/blob/main/source-code-p3)

[Return to Homepage](https://pozawa1.github.io/)
