# MongoDB Aggregation Pipeline — Full Tutorial

MongoDB Aggregation Pipeline is used to **process data step-by-step**, similar to SQL:

```sql
SELECT department, AVG(salary)
FROM employees
GROUP BY department
```

In MongoDB, this becomes an aggregation pipeline.

---

# 1. What is Aggregation Pipeline?

Aggregation works like a **data processing pipeline**.

Documents pass through multiple stages:

```text
Collection
   ↓
$match
   ↓
$group
   ↓
$sort
   ↓
Result
```

Each stage transforms the documents.

---

# 2. Basic Syntax

```js
db.collection.aggregate([
   { stage1 },
   { stage2 },
   { stage3 }
])
```

Example:

```js
db.students.aggregate([
   { $match: { age: { $gt: 18 } } },
   { $sort: { marks: -1 } }
])
```

---

# 3. Sample Data

We will use this collection:

```js
db.students.insertMany([
  {
    name: "Shorav",
    age: 20,
    department: "CSE",
    marks: 85,
    city: "Dhaka"
  },
  {
    name: "Rahim",
    age: 22,
    department: "EEE",
    marks: 75,
    city: "Khulna"
  },
  {
    name: "Karim",
    age: 21,
    department: "CSE",
    marks: 90,
    city: "Dhaka"
  }
])
```

---

# 4. Important Pipeline Stages

---

# `$match`

Works like SQL `WHERE`.

## Syntax

```js
{
   $match: {
      condition
   }
}
```

## Example

Students from CSE:

```js
db.students.aggregate([
   {
      $match: {
         department: "CSE"
      }
   }
])
```

---

# `$project`

Used to:

* show fields
* hide fields
* rename fields
* create new fields

## Syntax

```js
{
   $project: {
      field: 1,
      field2: 0
   }
}
```

---

## Include fields

```js
db.students.aggregate([
   {
      $project: {
         name: 1,
         marks: 1
      }
   }
])
```

Output:

```js
{
   name: "Shorav",
   marks: 85
}
```

---

## Exclude `_id`

```js
{
   $project: {
      _id: 0,
      name: 1
   }
}
```

---

## Rename field

```js
{
   $project: {
      studentName: "$name"
   }
}
```

---

## Create new field

```js
{
   $project: {
      name: 1,
      result: {
         $cond: {
            if: { $gte: ["$marks", 80] },
            then: "Pass",
            else: "Fail"
         }
      }
   }
}
```

---

# `$group`

Most important stage.

Used for:

* count
* average
* sum
* min/max
* grouping

---

## Syntax

```js
{
   $group: {
      _id: "$field",
      newField: { operator }
   }
}
```

---

## Count students per department

```js
db.students.aggregate([
   {
      $group: {
         _id: "$department",
         totalStudents: { $sum: 1 }
      }
   }
])
```

---

## Average marks

```js
db.students.aggregate([
   {
      $group: {
         _id: "$department",
         averageMarks: { $avg: "$marks" }
      }
   }
])
```

---

## Common Operators

| Operator | Meaning                |
| -------- | ---------------------- |
| `$sum`   | total                  |
| `$avg`   | average                |
| `$max`   | maximum                |
| `$min`   | minimum                |
| `$push`  | push values into array |
| `$first` | first document         |
| `$last`  | last document          |

---

## Example with `$push`

```js
db.students.aggregate([
   {
      $group: {
         _id: "$department",
         students: { $push: "$name" }
      }
   }
])
```

Output:

```js
{
   _id: "CSE",
   students: ["Shorav", "Karim"]
}
```

---

# `$sort`

Sort ascending or descending.

## Syntax

```js
{
   $sort: {
      field: 1
   }
}
```

| Value | Meaning    |
| ----- | ---------- |
| `1`   | ascending  |
| `-1`  | descending |

---

## Example

```js
db.students.aggregate([
   {
      $sort: {
         marks: -1
      }
   }
])
```

---

# `$limit`

Limit number of documents.

```js
{
   $limit: 2
}
```

---

# `$skip`

Skip documents.

```js
{
   $skip: 5
}
```

Useful for pagination.

---

# `$unwind`

Used with arrays.

Converts array items into separate documents.

---

## Example Data

```js
{
   name: "Shorav",
   skills: ["JS", "React", "MongoDB"]
}
```

---

## Example

```js
db.students.aggregate([
   {
      $unwind: "$skills"
   }
])
```

Output:

```js
{ name: "Shorav", skills: "JS" }
{ name: "Shorav", skills: "React" }
{ name: "Shorav", skills: "MongoDB" }
```

---

# `$lookup`

Used for JOIN.

Equivalent to SQL JOIN.

---

# Example Collections

## students

```js
{
   _id: 1,
   name: "Shorav",
   departmentId: 101
}
```

## departments

```js
{
   _id: 101,
   deptName: "CSE"
}
```

---

## Syntax

```js
{
   $lookup: {
      from: "departments",
      localField: "departmentId",
      foreignField: "_id",
      as: "departmentInfo"
   }
}
```

---

## Full Example

```js
db.students.aggregate([
   {
      $lookup: {
         from: "departments",
         localField: "departmentId",
         foreignField: "_id",
         as: "departmentInfo"
      }
   }
])
```

---

# `$addFields`

Add new fields.

```js
{
   $addFields: {
      status: "Active"
   }
}
```

---

# `$count`

Count documents.

```js
db.students.aggregate([
   {
      $count: "totalStudents"
   }
])
```

---

# `$facet`

Run multiple pipelines together.

---

## Example

```js
db.students.aggregate([
   {
      $facet: {
         highMarks: [
            { $match: { marks: { $gte: 80 } } }
         ],
         lowMarks: [
            { $match: { marks: { $lt: 80 } } }
         ]
      }
   }
])
```

---

# `$bucket`

Group data into ranges.

---

## Example

```js
db.students.aggregate([
   {
      $bucket: {
         groupBy: "$marks",
         boundaries: [0, 50, 80, 100],
         default: "Other",
         output: {
            count: { $sum: 1 }
         }
      }
   }
])
```

---

# 5. Expressions

Aggregation uses expressions heavily.

---

# Comparison Operators

| Operator | Meaning       |
| -------- | ------------- |
| `$eq`    | equal         |
| `$gt`    | greater than  |
| `$gte`   | greater/equal |
| `$lt`    | less than     |
| `$lte`   | less/equal    |
| `$ne`    | not equal     |

---

# Arithmetic Operators

| Operator    | Meaning        |
| ----------- | -------------- |
| `$add`      | addition       |
| `$subtract` | subtraction    |
| `$multiply` | multiplication |
| `$divide`   | division       |

---

## Example

```js
{
   $project: {
      total: {
         $multiply: ["$price", "$quantity"]
      }
   }
}
```

---

# String Operators

| Operator   | Meaning      |
| ---------- | ------------ |
| `$concat`  | join strings |
| `$toUpper` | uppercase    |
| `$toLower` | lowercase    |
| `$substr`  | substring    |

---

## Example

```js
{
   $project: {
      fullName: {
         $concat: ["$firstName", " ", "$lastName"]
      }
   }
}
```

---

# Array Operators

| Operator       | Meaning     |
| -------------- | ----------- |
| `$size`        | array size  |
| `$in`          | check value |
| `$arrayElemAt` | get index   |

---

# Date Operators

| Operator      | Meaning       |
| ------------- | ------------- |
| `$year`       | extract year  |
| `$month`      | extract month |
| `$dayOfMonth` | extract day   |

---

# 6. Complex Real Examples

---

# Example 1 — Top Department

```js
db.students.aggregate([
   {
      $group: {
         _id: "$department",
         avgMarks: { $avg: "$marks" }
      }
   },
   {
      $sort: {
         avgMarks: -1
      }
   },
   {
      $limit: 1
   }
])
```

---

# Example 2 — Pagination

```js
db.students.aggregate([
   {
      $sort: {
         marks: -1
      }
   },
   {
      $skip: 0
   },
   {
      $limit: 10
   }
])
```

---

# Example 3 — Search + Group

```js
db.students.aggregate([
   {
      $match: {
         city: "Dhaka"
      }
   },
   {
      $group: {
         _id: "$department",
         total: { $sum: 1 }
      }
   }
])
```

---

# 7. Aggregation in Mongoose

Using Mongoose:

```js
const result = await Student.aggregate([
   {
      $match: {
         department: "CSE"
      }
   },
   {
      $group: {
         _id: "$department",
         avgMarks: {
            $avg: "$marks"
         }
      }
   }
])
```

---

# 8. Performance Tips

---

## Use `$match` Early

GOOD:

```js
[
   { $match: { department: "CSE" } },
   { $group: { ... } }
]
```

BAD:

```js
[
   { $group: { ... } },
   { $match: { ... } }
]
```

---

## Use Indexes

Index fields used in:

* `$match`
* `$sort`

Example:

```js
db.students.createIndex({ department: 1 })
```

---

# 9. Most Common Interview Questions

---

# Difference Between `find()` and `aggregate()`

| find         | aggregate           |
| ------------ | ------------------- |
| simple query | advanced processing |
| cannot group | can group           |
| faster       | more powerful       |

---

# Difference Between `$project` and `$addFields`

| `$project`       | `$addFields`     |
| ---------------- | ---------------- |
| reshape document | add fields only  |
| can hide fields  | keeps old fields |

---

# 10. Advanced Stages

| Stage              | Purpose           |
| ------------------ | ----------------- |
| `$graphLookup`     | recursive lookup  |
| `$setWindowFields` | window functions  |
| `$merge`           | save output       |
| `$out`             | export collection |
| `$replaceRoot`     | replace document  |
| `$unionWith`       | union collections |

---

# 11. Best Learning Order

Learn in this order:

1. `$match`
2. `$project`
3. `$sort`
4. `$limit`
5. `$group`
6. `$unwind`
7. `$lookup`
8. expressions/operators
9. advanced stages

---

# 12. Practice Problems

Try solving:

1. Count students per city
2. Find highest marks per department
3. Join users with orders
4. Calculate total sales
5. Paginate products
6. Filter nested arrays
7. Average monthly revenue

---

# Official Documentation

* [MongoDB Aggregation Pipeline Docs](https://www.mongodb.com/docs/manual/core/aggregation-pipeline/?utm_source=chatgpt.com)
* [MongoDB Aggregation Operators](https://www.mongodb.com/docs/manual/reference/operator/aggregation/?utm_source=chatgpt.com)
* [Mongoose Aggregate API](https://mongoosejs.com/docs/api/aggregate.html?utm_source=chatgpt.com)
