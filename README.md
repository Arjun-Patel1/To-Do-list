# To-Do-list
It is a To-Do list project made by using html, css, javascript language

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>To-Do List</title>
    <link rel="stylesheet" href="styles.css">
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f4f4f4;
            color: #333;
            transition: background 0.3s, color 0.3s;
        }
        .container {
            max-width: 800px;
            margin: auto;
            background: white;
            padding: 20px;
            box-shadow: 0 0 10px rgba(0,0,0,0.1);
            border-radius: 10px;
        }
        header {
            display: flex;
            justify-content: space-between;
            align-items: center;
        }
        button {
            cursor: pointer;
            padding: 5px 10px;
        }
        table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 20px;
        }
        th, td {
            border: 1px solid #ddd;
            padding: 10px;
            text-align: center;
        }
        .high { color: red; }
        .medium { color: blue; }
        .low { color: green; }
        .completed { background-color: lightgreen; }
        .overdue { background-color: lightcoral; }
        .dark-mode {
            background-color: black;
            color: white;
        }
        .dark-mode .container {
            background-color: #222;
            color: white;
        }
        .dark-mode table, .dark-mode th, .dark-mode td {
            border-color: white;
        }
    </style>
</head>
<body>
    <div class="container">
        <header>
            <h1>To-Do List</h1>
            <button id="darkModeToggle">🌙</button>
        </header>
        <div class="task-input">
            <input type="text" id="task" placeholder="Enter task...">
            <input type="datetime-local" id="deadline">
            <select id="priority">
                <option value="high">High</option>
                <option value="medium">Medium</option>
                <option value="low">Low</option>
            </select>
            <select id="category">
                <option value="work">Work</option>
                <option value="personal">Personal</option>
                <option value="family">Family</option>
            </select>
            <button id="addTask">Add Task</button>
        </div>
        <table>
            <thead>
                <tr>
                    <th>#</th>
                    <th>Task</th>
                    <th>Deadline</th>
                    <th>Priority</th>
                    <th>Category</th>
                    <th>Done</th>
                    <th>Delete</th>
                </tr>
            </thead>
            <tbody id="taskList"></tbody>
        </table>
    </div>
    <script>
        document.getElementById("addTask").addEventListener("click", function() {
            let task = document.getElementById("task").value;
            let deadline = document.getElementById("deadline").value;
            let priority = document.getElementById("priority").value;
            let category = document.getElementById("category").value;
            
            if (task.trim() === "") {
                alert("Please enter a task!");
                return;
            }
            
            let table = document.getElementById("taskList");
            let row = table.insertRow();
            row.innerHTML = `<td>${table.rows.length}</td>
                             <td>${task}</td>
                             <td>${deadline}</td>
                             <td class="${priority}">${priority}</td>
                             <td>${category}</td>
                             <td><button onclick="markDone(this)">✔</button></td>
                             <td><button onclick="deleteTask(this)">❌</button></td>`;
            saveTasks();
            document.getElementById("task").value = "";
            document.getElementById("deadline").value = "";
            checkOverdue();
        });
        
        function markDone(button) {
            let row = button.parentElement.parentElement;
            row.classList.add("completed");
            row.style.textDecoration = "line-through";
            saveTasks();
        }
        
        function deleteTask(button) {
            let row = button.parentElement.parentElement;
            row.remove();
            saveTasks();
        }
        
        document.getElementById("darkModeToggle").addEventListener("click", function() {
            document.body.classList.toggle("dark-mode");
            localStorage.setItem("darkMode", document.body.classList.contains("dark-mode"));
        });
        
        function checkOverdue() {
            let rows = document.querySelectorAll("#taskList tr");
            let now = new Date();
            rows.forEach(row => {
                let deadlineCell = row.cells[2];
                if (deadlineCell) {
                    let deadline = new Date(deadlineCell.innerText);
                    if (deadline < now && !row.classList.contains("completed")) {
                        row.classList.add("overdue");
                    }
                }
            });
        }
        setInterval(checkOverdue, 60000);
        
        function saveTasks() {
            let tasks = [];
            document.querySelectorAll("#taskList tr").forEach(row => {
                let cells = row.querySelectorAll("td");
                tasks.push({
                    task: cells[1].innerText,
                    deadline: cells[2].innerText,
                    priority: cells[3].innerText,
                    category: cells[4].innerText,
                    completed: row.classList.contains("completed")
                });
            });
            localStorage.setItem("tasks", JSON.stringify(tasks));
        }
        
        function loadTasks() {
            let tasks = JSON.parse(localStorage.getItem("tasks")) || [];
            tasks.forEach((task, index) => {
                let table = document.getElementById("taskList");
                let row = table.insertRow();
                row.innerHTML = `<td>${index + 1}</td>
                                 <td>${task.task}</td>
                                 <td>${task.deadline}</td>
                                 <td class="${task.priority}">${task.priority}</td>
                                 <td>${task.category}</td>
                                 <td><button onclick="markDone(this)">✔</button></td>
                                 <td><button onclick="deleteTask(this)">❌</button></td>`;
                if (task.completed) {
                    row.classList.add("completed");
                    row.style.textDecoration = "line-through";
                }
            });
        }
        
        if (localStorage.getItem("darkMode") === "true") {
            document.body.classList.add("dark-mode");
        }
        
        loadTasks();
    </script>
</body>
</html>
