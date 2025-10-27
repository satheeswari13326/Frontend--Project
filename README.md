# Frontend--Project
Skip to content
Navigation Menu
t88

Code
Issues
Pull requests
 0 stars
 0 forks
 1 watching
 1 Branch
 0 Tags
 Activity
Public repository
padmavelan/t88
Name	
padmavelan
padmavelan
2 weeks ago
README.md
2 weeks ago
Repository files navigation
README
Front-end-project
import mongoose from "mongoose";

const connectDB = async () => { try { await mongoose.connect("mongodb://localhost:27017/formValidationDB"); console.log("✅ MongoDB Connected"); } catch (error) { console.error("❌ MongoDB Connection Failed:", error.message); process.exit(1); } };

export default connectDB; import mongoose from "mongoose";

const userSchema = mongoose.Schema( { name: { type: String, required: true, minlength: 3 }, email: { type: String, required: true, unique: true, match: /.+@.+..+/ }, password: { type: String, required: true, minlength: 6 }, }, { timestamps: true } );

export default mongoose.model("User", userSchema); import User from "../models/userModel.js";

// Register User export const registerUser = async (req, res) => { const { name, email, password, confirmPassword } = req.body;

// Server-side validation if (!name || !email || !password || !confirmPassword) { return res.status(400).json({ message: "All fields are required" }); } if (password !== confirmPassword) { return res.status(400).json({ message: "Passwords do not match" }); }

try { const userExists = await User.findOne({ email }); if (userExists) { return res.status(400).json({ message: "Email already registered" }); }

const user = await User.create({ name, email, password });
res.status(201).json({ message: "Registration Successful", user });
} catch (error) { res.status(500).json({ message: "Server error", error: error.message }); } }; import express from "express"; import { registerUser } from "../controllers/userController.js";

const router = express.Router();

router.post("/register", registerUser);

export default router; export const notFound = (req, res, next) => { const error = new Error(Not Found - ${req.originalUrl}); res.status(404); next(error); };

export const errorHandler = (err, req, res, next) => { const statusCode = res.statusCode === 200 ? 500 : res.statusCode; res.status(statusCode); res.json({ message: err.message, stack: process.env.NODE_ENV === "production" ? null : err.stack }); }; import express from "express"; import cors from "cors"; import connectDB from "./config/db.js"; import userRoutes from "./routes/userRoutes.js"; import { notFound, errorHandler } from "./middleware/errorMiddleware.js";

connectDB(); const app = express();

app.use(cors()); app.use(express.json());

app.use("/api/users", userRoutes);

app.use(notFound); app.use(errorHandler);

const PORT = process.env.PORT || 5000; app.listen(PORT, () => console.log(🚀 Server running on port ${PORT})); import React, { useState } from "react"; import axios from "axios"; import SuccessMessage from "./SuccessMessage";

export default function FormValidation() { const [formData, setFormData] = useState({ name: "", email: "", password: "", confirmPassword: "" }); const [errors, setErrors] = useState({}); const [success, setSuccess] = useState("");

// Validation rules const validate = (name, value) => { switch (name) { case "name": return value.length < 3 ? "Name must be at least 3 characters" : ""; case "email": return /^[^\s@]+@[^\s@]+.[^\s@]+$/.test(value) ? "" : "Invalid email"; case "password": return value.length < 6 ? "Password must be 6+ characters" : ""; case "confirmPassword": return value !== formData.password ? "Passwords do not match" : ""; default: return ""; } };

const handleChange = (e) => { const { name, value } = e.target; const error = validate(name, value); setErrors({ ...errors, [name]: error }); setFormData({ ...formData, [name]: value }); };

const handleSubmit = async (e) => { e.preventDefault(); if (Object.values(errors).some((err) => err !== "")) return alert("Fix validation errors first");

try {
  const { data } = await axios.post("http://localhost:5000/api/users/register", formData);
  setSuccess(data.message);
  setFormData({ name: "", email: "", password: "", confirmPassword: "" });
  setErrors({});
} catch (err) {
  setSuccess(err.response?.data?.message || "Error occurred");
}
};

return (

Interactive Form Validation
{success && } {["name", "email", "password", "confirmPassword"].map((field) => (
<input type={field.includes("password") ? "password" : "text"} name={field} placeholder={field.replace(/([A-Z])/g, " $1")} value={formData[field]} onChange={handleChange} className={w-full p-2 border rounded ${errors[field] ? "border-red-500" : "border-green-400"}} /> {errors[field] &&
{errors[field]}

}
))} Register
); } import React from "react";
export default function SuccessMessage({ message }) { return (

{message}
); } import React from "react"; import FormValidation from "./components/FormValidation";
function App() { return ; }

export default App; module.exports = { content: ["./src/**/*.{js,jsx,ts,tsx}"], theme: { extend: {} }, plugins: [], };

Releases
No releases published
Packages
No packages published
Footer
© 2025 GitHub, Inc.
Footer navigation
Terms
Privacy
Security
Status
Community
Docs
Contact
Manage cookies
Do not share my personal information
