# My-online-shop
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>RUBIDI ONLINE STORE</title>

<style>
* {box-sizing:border-box}
body {
 margin:0;
 font-family:Arial,sans-serif;
 background:#f4f4f4;
 color:#111;
}
header {
 background:#111;
 color:white;
 text-align:center;
 padding:30px 10px;
}
header h1 {
 font-size:35px;
 letter-spacing:5px;
 margin:0;
}
header p {color:#ccc}
nav {
 background:#222;
 padding:15px;
 text-align:center;
}
nav a {
 color:white;
 text-decoration:none;
 margin:10px;
}
.container {padding:20px}
.products {
 display:grid;
 grid-template-columns:repeat(2,1fr);
 gap:15px;
}
.product {
 background:white;
 padding:12px;
 border-radius:10px;
 text-align:center;
 box-shadow:0 2px 8px #ddd;
}
.product img {
 width:100%;
 height:150px;
 object-fit:cover;
 border-radius:8px;
}
button {
 background:#111;
 color:white;
 padding:11px;
 border:0;
 border-radius:6px;
 margin:5px 0;
 cursor:pointer;
}
.delete {background:#d00}
input {
 width:100%;
 padding:12px;
 margin:7px 0;
 border:1px solid #ccc;
 border-radius:6px;
}
section {
 background:white;
 padding:20px;
 margin-top:25px;
 border-radius:10px;
}
footer {
 background:#111;
 color:white;
 text-align:center;
 padding:20px;
 margin-top:30px;
}
@media(max-width:350px) {
 .products {grid-template-columns:1fr}
}
</style>
</head>

<body>

<header>
<h1>RUBIDI</h1>
<p>ONLINE STORE</p>
<p>YOUR STYLE. YOUR CHOICE.</p>
</header>

<nav>
<a href="#home">Home</a>
<a href="#products">Shop</a>
<a href="#cart">Cart</a>
<a href="#admin">Admin</a>
</nav>

<div class="container" id="home">

<h2 style="text-align:center">WELCOME TO RUBIDI</h2>
<p style="text-align:center">
Discover quality products at affordable prices.
</p>

<h2 id="products">OUR PRODUCTS</h2>
<div class="products" id="productList"></div>

<section id="cart">
<h2>YOUR SHOPPING CART</h2>
<div id="cartItems">Your cart is empty.</div>
<h3 id="total">Total: KSh 0</h3>

<input id="customer" placeholder="Your full name">
<input id="phone" placeholder="Your phone number">

<button onclick="checkout()">
ORDER ON WHATSAPP
</button>
</section>

<section id="admin">
<h2>RUBIDI ADMIN PANEL</h2>
<p>Add a new product below.</p>

<input id="productName" placeholder="Product name">
<input id="productPrice" type="number"
placeholder="Price in KSh">
<input id="productImage"
placeholder="Product image URL">

<button onclick="addProduct()">ADD PRODUCT</button>

</section>

</div>

<footer>
<h2>RUBIDI</h2>
<p>Thank you for shopping with us.</p>
<p>© 2026 RUBIDI ONLINE STORE</p>
</footer>

<script>

let products = JSON.parse(
 localStorage.getItem("rubidiProducts")
) || [];

let cart = [];

function saveProducts() {
 localStorage.setItem(
  "rubidiProducts",
  JSON.stringify(products)
 );
}

function showProducts() {
 let area = document.getElementById("productList");
 area.innerHTML = "";

 products.forEach((p,i) => {
 area.innerHTML += `
 <div class="product">
 <img src="${safe(p.image)}" alt="Product">
 <h3>${safe(p.name)}</h3>
 <p>KSh ${p.price.toLocaleString()}</p>
 <button onclick="addCart(${i})">ADD TO CART</button>
 <button class="delete"
 onclick="deleteProduct(${i})">DELETE</button>
 </div>`;
 });
}

function safe(value) {
 return String(value).replace(/[&<>"']/g, c => ({
 "&":"&amp;",
 "<":"&lt;",
 ">":"&gt;",
 '"':"&quot;",
 "'":"&#39;"
 })[c]);
}

function addProduct() {
 let name = document.getElementById("productName").value.trim();
 let price = Number(document.getElementById("productPrice").value);
 let image = document.getElementById("productImage").value.trim();

 if(!name || !Number.isFinite(price) || price<=0 ||
 !/^https?:\/\//i.test(image)) {
  alert("Enter a valid product name, price and image URL.");
  return;
 }

 products.push({name,price,image});
 saveProducts();
 showProducts();

 document.getElementById("productName").value="";
 document.getElementById("productPrice").value="";
 document.getElementById("productImage").value="";

 alert("Product added successfully!");
}

function deleteProduct(i) {
 if(confirm("Delete this product?")) {
  products.splice(i,1);
  saveProducts();
  showProducts();
 }
}

function addCart(i) {
 cart.push(products[i]);
 showCart();
}

function removeCart(i) {
 cart.splice(i,1);
 showCart();
}

function showCart() {
 let area = document.getElementById("cartItems");
 let total = 0;
 area.innerHTML = "";

 if(cart.length===0) {
  area.innerHTML="Your cart is empty.";
 }

 cart.forEach((p,i)=>{
  total += p.price;
  area.innerHTML += `
  <p>${safe(p.name)} - KSh ${p.price}
  <button onclick="removeCart(${i})">Remove</button>
  </p>`;
 });

 document.getElementById("total").innerText =
 "Total: KSh " + total.toLocaleString();
}

function checkout() {
 let name = document.getElementById("customer").value.trim();
 let phone = document.getElementById("phone").value.trim();

 if(!name || !phone || cart.length===0) {
  alert("Enter your details and add products first.");
  return;
 }

 // CHANGE THIS TO YOUR WHATSAPP NUMBER
 let whatsapp = "254700000000";

 let message = "NEW ORDER - RUBIDI\n";
 message += "Customer: "+name+"\n";
 message += "Phone: "+phone+"\n\n";

 let total=0;

 cart.forEach(p=>{
  message += p.name+" - KSh "+p.price+"\n";
  total+=p.price;
 });

 message += "\nTOTAL: KSh "+total;

 window.open(
  "https://wa.me/"+whatsapp+
  "?text="+encodeURIComponent(message),
  "_blank"
 );
}

showProducts();
showCart();

</script>

</body>
</html>
