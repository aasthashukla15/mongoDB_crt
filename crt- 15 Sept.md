# FACE2 --- MongoDB CRUD Practice Log

**Date:** 15 September 2026\
**Database:** `PCEA24CY001`\
**Collection:** `products`\
**Environment:** MongoDB Atlas + `mongosh`

## 1. Product Data Creation

Created product documents using a JavaScript `for` loop that inserts 50
products into `db.products`.

``` javascript
for (let i = 1; i <= 50; i++) {
    db.products.insertOne({
        productId: i,
        productName: "Product " + i,
        category:
            i % 5 === 0 ? "Laptop" :
            i % 5 === 1 ? "Mobile" :
            i % 5 === 2 ? "Headphones" :
            i % 5 === 3 ? "Keyboard" :
                          "Mouse",

        brand:
            i % 3 === 0 ? "Dell" :
            i % 3 === 1 ? "Samsung" :
                          "HP",

        price: 1000 + (i * 500),
        stock: 10 + i,
        rating: 3 + ((i % 3) * 0.5),
        inStock: i % 4 !== 0,

        tags: [
            "electronics",
            i % 2 === 0 ? "featured" : "new"
        ],

        seller: {
            sellerId: 1000 + i,
            sellerName: "Seller " + i
        },

        createdAt: new Date()
    });
}
```

## 2. Updating Product 20 Price

Used `updateMany()` to change the price of all documents having
`productId: 20`.

``` javascript
db.products.updateMany(
    { productId: 20 },
    { $set: { price: 17000 } }
)
```

Result:

``` text
acknowledged: true
matchedCount: 2
modifiedCount: 2
```

This showed that two documents had `productId: 20`.

## 3. Updating Product 20 Seller Name

### First attempt --- incorrect

``` javascript
db.products.upadteOne(
    { productId: 20 },
    { $set: { "seller,sellername": "Kartikey store" } }
)
```

Error:

``` text
TypeError: db.products.upadteOne is not a function
```

Problems: - `upadteOne` was misspelled. Correct spelling is
`updateOne`. - Nested field syntax was incorrect. A dot (`.`), not a
comma (`,`), is used between nested field names.

### Correct command

``` javascript
db.products.updateOne(
    { productId: 20 },
    { $set: { "seller.sellerName": "Kartikey Store" } }
)
```

Result:

``` text
acknowledged: true
matchedCount: 1
modifiedCount: 1
```

Verification:

``` javascript
db.products.find({ productId: 20 })
```

The first Product 20 document now contains:

``` javascript
seller: {
    sellerId: 1020,
    sellerName: "Kartikey Store"
}
```

The second Product 20 document still contains:

``` javascript
seller: {
    sellerId: 1020,
    sellerName: "Seller 20"
}
```

This demonstrates the difference between `updateOne()` and
`updateMany()`.

## 4. Increasing Product 20 Stock

Used `$inc` to increase the stock of one Product 20 document by 20.

``` javascript
db.products.updateOne(
    { productId: 20 },
    { $inc: { stock: 20 } }
)
```

Result:

``` text
acknowledged: true
matchedCount: 1
modifiedCount: 1
```

The selected Product 20 document changed from:

``` text
stock: 30
```

to:

``` text
stock: 50
```

## 5. Checking Product 20

Correct command:

``` javascript
db.products.find({ productId: 20 })
```

The final visible state included:

``` javascript
{
    productId: 20,
    productName: "Product 20",
    category: "Laptop",
    brand: "HP",
    price: 17000,
    stock: 50,
    inStock: false,
    tags: ["electronics", "featured"],
    seller: {
        sellerId: 1020,
        sellerName: "Kartikey Store"
    }
}
```

A second document with the same `productId: 20` remained with:

``` text
stock: 30
sellerName: "Seller 20"
```

## 6. Removing the Rating Field

### First attempt --- incorrect

``` javascript
db.products.updateOne(
    { productId: 20 },
    { $unset: Prating: "" }
})
```

This produced a syntax error because the `$unset` object was not written
correctly.

### Correct command

``` javascript
db.products.updateOne(
    { productId: 20 },
    { $unset: { rating: "" } }
)
```

Result:

``` text
acknowledged: true
matchedCount: 1
modifiedCount: 1
```

The `rating` field was removed from the first matching Product 20
document.

## 7. Another Incorrect Find Command

Incorrect:

``` javascript
db.products.find({ productId: 20 })
```

The attempted command in the session accidentally contained an extra
closing `}`:

``` javascript
db.products.find({ productId: 20 }})
```

This caused:

``` text
SyntaxError: Unexpected token
```

Correct command:

``` javascript
db.products.find({ productId: 20 })
```

## 8. Incorrect Direct Database Syntax

An incorrect command was also attempted:

``` javascript
db.productId : 20 })
```

This is not a valid MongoDB query.

Correct way to search the `products` collection:

``` javascript
db.products.find({ productId: 20 })
```

## 9. MongoDB Operators Practiced

### `$set`

Used to create or modify a field.

``` javascript
db.products.updateOne(
    { productId: 20 },
    { $set: { price: 17000 } }
)
```

### `$inc`

Used to increase or decrease a numeric field.

``` javascript
db.products.updateOne(
    { productId: 20 },
    { $inc: { stock: 20 } }
)
```

### `$unset`

Used to remove a field.

``` javascript
db.products.updateOne(
    { productId: 20 },
    { $unset: { rating: "" } }
)
```

### Nested field update

Used dot notation:

``` javascript
"seller.sellerName"
```

Example:

``` javascript
db.products.updateOne(
    { productId: 20 },
    { $set: { "seller.sellerName": "Kartikey Store" } }
)
```

## 10. Important Lessons Learned

1.  MongoDB method names must be spelled correctly:
    -   `updateOne()` --- correct
    -   `upadteOne()` --- incorrect
2.  Nested fields use dot notation:
    -   Correct: `"seller.sellerName"`
    -   Incorrect: `"seller,sellername"`
3.  `$set` needs a field and a value:

``` javascript
{ $set: { field: value } }
```

4.  `$inc` changes numeric values:

``` javascript
{ $inc: { stock: 20 } }
```

5.  `$unset` removes a field:

``` javascript
{ $unset: { rating: "" } }
```

6.  `updateOne()` updates only the first matching document.

7.  `updateMany()` updates all matching documents.

8.  Always verify an update using `find()` or `findOne()`.

## 11. Useful Verification Commands

Count documents:

``` javascript
db.products.countDocuments()
```

Find Product 20:

``` javascript
db.products.find({ productId: 20 })
```

Find one Product 20:

``` javascript
db.products.findOne({ productId: 20 })
```

Show all products:

``` javascript
db.products.find()
```

## 12. Today's Practical Summary

Today's MongoDB practical work covered:

-   Creating product documents
-   Working with MongoDB Atlas
-   Using `mongosh`
-   `find()`
-   `findOne()`
-   `updateOne()`
-   `updateMany()`
-   `$set`
-   `$inc`
-   `$unset`
-   Nested document fields
-   Dot notation
-   Checking update results
-   Understanding syntax errors
-   Understanding `matchedCount` and `modifiedCount`







### Read / Find

``` javascript
db.products.find({ category: "Laptop" })
```

``` javascript
db.products.find({ inStock: true })
```

``` javascript
db.products.find({ price: { $gt: 20000 } })
```

``` javascript
db.products.find({ price: { $lt: 10000 } })
```

``` javascript
db.products.find({
    price: { $gte: 10000, $lte: 30000 }
})
```

``` javascript
db.products.find({
    "seller.sellerName": "Seller 10"
})
```

### Update One

``` javascript
db.products.updateOne(
    { productId: 1 },
    { $set: { price: 10000 } }
)
```

``` javascript
db.products.updateOne(
    { productId: 2 },
    { $set: { price: 15000, stock: 100 } }
)
```

``` javascript
db.products.updateOne(
    { productId: 3 },
    { $set: { "seller.sellerName": "Premium Seller" } }
)
```

``` javascript
db.products.updateOne(
    { productId: 4 },
    { $push: { tags: "sale" } }
)
```

``` javascript
db.products.updateOne(
    { productId: 5 },
    { $addToSet: { tags: "electronics" } }
)
```

``` javascript
db.products.updateOne(
    { productId: 6 },
    { $inc: { stock: 20 } }
)
```

``` javascript
db.products.updateOne(
    { productId: 6 },
    { $inc: { stock: -5 } }
)
```

### Update Many

``` javascript
db.products.updateMany(
    { category: "Laptop" },
    { $set: { inStock: true } }
)
```

``` javascript
db.products.updateMany(
    { brand: "Samsung" },
    { $inc: { price: 1000 } }
)
```

### Multiple Operators

``` javascript
db.products.updateOne(
    { productId: 10 },
    {
        $set: { price: 25000, inStock: true },
        $inc: { stock: 10 },
        $push: { tags: "sale" }
    }
)
```

## Part 2 --- Product 20 Practical Work Actually Performed

### Incorrect method name

``` javascript
db.products.upadteOne(...)
```

Error:

``` text
TypeError: db.products.upadteOne is not a function
```

Correct method:

``` javascript
updateOne()
```

### Correct nested seller update

``` javascript
db.products.updateOne(
    { productId: 20 },
    { $set: { "seller.sellerName": "Aditya Store" } }
)
```

Result:

``` text
matchedCount: 1
modifiedCount: 1
```

### Product 20 price update

``` javascript
db.products.updateMany(
    { productId: 20 },
    { $set: { price: 17000 } }
)
```

Result:

``` text
matchedCount: 2
modifiedCount: 2
```

### Increase Product 20 stock

``` javascript
db.products.updateOne(
    { productId: 20 },
    { $inc: { stock: 20 } }
)
```

The selected Product 20 stock changed from 30 to 50.

### Remove rating from Product 20

Correct command:

``` javascript
db.products.updateOne(
    { productId: 20 },
    { $unset: { rating: "" } }
)
```

### Verify Product 20

``` javascript
db.products.find({ productId: 20 })
```

The first Product 20 document showed price `17000`, stock `50`, seller
name `Aditya Store`, and no `rating` field. The second Product 20
document remained with stock `30`, seller name `Seller 20`, and rating
`4`.

### Syntax mistakes corrected

Incorrect:

``` javascript
db.products.find({ productId: 20 }})
```

Correct:

``` javascript
db.products.find({ productId: 20 })
```

Incorrect:

``` javascript
db.productId : 20 })
```

Correct:

``` javascript
db.products.find({ productId: 20 })
```

## Part 3 --- Delete Operations

``` javascript
db.products.deleteOne({ productId: 10 })
```

``` javascript
db.products.deleteMany({ category: "Laptop" })
```

``` javascript
db.products.deleteMany({ price: { $gt: 30000 } })
```

``` javascript
db.products.deleteMany({})
```

`deleteMany({})` deletes all documents but keeps the collection.

``` javascript
db.products.drop()
```

`drop()` deletes the entire collection.

------------------------------------------------------------------------

# Part 4 --- Students Dataset

The additional MongoDB practical uses a `students` collection with
fields including:

``` text
rollNo
name
age
department
marks
skills
address
scholarship
```

## Logical Operators

Operators:

``` text
$and
$or
$not
$nor
```

### CSE AND marks greater than 85

``` javascript
db.students.find({
    $and: [
        { department: "CSE" },
        { marks: { $gt: 85 } }
    ]
})
```

### CSE OR IT

``` javascript
db.students.find({
    $or: [
        { department: "CSE" },
        { department: "IT" }
    ]
})
```

### Marks NOT greater than 80

``` javascript
db.students.find({
    marks: {
        $not: { $gt: 80 }
    }
})
```

### Neither CSE nor IT

``` javascript
db.students.find({
    $nor: [
        { department: "CSE" },
        { department: "IT" }
    ]
})
```

## Comparison Operators

  Operator   Meaning
  ---------- -----------------------
  `$eq`      Equal To
  `$gt`      Greater Than
  `$lt`      Less Than
  `$gte`     Greater Than or Equal
  `$lte`     Less Than or Equal
  `$ne`      Not Equal

### Department exactly CSE

``` javascript
db.students.find({
    department: { $eq: "CSE" }
})
```

### Marks greater than 80

``` javascript
db.students.find({
    marks: { $gt: 80 }
})
```

### Marks less than 70

``` javascript
db.students.find({
    marks: { $lt: 70 }
})
```

### Marks greater than or equal to 85

``` javascript
db.students.find({
    marks: { $gte: 85 }
})
```

### Marks less than or equal to 70

``` javascript
db.students.find({
    marks: { $lte: 70 }
})
```

### NOT from CSE

``` javascript
db.students.find({
    department: { $ne: "CSE" }
})
```

## Array Operators

Operators:

``` text
$in
$nin
$all
$elemMatch
$size
```

### Department CSE or IT

``` javascript
db.students.find({
    department: { $in: ["CSE", "IT"] }
})
```

### Department neither CSE nor IT

``` javascript
db.students.find({
    department: { $nin: ["CSE", "IT"] }
})
```

### BOTH Python and MongoDB skills

``` javascript
db.students.find({
    skills: { $all: ["Python", "MongoDB"] }
})
```

### Add subjects to Student 101

``` javascript
db.students.updateOne(
    { rollNo: 101 },
    {
        $set: {
            subjects: [
                { name: "Java", marks: 90 },
                { name: "MongoDB", marks: 88 },
                { name: "Python", marks: 95 }
            ]
        }
    }
)
```

### MongoDB subject with marks greater than 85

``` javascript
db.students.find({
    subjects: {
        $elemMatch: {
            name: "MongoDB",
            marks: { $gt: 85 }
        }
    }
})
```

### Exactly 3 skills

``` javascript
db.students.find({
    skills: { $size: 3 }
})
```

## Projection Operators

Projection means selecting which fields are displayed.

### Name, department and marks using `$project`

``` javascript
db.students.aggregate([
    {
        $project: {
            _id: 0,
            name: 1,
            department: 1,
            marks: 1
        }
    }
])
```

### Only name and marks

``` javascript
db.students.find(
    {},
    {
        _id: 0,
        name: 1,
        marks: 1
    }
)
```

### Everything except address

``` javascript
db.students.find(
    {},
    {
        address: 0
    }
)
```

### First two skills

``` javascript
db.students.find(
    {},
    {
        name: 1,
        skills: { $slice: 2 }
    }
)
```

## Element Operators

Operators:

``` text
$exists
$type
$regex
```

### Scholarship field exists

``` javascript
db.students.find({
    scholarship: { $exists: true }
})
```

### Scholarship field does not exist

``` javascript
db.students.find({
    scholarship: { $exists: false }
})
```

### Marks stored as integer

``` javascript
db.students.find({
    marks: { $type: "int" }
})
```

### Names starting with A

``` javascript
db.students.find({
    name: { $regex: "^A" }
})
```

------------------------------------------------------------------------

# Part 5 --- Quick Operator Summary

### Update

``` text
$set       → Add or modify a field
$inc       → Increase/decrease a number
$unset     → Remove a field
$push      → Add an array element
$addToSet  → Add an array element without duplicates
```

### Logical

``` text
$and
$or
$not
$nor
```

### Comparison

``` text
$eq
$gt
$lt
$gte
$lte
$ne
```

### Array

``` text
$in
$nin
$all
$elemMatch
$size
```

### Projection / Element

``` text
$project
$slice
$exists
$type
$regex
```

# Part 6 --- What I Practiced

-   MongoDB Atlas and `mongosh`
-   Creating documents with `insertOne()`
-   Reading with `find()` and `findOne()`
-   `updateOne()` and `updateMany()`
-   `deleteOne()` and `deleteMany()`
-   `$set`, `$inc`, `$unset`, `$push`, `$addToSet`
-   Nested document updates using dot notation
-   Logical operators
-   Comparison operators
-   Array operators
-   Projection and `$slice`
-   Element operators
-   Correcting syntax and method-name errors
-   Verifying database changes
