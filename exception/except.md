
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
