/* HTML */

<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>To-Do List</title>
    <link rel="stylesheet" href="style.css" />
</head>
<body>

    <header>
        <div class="todo-container">
    </header>

    <main>
        <h1>Minha Lista de Tarefas</h1>
            <div class="input-group">
                <input type="text" id="taskInput" placeholder="Adicione uma nova tarefa..." />
                <button id="addTaskBtn">Adicionar</button>
            </div>
            <ul id="taskList"></ul>
        </div>
    </main>

    <footer>
        <script src="script.js"></script>
    </footer>

</body>
</html>

/* CSS */

/* Estilo básico para a To-Do List */

body {
    font-family: Arial, sans-serif;
    background-color: #f2f2f2;
    display: flex;
    justify-content: center;
    align-items: flex-start;
    padding-top: 50px;
    height: 100vh;
}

.todo-container {
    background-color: white;
    padding: 30px;
    border-radius: 10px;
    width: 400px;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}

h1 {
    text-align: center;
    color: #333;
}

.input-group {
    display: flex;
    margin-bottom: 20px;
}

#taskInput {
    flex: 1;
    padding: 10px;
    font-size: 16px;
}

#addTaskBtn {
    padding: 10px 20px;
    background-color: #28a745;
    border: none;
    color: white;
    cursor: pointer;
}

#addTaskBtn:hover {
    background-color: #218838;
}

ul {
    list-style: none;
    padding: 0;
}

li {
    background-color: #f9f9f9;
    padding: 10px;
    margin-bottom: 10px;
    border-left: 5px solid #28a745;
    display: flex;
    justify-content: space-between;
    align-items: center;
}

li.completed {
    text-decoration: line-through;
    opacity: 0.6;
}

button.delete-btn {
    background-color: transparent;
    border: none;
    color: red;
    cursor: pointer;
    font-size: 16px;
}

/* JavaScript */

/**
 * Script principal da To-Do List
 * Funções: adicionar, remover, marcar tarefas como concluídas
 * Usa localStorage para persistência de dados
 */

// Referências aos elementos da interface
const taskInput = document.getElementById('taskInput');
const addTaskBtn = document.getElementById('addTaskBtn');
const taskList = document.getElementById('taskList');

// Carrega tarefas salvas ao iniciar
document.addEventListener('DOMContentLoaded', loadTasks);

// Evento para adicionar tarefa
addTaskBtn.addEventListener('click', addTask);

// Permite adicionar com Enter
taskInput.addEventListener('keyup', function (e) {
    if (e.key === 'Enter') {
        addTask();
    }
});

/**
 * Adiciona uma nova tarefa à lista
 */
function addTask() {
    const taskText = taskInput.value.trim();
    if (taskText === '') return;

    const task = {
        text: taskText,
        completed: false
    };

    saveTask(task);
    renderTask(task);
    taskInput.value = '';
}

/**
 * Renderiza uma tarefa na tela
 * @param {Object} task - Objeto contendo texto e status
 */
function renderTask(task) {
    const li = document.createElement('li');
    li.textContent = task.text;

    if (task.completed) {
        li.classList.add('completed');
    }

    // Marcar como concluída
    li.addEventListener('click', () => {
        li.classList.toggle('completed');
        task.completed = !task.completed;
        updateStorage();
    });

    // Botão de excluir
    const deleteBtn = document.createElement('button');
    deleteBtn.textContent = '🗑️';
    deleteBtn.className = 'delete-btn';
    deleteBtn.addEventListener('click', (e) => {
        e.stopPropagation();
        li.remove();
        removeTask(task.text);
    });

    li.appendChild(deleteBtn);
    taskList.appendChild(li);
}

/**
 * Salva tarefa no localStorage
 * @param {Object} task 
 */
function saveTask(task) {
    const tasks = getTasks();
    tasks.push(task);
    localStorage.setItem('tasks', JSON.stringify(tasks));
}

/**
 * Remove tarefa do localStorage
 * @param {string} taskText 
 */
function removeTask(taskText) {
    let tasks = getTasks();
    tasks = tasks.filter(t => t.text !== taskText);
    localStorage.setItem('tasks', JSON.stringify(tasks));
}

/**
 * Atualiza localStorage com tarefas visíveis
 */
function updateStorage() {
    const tasks = [];
    document.querySelectorAll('#taskList li').forEach(li => {
        tasks.push({
            text: li.firstChild.textContent.trim(),
            completed: li.classList.contains('completed')
        });
    });
    localStorage.setItem('tasks', JSON.stringify(tasks));
}

/**
 * Retorna tarefas salvas no localStorage
 * @returns {Array}
 */
function getTasks() {
    return JSON.parse(localStorage.getItem('tasks')) || [];
}

/**
 * Carrega e exibe tarefas salvas
 */
function loadTasks() {
    const tasks = getTasks();
    tasks.forEach(task => renderTask(task));
}
