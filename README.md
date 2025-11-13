<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>NDS - Niko Dance School</title>
<style>
    body {
        font-family: Arial, sans-serif;
        background-color: #f8f8f8;
        margin: 0;
        padding: 0;
    }

    h1 { text-align: center; }

    #login-page {
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
        height: 100vh;
    }

    .login-box {
        background: white;
        padding: 30px;
        border-radius: 12px;
        box-shadow: 0 0 15px rgba(0,0,0,0.1);
        width: 320px;
        text-align: center;
    }

    input[type="text"], input[type="password"] {
        width: 90%;
        padding: 10px;
        margin: 10px 0;
        border: 1px solid #ccc;
        border-radius: 5px;
    }

    button {
        padding: 8px 18px;
        background-color: #009688;
        color: white;
        border: none;
        border-radius: 5px;
        cursor: pointer;
        margin: 5px;
    }

    button:hover { background-color: #00796b; }

    .footer {
        font-size: 13px;
        margin-top: 15px;
        color: #444;
    }

    #dashboard {
        display: none;
        padding: 20px;
        text-align: center;
    }

    #menu {
        position: fixed;
        top: 10px;
        left: 10px;
    }

    select {
        padding: 6px;
        border-radius: 5px;
        border: 1px solid #ccc;
    }

    #logout-btn {
        position: fixed;
        top: 10px;
        right: 10px;
        background-color: #d32f2f;
    }

    #logout-btn:hover { background-color: #b71c1c; }

    .content { margin-top: 80px; }

    .training {
        border: 1px solid #ccc;
        border-radius: 10px;
        margin: 15px auto;
        padding: 10px;
        width: 300px;
        background-color: #fff;
        box-shadow: 0 0 5px rgba(0,0,0,0.05);
    }

    .status {
        font-weight: bold;
        color: #00796b;
    }

    .hidden { display: none; }
</style>
</head>
<body>

<!-- LOGIN PAGE -->
<div id="login-page">
    <div class="login-box">
        <h1>Login</h1>
        <input type="text" id="username" placeholder="Username"><br>
        <input type="password" id="password" placeholder="Password"><br>
        <button onclick="login()">LOGIN</button>
        <p class="footer">
            If you want to join, email me at <b>testern@hotmail.com</b><br>
            and I will add you to the system.
        </p>
    </div>
</div>

<!-- DASHBOARD -->
<div id="dashboard">
    <div id="menu">
        <select id="menuSelect" onchange="changePage()">
            <option value="trainings">My Trainings</option>
            <option value="data">My Data</option>
            <option value="messages">Messages</option>
        </select>
    </div>

    <button id="logout-btn" onclick="logout()">LOG OUT</button>

    <!-- Trainings -->
    <div id="trainings" class="content">
        <h1>My Trainings</h1>
        <div id="training-list"></div>
    </div>

    <!-- Data -->
    <div id="data" class="content hidden">
        <h1>My Data</h1>
        <p><b>Name:</b> <span id="user-name"></span></p>
        <p><b>Code:</b> <span id="user-code"></span></p>
    </div>

    <!-- Messages -->
    <div id="messages" class="content hidden">
        <h1>Messages</h1>
        <div id="message-list"></div>
    </div>
</div>

<script>
/* ===============================
   🔹 ADMIN SECTION – ADD USERS HERE
   =============================== */
const users = {
    "Nikolaj": {
        password: "nikopiko",
        name: "Nikolaj Coh",
        code: "1453",
        trainings: [
            { title: "Hip Hop Level 1", day: "PON, 23.5.2025", status: "" },
            { title: "Training Pro", day: "TOR, 26.12.2025", status: "" }
        ],
        messages: [
            "Pozdravljen Nikolaj! Moral se boš pri nas javiti, ker se moraš znova prijavit. TZ Stern info"
        ]
    }
};

let currentUserKey = null;

/* ===============================
   🔹 LOGIN SYSTEM
   =============================== */
function login() {
    const enteredUser = document.getElementById("username").value.trim();
    const pass = document.getElementById("password").value.trim();

    const userKey = Object.keys(users).find(
        key => key.toLowerCase() === enteredUser.toLowerCase()
    );

    if (userKey && users[userKey].password === pass) {
        currentUserKey = userKey;
        document.getElementById("login-page").style.display = "none";
        document.getElementById("dashboard").style.display = "block";
        loadUserData();
    } else {
        alert("Wrong username or password!");
    }
}

/* ===============================
   🔹 LOAD USER DATA
   =============================== */
function loadUserData() {
    const u = users[currentUserKey];

    // load saved progress
    const saved = JSON.parse(localStorage.getItem("nds_" + currentUserKey)) || {};
    if (saved.trainings) {
        u.trainings = saved.trainings;
    }

    // My Data
    document.getElementById("user-name").textContent = u.name;
    document.getElementById("user-code").textContent = u.code;

    // Trainings
    const trainingList = document.getElementById("training-list");
    trainingList.innerHTML = "";
    u.trainings.forEach((t, index) => {
        trainingList.innerHTML += `
            <div class="training">
                <p><b>${t.title}</b><br>${t.day}</p>
                <p>Status: <span class="status">${t.status || "Not selected"}</span></p>
                <button onclick="setStatus(${index}, 'YES')">✔</button>
                <button onclick="setStatus(${index}, 'NO')">✖</button>
            </div>`;
    });

    // Messages
    const messageList = document.getElementById("message-list");
    messageList.innerHTML = "";
    if (!u.messages || u.messages.length === 0) {
        messageList.innerHTML = "<p>No new messages.</p>";
    } else {
        u.messages.forEach(msg => {
            messageList.innerHTML += `<p>📩 ${msg}</p>`;
        });
    }
}

/* ===============================
   🔹 TRAINING STATUS UPDATE
   =============================== */
function setStatus(index, status) {
    const u = users[currentUserKey];
    u.trainings[index].status = status;
    localStorage.setItem("nds_" + currentUserKey, JSON.stringify(u));
    loadUserData(); // refresh display
}

/* ===============================
   🔹 LOGOUT
   =============================== */
function logout() {
    document.getElementById("dashboard").style.display = "none";
    document.getElementById("login-page").style.display = "flex";
    document.getElementById("username").value = "";
    document.getElementById("password").value = "";
    currentUserKey = null;
}

/* ===============================
   🔹 PAGE SWITCHING
   =============================== */
function changePage() {
    const val = document.getElementById("menuSelect").value;
    document.getElementById("trainings").classList.add("hidden");
    document.getElementById("data").classList.add("hidden");
    document.getElementById("messages").classList.add("hidden");
    document.getElementById(val).classList.remove("hidden");
}
</script>

</body>
</html>
