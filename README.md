## Project 3

[Home](https://liv-edi.github.io/cit281/)

In this project we cerated a coinage code module that could calculate the total value of a coin object. We then updated the server code file to be a Node.js web server using Fastify. The final server displayed a web page with web links to test the coinage code.

Technologies used for this project:
- VSCode
- Chrome
- Node.js
- Fastify

The purpose of this project was to gain experince breaking up projects into smaller more mamgable components, writing/executing server side Node.js code using VSCode, using/ loading a file as a webpage, and understanding project assignment decriptions. The project also allowed us to practice creating/ using code modules and refactoring using modern JS syntax.

From doing this project I learned how to create and use code modules with objects. I also learned how to use server side code with an actual webpage.

<img width="317" alt="Screen Shot 2022-06-04 at 6 38 07 PM" src="https://user-images.githubusercontent.com/105889862/172033511-e9128517-c0be-46dd-b1a7-6b0ecabca6f2.png">


```
const fs = require('fs');
const {coinCount} = require('./p3-module.js');
const fastify = require('fastify')({
    logger: true
});

fastify.get('/', (request, reply) => {
    fs.readFile(`${__dirname}/index.html`, (err, data) => {
        if(err) {
            console.log(err);
            reply.code(500);
            reply.header('Content-Type', 'text/html');
            reply.send("Error processing request");
        } else {
            reply.code(200);
            console.log("URL: ", request.url);
            reply.header('Content-Type', 'text/html');
            reply.send(data);

        }
    });
})

fastify.listen(8080, 'localhost', (err, address) => {
    if (err) {
        fastify.log.error(err)
        process.exit(1)
    }
    console.log(`Server running at ${'localhost'} ${8080}`);
})

fastify.get('/coin', (request, reply) => {
    const {denom = 0, count = 0} = request.query;
    console.log(denom);
    let coinValue = coinCount({denom, count});
    reply
        .code(200)
        .header('Content-Type', 'text/html')
        .send(`<h2>Value of ${count} of ${denom} is ${coinValue}</h2><br /><a href="/">Home</a>`);
})

fastify.get('/coins', (request, reply) => {
    const { option } = request.query;
    let coinValue = 0;
    const coins = [{denom: 25, count: 2},{denom: 1, count: 7}];
    switch (option) {
        case ('1'):
            coinValue = coinCount({ denom: 5, count: 3 }, { denom: 10, count: 2 });
            break;
        case ('2'):
            coinValue = coinCount(...coins);
            break;
        case ('3'):
            coinValue = coinCount(coins);
            break;
        default:
            break;
    }
    reply
        .code(200)
        .header('Content-Type', 'text/html')
        .send(`<h2>Option ${option} value is ${coinValue}</h2><br /><a href="/">Home</a>`)
})
```

```
module.exports = {
    coinCount
};

function validDenomination(coin) {
    const coinValues = [1, 5, 10, 25, 50, 100];
    return coinValues.indexOf(coin);
    
    
};
function valueFromCoinObject(obj) {
    const {denom = 0, count = 0} = obj;
    return denom * count;
};

function valueFromArray(arr) {
    //let result = 0;
    //for (i = 0; i < arr.length; i++) {
      //  result += valueFromCoinObject(arr[i]);
    //}
    //return result;
    //console.log(arr);
    const result = arr.reduce(
        //previous is the rteurn value of the previous iteration through the loop
        //current is the current bascially would be the equivalent of i in a for loop
        (previous, current) => previous + valueFromCoinObject(current), 0
    );
    return result;
};
function coinCount(...coinage) {
    //if (Array.isArray(coinage)) {
    return valueFromArray(coinage.flat());
    //} else {
    //    return valueFromArray(coinage.flat());
    //}
    //let arr = [].concat(...coinage);
    //console.log(arr);
    //arr.concat(...coinage);
    //return valueFromArray(coinage);
}
//console.log([{denom: 5, count: 3}]);
console.log("{}", coinCount({denom: 5, count: 3}));
console.log("{}s", coinCount({denom: 5, count: 3},{denom: 10, count: 2}));
const coins = [{denom: 25, count: 2},{denom: 1, count: 7}];
console.log("...[{}]", coinCount(...coins));
console.log("[{}]", coinCount(coins));
```

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
