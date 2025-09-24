<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.">
  <title> To-Do List</title>
  <style>
    body {
      font-family: "Poppins", sans-serif;
      background: linear-gradient(135deg, #1e3c72, #2a5298);
      color: #fff;
      display: flex;
      justify-content: center;
      align-items: flex-start;
      min-height: 100vh;
      margin: 0;
      padding: 20px;
    }

    .container {
      background:rgba(25, 255, 255, 0.);
      backdrop-filter: blur(20px);
      border-radius:15px;
      padding: 25px;
      width: 100%;
      max-width: 600px;
      box-shadow: 0 8px 25px rgba(0,0,0,0.3);
      animation: fadeIn 1s ease-in-out;
    }
  

    @keyframes fadeIn {
      from { opacity: 0; transform: translateY(-20px); }
      to { opacity: 1; transform: translateY(0); }
    }

    h1 {
      text-align: center;
      font-size: 32px;
      margin-bottom: 20px;
      color: #ffeb3b;
      text-shadow: 0px 0px 10px #ffeb3b;
    }

    .input-group {
      display: flex;
      flex-direction: column;
      gap: 12px;
      margin-bottom: 20px;
    }

    input, button, select {
      padding: 12px;
      border: none;
      border-radius: 10px;
      font-size: 16px;
    }

    input {
      outline: none;
      background: rgba(255,255,255,0.2);
      color: #fff;
    }

    input::placeholder {
      color: #ddd;
    }

    button {
      background:white
      color: white;
      cursor: pointer;
      transition: all 0.3s ease;
      font-weight: bold;
      letter-spacing: 1px;
    }

    button:hover {
      background: linear-gradient(135deg, #dd2476, #ff512f);
      transform: scale(1.sec);
    }

    .filter-group {
      display: flex;
      justify-content: space-between;
      margin-bottom: 20px;
    }

    .filter-group select {
      background: rgba(255,255,255,0.2);
      color: #fff;
      cursor: pointer;
    }

    ul {
      list-style: none;
      padding: 0;
      margin: 0;
    }

    li {
      background: rgba(255, 255, 255, 0.15);
      margin-bottom: 12px;
      padding: 14px;
      border-radius: 12px;
      display: flex;
      justify-content: space-between;
      align-items: center;
      box-shadow: 0px 4px 15px rgba(0,0,0,0.2);
      transition: transform 0.3s ease, background 0.3s ease;
    }

    li:hover {
      transform: translateY(-3px);
      background: rgba(255,255,255,0.25);
    }

    li span {
      display: block;
    }

    .task-text {
      font-size: 16px;
    }

    .task-time {
      font-size: 13px;
      color: #ffeb3b;
    }

    .delete-btn, .complete-btn {
      border: none;
      padding: 8px 12px;
      border-radius: 8px;
      color: #fff;
      cursor: pointer;
      transition: 0.3s;
      margin-left: 5px;
      font-size: 14px;
    }

    .delete-btn {
      background: #ff4f81;
    }

    .delete-btn:hover {
      background: #e6004c;
    }

    .complete-btn {
      background: #4caf50;
    }

    .complete-btn:hover {
      background: #2e7d32;
    }

    .completed {
      text-decoration: line-through;
      opacity: 0.7;
      color: #b2ff59;
    }
  </style>
</head>
<body>
  <div class="container">
    <h1>my To-Do List <h1>

    <div class="input-group">
      <input type="text" id="taskInput" placeholder="Enter your task...">
      <input type="datetime-local"
      id="dateTimeInput">
      <button onclick="addTask()">➕ Add Task</button>
    </div>

    <div class="filter-group">
      <label for="filter">Filter: </label>
      <select id="filter" onchange="filterTasks()">
        <option value="all">All</option>
        <option value="pending">Pending</option>
        <option value="completed">Completed</option>
      </select>
    </div>

    <ul id="taskList"></ul>
  </div>

  <script>
    const taskInput = document.getElementById("taskInput");
    const dateTimeInput = document.getElementById("dateTimeInput");
    const taskList = document.getElementById("taskList");
    const filterSelect = document.getElementById("filter");

    document.addEventListener("DOMContentLoaded", loadTasks);

    function addTask() {
      const taskText = taskInput.value.trim();
      const taskDateTime = dateTimeInput.value;

      if (taskText === "" || taskDateTime === "") {
        alert("Please enter both task and date/time!");
        return;
      }

      const task = {
        text: taskText,
        dateTime: taskDateTime,
        completed: false
      };

      saveTask(task);
      renderTask(task);

      taskInput.value = "";
      dateTimeInput.value = "";
    }

    function renderTask(task) {
      const li = document.createElement("li");

      const taskContent = document.createElement("span");
      const date = new Date(task.dateTime);
      taskContent.innerHTML = `<div class="task-text ${task.completed ? "completed" : ""}">
          ${task.text}
        </div>
        <div class="task-time">📅 ${date.toLocaleString()}</div>`;

      const buttonGroup = document.createElement("div");

      const completeBtn = document.createElement("button");
      completeBtn.textContent = task.completed ? "Undo" : "✔";
      completeBtn.classList.add("complete-btn");
      completeBtn.onclick = () => {
        task.completed = !task.completed;
        updateTask(task);
        taskContent.querySelector(".task-text").classList.toggle("completed");
        completeBtn.textContent = task.completed ? "Undo" : "✔";
      };

      const deleteBtn = document.createElement("button");
      deleteBtn.textContent = "🗑";
      deleteBtn.classList.add("delete-btn");
      deleteBtn.onclick = () => {
        li.remove();
        removeTask(task);
      };

      buttonGroup.appendChild(completeBtn);
      buttonGroup.appendChild(deleteBtn);

      li.appendChild(taskContent);
      li.appendChild(buttonGroup);
      taskList.appendChild(li);
    }

    function saveTask(task) {
      let tasks = JSON.parse(localStorage.getItem("tasks")) || [];
      tasks.push(task);
      localStorage.setItem("tasks", JSON.stringify(tasks));
    }

    function loadTasks() {
      let tasks = JSON.parse(localStorage.getItem("tasks")) || [];
      tasks.forEach(task => renderTask(task));
    }

    function updateTask(updatedTask) {
      let tasks = JSON.parse(localStorage.getItem("tasks")) || [];
      tasks = tasks.map(task => 
        task.text === updatedTask.text && task.dateTime === updatedTask.dateTime
          ? updatedTask
          : task
      );
      localStorage.setItem("tasks", JSON.stringify(tasks));
      filterTasks();
    }

    function removeTask(taskToRemove) {
      let tasks = JSON.parse(localStorage.getItem("tasks")) || [];
      tasks = tasks.filter(task => task.text !== taskToRemove.text || task.dateTime !== taskToRemove.dateTime);
      localStorage.setItem("tasks", JSON.stringify(tasks));
    }

    function filterTasks() {
      const filter = filterSelect.value;
      taskList.innerHTML = "";
      let tasks = JSON.parse(localStorage.getItem("tasks")) || [];
      tasks.forEach(task => {
        if (
          filter === "all" ||
          (filter === "pending" && !task.completed) ||
          (filter === "completed" && task.completed)
        ) {
          renderTask(task);
        }
      });
    }
  </script>
</body>
</html>
