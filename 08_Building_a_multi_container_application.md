## 107: Single Container Deployment Issues

* The app was simple - no outside dependencies
* Our image was built multiple times
* How do we connect to a database from a container?

***

## 108: Application Overview

***

## 109: A Quick Note

***

## 110: Application Architecture

* Browser
    * Nginx
        * React Server (Frontend)
        * Express Server (Backend API)
            * Redis
                * Worker
            * Postgres

*Browser -->Nginx --> React Server (Frontend: html of javascitips )
          |              |
          |              V
           --------> Express Server (Backend API: read/write/submit request)--> Redis (Inmemory datastore) <--> Worker
                             |
                             --> Postgres    

***

## 111: Worker process Setup

```
$ mkdir complex
$ cd complex
$ mkdir worker
```

Create file `package.json`

```
{
    "dependencies": {
        "nodemon" : "1.18.3",       # live reload of code anytime we change the src of our project
        "redis" : "2.8.0"
    },
    "scripts": {
        "start": "node index.js",
        "dev" : "nodemon"
    }
}
```

Create a file `index.js`            # all primary logic for connecting to Redix

```
const keys = require('./keys');
const redis = require('redis');

const redisClient = redis.createClient({
    host: keys.redisHost,
    port: keys.redisPort,
    retry_strategy: () => 1000
});
const sub = redisClient.duplicate();        # sub stands for subscription

function fib(index) {
    if (index < 2) return 1;
    return fib(index - 1) + fix(index - 2);
}

sub.on('message', (channel, message) => {                   # anythime we get a new 'message' run this callback function
    redisClient.hset('values', message, fib(parseInt(message)));        # hashset key:value pair
});
sub.subscribe('insert');
```

Create file keys.js

```
module.exports = {
    redisHost: process.env.REDIS_HOST,
    redisPort: process.env.REDIS_PORT
};
```

```
$ cd workder
$ node index.js
```

***

## 112: Express API Setup

```
$ mkdir server
$ cd server
```

Create file `package.json`

```
{
    "dependencies": {
        "express": "4.16.4",
        "pg": "7.4.3",              # postgres client
        "redis": "2.8.0",
        "cors": "2.8.5",
        "nodemon": "1.18.11",
        "body-parser": "*"
    },
    "scripts": {
        "dev": "nodemon",
        "start": "node index.js"
    }
}
```

Create `keys.js` 

```
module.exports = {
    redisHost: process.env.REDIS_HOST,
    redisPort: process.env.REDIS_PORT,
    pgUser: process.env.PGUSER,
    pgHost: process.env.PGHOST,
    pgDatabase: process.env.pgDATABASE,
    pgPassword: process.env.PGPASSWORD,
    pgPort: process.env.PGPORT
}
```

***

## 113: Connecting to Postgres

Update `index.js`

```
const keys = require('./keys');

// Express App Setup
const express = require('express');
const bodyParser = require('body-parser');
const cors = require('cors');                   # CORS: cross origin resource sharing

const app = express(); # make a new express application that will receive and respont to any http request
app.use(cors());
app.use(bodyParser.json());

// Postgres Client Setup
const { Pool } = require('pg');                 # Pool module from pg
const pgClient = new Pool({
    user: keys.pgUser,
    host: keys.pgHost,
    database: keys.pgDatabase,
    password: keys.pgPassword,
    port: keys.pgPort
});
pgClient.on('error', () => console.log('Lost PG connection'));

pgClient
    .query('CREATE TABLE IF NOT EXISTS values (number INT)')
    .catch(err => console.log(err));
```

***

## 114: More Express API Setup

```
// Redis Client Setup
const redis = require('redis');
const redisClient = redis.createClient({
    host: keys.redisHost;
    port: keys.redisPort,
    retry_strategy: () => 1000                  # try every 1 second if connection is disconnected
});
const redisPublisher = redisClient.duplicate();

// Express route handlers

app.get('/', (req, res) => {
    res.send('Hi');
});

app.get('/values/all', async (req, res) => {
    const values = await pgClient.query('SELECT * from values');

    res.send(values.rows);
});

app.get('/values/current', async (req, res) => {
    redisClient.hgetall('values', (err, values) => {
        res.send(values);
    });
});

app.post('/values', async (req, res) => {
    const index = req.body.index;

    if(parseInt(index) > 40) {
        return res.status(422).send('Index too high');
    }

    redisClient.hset('values', index, 'Nothing yet!');
    redisPublisher.publish('insert', index);
    pgClient.query('INSERT INTO values(number) VALUES($1)', [index]);

    res.dend({ working: true });

});

app.listen(5000, err => {
    console.log('Listening');
});
```

```
$ cd server
$ node index.js
```

***

## 115: Generating the React App

Instead of this:
```
$ npm install -g create-react-app
$ craete-react-app client
```

Just do this:
```
$ npx create-react-app client
```

***

## 116: Fetching Data in the React App

```
$ cd complex
$ create-react-app
$ create-react-app client
```

***

## 117: Rendering Logic in the App

create file `OtherPage.js` 

```
import React from 'react';
import { Link } from 'react-router-dom';

export default () => {
    return (
        <div>
            In some other page!
            <Link to="/">Go back home</Link>
        </div>
    );
};
```

create file `Fib.js`

```
import React, { Component } from 'react';
import axios from 'axios';

class Fib extends Component {
    state = {
        seenIndexes: [],
        values: {},
        index: ''
    };

    componentDidMount () {
        this.fetchValues();
        this.fetchIndexes();
    }

    async fetchValues() {
        const values = await axios.get('/api/values/current');
        this.setState({ 
            values: values.data 
        });
    }

    async fetchIndexes() {
        const seenIndexes = await axios.get('/api/values/all');
        this.setState({
            seenIndexes: seenIndexes.data
        });
    }
}

handleSubmit = async (event) => {
    event.preventDefault();

    await axios.post('/api/values', {
        this.state.index;
    });
    this.setState({ index: '' });
};

renderSeenIndexes() {
    return this.state.seenIndexes.map(({ number }) => number).join(', ')
}

renderValues() {
    const entries = [];

    for (let key in this.state.values) {
        entries.push(
            <div key={key}> 
                For index {key} I calculated {this.state.values[key]}
            </div>
        );
    }
}

render(){
    return(
        <div>
            <form onSubmit={this.handleSubmit}>
                <label>Enter your index:</label>
                <input 
                    value={this.state.index}
                    onChange={event => this.setState({ index: event.target.value })}
                />
                <button>Submit</button>
            </form>

            <h3>Indexes I have seen:</h3>
            {this.renderSeenIndexes()}

            <h3>Calculated Values:</h3>
            {this.renderValues()}
        </div>
    );
}

export default Fib;
```

***

## 118: Rendering Logic in the App

***

## 119: Exporting the Fib Class

***

## 120: Routing in the React App
