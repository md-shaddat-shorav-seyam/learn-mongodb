# learn-mongodb
------>>>>>>>>>mongodb full tutorial with example and real world usecase 
Here’s a **complete, practical MongoDB tutorial**—from basics to real-world usage—with examples you can actually use in projects.

---

# 🚀 What is MongoDB?

**MongoDB** is a **NoSQL database** that stores data in **JSON-like documents (BSON)** instead of tables.

👉 Unlike SQL:

* No fixed schema
* Flexible structure
* Scales easily (used in modern apps)

---

# 🧠 Core Concepts

### 1. Database → Collection → Document

```
Database → Collection → Document
```

Example:

```json
{
  "name": "Seyam",
  "age": 18,
  "skills": ["JS", "MongoDB"]
}
```

* **Database** → like a project
* **Collection** → like a table
* **Document** → like a row (but flexible)

---

# ⚙️ Install MongoDB

### Option 1: Local Install

* Download from official MongoDB website

### Option 2: Cloud (Recommended)

Use **MongoDB Atlas**

---

# 🛠️ MongoDB Basic Commands

### Connect to MongoDB shell

```bash
mongosh
```

---

## 📌 1. Create Database

```js
use myApp
```

---

## 📌 2. Create Collection & Insert Data

```js
db.users.insertOne({
  name: "Seyam",
  age: 18,
  city: "Kushtia"
})
```

Insert multiple:

```js
db.users.insertMany([
  { name: "A", age: 20 },
  { name: "B", age: 25 }
])
```

---

## 📌 3. Read Data

```js
db.users.find()
```

Filter:

```js
db.users.find({ age: 18 })
```

---

## 📌 4. Update Data

```js
db.users.updateOne(
  { name: "Seyam" },
  { $set: { age: 19 } }
)
```

---

## 📌 5. Delete Data

```js
db.users.deleteOne({ name: "A" })
```

---

# 🔍 Query Operators (Important)

### Comparison

```js
db.users.find({ age: { $gt: 18 } }) // greater than
```

### Logical

```js
db.users.find({
  $or: [{ age: 18 }, { city: "Dhaka" }]
})
```

---

# 🔗 Indexing (Performance Boost)

```js
db.users.createIndex({ name: 1 })
```

👉 Makes search faster

---

# 🔄 Aggregation (Powerful Feature)

Example:

```js
db.orders.aggregate([
  { $match: { status: "completed" } },
  { $group: { _id: "$userId", total: { $sum: "$amount" } } }
])
```

👉 Used for:

* Reports
* Analytics
* Dashboard

---

# 🧩 MongoDB with Node.js (Real Use)

Install:

```bash
npm install mongodb
```

---

## 📌 Example Backend

```js
const { MongoClient } = require("mongodb");

const uri = "your_connection_string";
const client = new MongoClient(uri);

async function run() {
  await client.connect();
  const db = client.db("myApp");
  const users = db.collection("users");

  // Insert
  await users.insertOne({ name: "Seyam", age: 18 });

  // Read
  const data = await users.find().toArray();
  console.log(data);
}

run();
```

---

# 🌍 Real-World Use Cases

## 1. 🛒 E-commerce App

Store:

* Users
* Products
* Orders

Example:

```json
{
  "product": "Laptop",
  "price": 50000,
  "userId": "123",
  "status": "delivered"
}
```

👉 Why MongoDB?

* Flexible product schema
* Fast scaling

---

## 2. 💬 Chat Application

Store messages:

```json
{
  "sender": "user1",
  "receiver": "user2",
  "message": "Hello!",
  "time": "2026-04-26"
}
```

👉 Works great with real-time apps

---

## 3. 📊 Analytics Dashboard

Use aggregation:

* Total sales
* Active users
* Growth

---

## 4. 🌐 Blogging Platform

Store:

```json
{
  "title": "MongoDB Guide",
  "content": "...",
  "author": "Seyam",
  "tags": ["db", "nosql"]
}
```

---

# ⚡ SQL vs MongoDB (Quick Idea)

| Feature   | SQL   | MongoDB  |
| --------- | ----- | -------- |
| Structure | Table | Document |
| Schema    | Fixed | Flexible |
| Scaling   | Hard  | Easy     |
| Relations | JOIN  | Embedded |

---

# 🔥 Best Practices

✔ Use indexes
✔ Avoid very large documents
✔ Use schema validation
✔ Use aggregation for analytics
✔ Design schema based on query (IMPORTANT)

---

# 🧠 Pro Tips (For You 💻)

Since you're into development:

* Use MongoDB with:

  * **Node.js**
  * **Express.js**
  * **Mongoose**

---

# 📚 Next Level Topics (If you want)

* Schema Design (VERY IMPORTANT)
* Mongoose deep dive
* Transactions
* Sharding (big systems)
* Authentication (JWT + MongoDB)

---

# 🚀 Want More?

If you want, I can give you:

✅ 10+ real project ideas using MongoDB
✅ Full MERN stack project (step-by-step)
✅ Practice problems (like interview questions)
✅ Advanced aggregation tricks

Just tell me 👍



--------------->>>>>>>>>>> from create schema to data falidation with node js full tutorial



Alright — now we’ll go **end-to-end**:
👉 **Schema design → validation → Node.js integration → real-world API**

I’ll use **MongoDB + Node.js + Express.js + Mongoose** (industry standard stack).

---

# 🚀 1. Project Setup

### Initialize project

```bash
npm init -y
npm install express mongoose dotenv
```

---

# 📁 2. Project Structure (Clean & Scalable)

```
project/
│── models/
│   └── User.js
│── routes/
│   └── userRoutes.js
│── controllers/
│   └── userController.js
│── config/
│   └── db.js
│── server.js
```

---

# 🔌 3. Connect MongoDB

### `config/db.js`

```js
const mongoose = require("mongoose");

const connectDB = async () => {
  try {
    await mongoose.connect(process.env.MONGO_URI);
    console.log("MongoDB Connected");
  } catch (err) {
    console.error(err);
    process.exit(1);
  }
};

module.exports = connectDB;
```

---

# 🧠 4. Create Schema (Core Part)

### `models/User.js`

```js
const mongoose = require("mongoose");

const userSchema = new mongoose.Schema({
  name: {
    type: String,
    required: [true, "Name is required"],
    minlength: 3,
    maxlength: 50,
    trim: true
  },

  email: {
    type: String,
    required: true,
    unique: true,
    lowercase: true,
    match: [/^\S+@\S+\.\S+$/, "Invalid email format"]
  },

  age: {
    type: Number,
    min: 0,
    max: 120
  },

  password: {
    type: String,
    required: true,
    minlength: 6
  },

  role: {
    type: String,
    enum: ["user", "admin"],
    default: "user"
  },

  createdAt: {
    type: Date,
    default: Date.now
  }
});

module.exports = mongoose.model("User", userSchema);
```

---

# ✅ 5. Data Validation (IMPORTANT)

Mongoose provides **built-in validation + custom validation**

---

## 🔹 Built-in Validation

Already used:

* `required`
* `minlength`
* `maxlength`
* `enum`
* `match`

---

## 🔹 Custom Validation Example

```js
email: {
  type: String,
  validate: {
    validator: function (v) {
      return v.includes("@gmail.com");
    },
    message: "Only Gmail allowed"
  }
}
```

---

## 🔹 Pre-save Hook (Advanced Validation)

```js
userSchema.pre("save", function (next) {
  if (this.password.length < 6) {
    return next(new Error("Password too short"));
  }
  next();
});
```

---

# 🎮 6. Controller (Business Logic)

### `controllers/userController.js`

```js
const User = require("../models/User");

// Create user
exports.createUser = async (req, res) => {
  try {
    const user = await User.create(req.body);
    res.status(201).json(user);
  } catch (err) {
    res.status(400).json({ error: err.message });
  }
};

// Get all users
exports.getUsers = async (req, res) => {
  const users = await User.find();
  res.json(users);
};
```

---

# 🌐 7. Routes

### `routes/userRoutes.js`

```js
const express = require("express");
const router = express.Router();
const { createUser, getUsers } = require("../controllers/userController");

router.post("/", createUser);
router.get("/", getUsers);

module.exports = router;
```

---

# 🧩 8. Main Server

### `server.js`

```js
require("dotenv").config();
const express = require("express");
const connectDB = require("./config/db");

const app = express();

connectDB();
app.use(express.json());

// routes
app.use("/api/users", require("./routes/userRoutes"));

app.listen(5000, () => {
  console.log("Server running on port 5000");
});
```

---

# 🧪 9. Test API

### POST `/api/users`

```json
{
  "name": "Seyam",
  "email": "seyam@gmail.com",
  "age": 18,
  "password": "123456"
}
```

---

# ❌ Validation Errors Example

If wrong data:

```json
{
  "name": "A",
  "email": "invalid",
  "password": "123"
}
```

👉 Response:

```json
{
  "error": "User validation failed: name: Path `name` is too short..."
}
```

---

# 🔒 10. Real-World Improvements

## 🔐 Password Hashing

Use:

```bash
npm install bcryptjs
```

```js
const bcrypt = require("bcryptjs");

userSchema.pre("save", async function (next) {
  this.password = await bcrypt.hash(this.password, 10);
  next();
});
```

---

## 🔍 Unique Email Handling

```js
if (err.code === 11000) {
  return res.status(400).json({ error: "Email already exists" });
}
```

---

## 📦 Separate Validation Layer (Best Practice)

Use:

```bash
npm install joi
```

👉 Example:

```js
const Joi = require("joi");

const schema = Joi.object({
  name: Joi.string().min(3).required(),
  email: Joi.string().email().required(),
  password: Joi.string().min(6).required()
});
```

---

# 🌍 Real-World Use Case (User System)

This structure is used in:

* Login/Register system
* E-commerce user accounts
* Admin dashboards
* SaaS apps

---

# ⚡ Key Concepts You Learned

✔ Schema design
✔ Built-in validation
✔ Custom validation
✔ Middleware (hooks)
✔ API creation
✔ Error handling

---

# 🧠 Pro Insight (Important)

👉 Always design schema based on:

* How you **query data**
* Not how you think data looks

---

# 🚀 Want Next Level?

I can guide you to:

✅ JWT Authentication (Login system)
✅ Full MERN project (like Facebook clone basics)
✅ Advanced schema design (relations, refs, populate)
✅ Production-level architecture

Just tell me 👍
