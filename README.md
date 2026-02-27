<!DOCTYPE html>
<html lang="it">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Nome Locale | Prenotazioni</title>

<link href="https://fonts.googleapis.com/css2?family=Poppins:wght@400;600;700&display=swap" rel="stylesheet">
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/flatpickr/dist/flatpickr.min.css">

<style>
:root{
  --primary:#c59d5f;
  --card:rgba(255,255,255,0.05);
  --danger:#ff4d4d;
  --success:#25D366;
}

body{
  font-family:'Poppins',sans-serif;
  min-height:100vh;
  display:flex;
  align-items:center;
  justify-content:center;
  background:radial-gradient(circle at top,#1c1c1c,#0f0f0f);
  color:white;
  margin:0;
}

.card{
  width:100%;
  max-width:420px;
  padding:35px 25px;
  border-radius:25px;
  backdrop-filter:blur(15px);
  background:var(--card);
  border:1px solid rgba(255,255,255,0.08);
  box-shadow:0 25px 60px rgba(0,0,0,0.6);
  text-align:center;
}

.logo{
  width:140px;
  margin-bottom:15px;
  border-radius:50%;
  border:3px solid var(--primary);
}

h1{font-size:24px;margin-bottom:5px;}
.subtitle{font-size:13px;color:#aaa;margin-bottom:25px;}

.btn{
  display:flex;
  align-items:center;
  justify-content:center;
  gap:12px;
  width:100%;
  padding:16px;
  margin-top:14px;
  border-radius:14px;
  font-weight:600;
  text-decoration:none;
  color:white;
  cursor:pointer;
  transition:0.3s;
  border:none;
}

.btn:hover{
  transform:translateY(-3px);
  box-shadow:0 8px 20px rgba(0,0,0,0.4);
}

.btn:disabled{
  opacity:0.6;
  cursor:not-allowed;
  transform:none;
}

.btn img{
  width:22px;
  height:22px;
}

.instagram { background: linear-gradient(45deg,#f09433,#e6683c,#dc2743,#cc2366,#bc1888);}
.whatsapp { background:#25D366; }
.trustpilot { background:#00b67a; }
.google { background:#4285F4; }
.thefork { background:#00C851; }

input,textarea{
  width:100%;
  padding:12px;
  border-radius:12px;
  border:none;
  margin-bottom:10px;
  font-size:14px;
  outline:none;
}

#calendarContainer{
  display:none;
  margin-top:15px;
}

.message{
  margin-top:10px;
  font-size:13px;
}

.footer{
  margin-top:30px;
  padding-top:15px;
  border-top:1px solid rgba(255,255,255,0.08);
  font-size:11px;
  color:#777;
}
</style>
</head>

<body>

<div class="card">

<img src="https://i.ibb.co/7Q3VwWk/logo.png" class="logo" alt="Logo Locale">

<h1>Nome Locale</h1>
<div class="subtitle">Benvenuto! Prenota o scopri i nostri canali</div>

<!-- PULSANTI SOCIAL -->
<a class="btn instagram" href="https://www.instagram.com" target="_blank">
<img src="https://cdn-icons-png.flaticon.com/512/2111/2111463.png">Instagram
</a>

<a class="btn whatsapp" href="https://wa.me/393311799476" target="_blank">
<img src="https://cdn-icons-png.flaticon.com/512/733/733585.png">WhatsApp
</a>

<a class="btn trustpilot" href="https://www.trustpilot.com/" target="_blank">
<img src="https://upload.wikimedia.org/wikipedia/commons/5/5c/Trustpilot_logo.png">Trustpilot
</a>

<a class="btn google" href="https://www.google.com/" target="_blank">
<img src="https://cdn-icons-png.flaticon.com/512/300/300221.png">Google
</a>

<a class="btn thefork" href="https://www.thefork.it/" target="_blank">
<img src="https://upload.wikimedia.org/wikipedia/commons/7/75/TheFork_logo.png">TheFork
</a>

<!-- PULSANTE PRENOTAZIONE -->
<button id="bookingBtn" class="btn" style="background:var(--primary);">
🍽️ Prenota un tavolo
</button>

<!-- FORM PRENOTAZIONE -->
<div id="calendarContainer">
<input type="text" id="clientName" placeholder="Nome cliente" maxlength="40">
<input type="text" id="bookingDate" placeholder="Seleziona data e ora">
<input type="number" id="people" placeholder="Numero persone" min="1" max="20">
<textarea id="notes" placeholder="Note (facoltativo)" maxlength="150"></textarea>

<button id="sendBooking" class="btn" style="background:var(--primary);">
Invia prenotazione
</button>

<div id="responseMessage" class="message"></div>
</div>

<div class="footer">
Powered by <strong style="color: var(--primary); letter-spacing:1px;">TUTOT</strong>
</div>

</div> <!-- FINE CARD -->

<script src="https://cdn.jsdelivr.net/npm/flatpickr"></script>
<script>
// ---------------- CONFIGURAZIONE ----------------
const CONFIG = {
  scriptURL: "https://script.google.com/macros/s/IL_TUO_SCRIPT_ID/exec", // <- inserisci qui la URL della Web App
  maxPeople: 20,
  openingHours: { lunch: { start: "12:00", end: "15:00" }, dinner: { start: "19:00", end: "23:30" } },
  closedDays: [1], // 1 = Lunedì chiuso
  numeroWhatsAppLocale: "393311799476" // numero ristorante
};
// -----------------------------------------------

const bookingBtn = document.getElementById('bookingBtn');
const calendarContainer = document.getElementById('calendarContainer');
const nameInput = document.getElementById('clientName');
const dateInput = document.getElementById('bookingDate');
const peopleInput = document.getElementById('people');
const notesInput = document.getElementById('notes');
const sendBtn = document.getElementById('sendBooking');
const responseMessage = document.getElementById('responseMessage');

let calendarInitialized = false;

// Mostra il form di prenotazione
bookingBtn.addEventListener('click', () => {
  calendarContainer.style.display='block';
  bookingBtn.style.display='none';

  if(!calendarInitialized){
    flatpickr(dateInput,{
      enableTime:true,
      dateFormat:"Y-m-d H:i",
      minDate:"today",
      time_24hr:true,
      disable:[
        function(date){ return CONFIG.closedDays.includes(date.getDay()); }
      ]
    });
    calendarInitialized = true;
  }
});

// Mostra messaggi lato cliente
function showMessage(text,type){
  responseMessage.textContent = text;
  responseMessage.style.color = type==="error" ? "#ff4d4d" : type==="success" ? "#25D366" : "#ccc";
}

// Validazione form
function validateForm(){
  const name = nameInput.value.trim();
  const date = dateInput.value.trim();
  const people = parseInt(peopleInput.value);
  if(name.length < 2){ showMessage("Inserisci un nome valido.","error"); return false; }
  if(!date){ showMessage("Seleziona data e ora.","error"); return false; }
  if(!people || people < 1 || people > CONFIG.maxPeople){ showMessage("Numero persone non valido.","error"); return false; }
  return true;
}

// Invio prenotazione al Google Apps Script + WhatsApp
sendBtn.addEventListener('click', async () => {
  if(!validateForm()) return;
  sendBtn.disabled = true;
  showMessage("Invio in corso...","info");

  const data = {
    type:"booking",
    name:nameInput.value.trim(),
    date:dateInput.value.trim(),
    people:peopleInput.value.trim(),
    notes:notesInput.value.trim(),
    timestamp:new Date().toISOString()
  };

  try{
    const response = await fetch(CONFIG.scriptURL,{
      method:"POST",
      headers:{"Content-Type":"application/json"},
      body:JSON.stringify(data)
    });
    const result = await response.json();

    if(result.status==="ok"){
      showMessage("Prenotazione inviata con successo!","success");
      sendBtn.style.display="none";

      // ---------- APRI WHATSAPP ----------
      const msg = `Ciao! Ho appena effettuato una prenotazione presso Nome Locale.\n\n` +
                  `Nome: ${data.name}\nData: ${data.date}\nPersone: ${data.people}\nNote: ${data.notes}`;
      const urlWhatsApp = `https://wa.me/${CONFIG.numeroWhatsAppLocale}?text=${encodeURIComponent(msg)}`;
      window.open(urlWhatsApp,'_blank');
      // -------------------------------------

    } else{
      showMessage("Errore server.","error");
      sendBtn.disabled=false;
    }
  }catch(error){
    showMessage("Errore di connessione.","error");
    sendBtn.disabled=false;
  }
});

// Conta visite NFC automaticamente
window.addEventListener("load", async () => {
  try{
    await fetch(CONFIG.scriptURL,{
      method:"POST",
      headers:{"Content-Type":"application/json"},
      body: JSON.stringify({type:"visit",timestamp:new Date().toISOString()})
    });
  }catch(e){}
});
</script>
</body>
</html>
