This project was bootstrapped with [Create React App](https://github.com/facebook/create-react-app).

## Available Scripts

In the project directory, you can run:

### `yarn start`

Runs the app in the development mode.<br />
Open [http://localhost:3000](http://localhost:3000) to view it in the browser.

The page will reload if you make edits.<br />
You will also see any lint errors in the console.

### `yarn test`

Launches the test runner in the interactive watch mode.<br />
See the section about [running tests](https://facebook.github.io/create-react-app/docs/running-tests) for more information.

### `yarn build`

Builds the app for production to the `build` folder.<br />
It correctly bundles React in production mode and optimizes the build for the best performance.

The build is minified and the filenames include the hashes.<br />
Your app is ready to be deployed!

See the section about [deployment](https://facebook.github.io/create-react-app/docs/deployment) for more information.

### `yarn eject`

**Note: this is a one-way operation. Once you `eject`, you can’t go back!**

If you aren’t satisfied with the build tool and configuration choices, you can `eject` at any time. This command will remove the single build dependency from your project.

Instead, it will copy all the configuration files and the transitive dependencies (webpack, Babel, ESLint, etc) right into your project so you have full control over them. All of the commands except `eject` will still work, but they will point to the copied scripts so you can tweak them. At this point you’re on your own.

You don’t have to ever use `eject`. The curated feature set is suitable for small and middle deployments, and you shouldn’t feel obligated to use this feature. However we understand that this tool wouldn’t be useful if you couldn’t customize it when you are ready for it.

## Learn More

You can learn more in the [Create React App documentation](https://facebook.github.io/create-react-app/docs/getting-started).

To learn React, check out the [React documentation](https://reactjs.org/).

### Code Splitting

This section has moved here: https://facebook.github.io/create-react-app/docs/code-splitting

### Analyzing the Bundle Size

This section has moved here: https://facebook.github.io/create-react-app/docs/analyzing-the-bundle-size

### Making a Progressive Web App

This section has moved here: https://facebook.github.io/create-react-app/docs/making-a-progressive-web-app

### Advanced Configuration

This section has moved here: https://facebook.github.io/create-react-app/docs/advanced-configuration

### Deployment

This section has moved here: https://facebook.github.io/create-react-app/docs/deployment

### `yarn build` fails to minify

This section has moved here: https://facebook.github.io/create-react-app/docs/troubleshooting#npm-run-build-fails-to-minify

=============================================

## MERN stack TODO

### FullStack react web application <br />

```javascript
npx create-react-app client
cd client/
```

Inside the client repo, create another folder called `server`.

```javascript
mkdir server
cd server/
```

Initiate the `package.json` using the below code segment.

```javascript
npm init -y
```

With the package.json file available in the server directory, we are ready to add some dependencies which will be useful in setting up the backend.

```javascript
npm install express body-parser cors mongoose
```

Let us have an idea about what each of the above package does:

**Express:** Express is a light weighted web framework of Node.js. This acts as our main server and has many compatible middleware to perform almost any kind of function in web development. <br />

**body-parser:** It is a middleware used to parse the posted data from the client side.<br>

**cors:** Cross-origin resource sharing (CORS) is a mechanism that allows restricted resources on a web page to be requested from another domain outside the domain from which the first resource was served. And cors is the Node.js package which performs the above function for us.<br>

**mongoose:** mongoose is an object modelling tool for MongoDB. It helps us access MongoDB in an object oriented way.

Then, we must also install a package called nodemon.

```javascript
npm install -g nodemon
```

If not MogoDB install, In MacOS, we must install and run Mongo by the following commands.

```javascript
brew install mongodb
mkdir -p /db/data
mongod
```

If the mongo shell not work, stop mondoDB

```javascript
brew services stop mongodb
```

And then run.

```javscript
 mongod
```

After the mongo shell opens:

```javscript
use todos
```

The above command will create a database named todos.

## Configuring the server and connecting the database

Let us first create a file called `server.js` inside the server folder.

```javascript
cd server/
touch server.js
```

Also create a directory called `db` inside the server and create a file called `index.js` inside it. This file will handle the connection of the database to the server.

```javascript
mkdir db
cd db/
touch index.js
```

The server.js file inside the server directory is given below.

```javascript
// server.js

const express = require('express')
const bodyParser = require('body-parser')
const cors = require('cors')

const db = require('./db')

const app = express()
const apiPort = 3000

app.use(bodyParser.urlencoded({ extended: true }))
app.use(cors())
app.use(bodyParser.json())

db.on('error', console.error.bind(console, 'MongoDB connection error:'))

app.listen(apiPort, () => console.log(`Server running on port ${apiPort}`))
```

<br />
The index.js file inside the db directory is given below.

```javascript
//index.js

const mongoose = require('mongoose')

mongoose.connect('mongodb://127.0.0.1:27017/todos', { useNewUrlParser: true }).catch((e) => {
	console.error('Connection error', e.message)
})

const db = mongoose.connection

module.exports = db
```

With the above code, we are creating an express server and we are binding the cors and body-parser middleware to the app object.

We make use of the mongoose library inside the index.js file of the db directory to connect the database. We are loading the database in the into the server.js file to complete the connection of MongoDB with the express server.

Also, note that the server listens to the port 3000.

To run the server:

```javascript
nodemon server.js
```

in the sever folder

<br />

## Creating a mongoose schema

Create a `models` folder inside the server. Inside the models directory, create a file named `todo-model.js.`

```javascript
cd server/
mkdir models
cd models/
touch todo-model.js
```

Mongoose allows us to create a model in an object oriented way.

```javascript
// todo-model.js

const mongoose = require('mongoose')
const Schema = mongoose.Schema

const Todo = new Schema(
	{
		todo: { type: String, required: true },
	},
	{ timestamps: true }
)

module.exports = mongoose.model('todos', Todo)
```

With the above code, we are ready to access the Mongo database using the Todo Schema.

<br />

## Endpoint implementation

Create two folders inside the server directory, namely, `routes` and `controllers`. Inside the routes folder create a file named `todo-router.js` and inside the controller folder create a file named `todo-ctrl.js`.

```javascript
cd server/
mkdir routes
cd routes/
touch todo-router.js
```

```javascript
cd server/
mkdir controllers
cd controllers/
touch todo-ctrl.js
```

We must first create a router instance inside the todo-router.js. The created router instance will be used to handle the implementations such as adding a Todo item or deleting a Todo item, etc.

```javascript
const express = require('express')
const router = express.Router()
```

The above lines will create a router instance.

```javascript
// todo-router.js

const express = require('express')

const todoCtrl = require('../controllers/todo-ctrl')

const router = express.Router()

router.post('/', todoCtrl.createItem)
router.get('/', todoCtrl.getTodos)

module.exports = router
```

todo-ctrl.js will have the implementations of the actions like adding a Todo item or deleting a Todo item.

```javascript
// todo-ctrl.js

const Todo = require('../models/todo-model')

createItem = (req, res) => {
	const body = req.body
	if (!body) {
		return res.status(400).json({
			success: false,
			error: 'You must provide an item',
		})
	}

	const todo = new Todo(body)

	if (!todo) {
		return res.status(400).json({ success: false, error: err })
	}

	todo
		.save()
		.then(() => {
			return res.status(200).json({
				success: true,
				id: todo._id,
				message: 'todo item created',
			})
		})
		.catch((error) => {
			return res.status(400).json({
				error,
				message: 'todo item not created',
			})
		})
}

getTodos = async (req, res) => {
	await Todo.find({}, (err, todos) => {
		if (err) {
			return res.status(400).json({ success: false, error: err })
		}
		if (!todos.length) {
			return res.status(404).json({ success: false, error: `Item not found` })
		}
		return res.status(200).json({ success: true, data: todos })
	}).catch((err) => console.log(err))
}

module.exports = { createItem, getTodos }
```

The `todo-ctrl.js` file has the code for main implementation of adding a todo item and returning all the todo items.

Inside the `todo-router.js`, we define the method of handling the request using the statement

```javascript
router.post('/', todoCtrl.createItem)
```

Here, todoCtrl is the imported version of the todo-ctrl.js inside the todo-router.js and the createItem is the function which is declared and defined inside the todo-ctrl.js which handles the addition of todo item into the database.

We can also see the another route which is used to get all the todo items which we need to display on the browser.

```javascript
// server.js

const express = require('express')
const bodyParser = require('body-parser')
const cors = require('cors')

const db = require('./db')
// routes
const todoRouter = require('./routes/todo-router')

const app = express()
const apiPort = 3000

app.use(bodyParser.urlencoded({ extended: true }))
app.use(cors())
app.use(bodyParser.json())

db.on('error', console.error.bind(console, 'MongoDB connection error:'))

app.use('/', todoRouter)

app.listen(apiPort, () => console.log(`Server running on port ${apiPort}`))
```

We also must load the routes into the server.js file.

We can test the API's using Postman with this we finish the backend implementation of the Todo application.

<br />

## Basic Commands of MongoDB

**1. Mongo:** This is one of the most common commands used in MongoDB. When used, you are asking the platform to connect to localhost on default port 27017.

**2. Mongo <host>/<database>:** This command is used when you want the platform to connect to a particular database. An example of this command in action can be, mongo 10.121.65.58/mydb.

**3. use <database name>:** If at any point in time, you need to switch between existing databases, use this command. Example, use mydb.

**4. Db:** If you need to view the current database you are using, make use of this command.

**5. show collections:** If you need to view all the collections in the current database, then make use of this command. Example: show collections.

**6. show dbs:** In the midst of programming, if you need to view the current database being used then use this command. Example: show dbs.

**7. db.collection.insertMany([ <document1>, <document2>, … ]):** If you need to insert multiple documents within an already existing collection.

**8. db.collection.insert( <document> ):** If you need to insert a single new document into an already existing collection.
db.collection.find(<query>): If you need to find a specific document within a collection by using a field value condition, then use this command. Example, db.books.find({“title”:”Treasure Island”}).

**9. db.collection.find():** If you need to find all the documents in an already existing collection, then make use of this command. Example, db.books.find().

**10. db.collection.findOne(<query>, <projection>):** If you need to find the first document that matches the query you have given, then make use of this command. Example: db.books.findOne({}, {\_id:false}).

**11. db.collection.find(<query>, <projection>):** If you need to find some specific fields of a document in a collection, then you can make use of this command. Example: db.books.find({“title”:”Treasure Island”}, {title:true, category:true, \_id:false}).

**12. db.collection.update(<query>, <update>):** If you need to remove certain in an existing document, by matching a query then you can make use of this command. Example: db.books.update({title : “Treasure Island”}, {\$unset : {category:””}}).

**13. db.collection.update(<query>, <update> ):** If you need to update some specific fields of a document that match the given query, then make use of this command. Example: db.books.update({title : “Treasure Island”}, {\$set : {category :”Adventure Fiction”}}).

**14. db.collection.remove(<query>,** {justOne:true}): If in a certain situation, you need to delete a single document that matches your query then use this command. Example: db.books.remove({title :”Treasure Island”}, {justOne:true}).

**15. db.collection.update(<query>, <update>,** {multi:true} ): If you need to delete certain fields of all the documents that match your query, then use this command. Example: db.books.update({category : “Fiction”}, {\$unset : {category:””}}, {multi:true}).

**16. db.collection.remove({}):** If you need to delete all the documents in a collection, irrespective if they match your query or not, then use this command. Example: db.books.remove({}).

**17. db.collection.remove(<query>):** If you need to delete all the documents that match a certain query, then make use of this command. Example: db.books.remove({“category” :”Fiction”}).

## Client side

We have already created the client side boilerplate code. We then create a `components` folder inside the `src` folder. We create a file named `Todo.js` inside the components folder.

```javascript
cd client/src/
mkdir components
cd components/
touch Todo.js
```

We use axios package of npm for fetching the data from the endpoints.

```javascript
npm install axios
```

```javascript
// Todo.js

import React, { Component } from 'react'
import axios from 'axios'

export class Todo extends Component {
	constructor(props) {
		super(props)

		this.state = {
			todos: [],
			item: '',
		}
	}

	changeHandler = (event) => {
		this.setState({ item: event.target.value })
	}

	clickHandler = (event) => {
		event.preventDefault()
		console.log(this.state.item)
		axios({
			method: 'post',
			url: 'http://localhost:3000/',
			data: {
				todo: this.state.item,
			},
		})
		this.setState({ item: '' })
	}

	componentDidMount() {
		axios.get('http://localhost:3000/').then((response) => {
			console.log(response.data.data)
			let data = []
			console.log(response.data.data.length)
			for (var i = 0; i < response.data.data.length; i++) {
				data.push(response.data.data[i].todo)
			}
			this.setState({ todos: data })
		})
	}
	componentDidUpdate() {
		axios.get('http://localhost:3000/').then((response) => {
			console.log(response.data.data)
			let data = []
			console.log(response.data.data.length)
			for (var i = 0; i < response.data.data.length; i++) {
				data.push(response.data.data[i].todo)
			}
			this.setState({ todos: data })
		})
	}

	render() {
		return (
			<div>
				<input type="text" onChange={this.changeHandler} />
				<button type="submit" onClick={this.clickHandler}>
					add
				</button>
				<div>
					<ul>
						{this.state.todos.map((todo, index) => (
							<li key={index}>{todo}</li>
						))}
					</ul>
				</div>
			</div>
		)
	}
}

export default Todo
```

The clickHandler method is used to post the data to the database. The componentDidMount method is a lifecycle method which gets triggered whenever a component inside the window is altered. So, when the todo item is posted, the endpoint to get all the todos will be hit by the componentDidMount method.

Make sure that you import the `Todo.js` inside the `App.js` and `run`:

```javascript
import React from 'react'
import logo from './logo.svg'
import './App.css'
import Todo from './components/Todo'

function App() {
	return (
		<div className="App">
			<Todo />
		</div>
	)
}

export default App
```
