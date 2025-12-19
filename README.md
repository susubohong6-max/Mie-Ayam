<!DOCTYPE html>
<html lang="id">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Toko Online + Konfirmasi Pesanan</title>
<style>
body { margin:0; font-family: Arial, sans-serif; background:#f2f2f2; }
header { background:#ff6b00; color:white; text-align:center; padding:15px; font-size:20px; font-weight:bold; }
.container { padding:15px; }
.product { background:white; border-radius:10px; padding:10px; margin-bottom:15px; box-shadow:0 2px 5px rgba(0,0,0,0.1); }
.product img { width:100%; border-radius:10px; }
.product h3 { margin:10px 0 5px; }
.price { color:#ff6b00; font-weight:bold; margin-bottom:10px; }
.btn { background:#ff6b00; color:white; border:none; width:100%; padding:12px; border-radius:8px; font-size:16px; font-weight:bold; cursor:pointer; }
.cart { background:white; border-radius:10px; padding:15px; margin-top:20px; box-shadow:0 2px 5px rgba(0,0,0,0.1); }
.cart h3 { margin-top:0; }
.cart-item { display:flex; justify-content:space-between; align-items:center; margin-bottom:8px; font-size:14px; }
.cart-item div { display:flex; align-items:center; }
.qty-btn { background:#ccc; border:none; padding:4px 8px; margin:0 4px; cursor:pointer; border-radius:4px; font-weight:bold; }
.remove-btn { background:transparent; border:none; color:#ff0000; cursor:pointer; font-size:14px; margin-left:8px; }
.total { font-weight:bold; margin-top:10px; font-size:16px; }
.checkout { background:#25D366; color:white; border:none; width:100%; padding:12px; border-radius:8px; font-size:16px; font-weight:bold; margin-top:10px; cursor:pointer; }

/* Modal Popup */
.modal { display:none; position:fixed; top:0; left:0; width:100%; height:100%; background:rgba(0,0,0,0.5); z-index:1000; }
.modal-content { background:white; width:90%; max-width:400px; margin:100px auto; padding:20px; border-radius:10px; }
.modal-content h3 { margin-top:0; }
.modal-content label { display:block; margin-top:10px; font-weight:bold; }
.modal-content input, .modal-content textarea, .modal-content select { width:100%; padding:6px; margin-top:5px; box-sizing:border-box; border-radius:5px; border:1px solid #ccc; }
.modal-content button { margin-top:10px; padding:10px; width:100%; border:none; border-radius:6px; font-weight:bold; cursor:pointer; }
#confirmBtn { background:#25D366; color:white; }
#cancelBtn { background:red; color:white; }

footer { text-align:center; padding:10px; font-size:12px; color:#666; margin-top:20px; }
</style>
</head>
<body>

<header>Mie Ayam Endang</header>

<div class="container">

<!-- PRODUK -->
<div class="product">
<img src="mie-ayam.jpg" alt="Mie Ayam">
<h3>Mie Ayam</h3>
<div class="price">Rp 9.000</div>
<button class="btn" onclick="addToCart('Mie Ayam',9000)">Tambah ke Keranjang</button>
</div>

<div class="product">
<img src="bakso.jpg" alt="Bakso">
<h3>Bakso</h3>
<div class="price">Rp 7.000</div>
<button class="btn" onclick="addToCart('Bakso',7000)">Tambah ke Keranjang</button>
</div>

<div class="product">
<img src="es-dawet.jpg" alt="Es Dawet">
<h3>Es Dawet</h3>
<div class="price">Rp 3.000</div>
<button class="btn" onclick="addToCart('Es Dawet',3000)">Tambah ke Keranjang</button>
</div>

<!-- KERANJANG -->
<div class="cart">
<h3>🛒 Keranjang Belanja</h3>
<div id="cartItems">Keranjang masih kosong</div>
<div class="total" id="totalHarga">Total: Rp 0</div>
<button class="checkout" onclick="openModal()">Checkout</button>
</div>

</div>

<!-- MODAL KONFIRMASI -->
<div class="modal" id="confirmModal">
<div class="modal-content">
<h3>Konfirmasi Pesanan</h3>
<label>Nama Pemesan</label>
<input type="text" id="namaPemesan" placeholder="Nama lengkap">

<label>Nomor Telepon</label>
<input type="text" id="nomorPemesan" placeholder="08xxxxxxxxxx">

<label>Metode Pengiriman</label>
<select id="metodePengiriman" onchange="toggleAlamat()">
<option value="ambil">Ambil di Tempat</option>
<option value="antar">Diantar (+Rp 1.000)</option>
</select>

<div id="alamatBox" style="display:none;">
<label>Alamat Lengkap</label>
<textarea id="alamatPemesan" placeholder="Desa, RT/RW, dsb"></textarea>
</div>

<button id="confirmBtn" onclick="checkoutWA()">Konfirmasi & Checkout</button>
<button id="cancelBtn" onclick="closeModal()">Batal</button>
</div>
</div>

<footer>
© 2025 Mie Ayam Teh Endang
</footer>

<script>
let cart = [];
let total = 0;

function addToCart(nama,harga){
const idx = cart.findIndex(i=>i.nama===nama);
if(idx>-1){ cart[idx].qty+=1; } else { cart.push({nama,harga,qty:1}); }
recalcTotal(); renderCart();
}

function recalcTotal(){
total = cart.reduce((sum,i)=>sum + i.harga*i.qty,0);
}

function renderCart(){
const cartItems = document.getElementById("cartItems");
cartItems.innerHTML="";
if(cart.length===0){ cartItems.innerHTML="Keranjang masih kosong"; }
else{
cart.forEach((item,index)=>{
cartItems.innerHTML+=`
<div class="cart-item">
<div>
<span>${index+1}. ${item.nama}</span>
<button class="remove-btn" onclick="removeItem(${index})">&times;</button>
</div>
<div>
<button class="qty-btn" onclick="changeQty(${index},-1)">-</button>
<span>${item.qty}</span>
<button class="qty-btn" onclick="changeQty(${index},1)">+</button>
<span style="margin-left:8px;">Rp ${(item.harga*item.qty).toLocaleString()}</span>
</div>
</div>
`;
});
}
document.getElementById("totalHarga").innerText="Total: Rp "+total.toLocaleString();
}

function changeQty(idx,delta){
if(cart[idx]){
cart[idx].qty+=delta;
if(cart[idx].qty<=0) cart.splice(idx,1);
recalcTotal(); renderCart();
}
}

function removeItem(idx){
if(cart[idx]){
cart.splice(idx,1); recalcTotal(); renderCart();
}
}

/* MODAL */
function openModal(){
if(cart.length===0){ alert("Keranjang masih kosong!"); return;}
document.getElementById("confirmModal").style.display="block";
}
function closeModal(){ document.getElementById("confirmModal").style.display="none"; }
function toggleAlamat(){
const val=document.getElementById("metodePengiriman").value;
document.getElementById("alamatBox").style.display= val==="antar" ? "block" : "none";
}

function checkoutWA(){
const nama=document.getElementById("namaPemesan").value.trim();
const nomor=document.getElementById("nomorPemesan").value.trim();
const metode=document.getElementById("metodePengiriman").value;
const alamat=document.getElementById("alamatPemesan").value.trim();

if(!nama){ alert("Nama pemesan wajib diisi!"); return; }
if(!nomor){ alert("Nomor pemesan wajib diisi!"); return; }
if(metode==="antar" && !alamat){ alert("Alamat wajib diisi!"); return; }

let totalFinal = total;
if(metode==="antar") totalFinal += 1000; // biaya antar

let pesan = `Halo, saya ingin pesan:%0A`;
cart.forEach((item,index)=>{
pesan += `${index+1}. ${item.nama} x${item.qty} - Rp ${(item.harga*item.qty).toLocaleString()}%0A`;
});
pesan += `%0ATotal: Rp ${totalFinal.toLocaleString()}`;
pesan += `%0ANama: ${nama}%0ANomor: ${nomor}%0AMetode: ${metode}`;
if(metode==="antar") pesan += `%0AAlamat: ${alamat}`;

window.open("https://wa.me/6281334927640?text=" + pesan, "_blank");
closeModal();
}
</script>

</body>
</html>
