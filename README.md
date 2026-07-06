# alzvcy-repository
<!DOCTYPE html>
<html lang="id">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width,initial-scale=1.0" />
<title>Special Letter</title>
<link href="https://fonts.googleapis.com/css2?family=Poppins:wght@300;500;700&family=Patrick+Hand&display=swap" rel="stylesheet">
<style>
*{margin:0;padding:0;box-sizing:border-box;font-family:'Poppins',sans-serif;}
body{overflow:hidden;background:linear-gradient(135deg,#FFF7F8,#FFE8F1,#F4F1FF,#DDF4FF);height:100vh;}
section{width:100%;height:100vh;position:absolute;top:0;left:0;display:flex;justify-content:center;align-items:center;flex-direction:column;transition:1s;}
#slide2{left:100%;background:linear-gradient(135deg,#FFF7F6,#FFF2FB,#EAF8FF);}
.circle{position:absolute;border-radius:50%;opacity:.35;animation:float 6s ease-in-out infinite;}
.c1{width:180px;height:180px;background:#ffd6e7;left:-50px;top:-30px;}
.c2{width:250px;height:250px;background:#d7efff;right:-80px;bottom:-80px;animation-delay:1s;}
.c3{width:120px;height:120px;background:#fbe8ff;top:60%;left:15%;animation-delay:2s;}
@keyframes float{0%{transform:translateY(0px);}50%{transform:translateY(-20px);}100%{transform:translateY(0px);}}
.title{font-size:48px;font-weight:bold;color:#ff78ab;margin-bottom:20px;text-shadow:0 0 15px white;}
.subtitle{font-size:20px;color:#666;margin-bottom:50px;}
.star{font-size:140px;cursor:pointer;transition:.5s;user-select:none;animation:blink 2s infinite;}
.star:hover{transform:scale(1.2);}
@keyframes blink{0%{filter:drop-shadow(0 0 0px gold);}50%{filter:drop-shadow(0 0 30px gold);}100%{filter:drop-shadow(0 0 0px gold);}}
.spin{animation:spin 1.5s;}
@keyframes spin{0%{transform:scale(1) rotate(0);}50%{transform:scale(1.4) rotate(360deg);}100%{transform:scale(1) rotate(720deg);}}
#message{margin-top:30px;font-size:26px;font-family:'Patrick Hand',cursive;color:#ff4d88;height:40px;}
button{padding:15px 40px;border:none;margin-top:50px;border-radius:50px;background:#ff8cb6;color:white;font-size:20px;cursor:pointer;display:none;transition:.4s;}
button:hover{transform:scale(1.08);}
.envelope{font-size:180px;cursor:pointer;transition:.5s;}
.envelope:hover{transform:scale(1.08);}
input{margin-top:20px;padding:15px;width:300px;font-size:18px;border:none;border-radius:15px;text-align:center;}
#openBtn{display:block;background:#ff82ad;}
.letter{width:650px;height:420px;background:white;padding:40px;border-radius:25px;box-shadow:0 20px 40px rgba(0,0,0,.15);display:none;margin-top:30px;overflow:auto;position:relative;z-index:5;}
.letter h2{color:#ff69a8;margin-bottom:15px;}
.letter p{line-height:32px;font-size:18px;}
/* PDF viewer fullscreen */
#pdfViewer{display:none;position:fixed;top:0;left:0;width:100%;height:100%;border:none;z-index:999;background:#fff;}
#closePdf{position:fixed;top:16px;right:16px;z-index:1001;display:none;padding:10px 14px;border-radius:10px;background:#ff567f;color:white;border:none;cursor:pointer;box-shadow:0 6px 18px rgba(0,0,0,.12);}
#openNew{position:fixed;top:16px;left:16px;z-index:1001;display:none;padding:10px 14px;border-radius:10px;background:#4b9cff;color:white;border:none;cursor:pointer;box-shadow:0 6px 18px rgba(0,0,0,.12);}
.heart{position:fixed;font-size:25px;animation:rain linear forwards;}
@keyframes rain{0%{transform:translateY(-100px);}100%{transform:translateY(110vh);}}
</style>
</head>
<body>

<div class="circle c1"></div>
<div class="circle c2"></div>
<div class="circle c3"></div>

<section id="slide1">
  <div class="title">For Someone Special ✨</div>
  <div class="subtitle">Klik bintang di bawah ini...</div>
  <div id="star" class="star">⭐</div>
  <div id="message"></div>
  <button id="nextBtn">Lanjut 💌</button>
</section>

<section id="slide2">
  <div class="envelope">💌</div>
  <h2 style="margin-bottom:20px;color:#ff6fa5;">Masukkan Namamu</h2>
  <input id="nama" placeholder="Tulis namamu...">
  <button id="openBtn">Buka Surat</button>

  <div class="letter" id="letter">
    <h2 id="judul"></h2>
    <p id="isi"></p>
  </div>
</section>

<!-- Embedded PDF iframe and controls -->
<iframe id="pdfViewer" src="" title="PDF Viewer"></iframe>
<button id="closePdf">Tutup PDF</button>
<button id="openNew">Buka di tab baru</button>

<script>
/* ====== CONFIG ======
   Ganti nilai DRIVE_PREVIEW jika ingin ambil file lain.
   Link ini sudah dikonversi ke Google Drive preview URL:
   https://drive.google.com/file/d/1ZEQ4kY0BrwiW4guHxZ492g6smMzjpiWK/preview
   Pastikan file di Google Drive diset "Anyone with the link - Viewer".
*/
const DRIVE_PREVIEW = 'https://drive.google.com/file/d/1ZEQ4kY0BrwiW4guHxZ492g6smMzjpiWK/preview';

const star=document.getElementById("star");
const message=document.getElementById("message");
const nextBtn=document.getElementById("nextBtn");
const slide1=document.getElementById("slide1");
const slide2=document.getElementById("slide2");
const openBtn=document.getElementById("openBtn");
const nama=document.getElementById("nama");
const judul=document.getElementById("judul");
const isi=document.getElementById("isi");
const letter=document.getElementById("letter");
const pdf=document.getElementById("pdfViewer");
const closePdf=document.getElementById("closePdf");
const openNew=document.getElementById("openNew");

let sudahKlik=false;
const kalimat=["Halo... 😊","Aku punya sesuatu untukmu ✨","Semoga harimu selalu menyenangkan 🌸","Klik tombol di bawah ya 💌"];

function ketik(teks,index=0){
  if(index<teks.length){
    message.innerHTML+=teks.charAt(index);
    setTimeout(()=>{ ketik(teks,index+1); },60);
  }
}

star.onclick=function(){
  if(sudahKlik) return;
  sudahKlik=true;
  star.classList.add("spin");
  let i=0;
  function tampil(){
    if(i>=kalimat.length){ nextBtn.style.display="block"; return; }
    message.innerHTML="";
    ketik(kalimat[i]);
    i++;
    setTimeout(tampil,2500);
  }
  setTimeout(tampil,1200);
}

nextBtn.onclick=function(){ slide1.style.left="-100%"; slide2.style.left="0"; }

function hujanLove(){
  const love=document.createElement("div");
  love.className="heart";
  love.innerHTML=Math.random()>0.5?"💖":"💕";
  love.style.left=Math.random()*100+"vw";
  love.style.fontSize=(20+Math.random()*30)+"px";
  love.style.animationDuration=(3+Math.random()*3)+"s";
  document.body.appendChild(love);
  setTimeout(()=>{ love.remove(); },6000);
}
function mulaiLove(){ setInterval(hujanLove,180); }

function ketikSurat(teks,index=0){
  if(index<teks.length){
    isi.innerHTML+=teks.charAt(index);
    setTimeout(()=>{ ketikSurat(teks,index+1); },35);
  }
}

/* Tampilkan PDF di iframe dan buka di tab baru (trigger dilakukan dari klik pengguna, jadi popup biasanya diizinkan) */
function showPdfAndOpenNewTab(previewUrl){
  pdf.src = previewUrl;
  pdf.style.display = 'block';
  closePdf.style.display = 'block';
  openNew.style.display = 'block';
  openNew.onclick = ()=> window.open(previewUrl, '_blank');

  // Buka di tab baru juga secara otomatis (karena ini dipanggil dari handler klik user, browser biasanya mengizinkan)
  window.open(previewUrl, '_blank');

  // Simple detection: jika embedding diblokir by X-Frame-Options, user can still view in new tab.
  setTimeout(()=>{
    // nothing intrusive here; just keep iframe for users whose browser supports embedding
  },1500);
}

/* Event buka surat */
openBtn.onclick=function(){
  let n=nama.value.trim();
  if(n==""){ alert("Isi namamu dulu ya 😊"); return; }
  judul.innerHTML="Untuk "+n+" ❤️";
  letter.style.display="block";
  isi.innerHTML="";
  const surat=`Hai ${n},

Terima kasih sudah mau membuka surat kecil ini.

Semoga hari-harimu selalu dipenuhi kebahagiaan, kesehatan, dan hal-hal baik.

Tetaplah menjadi seseorang yang selalu tersenyum walaupun dunia sedang tidak baik-baik saja.

Jangan lupa kalau kamu adalah orang yang berharga.

Semoga semua impianmu satu per satu bisa tercapai.

Terima kasih sudah hadir.

Semoga setelah membaca surat ini, kamu selalu merasa dicintai dan dihargai.

Have a wonderful day 🤍`;
  ketikSurat(surat);
  mulaiLove();

  // Tampilkan & buka PDF setelah surat ditampilkan beberapa detik
  setTimeout(()=>{
    showPdfAndOpenNewTab(DRIVE_PREVIEW);
  },9000);
}

/* tombol tutup PDF */
closePdf.onclick = function(){
  pdf.style.display = 'none';
  pdf.src = '';
  closePdf.style.display = 'none';
  openNew.style.display = 'none';
}
</script>

</body>
</html>
