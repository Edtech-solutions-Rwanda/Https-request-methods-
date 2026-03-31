const express = require("express")
const mysql2 = require("mysql2")


const app = express()
app.use(express.json())

const db = mysql2.createConnection({
host: "localhost",
user: "root",
password: "",
database: "l5_sod_2026"
});

db.connect(err => {
if(err) throw err;
console.log("MYSQL Connected")
});

// HTTP METHODS
// THIS IS GET METHOD
app.get('/students', (req, res) => {
db.query("SELECT * FROM students", (err, result) => {
if (err) return res.send(err);
res.json(result);
});
});

// THIS IS POST METHOD
app.post('/students', (req, res) => {
console.log("POST /students HIT");
console.log(req.body);

const name = req.body.name;

db.query("INSERT INTO students (name) VALUES (?)", [name], (err, result) => {
if (err) return res.send(err);
res.json(result);
});
});

// THIS IS GET SINGLE STUDENT METHOD
app.get('/students/:id', (req, res) => {
const id = req.params.id;
db.query("SELECT * FROM students WHERE id = ?", [id], (err, result) => {
if (err) return res.send(err);
if (result.length === 0) return res.status(404).json({message: 'Student not found'});
res.json(result[0]);
});
});

// THIS IS PUT METHOD
app.put('/students/:id', (req, res) => {
const id = req.params.id;
const name = req.body.name;
db.query("UPDATE students SET name = ? WHERE id = ?", [name, id], (err, result) => {
if (err) return res.send(err);
if (result.affectedRows === 0) return res.status(404).json({message: 'Student not found'});
res.json({message: 'Student updated'});
});
});

// THIS IS DELETE METHOD
app.delete('/students/:id', (req, res) => {
const id = req.params.id;
db.query("DELETE FROM students WHERE id = ?", [id], (err, result) => {
if (err) return res.send(err);
if (result.affectedRows === 0) return res.status(404).json({message: 'Student not found'});
res.json({message: 'Student deleted'});
});
});

app.listen(5000, () => console.log("Server runs on port 5000"));
