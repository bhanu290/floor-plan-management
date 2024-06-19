# Intelligent Floor Plan Management System

## Introduction

The Intelligent Floor Plan Management System is designed to provide a seamless workspace experience by allowing administrators to manage and optimize floor plans effectively. This system includes features for robust user authentication, version control,  meeting room optimization, and  error handling. This document provides a detailed case study of the system's implementation, including code snippets, data structures, algorithms, and time/space complexity analysis.

## 1. Authentication

### Implementation
We use JWT (JSON Web Token) for secure authentication and authorization.

### Code Snippet

#### `controllers/authController.js`

```javascript
const jwt = require("jsonwebtoken");
const User = require("../models/User");
const passKey = process.env.JWT_SECRET;

const authController = {
  login: async (req, res) => {
    const { username, password } = req.body;
    try {
      
      const user = await User.findOne({ username,password });
      
      if (user) {
        const payload = { username, role: user ? "user" : "admin" };
        const token = jwt.sign(payload, passKey);

        res.status(200).json({ message: "Login successful", token });
      } else {
        res.status(401).json({ message: "Please Sign up or Invalid credentials" });
      }
    } catch (error) {
      res
        .status(500)
        .json({ message: "An error occurred", error: error.message });
    }
  },
  signup: async (req, res) => {
    try {
      const { username, password } = req.body;

      const existingUser = await User.findOne({ username });
      if (existingUser) {
        res.status(409).json({ message: "Username already exists" });
      } else {
        const newUser = new User({ username, password });
        await newUser.save();
        res.status(201).json({ message: "User signup successful", newUser });
      }
    } catch (error) {
      res
        .status(500)
        .json({ message: "An error occurred", error: error.message });
    }
  },
};

module.exports = authController;
```
### Time and Space Complexity
- Time Complexity: O(1) for token generation and user retrieval.
- Space Complexity: O(1) for token storage.

## 2. Cost Estimation 

#### Data Structures:
- **MongoDB:** Used for database storage due to its flexibility and scalability.


## 3. Handling System Failure Cases

### Code Snippet

#### `utils/errorHandler.js`

```javascript
module.exports = (err, req, res, next) => {
  console.error(err.stack);
  res.status(500).json({ message: 'Something went wrong!' });
};

```


## 4. Object-Oriented Programming Language (OOPS)

### Code Snippet

#### `models/User.js`

```javascript
const mongoose = require('mongoose');

const UserSchema = new mongoose.Schema({
  username: {
    type: String,
    required: true,
  },
  password: {
    type: String,
    required: true,
  },
  role: {
    type: String,
    enum: ['admin', 'user'],
    default: 'user',
  },
}, { timestamps: true });

module.exports = mongoose.model('User', UserSchema);


```




## Meeting Room Optimization

#### Implementation
- **Recommendation System**: Suggests the best meeting room based on capacity and availability.
- **Dynamic Updates**: Continuously updates recommendations as bookings occur.

### Code Snippet

#### `services/meetingRoomService.js`

```javascript
const MeetingRoom = require('../models/MeetingRoom');

exports.bookMeetingRoom = async (requirements) => {
  const availableRooms = await MeetingRoom.find({ booked: false, capacity: { $gte: requirements.capacity } }).sort({ capacity: 1 });
  if (availableRooms.length === 0) throw new Error('No available rooms');

  const room = availableRooms[0];
  room.booked = true;
  room.bookedBy = requirements.userId;
  room.bookedAt = new Date();
  await room.save();

  return room;
};

exports.suggestMeetingRoom = async (requirements) => {
  const availableRooms = await MeetingRoom.find({ booked: false, capacity: { $gte: requirements.capacity } }).sort({ capacity: 1 });
  if (availableRooms.length === 0) return null;

  return availableRooms[0];
};
```

## Conclusion
This comprehensive case study provides a detailed overview of the Intelligent Floor Plan Management System, including robust user authentication, version control, meeting room optimization, and  error handling. The implementation uses efficient algorithms and data structures to ensure optimal time and space complexity, while also considering important trade-offs in system design.
