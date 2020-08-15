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

## `MERN stack TODO`

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

After the mongo shell opens:

```javscript
use todos
```

The above command will create a database named todos.

## Configuring the server and connecting the database

Let us first create a file called `server.js` inside the server folder.

```javascript
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

The todo-ctrl.js file has the code for main implementation of adding a todo item and returning all the todo items.

Inside the todo-router.js, we define the method of handling the request using the statement

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

## Client side

We have already created the client side boilerplate code. We then create a `components` folder inside the `src` folder. We create a file named `Todo.js` inside the components folder.

```javascript
cd client/
mkdir components
cd components/
touch Todo.js
```

We use axios package of npm for fetching the data from the endpoints.

```javascript
npm install axios
```

```javascript
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
