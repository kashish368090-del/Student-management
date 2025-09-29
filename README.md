# Student-management
Record
// server.js
import express from "express";
import mongoose from "mongoose";

const app = express();
app.use(express.json());

// MongoDB Connection
mongoose.connect("mongodb://127.0.0.1:27017/studentDB", {
  useNewUrlParser: true,
  useUnifiedTopology: true,
});

// Schema
const studentSchema = new mongoose.Schema({
  name: String,
  rollNo: Number,
  marks: Number,
});

// Model
const Student = mongoose.model("Student", studentSchema);

// API: Add Student
app.post("/students", async (req, res) => {
  const { name, rollNo, marks } = req.body;
  const student = new Student({ name, rollNo, marks });
  await student.save();
  res.json({ message: "Student added", student });
});

// API: Generate Report
app.get("/report", async (req, res) => {
  const students = await Student.find();

  const report = students.map((s) => {
    let grade;
    if (s.marks >= 90) grade = "A+";
    else if (s.marks >= 75) grade = "A";
    else if (s.marks >= 60) grade = "B";
    else if (s.marks >= 40) grade = "C";
    else grade = "Fail";

    return {
      name: s.name,
      rollNo: s.rollNo,
      marks: s.marks,
      grade,
    };
  });

  res.json(report);
});

app.listen(5000, () => console.log("Server running on port 5000"));
