<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Exclusive Admin Panel</title>
  <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@400;600;700&display=swap" rel="stylesheet">
  <style>
    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
      font-family: 'Poppins', sans-serif;
    }
    body {
      background: linear-gradient(135deg, #1f1c2c, #928dab);
      color: #fff;
      min-height: 100vh;
      display: flex;
      justify-content: center;
      align-items: center;
      flex-direction: column;
      padding: 20px;
    }
    .login-box {
      background: rgba(255, 255, 255, 0.05);
      backdrop-filter: blur(12px);
      border-radius: 20px;
      padding: 40px;
      width: 100%;
      max-width: 400px;
      box-shadow: 0 10px 30px rgba(0, 0, 0, 0.5);
    }
    .login-box h2 {
      text-align: center;
      margin-bottom: 25px;
      font-size: 26px;
      font-weight: 700;
      color: #00ffe1;
    }
    .login-box input[type="text"],
    .login-box input[type="password"] {
      width: 100%;
      padding: 12px 20px;
      margin-bottom: 20px;
      border: none;
      border-radius: 10px;
      background: #292d3e;
      color: #fff;
      font-size: 16px;
    }
    .login-box button {
      width: 100%;
      padding: 12px;
      border: none;
      border-radius: 10px;
      background: linear-gradient(135deg, #00ffe1, #007cf0);
      font-weight: bold;
      font-size: 16px;
      color: #121212;
      cursor: pointer;
      transition: 0.3s;
    }
    .login-box button:hover {
      background: linear-gradient(135deg, #ff512f, #dd2476);
      color: white;
    }
    .login-box .forgot {
      text-align: center;
      margin-top: 15px;
    }
    .login-box .forgot a {
      color: #00ffe1;
      text-decoration: none;
      font-weight: 600;
    }
    .dashboard {
      display: none;
      margin-top: 40px;
      width: 100%;
      max-width: 800px;
    }
    .card {
      background: #2b2b2b;
      border-left: 6px solid #00ffe1;
      border-radius: 15px;
      padding: 20px;
      margin-bottom: 20px;
      transition: all 0.3s ease;
      box-shadow: 0 4px 20px rgba(0,0,0,0.4);
    }
    .card.seen {
      border-left-color: #888;
      opacity: 0.8;
    }
    .status {
      float: right;
      font-weight: bold;
      color: #00ffe1;
    }
    .card.seen .status {
      color: #aaa;
    }
    .done-btn,
    .delete-btn {
      margin-top: 10px;
      margin-right: 10px;
      background-color: #00ffe1;
      padding: 8px 15px;
      border-radius: 8px;
      color: #121212;
      font-weight: bold;
      cursor: pointer;
      transition: 0.3s;
      border: none;
    }
    .done-btn:hover {
      background-color: #4CAF50;
      color: white;
    }
    .delete-btn:hover {
      background-color: #ff3c3c;
      color: white;
    }
    .verified {
      display: inline-block;
      margin-left: 10px;
      font-size: 16px;
      color: #4CAF50;
      font-weight: bold;
    }
    .premium {
      display: inline-block;
      margin-left: 10px;
      color: #ffcb00;
      font-weight: bold;
      font-size: 18px;
    }
    .premium-icon {
      font-size: 20px;
      color: gold;
      margin-left: 5px;
    }
    .footer {
      margin-top: 60px;
      font-size: 14px;
      text-align: center;
      color: #ccc;
    }
    @media(max-width: 600px){
      .login-box, .dashboard {
        padding: 20px;
      }
    }
  </style>
</head>
<body>
  <div class="login-box" id="loginBox">
    <h2>Admin Login</h2>
    <input type="text" id="uid" placeholder="Enter ID Number" />
    <input type="password" id="password" placeholder="Enter Password" />
    <button onclick="login()">Login</button>
    <div class="forgot">
      <a href="https://wa.me/message/BQ77IMY2MHW6E1" target="_blank">Forgot Password?</a>
    </div>
  </div>
  
  <div class="dashboard" id="dashboard">
    <h2 style="text-align:center; margin-bottom: 20px;">Bonus Claim Dashboard</h2>
    <div id="dataContainer"></div>
    <div class="footer">Powered by Developer Swygen Official</div>
  </div>

  <script type="module">
    import { initializeApp } from "https://www.gstatic.com/firebasejs/10.11.0/firebase-app.js";
    import { getFirestore, collection, getDocs, query, orderBy, doc, updateDoc, deleteDoc } from "https://www.gstatic.com/firebasejs/10.11.0/firebase-firestore.js";

    const firebaseConfig = {
      apiKey: "AIzaSyCdyMFDsMzkprimG3YKiS3Ipbq17rtASKI",
      authDomain: "game-wev.firebaseapp.com",
      projectId: "game-wev",
      storageBucket: "game-wev.appspot.com",
      messagingSenderId: "191658439875",
      appId: "1:191658439875:web:084d88f50f4dd6a6ee9e76"
    };

    const app = initializeApp(firebaseConfig);
    const db = getFirestore(app);

    window.login = async function () {
      const id = document.getElementById('uid').value;
      const pass = document.getElementById('password').value;
      if (id === '1472580369' && pass === 'swygenbd') {
        document.getElementById('loginBox').style.display = 'none';
        document.getElementById('dashboard').style.display = 'block';
        loadData();
      } else {
        alert("Invalid ID or Password");
      }
    }

    async function loadData() {
      const q = query(collection(db, "bonus_claims"), orderBy("time", "desc"));
      const querySnapshot = await getDocs(q);
      const container = document.getElementById("dataContainer");
      container.innerHTML = "";
      let serialNo = 1;
      querySnapshot.forEach((docSnap) => {
        const data = docSnap.data();
        const div = document.createElement("div");
        div.className = `card ${data.seen ? 'seen' : ''}`;
        div.innerHTML = `
          <h3>#NO - ${serialNo++} ${data.name} <span class="status">${data.seen ? 'Seen' : 'Unseen'}</span></h3>
          <p><strong>Email:</strong> ${data.email}</p>
          <p><strong>Telegram:</strong> ${data.telegram}</p>
          <p><strong>UID:</strong> ${data.uid}</p>
          <p><strong>Screenshot:</strong> <a href="${data.screenshot}" target="_blank">View</a></p>
          <button class="done-btn" onclick="markAsDone('${docSnap.id}')">
            ${data.verified ? 'Verified' : 'Done'}
          </button>
          <button class="delete-btn" onclick="deleteRequest('${docSnap.id}')">Delete</button>
          ${data.verified ? '<span class="verified">Verified</span>' : ''}
          ${data.premium ? '<span class="premium">Premium <span class="premium-icon">⭐</span></span>' : ''}
        `;
        container.appendChild(div);

        if (!data.seen) {
          updateDoc(doc(db, "bonus_claims", docSnap.id), { seen: true });
        }
      });
    }

    window.markAsDone = async function(docId) {
      const docRef = doc(db, "bonus_claims", docId);
      await updateDoc(docRef, { verified: true });
      loadData(); // Refresh the data
    }

    window.deleteRequest = async function(docId) {
      const confirmation = confirm("Are you sure you want to delete this request?");
      if (confirmation) {
        await deleteDoc(doc(db, "bonus_claims", docId));
        loadData(); // Refresh the list after deletion
      }
    }
  </script>
</body>
</html>
