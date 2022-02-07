# Overview

## Node.js

- JavaScript Runtime.
- Built on Chrome's V8 JavaScript Engine.
- Event-driven, non-blocking I/O model.
- `npm` is the largest ecosystem of open source libraries in the world.

# Module System

## Importing a core module

```javascript
const filesystem = require('fs')

filesystem.writeFileSync('filename', 'file content')
```

## Importing your own module

```javascript
// notes.js
const getNotes = function() {
    return "Some Notes"
}

module.exports = getNotes
```

```javascript
const getNotes = require('./notes.js')

const notes = getNotes()

console.log(notes)
```

## Importing `npm` modules

1. Create `package.json`
2. Run `npm install <package-name>`.
   This will install and add the dependency to the `package.json` file.
3. Import using `const dependency = require('dependency-name')`

### Printing in color

```javascript
const chalk = require('chalk')

console.log(chalk.green('Success!'))
```

# Command Line Arguments

## Using built-in utility

```javascript
console.log(progress.argv)
console.log(progress.argv[2 ])
```

## Using `yargs`

Yargs allows you to parse the command line arguments using well-test methods from `yargs`.

It supports adding argument description to `--help` page.

```javascript
const yargs = require('yargs')
```

More details can be found on official npm page.

# Asynchronous Node.JS

## Callback

Callback is the code provided to a function as the code to be executed later in the overall execution.

```javascript
someFunction(data1, data2, (error, data) => {
    ...
})
```

```javascript
const someFunction = (data1, data2, callback) => {
    ...
    callback(error, returnData)
}
```

It is a convention to have `<error, response>` as common parameters to a callback method.

## Destructuring Objects

```javascript
const product = {
    label = 'Red Notebook',
    price = 3
}

const {label: productLabel, price} = product

console.log(productLabel)
console.log(price)
```

# Web Servers

## Express

```plsql
const express = require('express')

const app = express()

app.get('', (req, res) => {
    res.send('Home Page')
        
	/**
	*	res.send('<h1>Homepage</h1>')
	*/
        
	/** 
	*	res.send({
	*		name: 'Homepage'
	*	})
	*/
})

app.listen(3000)
```

### Static HTML Files

1. Create a `public` folder which will contain the HTML files.

2. Create the path variable to that directory.

3. Tell Express to use that directory as the static data directory.

   ```javascript
   app.use(express.static(publicDirectoryPath))
   ```

### Dynamic Templating

1. Install NPM Package: `hbs`

2. Create directory in `src` as `views`.

3. Create view files as `index.hbs`.

4. Configure `app.js` to use HBS Views. 

5. Use HBS Syntax to use views.

   ```javascript
   /* app.js */
   
   app.set('view engine', 'hbs')
   
   app.get('', (req, res) => {
       res.render('index', {
       title: 'Weather App',
           name: 'Andrew'
       })
   })
   ```

   ```html
   /* index.html */
   
   ...
   <h1> {{title}} </h1>
   <h2> {{name}} </h2>
   ...
   ```

## Request Query

```javascript
app.get('/cars', (req, res) => {
	res.send(req.query)
})

// localhost:port/cars?name=AstonMartin
// returns { name: 'AstonMartin' }
```

