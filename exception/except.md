
# 1 timeStamp 
### timestamp is common practise 

In **Mongoose**, timestamps are a built-in feature that automatically adds and manages `createdAt` and `updatedAt` fields in your documents.

---

## 🔹 1. Enable Timestamps in Schema

You just need to set `timestamps: true` in your schema:

```js
const mongoose = require('mongoose');

const userSchema = new mongoose.Schema({
  name: String,
  email: String
}, {
  timestamps: true
});

const User = mongoose.model('User', userSchema);
```

---

## 🔹 2. What Happens Automatically?

Mongoose will add two fields:

```json
{
  "name": "John",
  "email": "john@example.com",
  "createdAt": "2026-04-26T10:00:00.000Z",
  "updatedAt": "2026-04-26T10:00:00.000Z"
}
```

### ✅ Behavior:

* `createdAt` → set once when document is created
* `updatedAt` → updated every time you modify the document

---

## 🔹 3. Custom Timestamp Field Names

You can rename them:

```js
const userSchema = new mongoose.Schema({
  name: String
}, {
  timestamps: {
    createdAt: 'created_on',
    updatedAt: 'updated_on'
  }
});
```

---

## 🔹 4. Disable One Field

```js
timestamps: {
  createdAt: true,
  updatedAt: false
}
```

---

## 🔹 5. Using with Updates

Mongoose automatically updates `updatedAt` for:

```js
await User.updateOne({ name: "John" }, { name: "Doe" });
await User.findByIdAndUpdate(id, { name: "New Name" });
```

---

## 🔹 6. Important Notes

* Works only if `timestamps: true` is enabled
* Uses ISO date format (`Date` object)
* You don’t need to manually set timestamps

---

## 🔹 7. Real-World Use Cases

* Track when a user registered
* Show "last updated" time in UI
* Sort documents by recent activity:

```js
User.find().sort({ createdAt: -1 });
```

---

If you want, I can show you:
👉 how to format timestamps in frontend
👉 or how to use them in aggregation queries (very useful for analytics)


++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++==
# 2 dataBase name saves at lowercase and plural form

export const Todo = mongoose.model("Todo",todoSchema)


When you write:

```js
export const Todo = mongoose.model("Todo", todoSchema)
```

it looks simple, but there’s an important hidden behavior in **Mongoose** about **model names vs collection names**.

---

# 🔥 Core Concept

### 1. Model Name ≠ Collection Name

* `"Todo"` → **Model name (Singular, PascalCase)**
* MongoDB will automatically convert it to:

👉 `"todos"` → **Collection name (lowercase + plural)**

---

# ⚙️ How Mongoose Converts It

Mongoose applies **pluralization + lowercase rule**:

| Model Name | Collection Name |
| ---------- | --------------- |
| `"Todo"`   | `"todos"`       |
| `"User"`   | `"users"`       |
| `"Person"` | `"people"`      |

👉 It uses an internal pluralization library.

---

# 🧠 Why lowercase?

Because **MongoDB** convention is:

* Collection names are **lowercase**
* Usually **plural**

So Mongoose enforces this automatically.

---

# 🧪 Example (Real Use Case)

```js
const todoSchema = new mongoose.Schema({
  title: String,
  completed: Boolean
})

export const Todo = mongoose.model("Todo", todoSchema)
```

Now when you insert:

```js
await Todo.create({ title: "Learn Mongoose", completed: false })
```

👉 Data is saved in:

```
todos  (NOT Todo)
```

---

# 🛑 Important Interview Point

👉 **Model name is for your code**
👉 **Collection name is for the database**

---

# 🧩 How to Control Collection Name (IMPORTANT)

If you want to **stop automatic lowercase + plural**, you can define it manually:

```js
export const Todo = mongoose.model("Todo", todoSchema, "Todo")
```

Now collection will be:

```
Todo  (exact name, no change)
```

---

# 🧠 Behind the Scenes Flow

```
"Todo" (Model)
   ↓
Lowercase → "todo"
   ↓
Plural → "todos"
   ↓
MongoDB collection
```

---

# 🚀 Best Practice

✔ Use **PascalCase singular** for models
✔ Let Mongoose handle collection naming

```js
User → users  
Order → orders  
Product → products
```

---

# ⚠️ Common Mistake

```js
mongoose.model("todos", schema)
```

❌ Wrong (already plural + lowercase)
👉 Leads to weird names like `"todoss"`

---

# 🎯 Summary

* `"Todo"` → model name
* `"todos"` → actual MongoDB collection
* Mongoose **automatically lowercase + pluralize**
* You can override it with the 3rd parameter

---

If you want, I can show you:

* 🔥 Full CRUD project using Todo (real-world API)
* 🔥 How Mongoose connects with Express + MongoDB step by step


