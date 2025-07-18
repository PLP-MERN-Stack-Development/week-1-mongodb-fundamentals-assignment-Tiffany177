[![Open in Visual Studio Code](https://classroom.github.com/assets/open-in-vscode-2e0aaae1b6195c2367325f4f02e2d04e9abb55f0b24a779b69b11b9e10269abc.svg)](https://classroom.github.com/online_ide?assignment_repo_id=19956438&assignment_repo_type=AssignmentRepo)
# MongoDB Fundamentals Assignment

This assignment focuses on learning MongoDB fundamentals including setup, CRUD operations, advanced queries, aggregation pipelines, and indexing.

## Assignment Overview

You will:
1. Set up a MongoDB database
2. Perform basic CRUD operations
3. Write advanced queries with filtering, projection, and sorting
4. Create aggregation pipelines for data analysis
5. Implement indexing for performance optimization

## Getting Started

1. Accept the GitHub Classroom assignment invitation
2. Clone your personal repository that was created by GitHub Classroom
3. Install MongoDB locally or set up a MongoDB Atlas account
4. Run the provided `insert_books.js` script to populate your database
5. Complete the tasks in the assignment document

## Files Included

- `Week1-Assignment.md`: Detailed assignment instructions
- `insert_books.js`: Script to populate your MongoDB database with sample book data

## Requirements

- Node.js (v18 or higher)
- MongoDB (local installation or Atlas account)
- MongoDB Shell (mongosh) or MongoDB Compass

## Submission

Your work will be automatically submitted when you push to your GitHub Classroom repository. Make sure to:

1. Complete all tasks in the assignment
2. Add your `queries.js` file with all required MongoDB queries
3. Include a screenshot of your MongoDB database
4. Update the README.md with your specific setup instructions

## Resources

- [MongoDB Documentation](https://docs.mongodb.com/)
- [MongoDB University](https://university.mongodb.com/)
- [MongoDB Node.js Driver](https://mongodb.github.io/node-mongodb-native/)

// app.js

const mongoose = require("mongoose");

// lol hope this connects 🤞
mongoose.connect("mongodb://localhost:27017/myDatabase", {
  useNewUrlParser: true,
  useUnifiedTopology: true
}).then(() => {
  console.log("Connected ✅");
}).catch((err) => {
  console.log("uh oh DB error:", err);
});

// User schema (I hope I got this right)
const userSchema = new mongoose.Schema({
  name: String,
  age: Number,
  email: String
});

userSchema.index({ email: 1 }); // just for performance? idk

const User = mongoose.model("User", userSchema);

const run = async () => {
  console.log("let's gooo");

  // CREATE
  const user1 = new User({
    name: "Ann",
    age: 22,
    email: "ann@email.com"
  });
  await user1.save();
  console.log("created user:", user1);

  // READ
  const all = await User.find();
  console.log("all users?", all);

  // UPDATE
  const first = await User.findOne();
  if (first) {
    await User.findByIdAndUpdate(first._id, { age: 28 });
    console.log("updated user age (i think)");
  }

  // DELETE
  const someone = await User.findOne();
  if (someone) {
    await User.findByIdAndDelete(someone._id);
    console.log("deleted someone idk who");
  }

  // FILTER
  const filtered = await User.find({ age: { $gt: 20 } });
  console.log("users older than 20:", filtered);

  // PROJECTION
  const names = await User.find({}, { name: 1, email: 1, _id: 0 });
  console.log("just name + email pls", names);

  // SORT
  const sorted = await User.find().sort({ age: -1 });
  console.log("oldest first I think?", sorted);

  // AGGREGATION
  const stats = await User.aggregate([
    { $match: { age: { $gt: 20 } } },
    { $group: { _id: null, total: { $sum: 1 }, avg: { $avg: "$age" } } }
  ]);
  console.log("stats time 📊:", stats);

  // INDEX
  try {
    await User.collection.createIndex({ email: 1 });
    console.log("indexed email just in case");
  } catch (e) {
    console.log("index error idk", e);
  }

  mongoose.disconnect();
  console.log("done 🚪");
};

run();
