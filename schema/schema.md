Alright—let’s break down **Mongoose Schema** in a way that’s both **complete and practical** (with real-world examples). This is one of the most important concepts in backend development with MongoDB.

---

# 🔷 What is a Schema in Mongoose?

In Mongoose, a **Schema** defines:

* Structure of documents
* Data types
* Validation rules
* Default values
* Relationships
* Middleware (hooks)

👉 Think of it as a **blueprint** for your MongoDB collection.

---

# 🔷 Basic Syntax

```js
import mongoose from "mongoose";

const userSchema = new mongoose.Schema({
  name: String,
  age: Number,
});
```

---

# 🔷 Full Schema Syntax (All Core Features)

```js
const userSchema = new mongoose.Schema({
  name: {
    type: String,
    required: true,
    trim: true,
    minlength: 3,
    maxlength: 50
  },

  email: {
    type: String,
    required: true,
    unique: true,
    lowercase: true,
    match: /.+\@.+\..+/   // regex validation
  },

  age: {
    type: Number,
    min: 0,
    max: 100
  },

  isActive: {
    type: Boolean,
    default: true
  },

  createdAt: {
    type: Date,
    default: Date.now
  },

  tags: [String],  // Array

  profile: {
    bio: String,
    website: String
  },

  role: {
    type: String,
    enum: ["user", "admin", "moderator"]
  },

  password: {
    type: String,
    required: true,
    select: false   // hidden by default
  },

  friend: {
    type: mongoose.Schema.Types.ObjectId,
    ref: "User"
  }

}, {
  timestamps: true,
  versionKey: false
});
```

---

# 🔷 Field Options (EVERY SYNTAX EXPLAINED)

## 1. `type`

Defines data type

```js
name: String
age: Number
isActive: Boolean
createdAt: Date
```

---

## 2. `required`

Makes field mandatory

```js
name: { type: String, required: true }
```

Custom message:

```js
required: [true, "Name is required"]
```

---

## 3. `default`

Sets default value

```js
isActive: { type: Boolean, default: true }
```

---

## 4. `unique`

Ensures uniqueness (creates index)

```js
email: { type: String, unique: true }
```

⚠️ Not a validator — it's a DB index.

---

## 5. `trim`

Removes whitespace

```js
name: { type: String, trim: true }
```

---

## 6. `lowercase / uppercase`

```js
email: { type: String, lowercase: true }
```

---

## 7. `min / max` (Numbers)

```js
age: { type: Number, min: 0, max: 100 }
```

---

## 8. `minlength / maxlength` (Strings)

```js
name: { minlength: 3, maxlength: 50 }
```

---

## 9. `enum`

Restricts values

```js
role: {
  type: String,
  enum: ["user", "admin"]
}
```

---

## 10. `match` (Regex validation)

```js
email: {
  type: String,
  match: /.+\@.+\..+/
}
```

---

## 11. `select`

Hide field by default

```js
password: {
  type: String,
  select: false
}
```

---

## 12. `validate` (Custom validation)

```js
age: {
  type: Number,
  validate: {
    validator: v => v >= 18,
    message: "Must be adult"
  }
}
```

---

# 🔷 Schema Types (Important)

```js
String
Number
Boolean
Date
Array
Object
ObjectId
Buffer
Mixed
Decimal128
Map
```

---

# 🔷 Arrays

```js
tags: [String]

comments: [{
  text: String,
  date: Date
}]
```

---

# 🔷 Nested Objects

```js
address: {
  city: String,
  zip: Number
}
```

---

# 🔷 ObjectId (Reference / Relationship)

```js
user: {
  type: mongoose.Schema.Types.ObjectId,
  ref: "User"
}
```

👉 Used for **relations (like foreign key)**

---

# 🔷 Schema Options (Second Argument)

```js
new mongoose.Schema({...}, options)
```

## Common options:

### 1. `timestamps`

```js
{ timestamps: true }
```

Adds:

* `createdAt`
* `updatedAt`

---

### 2. `versionKey`

```js
{ versionKey: false }
```

Removes `__v`

---

### 3. `collection`

```js
{ collection: "users" }
```

Force collection name

---

# 🔷 Indexes

```js
userSchema.index({ email: 1 });
```

Compound index:

```js
userSchema.index({ email: 1, age: -1 });
```

---

# 🔷 Virtuals (Computed Fields)

```js
userSchema.virtual("fullName").get(function () {
  return this.firstName + " " + this.lastName;
});
```

---

# 🔷 Middleware (Hooks)

## Pre-save

```js
userSchema.pre("save", function(next) {
  console.log("Before saving");
  next();
});
```

## Post-save

```js
userSchema.post("save", function(doc) {
  console.log("Saved:", doc);
});
```

---

# 🔷 Methods

## Instance method

```js
userSchema.methods.sayHello = function() {
  return "Hello " + this.name;
};
```

---

## Static method

```js
userSchema.statics.findByEmail = function(email) {
  return this.findOne({ email });
};
```

---

# 🔷 Model Creation

```js
const User = mongoose.model("User", userSchema);
```

👉 `"User"` → collection becomes `users`

---

# 🔷 Real-World Example (Library System)

```js
const bookSchema = new mongoose.Schema({
  title: { type: String, required: true },
  author: String,
  price: Number,
  available: { type: Boolean, default: true },
  borrower: {
    type: mongoose.Schema.Types.ObjectId,
    ref: "User"
  }
}, { timestamps: true });
```

---

# 🔥 Pro Tips (Important)

* Always use validation (`required`, `enum`)
* Use `select: false` for sensitive fields
* Use `timestamps: true`
* Use references for relationships
* Keep schema clean and scalable

---

# 🚀 If you want next:

I can:

* Build a **complete Library Management System (CRUD + Schema)**
* Teach **advanced schema (discriminators, plugins)**
* Give **interview questions on Mongoose**

Just tell me 👍
