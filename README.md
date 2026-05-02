# Bianco-Wash
<!DOCTYPE html>

<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Bianco Wash · Las Terrazas</title>
<link href="https://fonts.googleapis.com/css2?family=Bebas+Neue&family=DM+Sans:wght@300;400;500;600;700&display=swap" rel="stylesheet">
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.0/chart.umd.min.js"></script>

<!-- ════ FIREBASE SDK ════ -->

<script type="module">
  import { initializeApp } from "https://www.gstatic.com/firebasejs/10.12.0/firebase-app.js";
  import { getFirestore, collection, doc, addDoc, setDoc, updateDoc, deleteDoc,
           getDocs, onSnapshot, query, orderBy, serverTimestamp }
    from "https://www.gstatic.com/firebasejs/10.12.0/firebase-firestore.js";

  // ── Configuración real de Firebase — Bianco Wash ──
  const firebaseConfig = {
    apiKey: "AIzaSyBI3aQCGFfUyem2juzF5B7l8kl9-kC2fEQ",
    authDomain: "bianco-wash.firebaseapp.com",
    projectId: "bianco-wash",
    storageBucket: "bianco-wash.firebasestorage.app",
    messagingSenderId: "154890475092",
    appId: "1:154890475092:web:ff35a92e6e1895dd917fc1"
  };

  const app  = initializeApp(firebaseConfig);
  const db   = getFirestore(app);

  // Exponer db globalmente para que el código principal lo use
  window._db = db;
  window._fbReady = true;

  // Helpers globales
  window.fbCol  = (col)           => collection(db, col);
  window.fbDoc  = (col, id)       => doc(db, col, id);
  window.fbAdd  = (col, data)     => addDoc(collection(db, col), {...data, ts: serverTimestamp()});
  window.fbSet  = (col, id, data) => setDoc(doc(db, col, id), data, {merge:true});
  window.fbUpd  = (col, id, data) => updateDoc(doc(db, col, id), data);
  window.fbDel  = (col, id)       => deleteDoc(doc(db, col, id));
  window.fbGetAll = async (col)   => {
    const snap = await getDocs(collection(db, col));
    return snap.docs.map(d => ({id: d.id, ...d.data()}));
  };
  window.fbListen = (col, cb) => onSnapshot(collection(db, col), snap => {
    cb(snap.docs.map(d => ({id: d.id, ...d.data()})));
  });

  // Inicializar listeners en cuanto el DOM esté listo
  document.addEventListener('DOMContentLoaded', () => {
    window._initFirebaseListeners && window._initFirebaseListeners();
  });
</script>

<style>
:root{
  --bg:#060d1b;--surface:#0b1628;--surface2:#101f38;--surface3:#162540;
  --border:rgba(56,189,248,.13);--border2:rgba(56,189,248,.25);
  --blue:#0ea5e9;--blue-lt:#38bdf8;--blue-dk:#0369a1;--blue-glow:rgba(14,165,233,.2);
  --text:#ffffff;--text2:#cbd5e1;--muted:#94a3b8;
  --green:#22c55e;--orange:#f97316;--red:#ef4444;--gold:#fbbf24;
}
*,*::before,*::after{box-sizing:border-box;margin:0;padding:0}
html{scroll-behavior:smooth}
body{font-family:'DM Sans',sans-serif;background:var(--bg);color:var(--text);min-height:100vh;overflow-x:hidden}
input,select,textarea,button{font-family:inherit}
button{cursor:pointer;border:none;outline:none}
::-webkit-scrollbar{width:5px;height:5px}
::-webkit-scrollbar-track{background:var(--bg)}
::-webkit-scrollbar-thumb{background:var(--blue-dk);border-radius:3px}

/* TOAST */
#toast{position:fixed;bottom:24px;left:50%;transform:translateX(-50%) translateY(80px);
background:var(--surface2);border:1px solid var(--border2);color:#fff;padding:12px 22px;
border-radius:12px;font-size:.82rem;z-index:9999;transition:transform .3s;pointer-events:none}
#toast.show{transform:translateX(-50%) translateY(0)}

/* LOADER */
.fb-loading{display:flex;align-items:center;gap:10px;padding:20px;font-size:.8rem;color:var(--muted)}
.spinner{width:18px;height:18px;border:2px solid var(--border2);border-top-color:var(--blue);
border-radius:50%;animation:spin .7s linear infinite}
@keyframes spin{to{transform:rotate(360deg)}}

/* HEADER */
.hero{background:linear-gradient(160deg,#05101f,#081828,#060d1b);
padding:28px 20px 20px;text-align:center;border-bottom:1px solid var(--border);
position:relative;overflow:visible}
.hero::before{content:'';position:absolute;inset:0;
background:radial-gradient(ellipse 70% 50% at 50% -10%,rgba(14,165,233,.18),transparent);pointer-events:none}
.hero-logo{height:auto;width:min(340px,72vw);display:block;margin:0 auto 10px}
.hero-contact{font-size:.9rem;color:var(--text2);letter-spacing:.3px;max-width:340px;margin:0 auto}
.hero-contact a{color:var(--blue-lt);text-decoration:none}
.hero-admin-btn{position:absolute;top:16px;right:16px;z-index:10;
background:rgba(251,191,36,.1);border:1px solid rgba(251,191,36,.2);
color:var(--gold);font-size:.7rem;letter-spacing:1px;padding:6px 12px;
border-radius:8px;transition:background .2s;white-space:nowrap;cursor:pointer}
.hero-admin-btn:hover{background:rgba(251,191,36,.2)}

/* NAV */
.nav-wrap{position:sticky;top:0;z-index:200;background:rgba(6,13,27,.96);
backdrop-filter:blur(16px);border-bottom:1px solid var(--border);
display:flex;align-items:stretch;padding:0}
.nav{display:flex;flex:1;overflow-x:auto;scrollbar-width:none}
.nav::-webkit-scrollbar{display:none}
.nav-btn{flex-shrink:0;padding:14px 10px;font-family:'Bebas Neue',sans-serif;font-size:.78rem;
letter-spacing:1.5px;color:var(--muted);background:transparent;
border-bottom:2px solid transparent;margin-bottom:-1px;
transition:all .2s;white-space:nowrap;text-align:center}
.nav-btn:hover{color:var(--text)}
.nav-btn.active{color:var(--blue-lt);border-bottom-color:var(--blue-lt)}

/* PÁGINAS */
.page{display:none;padding:30px 16px 60px;max-width:1100px;margin:0 auto;animation:fadeUp .3s ease}
.page.active{display:block}
@keyframes fadeUp{from{opacity:0;transform:translateY(8px)}to{opacity:1;transform:translateY(0)}}
.page-title{font-family:'Bebas Neue',sans-serif;font-size:clamp(1.3rem,4vw,1.9rem);
letter-spacing:3px;color:var(--blue-lt);margin-bottom:4px}
.page-sub{font-size:.82rem;color:var(--text2);margin-bottom:26px;line-height:1.6}

/* PAQUETES */
.pkg-grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(280px,1fr));gap:18px;margin-bottom:28px}
.pkg-card{background:var(--surface);border:1px solid var(--border);border-radius:20px;
overflow:hidden;transition:transform .25s,box-shadow .25s;position:relative}
.pkg-card:hover{transform:translateY(-4px);box-shadow:0 16px 48px var(--blue-glow)}
.pkg-card::before{content:'';position:absolute;top:0;left:0;right:0;height:3px}
.pkg-card.express::before{background:linear-gradient(90deg,#38bdf8,#0ea5e9)}
.pkg-card.black::before{background:linear-gradient(90deg,#334155,#1e293b)}
.pkg-card.premium::before{background:linear-gradient(90deg,#fbbf24,#f59e0b)}
.pkg-head{padding:20px 20px 12px}
.pkg-badge{display:inline-block;font-size:.58rem;font-weight:700;letter-spacing:2px;
text-transform:uppercase;padding:3px 10px;border-radius:20px;margin-bottom:9px}
.be{background:rgba(14,165,233,.15);color:var(--blue-lt);border:1px solid rgba(56,189,248,.3)}
.bb{background:rgba(51,65,85,.4);color:#cbd5e1;border:1px solid rgba(148,163,184,.25)}
.bp{background:rgba(251,191,36,.12);color:var(--gold);border:1px solid rgba(251,191,36,.3)}
.pkg-name{font-family:'Bebas Neue',sans-serif;font-size:1.7rem;letter-spacing:3px;color:#fff;margin-bottom:4px}
.pkg-includes{padding:0 20px 16px}
.pkg-includes ul{list-style:none}
.pkg-includes li{font-size:.82rem;color:#fff;padding:5px 0 5px 16px;position:relative;
border-bottom:1px solid rgba(255,255,255,.05)}
.pkg-includes li:last-child{border-bottom:none}
.pkg-includes li::before{content:'✦';position:absolute;left:0;font-size:.45rem;color:var(--blue);top:8px}
.pkg-prices{background:var(--surface2);border-top:1px solid var(--border);
padding:14px 20px;display:grid;grid-template-columns:repeat(3,1fr);gap:8px}
.pc{text-align:center}
.pc-size{font-size:.6rem;letter-spacing:1.5px;text-transform:uppercase;color:#fff;margin-bottom:1px}
.pc-amt{font-family:'Bebas Neue',sans-serif;font-size:1.55rem;color:var(--blue-lt);line-height:1}
.pc-time{font-size:.6rem;color:var(--muted);margin-top:2px}

/* ADICIONALES */
.ad-grid{display:grid;grid-template-columns:repeat(auto-fill,minmax(265px,1fr));gap:14px}
.ad-card{background:var(--surface);border:1px solid var(--border);border-radius:14px;
padding:18px;transition:border-color .2s,transform .2s}
.ad-card:hover{border-color:var(--border2);transform:translateY(-2px)}
.ad-header{display:flex;align-items:center;gap:10px;margin-bottom:8px}
.ad-title{font-weight:700;font-size:.88rem;color:#fff;line-height:1.2}
.ad-desc{font-size:.76rem;color:var(--text2);margin-bottom:12px;line-height:1.5}
.ad-prices-row{display:grid;grid-template-columns:repeat(3,1fr);gap:6px}
.apb{background:var(--surface2);border:1px solid var(--border);border-radius:8px;padding:8px 4px;text-align:center}
.aps{font-size:.58rem;letter-spacing:1px;color:#fff;text-transform:uppercase;margin-bottom:2px}
.apa{font-family:'Bebas Neue',sans-serif;font-size:1.2rem;color:var(--blue-lt)}
.tap-wapp{font-size:.72rem;color:var(--orange);text-align:center;
background:rgba(249,115,22,.08);border:1px solid rgba(249,115,22,.2);
border-radius:6px;padding:6px;margin-top:4px}

/* FORM CITA */
.form-wrap{max-width:620px;margin:0 auto;background:var(--surface);
border:1px solid var(--border);border-radius:20px;overflow:hidden}
.form-head{background:linear-gradient(135deg,var(--surface2),var(--surface3));
padding:20px 24px;border-bottom:1px solid var(--border)}
.form-head h2{font-family:'Bebas Neue',sans-serif;font-size:1.4rem;letter-spacing:3px;color:var(--blue-lt)}
.form-head p{font-size:.76rem;color:var(--text2);margin-top:3px}
.form-body{padding:24px}
.fg{margin-bottom:16px}
.fl{display:block;font-size:.63rem;letter-spacing:1.5px;text-transform:uppercase;
color:#fff;margin-bottom:6px;font-weight:600}
.fi,.fs,.fta{width:100%;background:#07101f;border:1px solid var(--border);color:#fff;
font-size:.87rem;padding:10px 13px;border-radius:10px;outline:none;transition:border-color .2s}
.fi:focus,.fs:focus,.fta:focus{border-color:var(--blue)}
.fs option{background:#07101f}
.fta{resize:vertical;min-height:70px}
.frow{display:grid;grid-template-columns:1fr 1fr;gap:13px}
.chk-group{display:flex;flex-direction:column;gap:6px}
.chk-item{display:flex;align-items:flex-start;gap:9px;background:var(--surface2);
border:1px solid var(--border);border-radius:8px;padding:9px 12px;cursor:pointer;transition:border-color .2s}
.chk-item:hover{border-color:var(--border2)}
.chk-item input{width:15px;height:15px;accent-color:var(--blue);cursor:pointer;flex-shrink:0;margin-top:2px}
.chk-item label{font-size:.81rem;cursor:pointer;color:#fff;line-height:1.4}
.form-btn{width:100%;padding:14px;background:linear-gradient(135deg,var(--blue-dk),var(--blue));
color:#fff;font-family:'Bebas Neue',sans-serif;font-size:1.1rem;letter-spacing:3px;
border-radius:12px;transition:opacity .2s,transform .1s;margin-top:6px;position:relative}
.form-btn:hover{opacity:.88;transform:translateY(-1px)}
.form-btn:disabled{opacity:.5;cursor:not-allowed;transform:none}
.summary-preview{display:none;background:var(--surface2);border:1px solid var(--border2);
border-radius:12px;padding:18px;margin-bottom:14px}
.summary-preview h3{font-family:'Bebas Neue',sans-serif;font-size:1rem;letter-spacing:2px;
color:var(--blue-lt);margin-bottom:10px}
.sp-row{display:flex;justify-content:space-between;font-size:.81rem;padding:5px 0;
border-bottom:1px solid rgba(255,255,255,.05)}
.sp-row:last-child{border-bottom:none;font-weight:700;color:var(--blue-lt)}
.sp-lbl{color:var(--text2)}.sp-val{color:#fff;text-align:right}
.form-success{display:none;text-align:center;padding:34px 20px}
.form-success .big{font-size:3rem;margin-bottom:10px}
.form-success h3{font-family:'Bebas Neue',sans-serif;font-size:1.3rem;letter-spacing:2px;color:var(--green)}
.form-success p{font-size:.81rem;color:var(--text2);margin-top:8px;line-height:1.6}

/* MODAL LOGIN */
.modal-ov{display:none;position:fixed;inset:0;background:rgba(0,0,0,.87);
backdrop-filter:blur(8px);z-index:1000;align-items:center;justify-content:center}
.modal-ov.open{display:flex}
.modal{background:var(--surface);border:1px solid var(--border2);border-radius:20px;
padding:32px;width:min(390px,90vw);position:relative;box-shadow:0 0 80px var(--blue-glow)}
.modal-close{position:absolute;top:12px;right:14px;background:none;color:var(--muted);font-size:1.1rem}
.modal-close:hover{color:#fff}
.mlogo{font-family:'Bebas Neue',sans-serif;font-size:1.4rem;letter-spacing:4px;text-align:center;margin-bottom:4px}
.msub{text-align:center;font-size:.65rem;color:var(--muted);letter-spacing:2px;text-transform:uppercase;margin-bottom:22px}
.mi{width:100%;background:#07101f;border:1px solid var(--border);color:#fff;
padding:10px 13px;border-radius:10px;font-size:.88rem;outline:none;
transition:border-color .2s;margin-bottom:13px}
.mi:focus{border-color:var(--blue)}
.ml{display:block;font-size:.63rem;letter-spacing:1.5px;text-transform:uppercase;color:var(--muted);margin-bottom:5px}
.mb{width:100%;padding:12px;background:linear-gradient(135deg,var(--blue-dk),var(--blue));
color:#fff;font-family:'Bebas Neue',sans-serif;font-size:.95rem;letter-spacing:3px;
border-radius:10px;transition:opacity .2s}
.mb:hover{opacity:.88}
.merr{color:var(--red);font-size:.73rem;text-align:center;margin-top:10px;display:none}

/* ADMIN COMÚN */
.admin-bar{display:flex;align-items:center;justify-content:space-between;margin-bottom:20px;flex-wrap:wrap;gap:10px}
.admin-badge{display:inline-flex;align-items:center;gap:5px;background:rgba(251,191,36,.1);
border:1px solid rgba(251,191,36,.3);color:var(--gold);font-size:.65rem;letter-spacing:1px;
text-transform:uppercase;padding:4px 10px;border-radius:20px;font-weight:700}
.logout-btn{background:rgba(239,68,68,.1);border:1px solid rgba(239,68,68,.2);
color:var(--red);font-size:.68rem;letter-spacing:1px;padding:5px 11px;border-radius:8px;transition:background .2s}
.logout-btn:hover{background:rgba(239,68,68,.2)}
.stabs{display:flex;gap:0;border-bottom:1px solid var(--border);margin-bottom:20px;overflow-x:auto;scrollbar-width:none}
.stabs::-webkit-scrollbar{display:none}
.stab{padding:9px 15px;font-family:'Bebas Neue',sans-serif;font-size:.78rem;letter-spacing:2px;
color:var(--muted);background:none;border-bottom:2px solid transparent;margin-bottom:-1px;
transition:all .2s;white-space:nowrap}
.stab.active{color:var(--blue-lt);border-bottom-color:var(--blue-lt)}
.stab:hover:not(.active){color:#fff}

/* Dropdown "Editar" */
.edit-dropdown{position:relative;display:inline-block}
.edit-dropdown-content{display:none;position:absolute;top:100%;left:0;
background:rgba(6,13,27,.98);border:1px solid var(--border2);border-radius:10px;
min-width:180px;z-index:300;box-shadow:0 8px 32px rgba(0,0,0,.5);overflow:hidden}
.edit-dropdown-content.open{display:block}
.edit-dropdown-content button{display:block;width:100%;text-align:left;padding:11px 16px;
font-family:'Bebas Neue',sans-serif;font-size:.82rem;letter-spacing:1.5px;color:var(--muted);
background:none;border-bottom:1px solid var(--border);transition:all .2s}
.edit-dropdown-content button:last-child{border-bottom:none}
.edit-dropdown-content button:hover{color:#fff;background:var(--surface2)}

/* CALENDARIO */
.cal-wrap{background:var(--surface);border:1px solid var(--border);border-radius:16px;margin-bottom:20px;overflow:hidden}
.cal-head{display:flex;align-items:center;justify-content:space-between;padding:13px 18px;
background:var(--surface2);border-bottom:1px solid var(--border)}
.cal-head h3{font-family:'Bebas Neue',sans-serif;font-size:1.1rem;letter-spacing:2px;color:var(--blue-lt)}
.cal-nav{background:none;color:var(--muted);font-size:1.1rem;padding:4px 9px;border-radius:6px;transition:color .2s,background .2s}
.cal-nav:hover{color:#fff;background:var(--surface3)}
.cal-grid{display:grid;grid-template-columns:repeat(7,1fr);gap:1px;background:var(--border)}
.cal-dow{background:var(--surface2);text-align:center;font-size:.58rem;letter-spacing:1px;text-transform:uppercase;color:var(--muted);padding:7px 4px}
.cal-day{background:var(--surface);min-height:60px;padding:5px;cursor:pointer;transition:background .15s}
.cal-day:hover{background:var(--surface2)}
.cal-day.other-month{opacity:.3}
.cal-day.today .dn{background:var(--blue);color:#fff;border-radius:50%;width:20px;height:20px;display:flex;align-items:center;justify-content:center}
.cal-day.available{border-left:2px solid var(--green)}
.dn{font-size:.75rem;color:#fff;width:20px;height:20px;display:flex;align-items:center;justify-content:center}
.ddots{display:flex;flex-wrap:wrap;gap:2px;margin-top:3px}
.ddot{width:6px;height:6px;border-radius:50%;background:var(--gold)}
.ddot.done{background:var(--green)}.ddot.cancel{background:var(--red)}

/* POPUP CITA */
.cita-popup{display:none;position:fixed;inset:0;background:rgba(0,0,0,.82);
backdrop-filter:blur(6px);z-index:500;align-items:center;justify-content:center}
.cita-popup.open{display:flex}
.cp-box{background:var(--surface);border:1px solid var(--border2);border-radius:16px;
width:min(490px,92vw);max-height:85vh;overflow-y:auto}
.cp-head{padding:16px 18px;background:var(--surface2);border-bottom:1px solid var(--border);
display:flex;align-items:center;justify-content:space-between;position:sticky;top:0;z-index:1}
.cp-head h3{font-family:'Bebas Neue',sans-serif;font-size:1rem;letter-spacing:2px;color:var(--blue-lt)}
.cp-body{padding:18px}
.cp-row{display:flex;justify-content:space-between;font-size:.8rem;padding:6px 0;border-bottom:1px solid rgba(255,255,255,.05)}
.cp-row:last-child{border-bottom:none}
.cp-lbl{color:var(--text2)}.cp-val{color:#fff;text-align:right;font-weight:500}
.cp-actions{padding:14px 18px;border-top:1px solid var(--border);display:flex;gap:7px;flex-wrap:wrap}
.btn-sm{font-size:.7rem;letter-spacing:.5px;padding:7px 12px;border-radius:8px;font-weight:600;transition:opacity .2s}
.btn-sm:hover{opacity:.8}
.btn-green{background:rgba(34,197,94,.15);color:var(--green);border:1px solid rgba(34,197,94,.25)}
.btn-red{background:rgba(239,68,68,.12);color:var(--red);border:1px solid rgba(239,68,68,.2)}
.btn-blue{background:rgba(14,165,233,.15);color:var(--blue-lt);border:1px solid rgba(56,189,248,.25)}
.btn-gold{background:rgba(251,191,36,.12);color:var(--gold);border:1px solid rgba(251,191,36,.25)}

/* SOLICITUDES */
.sol-list{display:flex;flex-direction:column;gap:11px}
.sol-card{background:var(--surface);border:1px solid var(--border);border-radius:12px;padding:15px 17px}
.sol-top{display:flex;justify-content:space-between;align-items:flex-start;margin-bottom:9px}
.sol-name{font-weight:700;font-size:.88rem}
.sol-date{font-size:.68rem;color:var(--muted)}
.sol-info{font-size:.76rem;color:var(--text2);line-height:1.7;margin-bottom:11px}
.msg-box{background:#07101f;border:1px solid var(--border);border-radius:8px;padding:11px;
font-size:.76rem;color:var(--text2);line-height:1.7;white-space:pre-wrap;margin-top:9px;display:none}
.msg-actions{display:flex;gap:6px;margin-top:6px;flex-wrap:wrap}
.copy-btn{font-size:.66rem;padding:5px 10px;border-radius:6px;background:var(--surface3);
border:1px solid var(--border);color:var(--muted);transition:all .2s}
.copy-btn:hover{color:#fff;border-color:var(--blue)}

/* DISPONIBILIDAD */
.disp-months{display:flex;gap:8px;margin-bottom:14px}
.disp-month-btn{padding:6px 14px;border-radius:20px;font-size:.72rem;font-weight:600;
background:var(--surface2);border:1px solid var(--border);color:var(--muted);transition:all .2s}
.disp-month-btn.active{background:rgba(14,165,233,.15);border-color:var(--blue);color:var(--blue-lt)}
.disp-grid{display:grid;grid-template-columns:repeat(auto-fill,minmax(130px,1fr));gap:8px}
.disp-day{background:var(--surface);border:1px solid var(--border);border-radius:10px;
padding:10px;text-align:center;cursor:pointer;transition:all .2s;user-select:none}
.disp-day.on{background:rgba(34,197,94,.1);border-color:rgba(34,197,94,.4);color:var(--green)}
.disp-day:hover{border-color:var(--border2)}
.disp-date{font-size:.7rem;color:var(--text2);margin-bottom:3px}
.disp-label{font-family:'Bebas Neue',sans-serif;font-size:.8rem;letter-spacing:1px}
.disp-hours{display:flex;flex-wrap:wrap;gap:4px;margin-top:6px;justify-content:center}
.hr-chip{font-size:.6rem;padding:3px 7px;border-radius:10px;cursor:pointer;
background:var(--surface2);border:1px solid var(--border);color:var(--muted);transition:all .15s}
.hr-chip.on{background:rgba(14,165,233,.15);border-color:var(--blue);color:var(--blue-lt)}

/* COSTOS */
.gb{display:flex;flex-wrap:wrap;gap:14px;align-items:flex-end;
background:var(--surface2);border:1px solid var(--border);border-radius:12px;
padding:15px 18px;margin-bottom:22px}
.gb label{display:block;font-size:.6rem;letter-spacing:1.5px;text-transform:uppercase;color:var(--muted);margin-bottom:5px}
.ri{width:95px;background:#07101f;border:1px solid var(--border);color:var(--blue-lt);
font-family:'Bebas Neue',sans-serif;font-size:1.2rem;padding:6px 9px;border-radius:8px;text-align:center;outline:none}
.ri:focus{border-color:var(--blue)}
.emp-tog{display:flex;align-items:center;gap:7px;cursor:pointer}
.emp-tog input{accent-color:var(--orange);cursor:pointer;width:15px;height:15px}
.emp-tog label{font-size:.76rem;color:var(--text2);cursor:pointer}
.cost-card{background:var(--surface);border:1px solid var(--border);border-radius:14px;margin-bottom:18px;overflow:hidden}
.cc-head{display:flex;align-items:center;gap:11px;padding:13px 17px;border-bottom:1px solid var(--border);
background:linear-gradient(135deg,var(--surface2),var(--surface));cursor:pointer;user-select:none}
.cc-num{font-family:'Bebas Neue',sans-serif;font-size:1.7rem;color:var(--blue);line-height:1}
.cc-title{font-weight:700;font-size:.88rem;color:#fff}
.cc-desc{font-size:.7rem;color:var(--muted);margin-top:2px}
.chevron{margin-left:auto;color:var(--muted);font-size:.82rem;transition:transform .2s}
.cc-head.open .chevron{transform:rotate(180deg)}
.cc-body{display:none}.cc-body.open{display:block}
.igrid{display:grid;grid-template-columns:repeat(auto-fit,minmax(155px,1fr));gap:1px;background:var(--border)}
.ic{background:var(--surface);padding:12px 14px}
.ic label{display:block;font-size:.58rem;letter-spacing:1.5px;text-transform:uppercase;color:var(--muted);margin-bottom:5px}
.ic input{width:100%;background:#07101f;border:1px solid var(--border);color:#fff;
font-size:.85rem;font-weight:600;padding:7px 9px;border-radius:7px;outline:none;transition:border-color .2s}
.ic input:focus{border-color:var(--blue)}
.ic .hint{font-size:.63rem;color:var(--muted);margin-top:3px}
.ins-wrap{padding:13px 15px;border-top:1px solid var(--border)}
.ins-title{font-size:.6rem;letter-spacing:2px;text-transform:uppercase;color:var(--blue);margin-bottom:7px;font-weight:700}
.ins-hdr{display:grid;grid-template-columns:1fr 1fr 70px 70px 70px 32px;gap:5px;margin-bottom:5px}
.ins-hdr span{font-size:.56rem;color:var(--muted);text-transform:uppercase;letter-spacing:1px}
.ins-row{display:grid;grid-template-columns:1fr 1fr 70px 70px 70px 32px;gap:5px;align-items:center;margin-bottom:5px}
.ins-row input{background:#07101f;border:1px solid var(--border);color:#fff;font-size:.76rem;
padding:5px 7px;border-radius:6px;outline:none;width:100%;transition:border-color .2s}
.ins-row input:focus{border-color:var(--blue)}
.ins-cxu{font-family:'Bebas Neue',sans-serif;font-size:.9rem;color:var(--orange)}
.ins-del{background:none;color:var(--red);font-size:.9rem;padding:0 3px;opacity:.7;transition:opacity .2s}
.ins-del:hover{opacity:1}
.add-ins-btn{display:inline-flex;align-items:center;gap:5px;background:rgba(14,165,233,.07);
border:1px dashed rgba(56,189,248,.22);color:var(--blue-lt);font-size:.68rem;
letter-spacing:1px;padding:6px 11px;border-radius:7px;transition:background .2s;margin-top:3px}
.add-ins-btn:hover{background:rgba(14,165,233,.14)}
.ins-total{font-size:.73rem;color:var(--orange);margin-top:7px}
.ins-total strong{font-family:'Bebas Neue',sans-serif;font-size:.95rem}
.sizes-bar{display:grid;grid-template-columns:repeat(3,1fr);background:var(--border)}
.sz-hdr{background:var(--surface2);padding:9px;text-align:center;font-family:'Bebas Neue',sans-serif;font-size:.88rem;letter-spacing:2px;color:var(--blue)}
.res-row{display:grid;grid-template-columns:repeat(3,1fr);gap:1px;background:var(--border)}
.rc{background:var(--surface2);padding:13px;text-align:center}
.rl{font-size:.56rem;letter-spacing:2px;text-transform:uppercase;color:var(--muted);margin-bottom:3px}
.rv-cost{font-family:'Bebas Neue',sans-serif;font-size:1.1rem;color:var(--orange)}
.rv-mg{font-family:'Bebas Neue',sans-serif;font-size:1rem}
.good{color:var(--green)}.warn{color:var(--orange)}.bad{color:var(--red)}

/* CLIENTES */
.cli-layout{display:grid;grid-template-columns:1fr 320px;gap:18px;align-items:start}
.cli-grid{display:flex;flex-direction:column;gap:13px}
.cli-card{background:var(--surface);border:1px solid var(--border);border-radius:14px;padding:16px}
.cli-card:hover{border-color:var(--border2)}
.cli-top{display:flex;justify-content:space-between;align-items:flex-start;margin-bottom:9px}
.cli-name{font-weight:700;font-size:.92rem;color:#fff}
.cli-tel{font-size:.7rem;color:var(--muted)}
.stars-row{display:flex;gap:1px;margin-bottom:7px}
.star-btn{background:none;font-size:.95rem;cursor:pointer;transition:transform .1s;padding:0 1px}
.star-btn:hover{transform:scale(1.2)}
.propina-bd{display:inline-block;font-size:.58rem;font-weight:700;padding:2px 7px;border-radius:10px;margin-left:3px}
.p-si{background:rgba(34,197,94,.15);color:var(--green);border:1px solid rgba(34,197,94,.25)}
.p-no{background:rgba(100,116,139,.12);color:var(--muted);border:1px solid rgba(100,116,139,.2)}
.vtag{display:inline-block;background:var(--surface2);border:1px solid var(--border);
color:var(--text2);font-size:.66rem;padding:2px 6px;border-radius:5px;margin:2px}
.rec-panel{background:var(--surface);border:1px solid var(--border);border-radius:14px;
padding:15px;position:sticky;top:80px}
.rec-title{font-family:'Bebas Neue',sans-serif;font-size:.95rem;letter-spacing:2px;color:var(--gold);margin-bottom:12px}
.rec-card{background:var(--surface2);border:1px solid var(--border);border-radius:10px;padding:12px;margin-bottom:9px}
.rec-name{font-weight:600;font-size:.82rem;color:#fff;margin-bottom:2px}
.rec-info{font-size:.7rem;color:var(--muted);margin-bottom:7px}
.rec-btn{width:100%;padding:7px;background:rgba(251,191,36,.12);border:1px solid rgba(251,191,36,.25);
color:var(--gold);font-size:.7rem;letter-spacing:1px;border-radius:7px;transition:background .2s}
.rec-btn:hover{background:rgba(251,191,36,.2)}

/* ESTADÍSTICAS */
.stat-grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(160px,1fr));gap:11px;margin-bottom:22px}
.stat-box{background:var(--surface2);border:1px solid var(--border);border-radius:12px;padding:15px;text-align:center}
.stat-val{font-family:'Bebas Neue',sans-serif;font-size:1.7rem;color:var(--blue-lt);display:block}
.stat-lbl{font-size:.6rem;color:var(--muted);letter-spacing:1px;text-transform:uppercase;margin-top:3px}
.filter-row{display:flex;gap:8px;margin-bottom:18px;flex-wrap:wrap;align-items:center}
.filter-btn{padding:5px 14px;border-radius:20px;font-size:.72rem;font-weight:600;
background:var(--surface2);border:1px solid var(--border);color:var(--muted);transition:all .2s}
.filter-btn.active{background:rgba(14,165,233,.15);border-color:var(--blue);color:var(--blue-lt)}
.chart-grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(270px,1fr));gap:18px;margin-bottom:24px}
.chart-box{background:var(--surface);border:1px solid var(--border);border-radius:14px;padding:16px}
.chart-title{font-family:'Bebas Neue',sans-serif;font-size:.95rem;letter-spacing:2px;color:var(--blue-lt);margin-bottom:12px}
.chart-box canvas{max-height:190px}

/* EQUIPO */
.equipo-grid{display:grid;grid-template-columns:repeat(auto-fill,minmax(290px,1fr));gap:14px}
.emp-card{background:var(--surface);border:1px solid var(--border);border-radius:14px;padding:18px;position:relative}
.emp-card.owner{border-color:rgba(251,191,36,.3);background:linear-gradient(135deg,rgba(251,191,36,.04),var(--surface))}
.emp-avatar{width:54px;height:54px;border-radius:50%;background:linear-gradient(135deg,var(--blue-dk),var(--blue));
display:flex;align-items:center;justify-content:center;font-size:1.3rem;margin-bottom:9px;overflow:hidden}
.emp-avatar img{width:100%;height:100%;object-fit:cover}
.emp-name{font-weight:700;font-size:.92rem;color:#fff;margin-bottom:2px}
.emp-role{font-size:.65rem;color:var(--muted);text-transform:uppercase;letter-spacing:1px;margin-bottom:9px}
.emp-info-row{display:flex;justify-content:space-between;font-size:.74rem;padding:5px 0;border-bottom:1px solid rgba(255,255,255,.05)}
.emp-info-row:last-of-type{border-bottom:none}
.emp-info-lbl{color:var(--text2)}.emp-info-val{color:#fff;font-weight:500}
.emp-stats{display:grid;grid-template-columns:repeat(2,1fr);gap:7px;margin-top:11px}
.emp-stat{background:var(--surface2);border-radius:8px;padding:9px;text-align:center}
.emp-stat-val{font-family:'Bebas Neue',sans-serif;font-size:1.1rem;color:var(--blue-lt);display:block}
.emp-stat-lbl{font-size:.58rem;color:var(--muted);text-transform:uppercase;letter-spacing:.5px}
.owner-badge{position:absolute;top:11px;right:11px;background:rgba(251,191,36,.15);
border:1px solid rgba(251,191,36,.3);color:var(--gold);font-size:.58rem;
padding:3px 8px;border-radius:10px;font-weight:700;letter-spacing:1px}
.add-emp-btn{display:inline-flex;align-items:center;gap:7px;background:rgba(14,165,233,.1);
border:1px solid var(--border2);color:var(--blue-lt);font-size:.78rem;letter-spacing:1px;
padding:9px 18px;border-radius:10px;transition:background .2s;margin-bottom:18px}
.add-emp-btn:hover{background:rgba(14,165,233,.18)}
.emp-modal{display:none;position:fixed;inset:0;background:rgba(0,0,0,.87);
backdrop-filter:blur(6px);z-index:600;align-items:center;justify-content:center}
.emp-modal.open{display:flex}
.em-box{background:var(--surface);border:1px solid var(--border2);border-radius:16px;
width:min(500px,92vw);max-height:88vh;overflow-y:auto}
.em-head{padding:16px 20px;background:var(--surface2);border-bottom:1px solid var(--border);
display:flex;justify-content:space-between;align-items:center;position:sticky;top:0;z-index:1}
.em-head h3{font-family:'Bebas Neue',sans-serif;font-size:1rem;letter-spacing:2px;color:var(--blue-lt)}
.em-body{padding:20px}
.photo-upload{display:flex;flex-direction:column;align-items:center;margin-bottom:16px}
.photo-circle{width:70px;height:70px;border-radius:50%;background:var(--surface2);
border:2px dashed var(--border2);display:flex;align-items:center;justify-content:center;
font-size:1.6rem;cursor:pointer;overflow:hidden;transition:border-color .2s;margin-bottom:6px}
.photo-circle:hover{border-color:var(--blue)}
.photo-circle img{width:100%;height:100%;object-fit:cover}
.photo-hint{font-size:.65rem;color:var(--muted)}

/* UTILIDADES */
.tip-box{background:rgba(14,165,233,.05);border:1px solid rgba(56,189,248,.12);
border-radius:10px;padding:13px 17px;font-size:.76rem;color:var(--text2);margin-top:16px;line-height:1.7}
.tip-box strong{color:#fff}
.empty{text-align:center;padding:36px;color:var(--muted);font-size:.83rem}
.empty span{font-size:2.3rem;display:block;margin-bottom:8px}

/* Firebase setup banner */
.setup-banner{background:rgba(251,191,36,.08);border:1px solid rgba(251,191,36,.3);
border-radius:12px;padding:16px;margin-bottom:20px;font-size:.8rem;color:var(--text2);line-height:1.8}
.setup-banner strong{color:var(--gold);display:block;margin-bottom:6px;font-size:.9rem}
.setup-banner code{background:rgba(14,165,233,.12);padding:2px 6px;border-radius:4px;
font-size:.76rem;color:var(--blue-lt);font-family:monospace}

@media(max-width:700px){
.frow{grid-template-columns:1fr}
.cli-layout{grid-template-columns:1fr}
.rec-panel{position:static}
.ins-hdr,.ins-row{grid-template-columns:1fr 1fr 60px 60px auto}
}
</style>

</head>
<body>

<!-- TOAST -->

<div id="toast"></div>

<!-- HEADER -->

<header class="hero">
  <button class="hero-admin-btn" id="heroAdminBtn" onclick="openLogin()">🔒 Admin</button>
  <img src="https://i.imgur.com/placeholder.png" class="hero-logo" alt="Bianco Wash"
    onerror="this.style.display='none'"
    style="background:linear-gradient(135deg,#0369a1,#0ea5e9);border-radius:16px;padding:20px;width:200px;height:80px;object-fit:contain">
  <div style="font-family:'Bebas Neue',sans-serif;font-size:2.2rem;letter-spacing:6px;margin-bottom:4px">
    <span style="background:linear-gradient(135deg,#7dd3fc,#0ea5e9);-webkit-background-clip:text;-webkit-text-fill-color:transparent">BIANCO</span>
    <span style="color:#fff"> WASH</span>
  </div>
  <div style="font-size:.7rem;color:var(--muted);letter-spacing:3px;margin-bottom:8px">LAS TERRAZAS</div>
  <div class="hero-contact">📆 Agenda tu cita &nbsp;·&nbsp; 📲 <a href="tel:3111574234">311-157-4234</a></div>
</header>

<!-- NAV -->

<div class="nav-wrap">
  <nav class="nav" id="mainNav">
    <button class="nav-btn active" id="nbtn-paquetes" onclick="showPage('paquetes',this)">🚗 Paquetes</button>
    <button class="nav-btn" id="nbtn-adicionales" onclick="showPage('adicionales',this)">🫧 Adicionales</button>
    <button class="nav-btn" id="nbtn-agenda-pub" onclick="showPage('agenda-pub',this)">📅 Agendar Cita</button>
  </nav>
</div>

<!-- ════ PAQUETES ════ -->

<div id="page-paquetes" class="page active">
  <div class="page-title">NUESTROS PAQUETES</div>
  <div class="page-sub">Autolavado a domicilio · Precios según tamaño del vehículo.</div>
  <div class="pkg-grid">
    <div class="pkg-card express">
      <div class="pkg-head"><span class="pkg-badge be">EXPRESS</span><div class="pkg-name">PAQUETE EXPRESS</div></div>
      <div class="pkg-includes"><ul><li>Lavado exterior completo</li><li>Brillo en llantas</li></ul></div>
      <div class="pkg-prices">
        <div class="pc"><div class="pc-size">Chico</div><div class="pc-amt">$120</div><div class="pc-time">40 min aprox.</div></div>
        <div class="pc"><div class="pc-size">Mediano</div><div class="pc-amt">$140</div><div class="pc-time">50 min aprox.</div></div>
        <div class="pc"><div class="pc-size">Grande</div><div class="pc-amt">$160</div><div class="pc-time">1 hr aprox.</div></div>
      </div>
    </div>
    <div class="pkg-card black">
      <div class="pkg-head"><span class="pkg-badge bb">BLACK</span><div class="pkg-name">PAQUETE BLACK</div></div>
      <div class="pkg-includes"><ul>
        <li>Lavado exterior completo</li><li>Lavado de tapetes</li><li>Limpieza básica interior</li>
        <li>Aspirado básico</li><li>Brillo en llantas</li><li>Aroma</li>
      </ul></div>
      <div class="pkg-prices">
        <div class="pc"><div class="pc-size">Chico</div><div class="pc-amt">$180</div><div class="pc-time">1h 10m aprox.</div></div>
        <div class="pc"><div class="pc-size">Mediano</div><div class="pc-amt">$200</div><div class="pc-time">1h 30m aprox.</div></div>
        <div class="pc"><div class="pc-size">Grande</div><div class="pc-amt">$230</div><div class="pc-time">1h 50m aprox.</div></div>
      </div>
    </div>
    <div class="pkg-card premium">
      <div class="pkg-head"><span class="pkg-badge bp">⭐ PREMIUM</span><div class="pkg-name">PAQUETE PREMIUM</div></div>
      <div class="pkg-includes"><ul>
        <li>Lavado exterior completo</li><li>Lavado de tapetes</li><li>Detallado de interiores</li>
        <li>Aspirado profundo</li><li>Brillo en llantas</li><li>Brillo en plásticos</li>
        <li>Protección en plásticos</li><li>Aroma</li>
      </ul></div>
      <div class="pkg-prices">
        <div class="pc"><div class="pc-size">Chico</div><div class="pc-amt">$250</div><div class="pc-time">1h 30m aprox.</div></div>
        <div class="pc"><div class="pc-size">Mediano</div><div class="pc-amt">$280</div><div class="pc-time">2 hrs aprox.</div></div>
        <div class="pc"><div class="pc-size">Grande</div><div class="pc-amt">$320</div><div class="pc-time">2h 20m aprox.</div></div>
      </div>
    </div>
  </div>
  <div class="tip-box">📞 <strong>¿Listo para agendar?</strong> Ve a <em>Agendar Cita</em> o escríbenos al <strong>311-157-4234</strong>.</div>
</div>

<!-- ════ ADICIONALES ════ -->

<div id="page-adicionales" class="page">
  <div class="page-title">SERVICIOS ADICIONALES</div>
  <div class="page-sub">Complementa tu paquete con servicios especializados.</div>
  <div class="ad-grid" id="adPubGrid"></div>
</div>

<!-- ════ AGENDAR CITA ════ -->

<div id="page-agenda-pub" class="page">
  <div class="page-title">AGENDAR CITA</div>
  <div class="page-sub">Llena el formulario y te contactaremos para confirmar tu servicio.</div>
  <div class="form-wrap">
    <div class="form-head"><h2>NUEVA SOLICITUD</h2><p>Completa todos los campos para brindarte el mejor servicio.</p></div>
    <div class="form-body" id="citaFormBody">
      <div class="frow">
        <div class="fg"><label class="fl">Nombre</label><input type="text" class="fi" id="fNombre" placeholder="Tu nombre"></div>
        <div class="fg"><label class="fl">Apellido</label><input type="text" class="fi" id="fApellido" placeholder="Tu apellido"></div>
      </div>
      <div class="frow">
        <div class="fg"><label class="fl">Teléfono / WhatsApp</label><input type="tel" class="fi" id="fTel" placeholder="xxx-xxx-xxxx"></div>
        <div class="fg"><label class="fl">Coto / Fraccionamiento</label><input type="text" class="fi" id="fCoto" placeholder="Nombre del coto"></div>
      </div>
      <div class="fg"><label class="fl">Número de casa</label><input type="text" class="fi" id="fCasa" placeholder="Ej: 42, Casa B, Lote 7"></div>
      <div class="fg"><label class="fl">Vehículo (marca, modelo, año)</label><input type="text" class="fi" id="fVehiculo" placeholder="Ej: Nissan Sentra 2021"></div>
      <div class="fg">
        <label class="fl">Tamaño del vehículo</label>
        <select class="fs" id="fTamano" onchange="updatePreview()">
          <option value="">Selecciona...</option>
          <option value="ch">Chico — Compacto (Ej. March, Spark)</option>
          <option value="md">Mediano — Sedán / Camioneta 2 hileras</option>
          <option value="gd">Grande — Pickup / Camioneta 3 hileras</option>
        </select>
      </div>
      <div class="fg">
        <label class="fl">Paquete de lavado</label>
        <select class="fs" id="fPaquete" onchange="updatePreview()">
          <option value="">Selecciona...</option>
          <option value="express">Express — desde $120</option>
          <option value="black">Black — desde $180</option>
          <option value="premium">Premium — desde $250</option>
        </select>
      </div>
      <div class="fg">
        <label class="fl">Servicios adicionales (opcional)</label>
        <div class="chk-group" id="adCheckboxes"></div>
      </div>
      <div class="frow">
        <div class="fg">
          <label class="fl">Fecha disponible</label>
          <select class="fs" id="fFecha" onchange="updatePreview()">
            <option value="">Cargando fechas...</option>
          </select>
        </div>
        <div class="fg">
          <label class="fl">Hora preferida</label>
          <select class="fs" id="fHora" onchange="updatePreview()">
            <option value="">Selecciona...</option>
          </select>
        </div>
      </div>
      <div class="fg"><label class="fl">Notas adicionales (opcional)</label>
        <textarea class="fta" id="fNotas" placeholder="Indicaciones de acceso, cuidados especiales, etc."></textarea>
      </div>
      <div class="summary-preview" id="summaryPreview">
        <h3>📋 RESUMEN DE TU CITA</h3>
        <div id="summaryContent"></div>
      </div>
      <button class="form-btn" id="btnSubmitCita" onclick="submitCita()">CONFIRMAR SOLICITUD</button>
    </div>
    <div class="form-success" id="citaSuccess">
      <div class="big">✅</div>
      <h3>¡SOLICITUD ENVIADA!</h3>
      <p>Te contactaremos al <strong style="color:var(--blue-lt)">311-157-4234</strong> para confirmar.<br>¡Gracias por confiar en Bianco Wash!</p>
      <button class="btn-sm btn-blue" style="margin-top:16px" onclick="resetCitaForm()">Nueva solicitud</button>
    </div>
  </div>
</div>

<!-- ════ ADMIN: AGENDA ════ -->

<div id="page-admin-agenda" class="page">
  <div class="admin-bar">
    <div><div class="page-title">🗓 AGENDA</div></div>
    <div style="display:flex;gap:8px;align-items:center"><span class="admin-badge">🔒 Admin</span><button class="logout-btn" onclick="logout()">Cerrar sesión</button></div>
  </div>
  <div class="stabs">
    <button class="stab active" onclick="showStab('agenda','calendar',this)">📆 Calendario</button>
    <button class="stab" onclick="showStab('agenda','solicitudes',this)">📩 Solicitudes</button>
    <button class="stab" onclick="showStab('agenda','disponibilidad',this)">✅ Disponibilidad</button>
  </div>
  <div id="agenda-calendar"></div>
  <div id="agenda-solicitudes" style="display:none"></div>
  <div id="agenda-disponibilidad" style="display:none"></div>
</div>

<!-- ════ ADMIN: COSTOS ════ -->

<div id="page-admin-costos" class="page">
  <div class="admin-bar">
    <div><div class="page-title">💰 COSTOS OPERATIVOS</div></div>
    <div style="display:flex;gap:8px;align-items:center"><span class="admin-badge">🔒 Admin</span><button class="logout-btn" onclick="logout()">Cerrar sesión</button></div>
  </div>
  <div class="gb" style="justify-content:center;gap:24px">
    <div style="display:flex;flex-direction:column;align-items:center;gap:4px">
      <label>💰 Mi tarifa/hr</label>
      <input type="number" class="ri" id="hrOwner" value="120" oninput="recalcAll()">
    </div>
    <div style="display:flex;align-items:center;margin-top:14px">
      <label class="emp-tog"><input type="checkbox" id="useEmp" onchange="toggleEmp()">
      <label for="useEmp" style="font-size:.76rem;color:var(--text2);margin-left:7px">Incluir sueldo empleado (50%)</label></label>
    </div>
  </div>
  <div class="stabs">
    <button class="stab active" id="ctab-pkg" onclick="showCostTab('pkg')">📦 Paquetes</button>
    <button class="stab" id="ctab-ad" onclick="showCostTab('ad')">✨ Adicionales</button>
    <button class="stab" id="ctab-res" onclick="showCostTab('res')">📊 Resumen</button>
  </div>
  <div id="costos-pkg"></div>
  <div id="costos-ad" style="display:none"></div>
  <div id="costos-res" style="display:none"></div>
</div>

<!-- ════ ADMIN: CLIENTES ════ -->

<div id="page-admin-clientes" class="page">
  <div class="admin-bar">
    <div><div class="page-title">👥 CLIENTES</div></div>
    <div style="display:flex;gap:8px;align-items:center"><span class="admin-badge">🔒 Admin</span><button class="logout-btn" onclick="logout()">Cerrar sesión</button></div>
  </div>
  <div class="cli-layout">
    <div class="cli-grid" id="clientesGrid"></div>
    <div class="rec-panel"><div class="rec-title">⏰ SIN LAVAR +2 SEMANAS</div><div id="recGrid"></div></div>
  </div>
</div>

<!-- ════ ADMIN: ESTADÍSTICAS ════ -->

<div id="page-admin-stats" class="page">
  <div class="admin-bar">
    <div><div class="page-title">📊 ESTADÍSTICAS</div></div>
    <div style="display:flex;gap:8px;align-items:center"><span class="admin-badge">🔒 Admin</span><button class="logout-btn" onclick="logout()">Cerrar sesión</button></div>
  </div>
  <div class="filter-row">
    <span style="font-size:.73rem;color:var(--muted)">Periodo:</span>
    <button class="filter-btn" onclick="setFilter('semana',this)">Semana</button>
    <button class="filter-btn active" onclick="setFilter('mes',this)">Mes</button>
    <button class="filter-btn" onclick="setFilter('anio',this)">Año</button>
  </div>
  <div class="stat-grid" id="statsCards"></div>
  <div class="chart-grid" id="statsCharts"></div>
</div>

<!-- ════ ADMIN: EQUIPO ════ -->

<div id="page-admin-equipo" class="page">
  <div class="admin-bar">
    <div><div class="page-title">👔 EQUIPO</div></div>
    <div style="display:flex;gap:8px;align-items:center"><span class="admin-badge">🔒 Admin</span><button class="logout-btn" onclick="logout()">Cerrar sesión</button></div>
  </div>
  <button class="add-emp-btn" onclick="openEmpModal()">+ Agregar empleado</button>
  <div class="equipo-grid" id="equipoGrid"></div>
</div>

<!-- POPUP CITA DETALLE -->

<div class="cita-popup" id="citaPopup">
  <div class="cp-box">
    <div class="cp-head"><h3 id="cpTitle">DETALLE DE CITA</h3>
      <button class="modal-close" onclick="document.getElementById('citaPopup').classList.remove('open')">✕</button></div>
    <div class="cp-body" id="cpBody"></div>
    <div class="cp-actions" id="cpActions"></div>
  </div>
</div>

<!-- LOGIN MODAL -->

<div class="modal-ov" id="loginModal">
  <div class="modal">
    <button class="modal-close" onclick="closeLogin()">✕</button>
    <div class="mlogo"><span style="background:linear-gradient(135deg,#7dd3fc,#0ea5e9);-webkit-background-clip:text;-webkit-text-fill-color:transparent">BIANCO </span><span>WASH</span></div>
    <div class="msub">Acceso al sistema</div>
    <label class="ml">Usuario</label><input type="text" class="mi" id="loginUser" placeholder="Usuario">
    <label class="ml">Contraseña</label><input type="password" class="mi" id="loginPass" placeholder="Contraseña" onkeydown="if(event.key==='Enter')doLogin()">
    <button class="mb" onclick="doLogin()">INGRESAR</button>
    <div class="merr" id="loginErr">Usuario o contraseña incorrectos.</div>
  </div>
</div>

<!-- ADD EMPLEADO MODAL -->

<div class="emp-modal" id="empModal">
  <div class="em-box">
    <div class="em-head"><h3>NUEVO EMPLEADO</h3><button class="modal-close" onclick="document.getElementById('empModal').classList.remove('open')">✕</button></div>
    <div class="em-body">
      <div class="photo-upload">
        <div class="photo-circle" id="empPhotoCircle" onclick="document.getElementById('empPhotoInput').click()">📷</div>
        <input type="file" id="empPhotoInput" accept="image/*" style="display:none" onchange="previewEmpPhoto(this)">
        <div class="photo-hint">Toca para subir foto de perfil</div>
      </div>
      <div class="frow">
        <div class="fg"><label class="fl">Nombre completo</label><input type="text" class="fi" id="empNombre" placeholder="Nombre completo"></div>
        <div class="fg"><label class="fl">Teléfono</label><input type="text" class="fi" id="empTel" placeholder="xxx-xxx-xxxx"></div>
      </div>
      <div class="frow">
        <div class="fg"><label class="fl">Fecha inicio laboral</label><input type="date" class="fi" id="empFechaInicio"></div>
        <div class="fg"><label class="fl">Domicilio</label><input type="text" class="fi" id="empDom" placeholder="Calle y número"></div>
      </div>
      <div class="frow">
        <div class="fg"><label class="fl">Usuario (login)</label><input type="text" class="fi" id="empUser" placeholder="nombre.apellido"></div>
        <div class="fg"><label class="fl">Contraseña</label><input type="password" class="fi" id="empPass" placeholder="Contraseña"></div>
      </div>
      <button class="form-btn" id="btnSaveEmp" onclick="saveEmpleado()">💾 GUARDAR EMPLEADO</button>
    </div>
  </div>
</div>

<!-- ════════════════════════════════════════════════════════════
     JAVASCRIPT PRINCIPAL
════════════════════════════════════════════════════════════ -->

<script>
/* ══════════════════════════════
   CONSTANTES Y DATOS ESTÁTICOS
══════════════════════════════ */
const ADMIN_USER = 'Eliam Cobian', ADMIN_PASS = 'Cobianvilla11';
let SESSION = null;

// Estado reactivo (se rellena desde Firebase)
let solicitudes = [];
let citas       = [];
let clientes    = [];
let empleados   = [];
let fechasDisp  = {}; // {fecha: [hora1,hora2,...]}

let statsFilter  = 'mes';
let calY = new Date().getFullYear(), calM = new Date().getMonth();
let dispViewMode = 'mes';
let empPhotoData = null;

const ADMIN_PASS_MAP = { [ADMIN_USER]: ADMIN_PASS };

const pkgPrices  = {express:{ch:120,md:140,gd:160},black:{ch:180,md:200,gd:230},premium:{ch:250,md:280,gd:320}};
const pkgTimes   = {express:{ch:40,md:50,gd:60},black:{ch:70,md:90,gd:110},premium:{ch:90,md:120,gd:140}};
const sizeLabel  = {ch:'Chico',md:'Mediano',gd:'Grande'};
const MESES      = ['Enero','Febrero','Marzo','Abril','Mayo','Junio','Julio','Agosto','Septiembre','Octubre','Noviembre','Diciembre'];
const DIAS_SHORT = ['Dom','Lun','Mar','Mié','Jue','Vie','Sáb'];
const HOURS_LIST = ['7:00 AM','8:00 AM','9:00 AM','10:00 AM','11:00 AM','12:00 PM','1:00 PM','2:00 PM','3:00 PM','4:00 PM','5:00 PM','6:00 PM'];

const adData = [
  {id:'plasticos',icon:'🧴',nombre:'Brillo & Protección de Plásticos',desc:'Tablero, molduras, pilares, plásticos int/ext',tiempos:{ch:25,md:35,gd:45},precios:{ch:50,md:65,gd:80}},
  {id:'piel',icon:'🪑',nombre:'Brillo & Hidratación Asientos de Piel',desc:'Hidratación y protección UV de cuero genuino',tiempos:{ch:40,md:50,gd:60},precios:{ch:120,md:150,gd:180}},
  {id:'gotas',icon:'💧',nombre:'Remover Manchas de Gotas de Lluvia',desc:'Elimina calcificaciones en cristales y pintura',tiempos:{ch:60,md:80,gd:100},precios:{ch:180,md:220,gd:280}},
  {id:'pulido',icon:'✨',nombre:'Pulido en Pasta',desc:'Elimina rayones leves y restaura el brillo',tiempos:{ch:55,md:70,gd:90},precios:{ch:150,md:190,gd:240}},
  {id:'encerado',icon:'🛡️',nombre:'Encerado en Pasta',desc:'Sellado carnauba, protección hasta 3 meses',tiempos:{ch:45,md:55,gd:70},precios:{ch:120,md:150,gd:190}},
  {id:'cera',icon:'💨',nombre:'Cera Spray Protección 1 Mes',desc:'Aplicación express, brillo inmediato',tiempos:{ch:20,md:25,gd:32},precios:{ch:80,md:100,gd:130}},
  {id:'arcilla',icon:'🪨',nombre:'Descontaminación con Arcilla',desc:'Remoción de contaminantes incrustados en pintura',tiempos:{ch:60,md:80,gd:100},precios:{ch:180,md:220,gd:270}},
  {id:'tapiceria',icon:'🧹',nombre:'Lavado de Tapicería',desc:'Lavado profundo de tapizado — cotización por WhatsApp',tiempos:{ch:90,md:110,gd:140},precios:{ch:220,md:220,gd:220},tapiceria:true},
];

const pkgData = [
  {id:'express',num:1,nombre:'PAQUETE EXPRESS',desc:'Lavado exterior · Brillo en llantas',
   insumos:[{nombre:'Shampoo auto',marca:'Simoniz',precio:80,rendimiento:20},{nombre:'Llantera brillante',marca:'Armor All',precio:55,rendimiento:15}],
   traslado:25,equipo:8,tiempos:{ch:40,md:50,gd:60},precios:{ch:120,md:140,gd:160}},
  {id:'black',num:2,nombre:'PAQUETE BLACK',desc:'Ext + Tapetes + Interior básico + Aspirado + Llantas + Aroma',
   insumos:[{nombre:'Shampoo auto',marca:'Simoniz',precio:80,rendimiento:20},{nombre:'Llantera brillante',marca:'Armor All',precio:55,rendimiento:15},{nombre:'Limpiador tapetes',marca:"Meguiar's",precio:70,rendimiento:12},{nombre:'Aroma',marca:'Chemical Guys',precio:50,rendimiento:20}],
   traslado:25,equipo:12,tiempos:{ch:70,md:90,gd:110},precios:{ch:180,md:200,gd:230}},
  {id:'premium',num:3,nombre:'PAQUETE PREMIUM',desc:'Completo: Ext + Detallado int + Aspirado prof + Plásticos + Aroma',
   insumos:[{nombre:'Shampoo auto',marca:'Simoniz',precio:80,rendimiento:20},{nombre:'Llantera brillante',marca:'Armor All',precio:55,rendimiento:15},{nombre:'Limpiador tapetes',marca:"Meguiar's",precio:70,rendimiento:12},{nombre:'Nitro Protector Vinil',marca:'Nitro',precio:175,rendimiento:50},{nombre:'Aroma',marca:'Chemical Guys',precio:50,rendimiento:20}],
   traslado:25,equipo:18,tiempos:{ch:90,md:120,gd:140},precios:{ch:250,md:280,gd:320}},
];
let insCnt = {};

/* ══════════════════════════════
   TOAST
══════════════════════════════ */
let toastTimer;
function showToast(msg, duration=2500){
  const t = document.getElementById('toast');
  t.textContent = msg;
  t.classList.add('show');
  clearTimeout(toastTimer);
  toastTimer = setTimeout(()=>t.classList.remove('show'), duration);
}

/* ══════════════════════════════
   FIREBASE LISTENERS
   (se llama cuando Firebase está listo)
══════════════════════════════ */
window._initFirebaseListeners = function(){
  if(!window._fbReady){ setTimeout(window._initFirebaseListeners, 300); return; }

  // Disponibilidad (escucha en tiempo real)
  window.fbListen('disponibilidad', docs => {
    fechasDisp = {};
    docs.forEach(d => { fechasDisp[d.id] = d.horas || []; });
    populateFechas();
    if(document.getElementById('agenda-disponibilidad')?.style.display !== 'none')
      renderDisponibilidad();
    renderCalendar();
  });

  // Solicitudes
  window.fbListen('solicitudes', docs => {
    solicitudes = docs;
    if(document.getElementById('agenda-solicitudes')?.style.display !== 'none')
      renderSolicitudes();
  });

  // Citas
  window.fbListen('citas', docs => {
    citas = docs;
    renderCalendar();
    if(document.getElementById('page-admin-stats')?.classList.contains('active'))
      renderStats();
  });

  // Clientes
  window.fbListen('clientes', docs => {
    clientes = docs;
    if(document.getElementById('page-admin-clientes')?.classList.contains('active'))
      renderClientes();
  });

  // Empleados
  window.fbListen('empleados', docs => {
    empleados = docs;
    if(document.getElementById('page-admin-equipo')?.classList.contains('active'))
      renderEquipo();
  });
};

/* ══════════════════════════════
   NAVEGACIÓN
══════════════════════════════ */
function showPage(id, btn){
  document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
  document.getElementById('page-'+id)?.classList.add('active');
  document.querySelectorAll('.nav-btn').forEach(b => b.classList.remove('active'));
  if(btn) btn.classList.add('active');
  closeEditDropdown();
}
function showAdminPage(id){
  document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
  document.getElementById('page-'+id)?.classList.add('active');
  document.querySelectorAll('.nav-btn').forEach(b => b.classList.remove('active'));
  document.getElementById('nb-'+id)?.classList.add('active');
  closeEditDropdown();
  if(id==='admin-stats') renderStats();
  if(id==='admin-clientes') renderClientes();
  if(id==='admin-equipo') renderEquipo();
}
function showStab(section, tab, btn){
  const parent = document.getElementById('page-admin-'+section);
  if(!parent) return;
  parent.querySelectorAll('.stab').forEach(b => b.classList.remove('active'));
  if(btn) btn.classList.add('active');
  ['calendar','solicitudes','disponibilidad'].forEach(t=>{
    const el = document.getElementById(section+'-'+t);
    if(el) el.style.display = 'none';
  });
  const el = document.getElementById(section+'-'+tab);
  if(el) el.style.display = 'block';
  if(tab==='calendar') renderCalendar();
  if(tab==='solicitudes') renderSolicitudes();
  if(tab==='disponibilidad') renderDisponibilidad();
}
function toggleEditDropdown(){ document.getElementById('editDropdown')?.classList.toggle('open'); }
function closeEditDropdown(){ document.getElementById('editDropdown')?.classList.remove('open'); }
document.addEventListener('click', e=>{ if(!e.target.closest('#editNavWrap')) closeEditDropdown(); });

/* ══════════════════════════════
   LOGIN
══════════════════════════════ */
function openLogin(){ document.getElementById('loginModal').classList.add('open'); }
function closeLogin(){ document.getElementById('loginModal').classList.remove('open'); }

function doLogin(){
  const u = document.getElementById('loginUser').value.trim();
  const p = document.getElementById('loginPass').value;
  document.getElementById('loginErr').style.display = 'none';

  if(u === ADMIN_USER && p === ADMIN_PASS){
    SESSION = {user:u, role:'admin', nombre:'Eliam Cobián'};
    closeLogin();
    injectAdminNav();
    buildCostosUI();
    showAdminPage('admin-agenda');
    showToast('✅ Bienvenido, Eliam!');
    return;
  }
  const emp = empleados.find(e => e.user === u && e.pass === p);
  if(emp){
    SESSION = {user:u, role:'empleado', nombre:emp.nombre, empId:emp.id};
    closeLogin();
    injectEmpNav();
    showAdminPage('emp-perfil');
    showToast('✅ Bienvenido, '+emp.nombre+'!');
    return;
  }
  document.getElementById('loginErr').style.display = 'block';
}

function logout(){
  SESSION = null;
  document.querySelectorAll('.dyn-nav').forEach(b => b.remove());
  const heroBtn = document.getElementById('heroAdminBtn');
  heroBtn.textContent = '🔒 Admin';
  heroBtn.style.cssText = '';
  heroBtn.onclick = openLogin;
  showPage('paquetes', document.getElementById('nbtn-paquetes'));
  showToast('👋 Sesión cerrada');
}

function injectAdminNav(){
  document.querySelectorAll('.dyn-nav').forEach(b => b.remove());
  document.getElementById('heroAdminBtn').style.display = 'none';
  const nav = document.getElementById('mainNav');
  const pages = [
    {id:'admin-agenda',  icon:'🗓', lbl:'Agenda'},
    {id:'admin-costos',  icon:'💰', lbl:'Costos'},
    {id:'admin-clientes',icon:'👥', lbl:'Clientes'},
    {id:'admin-stats',   icon:'📊', lbl:'Estadísticas'},
    {id:'admin-equipo',  icon:'👔', lbl:'Equipo'},
  ];
  pages.forEach(p=>{
    const b = document.createElement('button');
    b.className = 'nav-btn dyn-nav'; b.id = 'nb-'+p.id;
    b.textContent = p.icon+' '+p.lbl;
    b.style.color = 'var(--gold)';
    b.onclick = ()=>showAdminPage(p.id);
    nav.appendChild(b);
  });
  // Botón cerrar sesión
  const heroBtn = document.getElementById('heroAdminBtn');
  heroBtn.textContent = '🔓 Salir';
  heroBtn.style.display = '';
  heroBtn.style.background = 'rgba(239,68,68,.1)';
  heroBtn.style.borderColor = 'rgba(239,68,68,.3)';
  heroBtn.style.color = 'var(--red)';
  heroBtn.onclick = logout;
}

function injectEmpNav(){
  document.querySelectorAll('.dyn-nav').forEach(b => b.remove());
  document.getElementById('heroAdminBtn').style.display = 'none';
  const nav = document.getElementById('mainNav');
  const b = document.createElement('button');
  b.className = 'nav-btn dyn-nav'; b.id = 'nb-emp-perfil';
  b.textContent = '👤 Mi Perfil'; b.style.color = 'var(--gold)';
  b.onclick = ()=>{ showAdminPage('emp-perfil'); renderEmpPerfil(empleados.find(e=>e.id===SESSION.empId)||{}); };
  nav.appendChild(b);
  const heroBtn = document.getElementById('heroAdminBtn');
  heroBtn.textContent = '🔓 Salir'; heroBtn.style.display = '';
  heroBtn.style.background = 'rgba(239,68,68,.1)';
  heroBtn.style.color = 'var(--red)'; heroBtn.onclick = logout;
}

/* ══════════════════════════════
   PÚBLICOS: ADICIONALES
══════════════════════════════ */
function renderAdPub(){
  document.getElementById('adPubGrid').innerHTML = adData.map(a=>{
    if(a.tapiceria){
      return `<div class="ad-card">
        <div class="ad-header"><span style="font-size:1.6rem">${a.icon}</span><div class="ad-title">${a.nombre}</div></div>
        <div class="ad-desc">${a.desc}</div>
        <div class="tap-wapp">📲 Cotización vía WhatsApp con foto del vehículo</div>
      </div>`;
    }
    return `<div class="ad-card">
      <div class="ad-header"><span style="font-size:1.6rem">${a.icon}</span><div class="ad-title">${a.nombre}</div></div>
      <div class="ad-desc">${a.desc}</div>
      <div class="ad-prices-row">
        <div class="apb"><div class="aps">Chico</div><div class="apa">$${a.precios.ch}</div></div>
        <div class="apb"><div class="aps">Mediano</div><div class="apa">$${a.precios.md}</div></div>
        <div class="apb"><div class="aps">Grande</div><div class="apa">$${a.precios.gd}</div></div>
      </div>
    </div>`;
  }).join('');
}

function renderAdCheckboxes(){
  document.getElementById('adCheckboxes').innerHTML = adData.map(a=>`
    <div class="chk-item">
      <input type="checkbox" id="adck-${a.id}" value="${a.id}" onchange="updatePreview()">
      <label for="adck-${a.id}">${a.icon} ${a.nombre}${a.tapiceria?'<span style="color:var(--orange);font-size:.72rem"> (WhatsApp con foto)</span>':''}</label>
    </div>`).join('');
}

/* ══════════════════════════════
   FORMULARIO CITA PÚBLICA
══════════════════════════════ */
function timeToMin(t){
  if(!t) return 0;
  const [hStr, period] = t.split(' ');
  let [h,m] = hStr.split(':').map(Number);
  if(period==='PM' && h!==12) h+=12;
  if(period==='AM' && h===12) h=0;
  return h*60+(m||0);
}
function addMinToTime(t, min){
  const base = timeToMin(t)+min;
  const h = Math.floor(base/60)%24, m = base%60;
  const period = h>=12?'PM':'AM';
  const dh = h%12||12;
  return `${dh}:${String(m).padStart(2,'0')} ${period}`;
}
function calcTotalDuration(tamano, paquete, additionals){
  const base = pkgTimes[paquete]?.[tamano]||0;
  let extra = 0;
  if(Array.isArray(additionals)){
    additionals.forEach(aid=>{
      const a = adData.find(x=>x.id===aid||x.nombre===aid);
      if(a && a.tiempos) extra += a.tiempos[tamano]||0;
    });
  }
  return base+extra;
}
function minToLabel(m){
  if(m>=60) return Math.floor(m/60)+'h'+(m%60?' '+m%60+'m':'');
  return m+'min';
}
function getAvailableHours(fecha, tamano, paquete, selectedAds){
  if(!fecha || !fechasDisp[fecha]) return [];
  const allHours = fechasDisp[fecha];
  const durMin = calcTotalDuration(tamano, paquete, selectedAds);
  return allHours.filter(h=>{
    const startMin = timeToMin(h);
    const endMin   = startMin+durMin;
    for(const c of citas){
      if(c.fecha!==fecha || c.status==='cancel') continue;
      const cStart = timeToMin(c.hora);
      const cDur   = calcTotalDuration(c.tamano, c.paquete, c.adicionales);
      const cEnd   = cStart+cDur;
      if(startMin<cEnd && endMin>cStart) return false;
    }
    return true;
  });
}

function populateFechas(){
  const sel = document.getElementById('fFecha'); if(!sel) return;
  const today = new Date().toISOString().split('T')[0];
  const validDates = Object.keys(fechasDisp).filter(f=>f>=today && fechasDisp[f].length>0).sort();
  sel.innerHTML = validDates.length
    ? '<option value="">Selecciona una fecha…</option>'
    : '<option value="">Sin fechas disponibles por ahora</option>';
  validDates.forEach(f=>{
    const d = new Date(f+'T12:00:00');
    const label = d.toLocaleDateString('es-MX',{weekday:'long',day:'numeric',month:'long'});
    const o = document.createElement('option'); o.value = f;
    o.textContent = label.charAt(0).toUpperCase()+label.slice(1);
    sel.appendChild(o);
  });
}

function populateHoras(){
  const sel = document.getElementById('fHora'); if(!sel) return;
  const fecha   = document.getElementById('fFecha').value;
  const tamano  = document.getElementById('fTamano').value;
  const paquete = document.getElementById('fPaquete').value;
  const adSel   = [...document.querySelectorAll('#adCheckboxes input:checked')].map(c=>c.value);
  sel.innerHTML = '<option value="">Selecciona hora…</option>';
  if(!fecha) return;
  const hours = getAvailableHours(fecha, tamano, paquete, adSel);
  hours.forEach(h=>{
    const o = document.createElement('option'); o.value = h; o.textContent = h;
    sel.appendChild(o);
  });
}

function updatePreview(){
  populateHoras();
  const tamano  = document.getElementById('fTamano').value;
  const paquete = document.getElementById('fPaquete').value;
  const fecha   = document.getElementById('fFecha').value;
  const hora    = document.getElementById('fHora').value;
  const prev    = document.getElementById('summaryPreview');
  if(!tamano || !paquete){ prev.style.display='none'; return; }
  const precio = pkgPrices[paquete]?.[tamano]||0;
  const adSel  = [...document.querySelectorAll('#adCheckboxes input:checked')].map(c=>c.value);
  let adTotal=0, adNames=[], hasWapp=false;
  adSel.forEach(aid=>{
    const a = adData.find(x=>x.id===aid);
    if(a){ if(a.tapiceria){ hasWapp=true; } else { adTotal+=a.precios[tamano]||0; adNames.push(a.nombre); } }
  });
  const durTotal = calcTotalDuration(tamano, paquete, adSel);
  const total    = precio+adTotal;
  const horaFin  = hora ? addMinToTime(hora, durTotal) : '';
  let html = `
    <div class="sp-row"><span class="sp-lbl">Paquete</span><span class="sp-val">${paquete.toUpperCase()}</span></div>
    <div class="sp-row"><span class="sp-lbl">Tamaño</span><span class="sp-val">${sizeLabel[tamano]||tamano}</span></div>
    <div class="sp-row"><span class="sp-lbl">Precio paquete</span><span class="sp-val">$${precio} MXN</span></div>`;
  if(adNames.length) html+=`<div class="sp-row"><span class="sp-lbl">Adicionales</span><span class="sp-val">${adNames.join(', ')} (+$${adTotal})</span></div>`;
  if(hasWapp) html+=`<div class="sp-row"><span class="sp-lbl" style="color:var(--orange)">Tapicería</span><span class="sp-val" style="color:var(--orange)">Cotización vía WhatsApp 📲</span></div>`;
  if(fecha) html+=`<div class="sp-row"><span class="sp-lbl">Fecha</span><span class="sp-val">${new Date(fecha+'T12:00:00').toLocaleDateString('es-MX',{weekday:'long',day:'numeric',month:'long'})}</span></div>`;
  if(hora)  html+=`<div class="sp-row"><span class="sp-lbl">Horario</span><span class="sp-val">${hora}${horaFin?' — '+horaFin:''}</span></div>`;
  html+=`<div class="sp-row"><span class="sp-lbl">Duración aprox.</span><span class="sp-val">${minToLabel(durTotal)}</span></div>`;
  html+=`<div class="sp-row"><span class="sp-lbl">TOTAL ESTIMADO</span><span class="sp-val" style="color:var(--blue-lt);font-size:1rem">$${total} MXN</span></div>`;
  document.getElementById('summaryContent').innerHTML = html;
  prev.style.display = 'block';
}

async function submitCita(){
  if(!window._fbReady){ showToast('⚠️ Conectando con la base de datos…'); return; }
  const nombre   = document.getElementById('fNombre').value.trim();
  const apellido = document.getElementById('fApellido').value.trim();
  const tel      = document.getElementById('fTel').value.trim();
  const coto     = document.getElementById('fCoto').value.trim();
  const casa     = document.getElementById('fCasa').value.trim();
  const vehiculo = document.getElementById('fVehiculo').value.trim();
  const tamano   = document.getElementById('fTamano').value;
  const paquete  = document.getElementById('fPaquete').value;
  const fecha    = document.getElementById('fFecha').value;
  const hora     = document.getElementById('fHora').value;
  if(!nombre||!apellido||!tel||!coto||!casa||!vehiculo||!tamano||!paquete||!fecha||!hora){
    showToast('⚠️ Por favor completa todos los campos'); return;
  }
  const adSel = [...document.querySelectorAll('#adCheckboxes input:checked')].map(c=>{
    const a = adData.find(x=>x.id===c.value); return a?a.nombre:'';
  }).filter(Boolean);
  const notas  = document.getElementById('fNotas').value;
  const precio = pkgPrices[paquete]?.[tamano]||0;
  const btn    = document.getElementById('btnSubmitCita');
  btn.disabled = true; btn.textContent = 'Enviando…';
  try {
    // Guardar solicitud en Firebase
    await window.fbAdd('solicitudes', {
      nombre:nombre+' '+apellido, tel, coto, casa, vehiculo,
      tamano, paquete, adicionales:adSel, fecha, hora, notas,
      status:'pending', costo:precio
    });
    // Crear/actualizar cliente
    const existing = clientes.find(c=>c.tel===tel);
    if(!existing){
      await window.fbAdd('clientes', {
        nombre:nombre+' '+apellido, tel, coto,
        vehiculos:[vehiculo], visitas:0, estrellas:0,
        propina:false, horarioIdeal:hora, notas:'', ultimaCita:fecha
      });
    }
    document.getElementById('citaFormBody').style.display = 'none';
    document.getElementById('citaSuccess').style.display  = 'block';
    showToast('✅ Solicitud enviada con éxito');
  } catch(err){
    showToast('❌ Error al enviar. Verifica tu conexión.');
    console.error(err);
    btn.disabled = false; btn.textContent = 'CONFIRMAR SOLICITUD';
  }
}

function resetCitaForm(){
  document.getElementById('citaFormBody').style.display  = 'block';
  document.getElementById('citaSuccess').style.display   = 'none';
  document.getElementById('btnSubmitCita').disabled       = false;
  document.getElementById('btnSubmitCita').textContent    = 'CONFIRMAR SOLICITUD';
  ['fNombre','fApellido','fTel','fCoto','fCasa','fVehiculo','fNotas'].forEach(id=>{
    const el = document.getElementById(id); if(el) el.value='';
  });
  ['fTamano','fPaquete','fFecha','fHora'].forEach(id=>{
    const el = document.getElementById(id); if(el) el.value='';
  });
  document.querySelectorAll('#adCheckboxes input:checked').forEach(c=>c.checked=false);
  document.getElementById('summaryPreview').style.display = 'none';
}

/* ══════════════════════════════
   CALENDARIO
══════════════════════════════ */
function renderCalendar(){
  const cont = document.getElementById('agenda-calendar'); if(!cont) return;
  const first = new Date(calY,calM,1), last = new Date(calY,calM+1,0);
  const today = new Date(); const startDow = first.getDay();
  let cells = '';
  for(let i=0;i<startDow;i++){
    const d = new Date(calY,calM,i-startDow+1);
    cells += `<div class="cal-day other-month"><div class="dn">${d.getDate()}</div></div>`;
  }
  for(let d=1;d<=last.getDate();d++){
    const ds = `${calY}-${String(calM+1).padStart(2,'0')}-${String(d).padStart(2,'0')}`;
    const isToday = today.getFullYear()===calY && today.getMonth()===calM && today.getDate()===d;
    const avail = fechasDisp[ds] && fechasDisp[ds].length>0;
    const dc = citas.filter(c=>c.fecha===ds && c.status!=='cancel');
    const dots = dc.map(c=>`<div class="ddot ${c.status==='done'?'done':''}" onclick="event.stopPropagation();openCitaDetail('${c.id}')"></div>`).join('');
    cells += `<div class="cal-day${avail?' available':''}${isToday?' today':''}" onclick="openDayPanel('${ds}')">
      <div class="dn">${d}</div><div class="ddots">${dots}</div>
    </div>`;
  }
  cont.innerHTML = `
    <div class="cal-wrap">
      <div class="cal-head">
        <button class="cal-nav" onclick="calPrev()">‹</button>
        <h3>${MESES[calM]} ${calY}</h3>
        <button class="cal-nav" onclick="calNext()">›</button>
      </div>
      <div class="cal-grid">
        ${DIAS_SHORT.map(d=>`<div class="cal-dow">${d}</div>`).join('')}${cells}
      </div>
    </div>
    <div class="tip-box">🟡 Punto = cita pendiente &nbsp;·&nbsp; 🟢 Verde = completada &nbsp;·&nbsp; Borde verde = día disponible</div>`;
}
function calPrev(){ calM--; if(calM<0){calM=11;calY--;} renderCalendar(); }
function calNext(){ calM++; if(calM>11){calM=0;calY++;} renderCalendar(); }

function openDayPanel(ds){
  const dc = citas.filter(c=>c.fecha===ds && c.status!=='cancel');
  if(dc.length===1){ openCitaDetail(dc[0].id); return; }
  const d = new Date(ds+'T12:00:00');
  document.getElementById('cpTitle').textContent = d.toLocaleDateString('es-MX',{weekday:'long',day:'numeric',month:'long'});
  if(dc.length>1){
    document.getElementById('cpBody').innerHTML = dc.map(c=>`
      <div style="padding:8px 0;border-bottom:1px solid var(--border);cursor:pointer" onclick="openCitaDetail('${c.id}')">
        <div style="font-weight:700;font-size:.85rem">${c.nombre}</div>
        <div style="font-size:.72rem;color:var(--muted)">${c.hora} · ${c.paquete.toUpperCase()} · ${sizeLabel[c.tamano]||c.tamano}</div>
      </div>`).join('');
  } else {
    document.getElementById('cpBody').innerHTML = '<div class="empty"><span>📅</span>Sin citas este día.</div>';
  }
  document.getElementById('cpActions').innerHTML = '<button class="btn-sm btn-blue" onclick="document.getElementById(\'citaPopup\').classList.remove(\'open\')">Cerrar</button>';
  document.getElementById('citaPopup').classList.add('open');
}

function openCitaDetail(id){
  const c = citas.find(x=>x.id===id); if(!c) return;
  const d = new Date(c.fecha+'T12:00:00').toLocaleDateString('es-MX',{weekday:'long',day:'numeric',month:'long',year:'numeric'});
  const dur = calcTotalDuration(c.tamano, c.paquete, c.adicionales);
  const fin = addMinToTime(c.hora, dur);
  const nombresEmp = ['Eliam Cobián', ...empleados.map(e=>e.nombre)];
  document.getElementById('cpTitle').textContent = 'CITA — '+c.nombre;
  document.getElementById('cpBody').innerHTML = `
    <div class="cp-row"><span class="cp-lbl">Cliente</span><span class="cp-val">${c.nombre}</span></div>
    <div class="cp-row"><span class="cp-lbl">Teléfono</span><span class="cp-val">${c.tel}</span></div>
    <div class="cp-row"><span class="cp-lbl">Domicilio</span><span class="cp-val">${c.coto} #${c.casa||''}</span></div>
    <div class="cp-row"><span class="cp-lbl">Vehículo</span><span class="cp-val">${c.vehiculo} — ${sizeLabel[c.tamano]||c.tamano}</span></div>
    <div class="cp-row"><span class="cp-lbl">Paquete</span><span class="cp-val">${c.paquete.toUpperCase()}</span></div>
    <div class="cp-row"><span class="cp-lbl">Adicionales</span><span class="cp-val">${(c.adicionales||[]).length?(c.adicionales).join(', '):'—'}</span></div>
    <div class="cp-row"><span class="cp-lbl">Fecha</span><span class="cp-val">${d}</span></div>
    <div class="cp-row"><span class="cp-lbl">Horario</span><span class="cp-val">${c.hora} — ${fin} (${minToLabel(dur)})</span></div>
    <div class="cp-row"><span class="cp-lbl">Asignado a</span><span class="cp-val">
      <select style="background:#07101f;border:1px solid var(--border);color:#fff;border-radius:6px;padding:3px 7px;font-size:.76rem" onchange="assignCita('${c.id}',this.value)">
        ${nombresEmp.map(n=>`<option ${c.asignado===n?'selected':''}>${n}</option>`).join('')}
      </select>
    </span></div>
    <div class="cp-row"><span class="cp-lbl">Propina recibida</span><span class="cp-val">
      <input type="number" value="${c.propinaDada||0}" min="0" placeholder="$0"
        style="width:80px;background:#07101f;border:1px solid var(--border);color:var(--gold);border-radius:6px;padding:4px 8px;font-size:.82rem;outline:none"
        onchange="setPropina('${c.id}',this.value)"> MXN
    </span></div>
    <div class="cp-row"><span class="cp-lbl">Notas</span><span class="cp-val">${c.notas||'—'}</span></div>
    <div class="cp-row"><span class="cp-lbl">TOTAL</span><span class="cp-val" style="color:var(--blue-lt);font-family:'Bebas Neue',sans-serif;font-size:1.1rem">$${(c.costo||0)+(c.propinaDada||0)} MXN</span></div>`;
  document.getElementById('cpActions').innerHTML = `
    <button class="btn-sm btn-green" onclick="completarCita('${c.id}')">✓ Completada</button>
    <button class="btn-sm btn-red" onclick="eliminarCita('${c.id}')">🗑 Eliminar</button>
    <button class="btn-sm btn-blue" onclick="document.getElementById('citaPopup').classList.remove('open')">Cerrar</button>`;
  document.getElementById('citaPopup').classList.add('open');
}

async function assignCita(id, nombre){
  try { await window.fbUpd('citas', id, {asignado:nombre}); showToast('✅ Asignado a '+nombre); }
  catch(e){ showToast('❌ Error al asignar'); }
}
async function setPropina(id, val){
  const v = parseFloat(val)||0;
  try { await window.fbUpd('citas', id, {propinaDada:v}); }
  catch(e){ showToast('❌ Error al guardar propina'); }
}
async function completarCita(id){
  const c = citas.find(x=>x.id===id); if(!c) return;
  try {
    await window.fbUpd('citas', id, {status:'done'});
    // Actualizar cliente
    const cli = clientes.find(x=>x.tel===c.tel);
    if(cli){
      await window.fbUpd('clientes', cli.id, {
        visitas:(cli.visitas||0)+1,
        ultimaCita:c.fecha
      });
    }
    document.getElementById('citaPopup').classList.remove('open');
    showToast('✅ Cita marcada como completada');
    renderCalendar();
  } catch(e){ showToast('❌ Error al completar cita'); }
}
async function eliminarCita(id){
  if(!confirm('¿Eliminar esta cita? Esta acción no se puede deshacer.')) return;
  try {
    await window.fbDel('citas', id);
    document.getElementById('citaPopup').classList.remove('open');
    showToast('🗑 Cita eliminada');
    renderCalendar();
  } catch(e){ showToast('❌ Error al eliminar cita'); }
}

/* ══════════════════════════════
   SOLICITUDES
══════════════════════════════ */
function renderSolicitudes(){
  const cont = document.getElementById('agenda-solicitudes'); if(!cont) return;
  const pend = solicitudes.filter(s=>s.status==='pending');
  if(!pend.length){ cont.innerHTML='<div class="empty"><span>📩</span>Sin solicitudes pendientes.</div>'; return; }
  cont.innerHTML = `<div class="sol-list">${pend.map(s=>{
    const d = new Date(s.fecha+'T12:00:00').toLocaleDateString('es-MX',{day:'numeric',month:'long',year:'numeric'});
    const nombresEmp = ['Eliam Cobián',...empleados.map(e=>e.nombre)];
    return `<div class="sol-card" id="sol-${s.id}">
      <div class="sol-top"><div><div class="sol-name">${s.nombre}</div><div style="font-size:.7rem;color:var(--muted)">${s.tel} · ${s.coto} #${s.casa||''}</div></div><div class="sol-date">${d} · ${s.hora}</div></div>
      <div class="sol-info">🚗 ${s.vehiculo} — <strong>${sizeLabel[s.tamano]||s.tamano}</strong><br>
        📦 ${s.paquete?.toUpperCase()} — $${s.costo} MXN
        ${(s.adicionales||[]).length?'<br>✨ '+(s.adicionales).join(', '):''}
        ${s.notas?'<br>📝 '+s.notas:''}
      </div>
      <div style="display:flex;align-items:center;gap:8px;margin-bottom:10px;flex-wrap:wrap">
        <label style="font-size:.62rem;color:var(--muted);text-transform:uppercase;letter-spacing:1px">Asignar:</label>
        <select id="asig-${s.id}" style="background:#07101f;border:1px solid var(--border);color:#fff;border-radius:6px;padding:4px 8px;font-size:.76rem">
          ${nombresEmp.map(n=>`<option>${n}</option>`).join('')}
        </select>
      </div>
      <div style="display:flex;gap:7px;flex-wrap:wrap">
        <button class="btn-sm btn-green" onclick="aceptarSol('${s.id}')">✓ Aceptar</button>
        <button class="btn-sm btn-red" onclick="rechazarSol('${s.id}')">✕ Rechazar</button>
      </div>
      <div class="msg-box" id="msg-${s.id}"></div>
      <div class="msg-actions" id="msgact-${s.id}" style="display:none">
        <button class="copy-btn" onclick="copyMsg('${s.id}')">📋 Copiar WhatsApp</button>
        <button class="copy-btn" onclick="cerrarSol('${s.id}')">✕ Cerrar</button>
      </div>
    </div>`;
  }).join('')}</div>`;
}

async function aceptarSol(id){
  const s = solicitudes.find(x=>x.id===id); if(!s) return;
  const asig = document.getElementById('asig-'+id)?.value||'Eliam Cobián';
  try {
    await window.fbUpd('solicitudes', id, {status:'accepted', asignado:asig});
    await window.fbAdd('citas', {
      nombre:s.nombre, tel:s.tel, coto:s.coto, casa:s.casa,
      vehiculo:s.vehiculo, tamano:s.tamano, paquete:s.paquete,
      adicionales:s.adicionales||[], fecha:s.fecha, hora:s.hora,
      notas:s.notas||'', status:'pending', asignado:asig,
      costo:s.costo, propinaDada:0
    });
    const d = new Date(s.fecha+'T12:00:00').toLocaleDateString('es-MX',{weekday:'long',day:'numeric',month:'long'});
    const msg = `¡Hola ${s.nombre.split(' ')[0]}! 😊 Soy de *Bianco Wash Las Terrazas* y te confirmo tu cita 🚗✨\n\n📅 ${d}\n🕐 ${s.hora}\n📍 ${s.coto} #${s.casa}\n📦 ${s.paquete?.toUpperCase()}${(s.adicionales||[]).length?'\n✨ '+s.adicionales.join(', '):''}\n💰 $${s.costo} MXN\n\n¡Nos vemos pronto! 🫧`;
    showSolMsg(id, msg);
    renderCalendar();
    showToast('✅ Solicitud aceptada y cita creada');
  } catch(e){ showToast('❌ Error: '+e.message); }
}

async function rechazarSol(id){
  const s = solicitudes.find(x=>x.id===id); if(!s) return;
  try {
    await window.fbUpd('solicitudes', id, {status:'rejected'});
    const alts = Object.keys(fechasDisp).filter(f=>f>s.fecha && fechasDisp[f].length>0).sort().slice(0,2);
    const altLabel = alts.length
      ? alts.map(f=>new Date(f+'T12:00:00').toLocaleDateString('es-MX',{weekday:'long',day:'numeric',month:'long'})).join(' o ')
      : 'próximamente';
    const msg = `¡Hola ${s.nombre.split(' ')[0]}! 😊 Gracias por contactar a *Bianco Wash Las Terrazas*.\n\nLamentablemente para esa fecha no tengo disponibilidad, pero podemos reagendar para el ${altLabel} 📅\n\n¿Te acomoda alguna opción? ¡Quedo al pendiente! 🫧`;
    showSolMsg(id, msg);
    showToast('❌ Solicitud rechazada');
  } catch(e){ showToast('❌ Error: '+e.message); }
}

function showSolMsg(id, msg){
  const box = document.getElementById('msg-'+id);
  const act = document.getElementById('msgact-'+id);
  if(box){ box.textContent=msg; box.style.display='block'; }
  if(act) act.style.display='flex';
}
function copyMsg(id){
  const box = document.getElementById('msg-'+id);
  if(box) navigator.clipboard.writeText(box.textContent).then(()=>showToast('📋 Mensaje copiado'));
}
function cerrarSol(id){
  const card = document.getElementById('sol-'+id); if(card) card.remove();
}

/* ══════════════════════════════
   DISPONIBILIDAD
══════════════════════════════ */
function renderDisponibilidad(){
  const cont = document.getElementById('agenda-disponibilidad'); if(!cont) return;
  const now = new Date();
  const todayStr = now.toISOString().split('T')[0];
  let days = [];
  if(dispViewMode==='mes'){
    const y=now.getFullYear(), m=now.getMonth();
    const last = new Date(y,m+1,0).getDate();
    for(let d=1;d<=last;d++){
      const ds = `${y}-${String(m+1).padStart(2,'0')}-${String(d).padStart(2,'0')}`;
      if(ds>=todayStr) days.push(ds);
    }
  } else {
    const y=now.getFullYear();
    for(let m=now.getMonth();m<12;m++){
      const last=new Date(y,m+1,0).getDate();
      for(let d=1;d<=last;d++){
        const ds=`${y}-${String(m+1).padStart(2,'0')}-${String(d).padStart(2,'0')}`;
        if(ds>=todayStr) days.push(ds);
      }
    }
  }
  cont.innerHTML = `
    <div class="disp-months">
      <button class="disp-month-btn${dispViewMode==='mes'?' active':''}" onclick="dispViewMode='mes';renderDisponibilidad()">Mes actual</button>
      <button class="disp-month-btn${dispViewMode==='anio'?' active':''}" onclick="dispViewMode='anio';renderDisponibilidad()">Año actual</button>
    </div>
    <div style="font-size:.78rem;color:var(--text2);margin-bottom:14px">
      Toca un horario para activarlo/desactivarlo. Los cambios se guardan automáticamente en la nube ☁️
    </div>
    <div class="disp-grid">
      ${days.map(f=>{
        const d = new Date(f+'T12:00:00');
        const label = d.toLocaleDateString('es-MX',{weekday:'short',day:'numeric',month:'short'});
        const on = fechasDisp[f] && fechasDisp[f].length>0;
        const chips = HOURS_LIST.map(h=>{
          const active = fechasDisp[f] && fechasDisp[f].includes(h);
          return `<div class="hr-chip${active?' on':''}" onclick="toggleHour('${f}','${h}')">${h}</div>`;
        }).join('');
        return `<div class="disp-day${on?' on':''}" id="disp-${f}">
          <div class="disp-date">${label}</div>
          <div class="disp-label">${on?'✓ Activo':'Inactivo'}</div>
          <div class="disp-hours">${chips}</div>
        </div>`;
      }).join('')}
    </div>`;
}

async function toggleHour(fecha, hora){
  if(!window._fbReady){ showToast('⚠️ Cargando Firebase…'); return; }
  const current = fechasDisp[fecha] ? [...fechasDisp[fecha]] : [];
  const idx = current.indexOf(hora);
  if(idx>=0) current.splice(idx,1); else current.push(hora);
  // Ordenar horas
  current.sort((a,b)=>timeToMin(a)-timeToMin(b));
  try {
    await window.fbSet('disponibilidad', fecha, {horas:current, fecha});
    showToast(current.length ? '✅ '+hora+' activado' : '🔴 Hora desactivada');
  } catch(e){ showToast('❌ Error al guardar: '+e.message); }
}

/* ══════════════════════════════
   COSTOS OPERATIVOS (local, sin Firebase)
══════════════════════════════ */
function buildCostosUI(){ buildPkgUI(); buildAdUI(); }

function buildPkgUI(){
  const cont = document.getElementById('costos-pkg'); if(!cont) return;
  cont.innerHTML = pkgData.map(pkg=>`
    <div class="cost-card">
      <div class="cc-head open" onclick="this.classList.toggle('open');this.nextElementSibling.classList.toggle('open')">
        <div class="cc-num">${pkg.num}</div>
        <div><div class="cc-title">${pkg.nombre}</div><div class="cc-desc">${pkg.desc}</div></div>
        <span class="chevron">▼</span>
      </div>
      <div class="cc-body open">
        <div class="ins-wrap">
          <div class="ins-title">📦 INSUMOS</div>
          <div class="ins-hdr"><span>Nombre</span><span>Marca</span><span>Precio</span><span>Rend.</span><span>C/uso</span><span></span></div>
          <div id="ins-${pkg.id}"></div>
          <button class="add-ins-btn" onclick="addIns('${pkg.id}')">+ Agregar insumo</button>
          <div class="ins-total">Total insumos/uso: <strong id="ti-${pkg.id}">$0.00</strong></div>
        </div>
        <div class="igrid">
          <div class="ic"><label>Traslado/gasolina ($)</label><input type="number" id="pt-${pkg.id}" value="${pkg.traslado}" oninput="recalcPkg('${pkg.id}')"></div>
          <div class="ic"><label>Desgaste equipo ($)</label><input type="number" id="pe-${pkg.id}" value="${pkg.equipo}" oninput="recalcPkg('${pkg.id}')"></div>
          <div class="ic"><label>Tiempo CH (min)</label><input type="number" id="pch-${pkg.id}" value="${pkg.tiempos.ch}" oninput="recalcPkg('${pkg.id}')"></div>
          <div class="ic"><label>Tiempo MD (min)</label><input type="number" id="pmd-${pkg.id}" value="${pkg.tiempos.md}" oninput="recalcPkg('${pkg.id}')"></div>
          <div class="ic"><label>Tiempo GD (min)</label><input type="number" id="pgd-${pkg.id}" value="${pkg.tiempos.gd}" oninput="recalcPkg('${pkg.id}')"></div>
        </div>
        <div class="sizes-bar"><div class="sz-hdr">CHICO</div><div class="sz-hdr">MEDIANO</div><div class="sz-hdr">GRANDE</div></div>
        <div class="igrid" style="border-top:1px solid var(--border)">
          <div class="ic"><label>Precio CH ($)</label><input type="number" id="pp-ch-${pkg.id}" value="${pkg.precios.ch}" oninput="recalcPkg('${pkg.id}')"></div>
          <div class="ic"><label>Precio MD ($)</label><input type="number" id="pp-md-${pkg.id}" value="${pkg.precios.md}" oninput="recalcPkg('${pkg.id}')"></div>
          <div class="ic"><label>Precio GD ($)</label><input type="number" id="pp-gd-${pkg.id}" value="${pkg.precios.gd}" oninput="recalcPkg('${pkg.id}')"></div>
        </div>
        <div class="res-row" id="pr-${pkg.id}"></div>
      </div>
    </div>`).join('');
  pkgData.forEach(pkg=>{ pkg.insumos.forEach(ins=>addInsRow(pkg.id,ins)); recalcPkg(pkg.id); });
}

function addIns(pkgId,ins={}){ addInsRow(pkgId,ins); recalcPkg(pkgId); }
function addInsRow(pkgId,ins={}){
  if(!insCnt[pkgId]) insCnt[pkgId]=0;
  const iid = `i-${pkgId}-${insCnt[pkgId]++}`;
  const cont = document.getElementById('ins-'+pkgId); if(!cont) return;
  const div = document.createElement('div'); div.className='ins-row'; div.id=iid;
  div.innerHTML = `
    <input type="text" placeholder="Nombre" value="${ins.nombre||''}" oninput="recalcPkg('${pkgId}')">
    <input type="text" placeholder="Marca"  value="${ins.marca||''}"  oninput="recalcPkg('${pkgId}')">
    <input type="number" placeholder="$"    value="${ins.precio||0}"  oninput="recalcPkg('${pkgId}')">
    <input type="number" placeholder="Usos" value="${ins.rendimiento||1}" min="1" oninput="recalcPkg('${pkgId}')">
    <span class="ins-cxu" id="cxu-${iid}">$${ins.precio&&ins.rendimiento?(ins.precio/ins.rendimiento).toFixed(2):'0.00'}</span>
    <button class="ins-del" onclick="document.getElementById('${iid}').remove();recalcPkg('${pkgId}')">🗑</button>`;
  cont.appendChild(div);
}

function recalcPkg(pkgId){
  let ti=0;
  document.querySelectorAll(`#ins-${pkgId} .ins-row`).forEach(row=>{
    const inps = row.querySelectorAll('input[type=number]');
    const p=parseFloat(inps[0]?.value)||0, r=parseFloat(inps[1]?.value)||1;
    const cxu=p/r; ti+=cxu;
    const el=row.querySelector('.ins-cxu'); if(el) el.textContent='$'+cxu.toFixed(2);
  });
  document.getElementById('ti-'+pkgId)?.setAttribute('textContent','');
  const tiEl=document.getElementById('ti-'+pkgId); if(tiEl) tiEl.textContent='$'+ti.toFixed(2);
  const tras=parseFloat(document.getElementById('pt-'+pkgId)?.value)||0;
  const eq  =parseFloat(document.getElementById('pe-'+pkgId)?.value)||0;
  const tch =parseFloat(document.getElementById('pch-'+pkgId)?.value)||0;
  const tmd =parseFloat(document.getElementById('pmd-'+pkgId)?.value)||0;
  const tgd =parseFloat(document.getElementById('pgd-'+pkgId)?.value)||0;
  const pch =parseFloat(document.getElementById('pp-ch-'+pkgId)?.value)||0;
  const pmd =parseFloat(document.getElementById('pp-md-'+pkgId)?.value)||0;
  const pgd =parseFloat(document.getElementById('pp-gd-'+pkgId)?.value)||0;
  const useEmp  =document.getElementById('useEmp')?.checked;
  const hrOwner =parseFloat(document.getElementById('hrOwner')?.value)||120;
  const cont=document.getElementById('pr-'+pkgId); if(!cont) return;
  cont.innerHTML=[{s:'CH',p:pch,t:tch},{s:'MD',p:pmd,t:tmd},{s:'GD',p:pgd,t:tgd}].map(({s,p,t})=>{
    const moCost=useEmp?p*0.5:hrOwner*(t/60);
    const cost=ti+tras+eq+(useEmp?moCost:0);
    const mg=p-cost-(useEmp?0:moCost); const pct=p>0?(mg/p*100).toFixed(0):0;
    const cls=pct>=30?'good':pct>=15?'warn':'bad';
    return `<div class="rc"><div class="rl">${s} — Costo op.</div><div class="rv-cost">$${cost.toFixed(0)}</div>
      <div class="rl" style="margin-top:7px">Utilidad</div>
      <div class="rv-mg ${cls}">$${(p-ti-tras-eq-moCost).toFixed(0)} · ${pct}%</div></div>`;
  }).join('');
}

function buildAdUI(){
  const cont=document.getElementById('costos-ad'); if(!cont) return;
  cont.innerHTML=adData.map(a=>`
    <div class="cost-card">
      <div class="cc-head open" onclick="this.classList.toggle('open');this.nextElementSibling.classList.toggle('open')">
        <span style="font-size:1.4rem">${a.icon}</span>
        <div><div class="cc-title">${a.nombre}</div></div>
        <span class="chevron">▼</span>
      </div>
      <div class="cc-body open">
        <div class="igrid">
          <div class="ic"><label>Tiempo CH (min)</label><input type="number" id="atch-${a.id}" value="${a.tiempos.ch}" oninput="recalcAd('${a.id}')"></div>
          <div class="ic"><label>Tiempo MD (min)</label><input type="number" id="atmd-${a.id}" value="${a.tiempos.md}" oninput="recalcAd('${a.id}')"></div>
          <div class="ic"><label>Tiempo GD (min)</label><input type="number" id="atgd-${a.id}" value="${a.tiempos.gd}" oninput="recalcAd('${a.id}')"></div>
        </div>
        <div class="sizes-bar"><div class="sz-hdr">CHICO</div><div class="sz-hdr">MEDIANO</div><div class="sz-hdr">GRANDE</div></div>
        <div class="igrid" style="border-top:1px solid var(--border)">
          <div class="ic"><label>Precio CH ($)</label><input type="number" id="apch-${a.id}" value="${a.precios.ch}" oninput="recalcAd('${a.id}')"></div>
          <div class="ic"><label>Precio MD ($)</label><input type="number" id="apmd-${a.id}" value="${a.precios.md}" oninput="recalcAd('${a.id}')"></div>
          <div class="ic"><label>Precio GD ($)</label><input type="number" id="apgd-${a.id}" value="${a.precios.gd}" oninput="recalcAd('${a.id}')"></div>
        </div>
        <div class="res-row" id="ar2-${a.id}"></div>
      </div>
    </div>`).join('');
  adData.forEach(a=>recalcAd(a.id));
}

function recalcAd(id){
  const hr    =parseFloat(document.getElementById('hrOwner')?.value)||120;
  const useEmp=document.getElementById('useEmp')?.checked;
  const tch   =parseFloat(document.getElementById('atch-'+id)?.value)||0;
  const tmd   =parseFloat(document.getElementById('atmd-'+id)?.value)||0;
  const tgd   =parseFloat(document.getElementById('atgd-'+id)?.value)||0;
  const pch   =parseFloat(document.getElementById('apch-'+id)?.value)||0;
  const pmd   =parseFloat(document.getElementById('apmd-'+id)?.value)||0;
  const pgd   =parseFloat(document.getElementById('apgd-'+id)?.value)||0;
  const cont  =document.getElementById('ar2-'+id); if(!cont) return;
  cont.innerHTML=[{s:'CH',t:tch,p:pch},{s:'MD',t:tmd,p:pmd},{s:'GD',t:tgd,p:pgd}].map(({s,t,p})=>{
    const mo=useEmp?p*0.5:hr*(t/60);
    const util=p-mo; const pct=p>0?(util/p*100).toFixed(0):0;
    const cls=pct>=40?'good':pct>=20?'warn':'bad';
    return `<div class="rc"><div class="rl">${s}</div>
      <div class="rl" style="margin-top:5px">Mano de obra</div><div style="font-family:'Bebas Neue',sans-serif;font-size:.95rem;color:var(--blue-lt)">$${mo.toFixed(0)}</div>
      <div class="rl" style="margin-top:5px">Utilidad</div><div class="rv-mg ${cls}">$${util.toFixed(0)} · ${pct}%</div></div>`;
  }).join('');
}

function showCostTab(tab){
  ['pkg','ad','res'].forEach(t=>{
    document.getElementById('costos-'+t).style.display=t===tab?'block':'none';
    document.getElementById('ctab-'+t).classList.toggle('active',t===tab);
  });
  if(tab==='res') buildResumen();
}

function buildResumen(){
  const cont=document.getElementById('costos-res'); if(!cont) return;
  const useEmp=document.getElementById('useEmp')?.checked;
  const hrOwner=parseFloat(document.getElementById('hrOwner')?.value)||120;
  const rows=[];
  pkgData.forEach(pkg=>{
    let ti=0;
    document.querySelectorAll(`#ins-${pkg.id} .ins-row`).forEach(row=>{
      const inps=row.querySelectorAll('input[type=number]');
      ti+=(parseFloat(inps[0]?.value)||0)/(parseFloat(inps[1]?.value)||1);
    });
    const tras=parseFloat(document.getElementById('pt-'+pkg.id)?.value)||0;
    const eq  =parseFloat(document.getElementById('pe-'+pkg.id)?.value)||0;
    ['ch','md','gd'].forEach(s=>{
      const t=parseFloat(document.getElementById(`p${s}-${pkg.id}`)?.value)||0;
      const p=parseFloat(document.getElementById(`pp-${s}-${pkg.id}`)?.value)||0;
      const mo=useEmp?p*0.5:hrOwner*(t/60);
      const cost=ti+tras+eq; const mg=p-cost-mo;
      const pct=p>0?(mg/p*100).toFixed(0):0;
      rows.push({tipo:'📦 '+pkg.nombre.replace('PAQUETE ',''),s:s.toUpperCase(),cost:cost.toFixed(0),p,mg:mg.toFixed(0),pct,isPkg:true});
    });
  });
  const avgAll=(rows.reduce((s,r)=>s+parseFloat(r.pct),0)/rows.length).toFixed(0);
  const best=rows.reduce((b,r)=>parseFloat(r.pct)>parseFloat(b.pct)?r:b,rows[0]);
  const worst=rows.reduce((b,r)=>parseFloat(r.pct)<parseFloat(b.pct)?r:b,rows[0]);
  cont.innerHTML=`
    <div class="stat-grid">
      <div class="stat-box"><span class="stat-val">${avgAll}%</span><div class="stat-lbl">Margen promedio</div></div>
      <div class="stat-box"><span class="stat-val good">${best?.pct}%</span><div class="stat-lbl">Mejor: ${best?.tipo} ${best?.s}</div></div>
      <div class="stat-box"><span class="stat-val warn">${worst?.pct}%</span><div class="stat-lbl">Más ajustado: ${worst?.tipo}</div></div>
    </div>
    <div style="overflow-x:auto;border-radius:12px;border:1px solid var(--border)">
      <table style="width:100%;min-width:480px;border-collapse:collapse">
        <thead><tr style="background:var(--surface2)">
          ${['Servicio','Tam.','Costo Op.','Precio','Utilidad','%'].map(h=>`<th style="padding:10px 12px;text-align:left;font-size:.58rem;letter-spacing:2px;text-transform:uppercase;color:var(--blue);border-bottom:1px solid var(--border)">${h}</th>`).join('')}
        </tr></thead>
        <tbody>${rows.map((r,i)=>{
          const pct=parseFloat(r.pct),col=pct>=30?'var(--green)':pct>=15?'var(--orange)':'var(--red)';
          return `<tr style="border-bottom:1px solid rgba(255,255,255,.04);${i%2?'background:rgba(255,255,255,.015)':''}">
            <td style="padding:9px 12px;font-size:.76rem;color:#fff">${r.tipo}</td>
            <td style="padding:9px 12px;font-family:'Bebas Neue',sans-serif;color:var(--blue-lt)">${r.s}</td>
            <td style="padding:9px 12px;color:var(--orange)">$${r.cost}</td>
            <td style="padding:9px 12px;font-weight:700;color:#fff">$${r.p}</td>
            <td style="padding:9px 12px;color:${col}">$${r.mg}</td>
            <td style="padding:9px 12px;font-family:'Bebas Neue',sans-serif;font-size:1rem;color:${col}">${r.pct}%</td>
          </tr>`;
        }).join('')}</tbody>
      </table>
    </div>`;
}
function toggleEmp(){ recalcAll(); }
function recalcAll(){ pkgData.forEach(p=>recalcPkg(p.id)); adData.forEach(a=>recalcAd(a.id)); }

/* ══════════════════════════════
   CLIENTES
══════════════════════════════ */
function renderClientes(){
  const hoy=new Date();
  const sorted=[...clientes].sort((a,b)=>new Date(b.ultimaCita||0)-new Date(a.ultimaCita||0));
  document.getElementById('clientesGrid').innerHTML=sorted.map(c=>{
    const dias=Math.floor((hoy-new Date((c.ultimaCita||'2000-01-01')+'T12:00:00'))/86400000);
    return `<div class="cli-card">
      <div class="cli-top">
        <div><div class="cli-name">${c.nombre}</div><div class="cli-tel">${c.tel} · ${c.coto||''}</div></div>
        <div style="text-align:right"><div style="font-size:.7rem;color:var(--muted)">${c.visitas||0} visita${(c.visitas||0)!==1?'s':''}</div>
          <div style="font-size:.65rem;color:${dias>14?'var(--orange)':'var(--muted)'}">Hace ${dias}d</div></div>
      </div>
      <div class="stars-row">${[1,2,3,4,5].map(s=>`<button class="star-btn" onclick="setStar('${c.id}',${s})">${s<=(c.estrellas||0)?'⭐':'☆'}</button>`).join('')}</div>
      <div style="font-size:.76rem;margin-bottom:7px">
        Propina: <span class="propina-bd ${c.propina?'p-si':'p-no'}">${c.propina?'✓ Sí':'✗ No'}</span>
        <button class="btn-sm btn-blue" style="padding:2px 7px;font-size:.58rem;margin-left:5px" onclick="togglePropina('${c.id}',${!c.propina})">${c.propina?'Quitar':'Marcar'}</button>
      </div>
      <div style="margin-bottom:7px">
        ⏰ Horario ideal:
        <select style="background:#07101f;border:1px solid var(--border);color:#fff;border-radius:6px;padding:3px 6px;font-size:.7rem;margin-left:4px" onchange="setHorario('${c.id}',this.value)">
          ${HOURS_LIST.map(h=>`<option ${c.horarioIdeal===h?'selected':''}>${h}</option>`).join('')}
        </select>
      </div>
      <div style="margin-bottom:7px">${(c.vehiculos||[]).map(v=>`<span class="vtag">🚗 ${v}</span>`).join('')}</div>
      <textarea style="width:100%;background:#07101f;border:1px solid var(--border);color:var(--text2);font-size:.7rem;padding:6px;border-radius:6px;resize:none;outline:none" rows="2"
        placeholder="Notas..." onblur="setNota('${c.id}',this.value)">${c.notas||''}</textarea>

```
</div>`;
```

}).join(’’);
// Recordatorios
const rec=clientes.filter(c=>Math.floor((hoy-new Date((c.ultimaCita||‘2000-01-01’)+‘T12:00:00’))/86400000)>14)
.sort((a,b)=>new Date(b.ultimaCita||0)-new Date(a.ultimaCita||0));
document.getElementById(‘recGrid’).innerHTML=rec.length?rec.map(c=>` <div class="rec-card" id="rec-${c.id}"> <div class="rec-name">${c.nombre}</div> <div class="rec-info">${'⭐'.repeat(c.estrellas||0)||'Sin calif.'} · ${c.tel}</div> <button class="rec-btn" onclick="genRec('${c.id}')">📲 Generar recordatorio</button> <div class="msg-box" id="rec-msg-${c.id}"></div> <div id="rec-act-${c.id}" style="display:none;gap:6px;margin-top:6px;flex-wrap:wrap"> <button class="copy-btn" onclick="copyRecMsg('${c.id}')">📋 Copiar</button> <button class="copy-btn" onclick="cerrarRec('${c.id}')">✕ Cerrar</button> </div> </div>`).join(’’):’<div style="font-size:.76rem;color:var(--muted);text-align:center;padding:16px">¡Todos al día! 🎉</div>’;
}

async function setStar(id,s){
try { await window.fbUpd(‘clientes’,id,{estrellas:s}); showToast(‘⭐ Calificación guardada’); }
catch(e){ showToast(‘❌ Error al guardar’); }
}
async function togglePropina(id,val){
try { await window.fbUpd(‘clientes’,id,{propina:val}); }
catch(e){ showToast(‘❌ Error al guardar’); }
}
async function setHorario(id,v){
try { await window.fbUpd(‘clientes’,id,{horarioIdeal:v}); }
catch(e){ showToast(‘❌ Error al guardar’); }
}
async function setNota(id,v){
try { await window.fbUpd(‘clientes’,id,{notas:v}); showToast(‘💾 Nota guardada’); }
catch(e){ showToast(‘❌ Error al guardar’); }
}

function genRec(id){
const c=clientes.find(x=>x.id===id); if(!c) return;
const vh=(c.vehiculos||[])[0]||‘tu vehículo’;
const msg=`¡Hola ${c.nombre.split(' ')[0]}! 😊 Soy de *Bianco Wash Las Terrazas*.\n\nTe escribo porque ha pasado un tiempo desde tu último lavado y quería saber si te gustaría agendar una cita para darle mantenimiento a ${vh} 🚗✨\n\nContamos con paquetes desde $120 MXN a domicilio, ¡sin que te muevas! 🫧\n\n¿Te interesa? Escríbeme aquí o al 311-157-4234. ¡Saludos!`;
const box=document.getElementById(‘rec-msg-’+id);
const act=document.getElementById(‘rec-act-’+id);
if(box){box.textContent=msg;box.style.display=‘block’;}
if(act)act.style.display=‘flex’;
}
function copyRecMsg(id){
const box=document.getElementById(‘rec-msg-’+id);
if(box) navigator.clipboard.writeText(box.textContent).then(()=>showToast(‘📋 Mensaje copiado’));
}
function cerrarRec(id){
const box=document.getElementById(‘rec-msg-’+id);
const act=document.getElementById(‘rec-act-’+id);
if(box)box.style.display=‘none’;if(act)act.style.display=‘none’;
}

/* ══════════════════════════════
ESTADÍSTICAS
══════════════════════════════ */
function setFilter(f,btn){
statsFilter=f;
document.querySelectorAll(’.filter-btn’).forEach(b=>b.classList.remove(‘active’));
btn.classList.add(‘active’);
renderStats();
}
function getFilteredCitas(){
const hoy=new Date();
return citas.filter(c=>{
if(c.status!==‘done’) return false;
const d=new Date((c.fecha||’’)+‘T12:00:00’);
if(statsFilter===‘semana’){
const lunes=new Date(hoy);lunes.setDate(hoy.getDate()-((hoy.getDay()+6)%7));lunes.setHours(0,0,0,0);
return d>=lunes&&d<=hoy;
}
if(statsFilter===‘mes’) return d.getMonth()===hoy.getMonth()&&d.getFullYear()===hoy.getFullYear();
if(statsFilter===‘anio’) return d.getFullYear()===hoy.getFullYear();
return true;
});
}
function renderStats(){
const hoy=new Date(); const fc=getFilteredCitas();
const ingresos=fc.reduce((s,c)=>s+(c.costo||0)+(c.propinaDada||0),0);
const propinas=fc.reduce((s,c)=>s+(c.propinaDada||0),0);
const promTicket=fc.length?Math.round(ingresos/fc.length):0;
document.getElementById(‘statsCards’).innerHTML=` <div class="stat-box"><span class="stat-val">$${ingresos.toLocaleString()}</span><div class="stat-lbl">Ingresos (MXN)</div></div> <div class="stat-box"><span class="stat-val" style="color:var(--gold)">$${propinas.toLocaleString()}</span><div class="stat-lbl">Propinas</div></div> <div class="stat-box"><span class="stat-val">$${promTicket}</span><div class="stat-lbl">Ticket promedio</div></div> <div class="stat-box"><span class="stat-val">${fc.length}</span><div class="stat-lbl">Citas completadas</div></div>`;
[‘chartIngresos’,‘chartPkgs’,‘chartTam’].forEach(id=>{const c=Chart.getChart(id);if(c)c.destroy();});
let labels=[], ingresoData=[];
if(statsFilter===‘semana’){
const dias=[‘Lun’,‘Mar’,‘Mié’,‘Jue’,‘Vie’,‘Sáb’,‘Dom’];
const lunes=new Date(hoy);lunes.setDate(hoy.getDate()-((hoy.getDay()+6)%7));lunes.setHours(0,0,0,0);
for(let i=0;i<7;i++){
const d=new Date(lunes);d.setDate(lunes.getDate()+i);
labels.push(dias[i]);
const ds=d.toISOString().split(‘T’)[0];
ingresoData.push(fc.filter(c=>c.fecha===ds).reduce((s,c)=>s+(c.costo||0)+(c.propinaDada||0),0));
}
}else if(statsFilter===‘mes’){
const y=hoy.getFullYear(),m=hoy.getMonth();
const last=new Date(y,m+1,0).getDate();
labels=Array.from({length:last},(_,i)=>String(i+1));
ingresoData=Array(last).fill(0);
fc.forEach(c=>{const d=new Date((c.fecha||’’)+‘T12:00:00’);if(d.getMonth()===m)ingresoData[d.getDate()-1]+=(c.costo||0)+(c.propinaDada||0);});
}else{
labels=MESES.slice(0,hoy.getMonth()+1);
ingresoData=Array(labels.length).fill(0);
fc.forEach(c=>{const d=new Date((c.fecha||’’)+‘T12:00:00’);if(d.getMonth()<labels.length)ingresoData[d.getMonth()]+=(c.costo||0)+(c.propinaDada||0);});
}
const pkgCount={express:0,black:0,premium:0};fc.forEach(c=>{if(pkgCount[c.paquete]!==undefined)pkgCount[c.paquete]++;});
const tamCount={ch:0,md:0,gd:0};fc.forEach(c=>{if(tamCount[c.tamano]!==undefined)tamCount[c.tamano]++;});
const periodoLabel=statsFilter===‘semana’?‘SEMANA’:statsFilter===‘mes’?‘MES’:‘AÑO’;
document.getElementById(‘statsCharts’).innerHTML=` <div class="chart-box"><div class="chart-title">INGRESOS — ${periodoLabel}</div><canvas id="chartIngresos"></canvas></div> <div class="chart-box"><div class="chart-title">CITAS POR PAQUETE</div><canvas id="chartPkgs"></canvas></div> <div class="chart-box"><div class="chart-title">DISTRIBUCIÓN POR TAMAÑO</div><canvas id="chartTam"></canvas></div>`;
const co={responsive:true,plugins:{legend:{labels:{color:’#94a3b8’,font:{size:10}}}}};
new Chart(document.getElementById(‘chartIngresos’),{type:‘bar’,data:{labels,datasets:[{label:’$MXN’,data:ingresoData,backgroundColor:‘rgba(14,165,233,.5)’,borderColor:’#0ea5e9’,borderWidth:1,borderRadius:4}]},options:{…co,scales:{x:{ticks:{color:’#64748b’}},y:{ticks:{color:’#64748b’,callback:v=>’$’+v}}}}});
new Chart(document.getElementById(‘chartPkgs’),{type:‘doughnut’,data:{labels:[‘Express’,‘Black’,‘Premium’],datasets:[{data:[pkgCount.express,pkgCount.black,pkgCount.premium],backgroundColor:[‘rgba(56,189,248,.6)’,‘rgba(100,116,139,.6)’,‘rgba(251,191,36,.6)’],borderColor:[’#38bdf8’,’#64748b’,’#fbbf24’],borderWidth:1}]},options:co});
new Chart(document.getElementById(‘chartTam’),{type:‘pie’,data:{labels:[‘Chico’,‘Mediano’,‘Grande’],datasets:[{data:[tamCount.ch,tamCount.md,tamCount.gd],backgroundColor:[‘rgba(34,197,94,.6)’,‘rgba(14,165,233,.6)’,‘rgba(249,115,22,.6)’],borderColor:[’#22c55e’,’#0ea5e9’,’#f97316’],borderWidth:1}]},options:co});
}

/* ══════════════════════════════
EQUIPO
══════════════════════════════ */
function previewEmpPhoto(input){
const file=input.files[0];if(!file)return;
const reader=new FileReader();
reader.onload=e=>{
empPhotoData=e.target.result;
const circle=document.getElementById(‘empPhotoCircle’);
if(circle){circle.innerHTML=`<img src="${e.target.result}" style="width:100%;height:100%;object-fit:cover;border-radius:50%">`;}
};reader.readAsDataURL(file);
}

function renderEquipo(){
const hoy=new Date();
const ownerCitas=citas.filter(c=>c.asignado===‘Eliam Cobián’);
const ownerIngresos=ownerCitas.filter(c=>c.status===‘done’).reduce((s,c)=>s+(c.costo||0)+(c.propinaDada||0),0);
const empCards=empleados.map(e=>{
const inicio=new Date((e.fechaInicio||‘2024-01-01’)+‘T12:00:00’);
const meses=Math.max(0,Math.floor((hoy-inicio)/(86400000*30)));
const ec=citas.filter(c=>c.asignado===e.nombre);
const ei=ec.filter(c=>c.status===‘done’).reduce((s,c)=>s+(c.costo||0)+(c.propinaDada||0),0);
return `<div class="emp-card"> <div class="emp-avatar">${e.foto?`<img src="${e.foto}">`:'👤'}</div> <div class="emp-name">${e.nombre}</div><div class="emp-role">Empleado</div> <div class="emp-info-row"><span class="emp-info-lbl">Teléfono</span><span class="emp-info-val">${e.tel||'—'}</span></div> <div class="emp-info-row"><span class="emp-info-lbl">Desde</span><span class="emp-info-val">${meses} mes${meses!==1?'es':''}</span></div> <div class="emp-info-row"><span class="emp-info-lbl">Usuario</span><span class="emp-info-val">${e.user}</span></div> <div class="emp-stats"> <div class="emp-stat"><span class="emp-stat-val">${ec.length}</span><div class="emp-stat-lbl">Servicios</div></div> <div class="emp-stat"><span class="emp-stat-val">$${ei.toLocaleString()}</span><div class="emp-stat-lbl">Generado</div></div> </div> <button class="btn-sm btn-red" style="margin-top:11px;width:100%" onclick="removeEmpleado('${e.id}')">🗑 Eliminar</button> </div>`;
}).join(’’);
document.getElementById(‘equipoGrid’).innerHTML=` <div class="emp-card owner"> <span class="owner-badge">👑 PROPIETARIO</span> <div class="emp-avatar">👑</div> <div class="emp-name">Eliam Cobián</div><div class="emp-role">Administrador · Propietario</div> <div class="emp-info-row"><span class="emp-info-lbl">WhatsApp</span><span class="emp-info-val">311-157-4234</span></div> <div class="emp-info-row"><span class="emp-info-lbl">Usuario</span><span class="emp-info-val">Eliam Cobian</span></div> <div class="emp-stats"> <div class="emp-stat"><span class="emp-stat-val">${ownerCitas.length}</span><div class="emp-stat-lbl">Servicios</div></div> <div class="emp-stat"><span class="emp-stat-val">$${ownerIngresos.toLocaleString()}</span><div class="emp-stat-lbl">Generado</div></div> </div> </div>${empCards}`;
}

function openEmpModal(){ empPhotoData=null; document.getElementById(‘empModal’).classList.add(‘open’); }

async function saveEmpleado(){
const nombre=document.getElementById(‘empNombre’).value.trim();
const user  =document.getElementById(‘empUser’).value.trim();
const pass  =document.getElementById(‘empPass’).value.trim();
if(!nombre||!user||!pass){showToast(‘⚠️ Nombre, usuario y contraseña son obligatorios’);return;}
const btn=document.getElementById(‘btnSaveEmp’);
btn.disabled=true;btn.textContent=‘Guardando…’;
try{
await window.fbAdd(‘empleados’,{
nombre, user, pass, tel:document.getElementById(‘empTel’).value,
fechaInicio:document.getElementById(‘empFechaInicio’).value||new Date().toISOString().split(‘T’)[0],
dom:document.getElementById(‘empDom’).value, foto:empPhotoData||null
});
document.getElementById(‘empModal’).classList.remove(‘open’);
// Limpiar form
[‘empNombre’,‘empTel’,‘empFechaInicio’,‘empDom’,‘empUser’,‘empPass’].forEach(id=>{
const el=document.getElementById(id);if(el)el.value=’’;
});
document.getElementById(‘empPhotoCircle’).innerHTML=‘📷’;
empPhotoData=null;
showToast(‘✅ Empleado guardado’);
} catch(e){ showToast(’❌ Error: ’+e.message); }
finally{ btn.disabled=false;btn.textContent=‘💾 GUARDAR EMPLEADO’; }
}

async function removeEmpleado(id){
if(!confirm(’¿Eliminar empleado? Esta acción no se puede deshacer.’))return;
try{ await window.fbDel(‘empleados’,id); showToast(‘🗑 Empleado eliminado’); }
catch(e){ showToast(’❌ Error: ’+e.message); }
}

/* ══════════════════════════════
INIT
══════════════════════════════ */
renderAdPub();
renderAdCheckboxes();
renderCalendar();
</script>

<!-- ════ INSTRUCCIONES FIREBASE (solo visibles en consola) ════ -->

<script>
console.log(`
%c🔥 BIANCO WASH — Configuración Firebase%c

Para activar la base de datos:
1. Ve a https://console.firebase.google.com
2. Crea proyecto "biancowash"
3. Activa Firestore Database (modo prueba)
4. Ve a Configuración del proyecto → Tus apps → Web
5. Copia tu firebaseConfig y pégalo en el <script type="module"> al inicio del HTML
6. En Firestore Rules usa:
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /solicitudes/{doc} { allow read, write: if true; }
       match /citas/{doc}       { allow read, write: if true; }
       match /clientes/{doc}    { allow read, write: if true; }
       match /disponibilidad/{doc} { allow read, write: if true; }
       match /empleados/{doc}   { allow read, write: if true; }
     }
   }
`, 'color:#0ea5e9;font-weight:bold;font-size:14px', 'color:inherit');
</script>

</body>
</html>