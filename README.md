const mongoose = require('mongoose');

// ✅ Define the adminSchema first const adminSchema = new mongoose.Schema({ username: { type: String, required: true, }, password: { type: String, required: true, }, });

// ✅ Then create and export the model const Admin = mongoose.model('Admin', adminSchema); module.exports = Admin; // models/Feedback.js const mongoose = require('mongoose');

const feedbackSchema = new mongoose.Schema({ name: String, email: String, rating: String, comments: String, }, { timestamps: true });

module.exports = mongoose.model('Feedback', feedbackSchema); const express = require('express'); const router = express.Router(); const Admin = require('../models/Admin'); // ✅ correct file const bcrypt = require('bcrypt'); const jwt = require('jsonwebtoken');

// POST /admin/login router.post('/login', async (req, res) => { const { username, password } = req.body; const admin = await Admin.findOne({ username }); if (!admin) return res.status(401).send('Invalid username');

const match = await bcrypt.compare(password, admin.password); if (!match) return res.status(401).send('Invalid password');

const token = jwt.sign({ id: admin._id }, process.env.JWT_SECRET, { expiresIn: '1h' }); res.send({ token }); });

module.exports = router; const express = require("express"); const router = express.Router(); const Feedback = require("../models/Feedback");

router.post("/", async (req, res) => { try { const feedback = new Feedback(req.body); await feedback.save(); res.status(201).json({ message: "Feedback submitted" }); } catch (error) { res.status(500).json({ error: error.message }); } });

router.get("/", async (req, res) => { try { const feedbacks = await Feedback.find(); res.json(feedbacks); } catch (error) { res.status(500).json({ error: error.message }); } });

module.exports = router; require('dotenv').config(); const express = require("express"); const mongoose = require("mongoose"); const cors = require("cors");

const app = express(); app.use(cors()); app.use(express.json());

// DB Connect mongoose.connect(process.env.MONGO_URI, { useNewUrlParser: true, useUnifiedTopology: true, }).then(() => console.log("MongoDB Connected"));

// Routes app.use("/api/feedback", require("./routes/Feedbackroutes"));

app.listen(process.env.PORT, () => { console.log(Server running on port http://localhost:5000); }); app.get('/', (req, res) => { res.send('Student Feedback Backend is Running'); });

mongoose.connect(process.env.MONGO_URI, { useNewUrlParser: true, useUnifiedTopology: true }).then(() => { console.log('MongoDB Connected'); app.listen(5000, () => { console.log('Server running on port http://localhost:5000'); }); });import { useEffect, useState } from 'react'; import axios from 'axios';

const AdminDashboard = () => { const [feedbacks, setFeedbacks] = useState([]);

useEffect(() => { const fetchFeedbacks = async () => { try { const res = await axios.get('http://localhost:5000/api/feedback'); setFeedbacks(res.data); } catch (err) { alert('Failed to fetch feedbacks'); } }; fetchFeedbacks(); }, []);

return (

Admin Dashboard
{feedbacks.map((fb) => (
Name: {fb.name}

Email: {fb.email}

Rating: {fb.rating}

Comment: {fb.comment}

Submitted on: {new Date(fb.createdAt).toLocaleString()}

))}
); };
export default AdminDashboard; import { useState } from 'react'; import { useNavigate } from 'react-router-dom';

const AdminLogin = () => { const [email, setEmail] = useState(''); const [password, setPassword] = useState(''); const navigate = useNavigate();

const handleLogin = (e) => { e.preventDefault(); if (email === 'admin@example.com' && password === 'admin123') { navigate('/admin'); } else { alert('Invalid credentials'); } };

return (

Admin Login
<input type="email" placeholder="Email" className="w-full px-4 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-indigo-400" onChange={(e) => setEmail(e.target.value)} /> <input type="password" placeholder="Password" className="w-full px-4 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-indigo-400" onChange={(e) => setPassword(e.target.value)} /> Login
); };
export default AdminLogin; import React, { useState } from 'react'; import axios from 'axios'; import { useNavigate } from 'react-router-dom';

function FeedbackForm() { const [name, setName] = useState(''); const [email, setEmail] = useState(''); const [rating, setRating] = useState(''); const [comments, setComments] = useState(''); const navigate = useNavigate();

const handleSubmit = async (e) => { e.preventDefault(); console.log('Submitting feedback...');

try {
  await axios.post('http://localhost:5000/api/feedback', {
    name,
    email,
    rating,
    comments,
  });
  navigate('/thankyou');
} catch (err) {
  console.error('Submission Error:', err.response?.data || err.message);
  alert('Failed to submit feedback. Please try again.');
}
};

return (

📋 Student Feedback Form
    <form onSubmit={handleSubmit} className="space-y-6">
      {/* Name */}
      <div>
        <label className="block text-gray-700 font-medium mb-2">Name</label>
        <input
          type="text"
          placeholder="Enter your name"
          className="w-full px-4 py-3 border border-gray-300 rounded-xl focus:ring-4 focus:ring-blue-300 focus:outline-none transition-all"
          value={name}
          onChange={(e) => setName(e.target.value)}
          required
        />
      </div>

      {/* Email */}
      <div>
        <label className="block text-gray-700 font-medium mb-2">Email</label>
        <input
          type="email"
          placeholder="Enter your email"
          className="w-full px-4 py-3 border border-gray-300 rounded-xl focus:ring-4 focus:ring-blue-300 focus:outline-none transition-all"
          value={email}
          onChange={(e) => setEmail(e.target.value)}
          required
        />
      </div>

      {/* Rating */}
      <div>
        <label className="block text-gray-700 font-medium mb-2">Course Rating</label>
        <select
          className="w-full px-4 py-3 border border-gray-300 rounded-xl focus:ring-4 focus:ring-blue-300 focus:outline-none transition-all"
          value={rating}
          onChange={(e) => setRating(e.target.value)}
          required
        >
          <option value="">Select rating</option>
          <option value="1">1 ⭐ - Poor</option>
          <option value="2">2 ⭐⭐ - Fair</option>
          <option value="3">3 ⭐⭐⭐ - Good</option>
          <option value="4">4 ⭐⭐⭐⭐ - Very Good</option>
          <option value="5">5 ⭐⭐⭐⭐⭐ - Excellent</option>
        </select>
      </div>

      {/* Comments */}
      <div>
        <label className="block text-gray-700 font-medium mb-2">Comments</label>
        <textarea
          placeholder="Write your comments..."
          rows="4"
          className="w-full px-4 py-3 border border-gray-300 rounded-xl focus:ring-4 focus:ring-blue-300 focus:outline-none transition-all resize-none"
          value={comments}
          onChange={(e) => setComments(e.target.value)}
          required
        />
      </div>

      {/* Submit */}
      <button
        type="submit"
        className="w-full py-3 bg-blue-600 text-white text-lg font-semibold rounded-xl hover:bg-blue-700 active:scale-[0.98] transition-all duration-200 shadow-md"
      >
        Submit Feedback
      </button>
    </form>
  </div>
</div>
); }

export default FeedbackForm; const ThankYou = () => { return (

Thank You!
We appreciate your feedback.

); };
export default ThankYou;

import React from 'react'; import { BrowserRouter as Router, Routes, Route } from 'react-router-dom';

import FeedbackForm from './pages/FeedbackForm'; import AdminLogin from './pages/AdminLogin'; import AdminDashboard from './pages/AdminDashboard'; import ThankYou from './pages/ThankYou';

function App() { return (

Student Feedback System
    <main className="max-w-4xl mx-auto px-4">
      <Routes>
        <Route path="/" element={<FeedbackForm />} />
        <Route path="/thankyou" element={<ThankYou />} />
        <Route path="/admin" element={<AdminLogin />} />
        <Route path="/dashboard" element={<AdminDashboard />} />
      </Routes>
    </main>

    <footer className="text-center text-gray-500 text-sm mt-10 pb-4">
      © {new Date().getFullYear()} Feedback System — All rights reserved.
    </footer>
  </Router>
</div>
); }

export default App;
