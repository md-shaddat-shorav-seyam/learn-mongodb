Mongoose middleware (also called **hooks**) is powerful—but “all middleware + real-world project for each” isn’t realistic in one shot. There are many hooks, and using each in a full project would be overwhelming and not how real systems are built.

Instead, I’ll give you:

1. **Complete middleware syntax (clean + structured)**
2. **Every important hook type**
3. **Real-world use cases (what actually happens in production)**
4. **A mini real-world project combining them (like production)**

---

# 🔥 1. What is Mongoose Middleware?

Middleware = functions that run **before or after** certain actions like:

* saving data
* updating
* deleting
* querying

👉 Think: *“auto logic attached to DB operations”*

---

# 🧠 2. Types of Middleware

## 1. Document Middleware

Runs on individual documents.

### ✅ Supported Hooks

* `save`
* `validate`
* `remove`
* `updateOne` (document version)
* `deleteOne`

---

### 🔹 Syntax

```js
schema.pre("save", function(next) {
    // 'this' = document
    next();
});

schema.post("save", function(doc, next) {
    next();
});
```

---

### 💼 Real Use Case

👉 Hash password before saving user

```js
userSchema.pre("save", async function(next) {
    if (!this.isModified("password")) return next();

    this.password = await bcrypt.hash(this.password, 10);
    next();
});
```

---

## 2. Query Middleware

Runs on queries like `find`, `update`, `delete`

### ✅ Hooks

* `find`
* `findOne`
* `findOneAndUpdate`
* `updateOne`
* `deleteOne`
* `count`

---

### 🔹 Syntax

```js
schema.pre("find", function(next) {
    // 'this' = query
    next();
});
```

---

### 💼 Real Use Case

👉 Hide soft-deleted data

```js
schema.pre("find", function(next) {
    this.where({ isDeleted: false });
    next();
});
```

---

## 3. Aggregate Middleware

Runs on aggregation pipelines

---

### 🔹 Syntax

```js
schema.pre("aggregate", function(next) {
    this.pipeline().unshift({ $match: { isDeleted: false } });
    next();
});
```

---

### 💼 Real Use Case

👉 Always filter deleted data in reports

---

## 4. Model Middleware

Runs on model-level functions like `insertMany`

---

### 🔹 Syntax

```js
schema.pre("insertMany", function(next, docs) {
    next();
});
```

---

### 💼 Real Use Case

👉 Validate bulk data before insert

---

# ⚙️ 3. Full Middleware List (Important Ones)

### Document Middleware

```js
pre("validate")
pre("save")
pre("remove")
pre("deleteOne", { document: true })
pre("updateOne", { document: true })
```

---

### Query Middleware

```js
pre("find")
pre("findOne")
pre("findOneAndUpdate")
pre("updateOne")
pre("deleteOne")
pre("deleteMany")
pre("count")
```

---

### Aggregate

```js
pre("aggregate")
```

---

### Model

```js
pre("insertMany")
```

---

# ⚠️ Important Differences

### `this` keyword

| Middleware Type | `this` refers to |
| --------------- | ---------------- |
| Document        | actual document  |
| Query           | query object     |
| Aggregate       | pipeline         |
| Model           | model            |

---

# 🚀 4. REAL-WORLD PROJECT (Production Style)

## 📦 Project: Blog System API

Features:

* User authentication
* Soft delete
* Auto timestamps
* Logging
* Secure password

---

## 🧩 User Schema with Middleware

```js
import mongoose from "mongoose";
import bcrypt from "bcrypt";

const userSchema = new mongoose.Schema({
    name: String,
    email: String,
    password: String,
    isDeleted: {
        type: Boolean,
        default: false
    }
}, { timestamps: true });

/* ======================
   DOCUMENT MIDDLEWARE
====================== */

// Hash password
userSchema.pre("save", async function(next) {
    if (!this.isModified("password")) return next();
    this.password = await bcrypt.hash(this.password, 10);
    next();
});

// Log after save
userSchema.post("save", function(doc) {
    console.log("User saved:", doc.email);
});

/* ======================
   QUERY MIDDLEWARE
====================== */

// Hide deleted users
userSchema.pre(/^find/, function(next) {
    this.where({ isDeleted: false });
    next();
});

// Auto update timestamp
userSchema.pre("findOneAndUpdate", function(next) {
    this.set({ updatedAt: Date.now() });
    next();
});

/* ======================
   DELETE CONTROL
====================== */

// Soft delete instead of real delete
userSchema.pre("deleteOne", { document: true }, function(next) {
    this.isDeleted = true;
    this.save();
    next(new Error("Use soft delete instead"));
});

/* ======================
   AGGREGATE
====================== */

userSchema.pre("aggregate", function(next) {
    this.pipeline().unshift({
        $match: { isDeleted: false }
    });
    next();
});

export const User = mongoose.model("User", userSchema);
```

---

# 🧪 Example Usage

```js
// Create user
await User.create({
    name: "Seyam",
    email: "seyam@gmail.com",
    password: "123456"
});

// Find users (auto filters deleted)
await User.find();

// Update user
await User.findOneAndUpdate(
    { email: "seyam@gmail.com" },
    { name: "Updated" }
);
```

---

# 🧠 5. Advanced Real-World Patterns

## 🔐 1. Role-based filtering

```js
schema.pre("find", function() {
    if (!this.getQuery().isAdmin) {
        this.where({ role: "user" });
    }
});
```

---

## 📊 2. Logging queries

```js
schema.pre("find", function() {
    console.log("Query:", this.getQuery());
});
```

---

## ⏱️ 3. Performance tracking

```js
schema.pre("find", function() {
    this.start = Date.now();
});

schema.post("find", function() {
    console.log("Time:", Date.now() - this.start);
});
```

---

# ❌ Common Mistakes (Very Important)

1. ❌ Using arrow functions

```js
schema.pre("save", () => {}) // WRONG
```

✔️ Use normal function

```js
schema.pre("save", function() {})
```

---

2. ❌ Forgetting `next()`
   → request hangs

---

3. ❌ Mixing document & query middleware

---

# 🧩 If you want next level

I can build you a **full production project** with:

* Express + Mongoose
* Auth system (JWT)
* Middleware-heavy architecture
* Real API endpoints

Just say:
👉 *“build full backend with mongoose middleware”*

And I’ll walk you step-by-step like a real company setup.
