
<html lang="en">
<head>
   <head>
<meta charset="UTF-8">
<title>BookFlix</title>

<script src="https://cdnjs.cloudflare.com/ajax/libs/qrcodejs/1.0.0/qrcode.min.js"></script>

<style>
body {
  margin:0;
  font-family:Arial;
  background:#0d0d0d;
  color:#fff;
}
/* logout {
  padding:10px 20px;
  background:#e50914;
  color:#fff;
  border:none;
  cursor:pointer;
  transition:0.3s;
  curve:10px;
  corners:10px
} */

/* AUTH */
.auth {
  display:flex;
  justify-content:center;
  align-items:center;
  height:100vh;
  flex-direction:column;
  animation:fadeIn 1s;
}
/* LOGIN BUTTON */
button {
  padding:15px 25px;
  background:#e50914;
  color:#fff;
  border:none;
  cursor:pointer;
  transition:0.3s;
}
button:hover {
  transform:scale(1.1);
  background:rgba(34, 144, 178, 0);
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
  transition:0.3s;
}
button:hover {
  transform:scale(1.1);
  background:rgba(34, 144, 178, 0);
}

.hidden { display:none; }

/* HEADER */
header {
  padding:15px;
  background:#111;
  display:flex;
  justify-content:space-between;
}

/* BANNER */
.banner {
  height:220px;
  background:url('https://images.unsplash.com/photo-1512820790803-83ca734da794');
  background-size:cover;
  display:flex;
  align-items:center;
  padding:20px;
  animation:zoomIn 2s;
  radius-bottom:10px;
}

/* BOOK ROW */
.row {
  display:flex;
  overflow:auto;
}

/* CARD */
.card {
  min-width:180px;
  background:#1a1a1a;
  margin:10px;
  border-radius:10px;
  padding:10px;
  text-align:center;
  transition:0.4s;
}
.card:hover {
  transform:scale(1.1);
  box-shadow:0 0 15px red;
}

.card img {
  width:100%;
  height:200px;
  border-radius:8px;
}

/* PAYMENT BOX */
#paymentBox {
  position:fixed;
  top:50%;
  left:50%;
  transform:translate(-50%,-50%);
  background:#111;
  padding:20px;
  border-radius:10px;
  text-align:center;
  animation:fadeIn 0.5s;
}

/* ANIMATIONS */
@keyframes fadeIn {
  from {opacity:0;}
  to {opacity:1;}
}

@keyframes zoomIn {
  from {transform:scale(1.2);}
  to {transform:scale(1);}
}
#h2 {
  font-size: 24px;
  margin-bottom: 10px;
  border-bottom: 2px solid #09e552;
  display: inline-block;
  animation: fadeIn 1s;
}

</style>
</head>

<body>

<!-- LOGIN -->
<div id="authPage" class="auth">
  <h2 id="authTitle">Login</h2>

  <input type="email" id="authEmail" placeholder="Enter Email">
  <input type="password" id="authPassword" placeholder="Enter Password">

  <button onclick="handleAuth()">Login</button>

  <p onclick="toggleAuth()" style="cursor:pointer;color:#ccc;">
    Don't have account? Signup
  </p>

  <p id="authMsg"></p>
</div>

<!-- MAIN -->
<div id="mainPage" class="hidden">

<header>
  <button onclick="logout()">Logout</button>
</header>

<div class="banner">
  <h2> Welcome to BookFlix</h2>
</div>

<div id="books"></div>

</div>

<!-- PAYMENT -->
<div id="paymentBox" class="hidden">
  <h3 id="bookName"></h3>
  <div id="qrcode"></div>

  <p>UPI: 9140660520@ptyes</p>

  <button onclick="downloadQR()">⬇ Download QR</button>
  <button onclick="paymentDone()">✅ Payment Done</button>
  <button onclick="closePayment()">❌ Close</button>
</div>


<script>
    function buy(name,price){
  paymentBox.classList.remove("hidden");
  bookName.innerText=name+" - ₹"+price;

  qrcode.innerHTML="";

  let upi=`upi://pay?pa=9140660520@ptyes&pn=BookFlix&am=${price}&cu=INR`;

  new QRCode(qrcode,{
    text:upi,
    width:200,
    height:200
  });

  localStorage.setItem("lastOrder", JSON.stringify({name,price}));
}

function paymentDone(){
  let order = JSON.parse(localStorage.getItem("lastOrder"));
  alert("✅ Payment Received for " + order.name);
  closePayment();
}

function downloadQR(){
  let img = document.querySelector("#qrcode img");

  if(img){
    let link = document.createElement("a");
    link.href = img.src;
    link.download = "payment_qr.png";
    link.click();
  }
}

function closePayment(){
  paymentBox.classList.add("hidden");
}

// LOGIN
let isLogin = true;

function toggleAuth(){
  isLogin = !isLogin;
  authTitle.innerText = isLogin ? "Login" : "Signup";
}

// AUTH
function handleAuth(){
  let email = authEmail.value;
  let password = authPassword.value;

  if(email===""||password===""){
    authMsg.innerText="Fill all fields!";
    return;
  }

  let users = JSON.parse(localStorage.getItem("users"))||[];

  if(isLogin){
    let user = users.find(u=>u.email===email && u.password===password);
    if(user){
      localStorage.setItem("loggedIn","true");
      openMain();
    }else{
      authMsg.innerText="Invalid Login!";
    }
  }else{
    users.push({email,password});
    localStorage.setItem("users",JSON.stringify(users));
    authMsg.innerText="Signup Success!";
    toggleAuth();
  }
}

// OPEN MAIN
function openMain(){
  authPage.classList.add("hidden");
  mainPage.classList.remove("hidden");
  loadBooks();
}

// LOGOUT
function logout(){
  localStorage.removeItem("loggedIn");
  location.reload();
}

// BOOKS
function loadBooks(){
  const container = document.getElementById("books");
  container.innerHTML="";

  const books = [
      {name:"Physics NCERT",price:100,img:"https://covers.openlibrary.org/b/id/240727-L.jpg"},
    {name:"Math NCERT",price:100,img:"https://covers.openlibrary.org/b/id/8231856-L.jpg"},
    {name:"Science NCERT",price:90,img:"https://covers.openlibrary.org/b/id/10521256-L.jpg"},
    {name:"Chemistry NCERT",price:110,img:"https://covers.openlibrary.org/b/id/11153231-L.jpg"},
    {name:"Biology NCERT",price:120,img:"https://covers.openlibrary.org/b/id/10958379-L.jpg"},
    {name:"English NCERT",price:95,img:"https://covers.openlibrary.org/b/id/8226191-L.jpg"},
    {name:"History NCERT",price:85,img:"https://covers.openlibrary.org/b/id/8235116-L.jpg"},
    {name:"Geography NCERT",price:80,img:"https://covers.openlibrary.org/b/id/8235116-L.jpg"}
    
  ];

  for(let i=1;i<=12;i++){
    let section=document.createElement("div");
    section.innerHTML=`<h3>Class ${i}</h3>`;

    let row=document.createElement("div");
    row.className="row";

    books.forEach(book=>{
      let card=document.createElement("div");
      card.className="card";

      card.innerHTML=`
        <img src="${book.img}">
        <p>${book.name}</p>
        <p>₹${book.price}</p>
        <button onclick="buy('${book.name}',${book.price})">Buy</button>
      `;

      row.appendChild(card);
    });

    section.appendChild(row);
    container.appendChild(section);
  }
}

// BUY + QR
function buy(name,price){
  paymentBox.classList.remove("hidden");
  bookName.innerText=name+" - ₹"+price;

  qrcode.innerHTML="";
  let upi=`upi://pay?pa=9140660520@ptyes&pn=BookFlix&am=${price}&cu=INR`;

  new QRCode(qrcode,{
    text:upi,
    width:200,
    height:200
  });
}

// CLOSE
function closePayment(){
  paymentBox.classList.add("hidden");
}

// AUTO LOGIN
// if(localStorage.getItem("loggedIn")==="true"){
//   openMain();
// }
// searchInput.addEventListener("input", function(){
//   let query = this.value.toLowerCase();
//   let cards = document.querySelectorAll(".card");

//   cards.forEach(card=>{
//     let title = card.querySelector("p").innerText.toLowerCase();
//     if(title.includes(query)){
//       card.style.display = "block";
//     }else{
//       card.style.display = "none";
//     }
//   });
// });
text.addEventListener("input", function(){
  let query = this.value.toLowerCase();
  let cards = document.querySelectorAll(".card");

  cards.forEach(card=>{
    let title = card.querySelector("p").innerText.toLowerCase();
    if(title.includes(query)){
      card.style.display = "block";
    }else{
      card.style.display = "none";
    }
  });
});

</script>

</body>
</html>
