# Apple Pie Pets API

A robust RESTful API crafted with Node.js, Express, and MongoDB, designed to manage virtual pets for the [Apple Pies Pets Client](https://github.com/timmshinbone/applePiesPetsClient). This backend delivers full CRUD capabilities, harnessing OOP and MVC principles for a structured, scalable architecture. It’s live at <a href="https://avocadospetsapi.fly.dev/" target="_blank">https://avocadospetsapi.fly.dev</a>.

## Overview
This API powers pet management by handling requests to create, retrieve, update, and delete pet records. Built with MongoDB and deployed live via MongoDB Atlas and fly.io, it ensures flexible, document-based storage and seamless integration with the frontend.

## OOP in Action
The API’s design leans heavily on OOP to keep it organized and extensible:

#### Encapsulation
- **Pet Model**: A `Pet` model built from mongoose's built in `Schema` function builds a class and wraps data and operations:
```javascript
const petSchema = new mongoose.Schema(
	{
		name: {
			type: String,
			required: true,
		},
		type: {
			type: String,
			required: true,
		},
		age: {
			type: Number,
			required: true,
		},
		adoptable: {
			type: Boolean,
			required: true,
			default: false,
		},
        toys: [toySchema],
		owner: {
			type: mongoose.Schema.Types.ObjectId,
			ref: 'User'
		},
	},
	{
		timestamps: true,
        toObject: { virtuals: true },
        toJSON: { virtuals: true },
	}
)
```
Pet properties and MongoDB interactions are contained within the class, shielding external code from database specifics.

The Pet and other models utilize inheritance from mongoose's built in model class, giving them reusable functionality(via abstraction, discussed later), as well as definition.

---

#### Polymorphism
Flexible Responses: Routes handle diverse requests with a unified output:
```javascript

app.get('/pets', async (req, res) => res.json(await Pet.findAll()));
app.get('/pets/:id', async (req, res) => {
  const pet = await Pet.findById(req.params.id);
  res.json(pet || { error: 'Pet not found' });
});
```
The same `res.json()` adapts to full lists or single objects, demonstrating polymorphic flexibility.

#### Abstraction
MongoDB Layer: The MongoDB driver is abstracted into model methods, all thanks to MongooseJs. Here is an example of how we connect a MongoDB database to mongoose:
```javascript
const { MongoClient } = require('mongodb');
const client = new MongoClient(process.env.MONGO_URI);
const db = client.db('pets_db');
```
Routes rely on `Pet.findAll()` instead of raw queries, simplifying logic.

### MVC Structure
The API follows an MVC-inspired approach suited for a RESTful backend:

**Model:** The Pet class and MongoDB manage data storage and retrieval.
**View:** JSON responses serve as the output layer, formatted for client consumption.
**Controller:** Express routes orchestrate the flow:
```javascript
router.post('/', async (req, res) => {
    const pet = new Pet({ name: req.body.name, species: req.body.species });
    await pet.save();
    res.status(201).json(pet);
});
```
Routes connect user requests to model operations and response delivery.

### API Endpoints
The following routes define the API’s functionality:

#### Auth Routes

| Verb   | URI Pattern            | Controller#Action |
|--------|------------------------|-------------------|
| POST   | `/sign-up`             | `users#signup`    |
| POST   | `/sign-in`             | `users#signin`    |
| PATCH  | `/change-password/` | `users#changepw`  |
| DELETE | `/sign-out/`        | `users#signout`   |

#### Pet Routes

| Verb   | URI Pattern            | Controller#Action |
|--------|------------------------|-------------------|
| GET   | `/pets`             | `pets#index`    |
| GET   | `/pets/:id`             | `pets#show`    |
| POST   | `/pets`             | `pets#create`    |
| PATCH  | `/pets/:id` | `pets#update`  |
| DELETE | `/pets/:id`        | `pets#delete`   |

#### Toy Routes

| Verb   | URI Pattern            | Controller#Action |
|--------|------------------------|-------------------|
| POST   | `/toys/:petId`         | `toys#create`    |
| PATCH  | `/toys/:petId/:toyId`  | `toys#update`  |
| DELETE | `/toys/:petId/:toyId`  | `toys#delete`   |


**MVC Insight:** Routes (Controllers) call Pet methods (Model) to manipulate data, then return JSON (View). This division enhances clarity and growth potential.

### Entities

The following make up the models used in this API:

```js
User is comprised of the following:

    email: {
        type: String,
        required: true,
        unique: true,
    },
    hashedPassword: {
        type: String,
        required: true,
    },
    token: String,
```

```js
Pet is comprised of the following:

    name: {
        type: String,
        required: true
    },
    type: {
        type: String,
        required: true,
    },
    age: {
        type: Number,
        required: true,
    },
    adoptable: {
        type: Boolean,
        required: true,
        default: false,
    },
    toys: [toySchema],
    owner: {
        type: mongoose.Schema.Types.ObjectId,
        ref: 'User,
    }
```

```js
Toy is comprised of the following:

    name: {
        type: String,
        required: true
    },
    description: {
        type: String
    },
    isSqueaky: {
        type: Boolean,
        required: true,
        default: false,
    },
    condition: {
        type: String,
        enum: ['new', 'used', 'disgusting'],
        default: 'new',
    }
```
---

### Deployment
Hosted on Fly.io at <a href="https://avocadospetsapi.fly.dev/" target="_blank">https://avocadospetsapi.fly.dev</a>, the API integrates with MongoDB Atlas for cloud-based storage. Fly.io’s containerized deployment pairs with Atlas’s managed MongoDB, configured via environment variables (MONGO_URI, PORT) for security and scalability.

### Local Setup
**Clone this repo:** `git clone https://github.com/timmshinbone/applePiePetsAPI.git`
**Install packages:** `npm install`
**Add a .env file with MONGO_URI from MongoDB Atlas**
**Launch the server:** `npm start`

Check out the live API <a href="https://avocadospetsapi.fly.dev/" target="_blank">here</a>!

Powered by Node.js, Express, and MongoDB for pet-loving developers.