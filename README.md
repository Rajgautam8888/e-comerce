<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>BookFlix OTP Login</title>

<style>
body { margin:0; font-family:Arial; background:#0d0d0d; color:#fff; }

/* Auth UI */
.auth {
  display:flex;
  justify-content:center;
  align-items:center;
  height:100vh;
  flex-direction:column;
}

input {
  padding:10px;
  margin:10px;
  width:250px;
  border-radius:5px;
  border:none;
}

button {
  padding:10px 20px;
  background:#e50914;
  color:#fff;
  border:none;
  cursor:pointer;
}

.hidden { display:none; }

/* Main UI */
header {
  padding:15px;
  background:#111;
  display:flex;
  justify-content:space-between;
}

.banner {
  height:200px;
  background:url('https://images.unsplash.com/photo-1512820790803-83ca734da794');
  background-size:cover;
  display:flex;
  align-items:center;
  padding:20px;
}

.row { display:flex; overflow:auto; }

.card {
  min-width:180px;
  background:#1a1a1a;
  margin:10px;
  border-radius:10px;
}

.card img { width:100%; height:200px; }
</style>
</head>

<body>

<!-- 🔐 STEP 1: EMAIL -->
<div id="emailPage" class="auth">
  <h2>Enter Gmail</h2>
  <input type="email" id="email" placeholder="Enter Gmail">
  <button onclick="sendOTP()">Send OTP</button>
  <p id="msg1"></p>
</div>

<!-- 🔢 STEP 2: OTP -->
<div id="otpPage" class="auth hidden">
  <h2>Enter OTP</h2>
  <input type="number" id="otpInput" placeholder="Enter OTP">
  <button onclick="verifyOTP()">Verify</button>
  <p id="msg2"></p>
</div>

<!-- 📚 MAIN PAGE -->
<div id="mainPage" class="hidden">

<header>
  <h2>📚 BookFlix</h2>
  <button onclick="logout()">Logout</button>
</header>

<div class="banner">
  <h2>Welcome to BookFlix</h2>
</div>

<div id="books"></div>

</div>

<script>

let generatedOTP = "";

// 📧 SEND OTP
function sendOTP(){
  let email = document.getElementById("email").value;

  if(!email.includes("@gmail.com")){
    document.getElementById("msg1").innerText = "Enter valid Gmail!";
    return;
  }

  // Generate OTP
  generatedOTP = Math.floor(1000 + Math.random()*9000).toString();

  alert("Your OTP is: " + generatedOTP); // Demo only

  document.getElementById("emailPage").classList.add("hidden");
  document.getElementById("otpPage").classList.remove("hidden");
}

// ✅ VERIFY OTP
function verifyOTP(){
  let userOTP = document.getElementById("otpInput").value;

  if(userOTP === generatedOTP){
    localStorage.setItem("userLoggedIn", "true");

    document.getElementById("otpPage").classList.add("hidden");
    document.getElementById("mainPage").classList.remove("hidden");

    loadBooks();
  } else {
    document.getElementById("msg2").innerText = "Wrong OTP!";
  }
}

// 🔓 LOGOUT
function logout(){
  localStorage.removeItem("userLoggedIn");
  location.reload();
}

// 📚 BOOKS
function loadBooks(){
  const container = document.getElementById("books");

  for(let i=1;i<=12;i++){
    let section = document.createElement("div");
    section.innerHTML = `<h3>Class ${i}</h3>`;

    let row = document.createElement("div");
    row.className = "row";

    ["Physics","Math","Science"].forEach(sub=>{
      let card = document.createElement("div");
      card.className = "card";
      card.innerHTML = `
        <img src="https://source.unsplash.com/200x300/?${sub},book">
        <p>${sub} Book</p>
        <button onclick="buy('${sub}')">Buy</button>
      `;
      row.appendChild(card);
    });

    section.appendChild(row);
    container.appendChild(section);
  }
}

// 💰 BUY
function buy(name){
  alert("Proceeding to payment for " + name);
}

// 🔁 AUTO LOGIN
if(localStorage.getItem("userLoggedIn") === "true"){
  document.getElementById("emailPage").classList.add("hidden");
  document.getElementById("mainPage").classList.remove("hidden");
  loadBooks();
}

</script>

</body>
</html>
