# MongoDB
A NoSQL Database that stores data in flexible, JSON like document called BSON (Binary Object Notations)
Unlike relational databases MySQL doesnt use tables, row, and columns with rigid schema and offers a dynamic structure.
## MongoDB and Mongoose
| **Aspect**         | **MongoDB**                                                                                 | **Mongoose**                                                  |
| ------------------ | ------------------------------------------------------------------------------------------- | ------------------------------------------------------------- |
| **Type**           | Database (NoSQL document store)                                                             | ODM (Object Data Modeling) library for Node.js                |
| **Role**           | Stores and retrieves data                                                                   | Provides an abstraction layer for working with MongoDB        |
| **Data Model**     | Schema-less (documents can have varying fields)                                             | Schema-based (defines structure and validation for documents) |
| **Language**       | Native database, queried via MongoDB Query Language (MQL) or drivers                        | Written in JavaScript, works on top of MongoDB                |
| **Usage**          | Directly interact with the database using official MongoDB driver                           | Interact through models, schemas, and middleware              |
| **Validation**     | No built-in schema enforcement (validation optional via rules or schema validation feature) | Enforces schema and validation at the application level       |
| **Relationships**  | No native joins (use `$lookup` in aggregation)                                              | Supports references and population to mimic relationships     |
| **Learning Curve** | Requires understanding MongoDB query syntax and BSON                                        | Requires learning Mongoose syntax and MongoDB basics          |
| **Performance**    | Direct access — less overhead                                                               | Adds abstraction layer — slightly more overhead               |
| **Flexibility**    | Fully flexible, no schema constraints                                                       | More restrictive — enforces schema consistency                |

## Core Conepts
- **Document** - The basic unit of data in MongoDB. It is a set of key-value pairs, the values can be of any data type including document, array or array of documents.
- **Collection** - A group of documents. Collections dont enforce a schema, meaning the documents within a single collection can have different fields.
- **Database** - A container for collections.
## Key Features
- **Flexible Schema** - No need to pre-define the structure of data. Can add new fields to documents, and different documents in the same collection can have different fields.
- **Horizontal Scalability** - MongoDB is designed to scale out, not just up. This means we can distribute the data across multiple servers **(Sharding)** to handle massice data loads, a process that is often more complex with relational databases.
- **Indexing** - MongoDB supports various types of indexes, which are crucial for optimizing query performance. We can create indexes on any field, including nested documents and arrays.
- **High Availability** - Through replica sets, MongoDB provides automatic failover and data redundancy. A replica set is a group of MongoDB servers that hold identical data. If the primary server fails, a secondary server is automatically promoted to primary, ensuring continuous operation.
## Database Security
### Validate and Sanitize all Inputs
- use a **validation library** like Joi, Yup, Zod before passing anything to Mongoose
- Reject unexpected types
```javascript
if (typeof req.body.username !== "string") {
    throw new Error("Invalid Username");
}
```
- Strip out keys starting with **$** or **.** to block operator injection
```javascript
function sanitize(obj) {
  for (const key in obj) {
    if (key.startsWith('$') || key.includes('.')) {
      delete obj[key];
    } else if (typeof obj[key] === 'object') {
      sanitize(obj[key]);
    }
  }
  return obj;
}
```
### Use Strict Schema Enforcement
- Mongoose 6+ has strictQuery mode enabled by default — keep it that way.
- Define schemas with explicit types so Mongoose auto-casts and rejects wrong types:
```javascript
const UserSchema = new mongoose.Schema({
  username: { type: String, required: true },
  password: { type: String, required: true }
});
```
### Avoid Unsafe Features
- Don't use **$where**, **$function**, arbitrary aggregation pipelines from use input.
- Avoid passing raw objects directly from requests into queries
```javascript
// Bad
User.find(req.body); // user can inject $ne, $gt, etc.
// Good
User.find({ username: req.body.username });
```
### Apply Query Allowlisting
- Explicitly pick which fields a user can filter by
```javascript
const allowedFilters = ['username', 'email'];
const filters = {};
for (const key of allowedFilters) {
  if (req.body[key]) filters[key] = req.body[key];
}
User.find(filters);
```
### Limit Query Size and Rate
- To prevent denial-of-service (DoS) attacks.
```javascript
const cursor = db.collection('logs').find({}, { limit: 100 }).maxTimeMS(2000);
```
- and use rate limiting
```javascript
const rateLimit = require('express-rate-limit');

const limiter = rateLimit({
  windowMs: 60 * 1000,
  max: 50
});

app.use(limiter);
```
### Use Least Privilege MongoDB Users
- Create a dedicated DB user for the app with readWrite on only the needed DB.
- Never connect with the admin user in production.
```javascript
use admin
db.createUser({
  user: "appUser",
  pwd: "StrongPass123!",
  roles: [{ role: "readWrite", db: "myAppDB" }]
});
```
## Functions
### Create
- **insertOne:** Creates a document within the specified collection.
```javascript
db.users.insertOne({ name: “Arafat” })
// Create a document with the name of Arafat into the users collection
```
- **insertMany:** Creates multiple documents within the specified collection.
```javascript
db.users.insertMany([{ name: “John” }, { age: “Roy” }])
// Create two documents with the name John and Roy into the users collection
```
### Read
- **find:** Get all documents.
```javascript
db.users.find()
```
- **find(<filterObject>):** Find all documents based on the filter object
```javascript
db.users.find({ name: “Arafat” })
// Get all users with the name Arafat
db.users.find({ “address.street”: “434 Lund Sweden” })
// Get all users whose adress field has a street field with the value 434 Lund Sweden
```
- **find(<filterObject>, <selectObject>):** Find all documents that match the filter object but only return the field specified in the select object
```javascript
db.users.find({ name: “Arafat” }, { name: 1, hobby: 1 })
// Get all users with the name Arafat but only return their name, hobby, and _id
db.users.find({}, { hobby: 0 })
// Get all users and return all fields except for hobby
```
- **findOne:** Returns the first document that matches the filter object.
```javascript
db.users.findOne({ name: “Arafat” })
// Get the first user with the name Arafat
```
- **countDocuments:** Returns the count of the documents that match the filter object.
```javascript
db.users.countDocuments({ name: “Arafat” })
// Get the number of users with the name Arafat
```
### Update
- **updateOne:** Updates the first document.
```javascript
db.users.updateOne({ name: "Arafat" }, { $set: { name: "Theo" } })
// Update the first user with a name of Arafat to the name of Theo
```
- **updateMany:** Updates multiple docments.
```javascript
db.users.updateMany({ age: 16 }, { $inc: { age: 6 } })
// Update all users with an age of 16 by adding 6 to their age
```
- **replaceOne:** Replace the first document. This will completely overwrite the entire object and not just update individual fields.
```javascript
db.users.replaceOne({ age: 12 }, { age: 13 })
// Replace the first user with an age of 12 with an object that has the age of 13 as its only field
```
### Delete
- **deleteOne:** Delete a single document from a collection.
```javascript
db.users.deleteOne({ name: "Arafat" })
// Delete the first user with an name of Arafat
```
- **deleteMany:** Delete multiple documents from a collection.
```javascript
db.users.deleteMany({ age: 26 })
// Delete all users with an age of 26
```
### Complex Filter Object
- **$eq:** equals.
```javascript
db.users.find({ name: { $eq: “Arafat” } })
// Get all the users with the name Arafat
```
- **$ne:** not equal to.
```javascript
db.users.find({ name: { $ne: “Arafat” } })
// Get all users with a name other than Kyle
```
- **$gt / $gte:** Greater than and greater than or eqal.
```javascript
db.users.find({ age: { $gt: 26 } })
// Get all users with an age greater than 26
db.users.find({ age: { $gte: 34 } })
// Get all users with an age greater than or equal to 34
```
- **$lt / $lte:** Less than and less than or eqal.
```javascript
db.users.find({ age: { $lt: 26 } })
// Get all users with an age less than 26
db.users.find({ age: { $lte: 34 } })
// Get all users with an age less than or equal to 34
```
- **$in:** Check if a value is one of many values.
```javascript
db.users.find({ name: { $in: [“Roy”, “Leo”] } })
// Get all users with a name of Roy or Leo
```
- **$nin:** Check if a value is none of many values.
```javascript
db.users.find({ name: { $nin: [“Roy”, “Leo”] } })
// Get all users that do not have the name Roy or Leo
```
- **$and:** Returns true if all expressions are true
```javascript
db.users.find({ $and: [{ age: 6 }, { name: “Arafat” }] })
// Get all users that have an age of 6 and the name Arafat
db.users.find({ age: 6, name: “Arafat” })
// Alternative way to do same thing
```
- **$or:** returns true if any expression is true
```javascript
db.users.find({ $or: [{ age: 6 }, { name: “Arafat” }] })
// Get all users that have an age of 6 or the name Arafat
```
- **$not:** Negates the expression
```javascript
db.users.find({ name: { $not: { $eq: “Arafat” } } })
// Get all users with a name other than Arafat
```
- **$exists:** Matches documents that have the specified field.
```javascript
db.users.find({ name: { $exists: true } })
// Returns all users that have a name field
```
- **$expr:** performs an expression evaluation in the query.
```javascript
db.users.find({ $expr: { $gt: [“$balance”, “$debt”] } })
// Get all users that have a balance that is greater than their debt
```
### Complex Update Object
- **$set:** Updates only the fields passed to $set.
```javascript
db.users.updateOne({ age: 12 }, { $set: { name: “Roy” } })
// Update the name of the first user with the age of 12 to the value Roy
```
- **$inc:** Increments the value of a field by a specified amount.
```javascript
db.users.updateOne({ debt: 200 }, { $inc: { debt: 100 } })
// Add 100 to the debt of the first user with the debt of 200
db.users.updateOne({ debt: 200 }, { $inc: { debt: -100 } })
// Remove 100 from the debt of the first user with the debt of 200
```
- **$rename:** Rename a field
```javascript
db.users.updateMany({}, { $rename: { loss: “Profit” } })
// Rename the field loss to profit for all users
```
- **$unset:** Remove a field.
```javascript
db.users.updateOne({ age: 26 }, { $unset: { age: "" } })
// Remove the age field from the first user with an age of 26
```
- **$push:** Adds new elements to an array
```javascript
db.users.updateMany({}, { $push: { enemy: “Ria” } })
// Add Ria to the enemy array for all users
```
- **$pull:** Rmoves all array elements that match a specified condition.
```javascript
db.users.updateMany({}, { $pull: { enemy: “Arafat” } })
// Remove Mike from the friends array for all users
```
### Modifiers for read
- **sort:** Sort the results of a find by the given fields.
```javascript
db.users.find().sort({ debt: 1, balance: -1 })
// Returns all users sorted by name in alphabetical order and then if any duplicated names exits, sorts by age in reverse order.
```
- **limit:** Returns a specified number of documents.
```javascript
db.users.find().limit(5)
// Returns the first 5 users
```
- **skip:** Skip a specified number of documents from the start.
```javascript
db.users.find().skip(7)
// Skip the first 7 users when returning results. Freat for pagination when
```
