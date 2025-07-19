# todolist
index.html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>ToDo </title>
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <div class="container">
    <h1>📝 ToDo </h1>

    <form id="task-form">
      <input type="text" id="task-title" placeholder="Task title" required>
      <textarea id="task-desc" placeholder="Description (optional)"></textarea>
      <input type="date" id="task-date">
      <select id="task-priority">
        <option value="Low">Low Priority</option>
        <option value="Medium" selected>Medium Priority</option>
        <option value="High">High Priority</option>
      </select>
      <button type="submit">Add Task</button>
    </form>

    <ul id="task-list"></ul>
  </div>

  <script src="script.js"></script>
</body>
</html>
style.css
body {
  font-family: 'Segoe UI', sans-serif;
  background: linear-gradient(to right, #74ebd5, #acb6e5);
  margin: 0;
  padding: 20px;
}

.container {
  background: #ffffffcc;
  padding: 20px;
  border-radius: 15px;
  max-width: 600px;
  margin: auto;
  box-shadow: 0 8px 16px rgba(0,0,0,0.2);
}

h1 {
  text-align: center;
  color: #333;
}

form {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

input, textarea, select, button {
  padding: 10px;
  font-size: 1em;
  border-radius: 10px;
  border: 1px solid #aaa;
}

button {
  background-color: #28a745;
  color: white;
  border: none;
  cursor: pointer;
  font-weight: bold;
}

button:hover {
  background-color: #218838;
}

ul {
  list-style: none;
  padding: 0;
  margin-top: 20px;
}

li {
  background: #f8f9fa;
  margin-bottom: 10px;
  padding: 15px;
  border-left: 6px solid #17a2b8;
  border-radius: 10px;
  position: relative;
}

li.completed {
  text-decoration: line-through;
  opacity: 0.6;
  border-left-color: #6c757d;
}

.task-actions {
  position: absolute;
  right: 10px;
  top: 10px;
  display: flex;
  gap: 8px;
}

.priority-High {
  border-left-color: #dc3545;
}
.priority-Medium {
  border-left-color: #ffc107;
}
.priority-Low {
  border-left-color: #28a745;
}
script.js
let tasks = JSON.parse(localStorage.getItem('tasks')) || [];

const taskForm = document.getElementById('task-form');
const taskList = document.getElementById('task-list');

taskForm.addEventListener('submit', function (e) {
  e.preventDefault();
  const title = document.getElementById('task-title').value.trim();
  const desc = document.getElementById('task-desc').value.trim();
  const dueDate = document.getElementById('task-date').value;
  const priority = document.getElementById('task-priority').value;

  if (title === '') return;

  const newTask = {
    id: Date.now(),
    title,
    desc,
    dueDate,
    priority,
    completed: false
  };

  tasks.push(newTask);
  saveTasks();
  renderTasks();
  taskForm.reset();
});

function saveTasks() {
  localStorage.setItem('tasks', JSON.stringify(tasks));
}

function renderTasks() {
  taskList.innerHTML = '';
  tasks.forEach(task => {
    const li = document.createElement('li');
    li.className = `${task.completed ? 'completed' : ''} priority-${task.priority}`;
    li.innerHTML = `
      <strong>${task.title}</strong> 
      ${task.desc ? `<p>${task.desc}</p>` : ''}
      ${task.dueDate ? `<small>Due: ${task.dueDate}</small><br>` : ''}
      <div class="task-actions">
        <button onclick="toggleTask(${task.id})">✔️</button>
        <button onclick="editTask(${task.id})">✏️</button>
        <button onclick="deleteTask(${task.id})">🗑️</button>
      </div>
    `;
    taskList.appendChild(li);
  });
}

function toggleTask(id) {
  tasks = tasks.map(task =>
    task.id === id ? { ...task, completed: !task.completed } : task
  );
  saveTasks();
  renderTasks();
}

function editTask(id) {
  const task = tasks.find(t => t.id === id);
  const newTitle = prompt("Edit Title:", task.title);
  if (newTitle !== null) task.title = newTitle.trim();
  const newDesc = prompt("Edit Description:", task.desc || "");
  if (newDesc !== null) task.desc = newDesc.trim();
  saveTasks();
  renderTasks();
}

function deleteTask(id) {
  if (confirm("Are you sure you want to delete this task?")) {
    tasks = tasks.filter(task => task.id !== id);
    saveTasks();
    renderTasks();
  }
}

// Initial render
renderTasks();
