<!DOCTYPE html>
<html lang="ru">
<head>
<meta charset="UTF-8" />
<title>Legal Acts Portal</title>
<script src="https://cdnjs.cloudflare.com/ajax/libs/qrcodejs/1.0.0/qrcode.min.js"></script>
<style>
  body { max-width: 600px; margin: 20px auto; font-family: Arial; }
  input, button, select { padding: 8px; margin: 5px 0; width: 100%; }
  #docs { margin-top: 20px; }
  .doc { border: 1px solid #ccc; padding: 10px; margin-bottom: 10px; }
  .admin-buttons button { margin-right: 5px; }
</style>
</head>
<body>

<h2>Вход</h2>
<input id="email" type="text" placeholder="Логин (например, 12345-12345)" />
<input id="password" type="password" placeholder="Пароль" />
<button id="loginBtn">Войти</button>
<button id="logoutBtn" style="display:none;">Выйти</button>

<div id="userInfo" style="margin-top:10px; font-weight:bold;"></div>

<div id="uploadSection" style="display:none;">
  <h2>Загрузка документа</h2>
  <input id="fio" placeholder="ФИО (3 слова)" />
  <select id="docType">
    <option value="Постановление">Постановление</option>
    <option value="Указ">Указ</option>
  </select>
  <input id="department" placeholder="Отдел" />
  <input id="fileInput" type="file" />
  <button id="uploadBtn">Загрузить документ</button>
</div>

<div id="docs"></div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/11.9.1/firebase-app.js";
import { getAuth, signInWithEmailAndPassword, signOut, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/11.9.1/firebase-auth.js";
import { getFirestore, collection, addDoc, query, where, getDocs, doc, updateDoc } from "https://www.gstatic.com/firebasejs/11.9.1/firebase-firestore.js";
import { getStorage, ref, uploadBytes, getDownloadURL } from "https://www.gstatic.com/firebasejs/11.9.1/firebase-storage.js";

const firebaseConfig = {
  apiKey: "AIzaSyBJX1aiBnBg_dVQ_AOA5L3rsvQyLgIb27E",
  authDomain: "legal-acts-portal.firebaseapp.com",
  projectId: "legal-acts-portal",
  storageBucket: "legal-acts-portal.appspot.com",
  messagingSenderId: "969951897920",
  appId: "1:969951897920:web:3e35b87565cfcc9e0832a5"
};

const app = initializeApp(firebaseConfig);
const auth = getAuth(app);
const db = getFirestore(app);
const storage = getStorage(app);

// Жестко заданные аккаунты с ролями (логин, пароль, роль, имя)
const ACCOUNTS = {
  "12345-12345": { password: "5864", role: "minjust", name: "Министерство Юстиции" },
  "5678-2345": { password: "5648", role: "president", name: "Президент РФ" }
};

let currentUser = null;

const emailInput = document.getElementById('email');
const passwordInput = document.getElementById('password');
const loginBtn = document.getElementById('loginBtn');
const logoutBtn = document.getElementById('logoutBtn');
const userInfo = document.getElementById('userInfo');

const uploadSection = document.getElementById('uploadSection');
const fioInput = document.getElementById('fio');
const docTypeSelect = document.getElementById('docType');
const departmentInput = document.getElementById('department');
const fileInput = document.getElementById('fileInput');
const uploadBtn = document.getElementById('uploadBtn');

const docsDiv = document.getElementById('docs');

loginBtn.onclick = () => {
  const login = emailInput.value.trim();
  const pass = passwordInput.value.trim();

  if (!(login in ACCOUNTS)) {
    alert('Неверный логин');
    return;
  }
  if (ACCOUNTS[login].password !== pass) {
    alert('Неверный пароль');
    return;
  }

  currentUser = { login, ...ACCOUNTS[login] };
  onUserLoggedIn();
};

logoutBtn.onclick = () => {
  currentUser = null;
  onUserLoggedOut();
};

function onUserLoggedIn() {
  userInfo.textContent = `Вы вошли как: ${currentUser.name} (${currentUser.role})`;
  loginBtn.style.display = 'none';
  logoutBtn.style.display = 'block';
  emailInput.style.display = 'none';
  passwordInput.style.display = 'none';
  uploadSection.style.display = 'block';
  loadDocuments();
}

function onUserLoggedOut() {
  userInfo.textContent = '';
  loginBtn.style.display = 'block';
  logoutBtn.style.display = 'none';
  emailInput.style.display = 'block';
  passwordInput.style.display = 'block';
  uploadSection.style.display = 'none';
  docsDiv.innerHTML = '';
}

uploadBtn.onclick = async () => {
  const fio = fioInput.value.trim();
  if (fio.split(' ').length !== 3) {
    alert('ФИО должно содержать 3 слова');
    return;
  }
  if (!fileInput.files.length) {
    alert('Выберите файл');
    return;
  }

  const file = fileInput.files[0];
  try {
    // Загружаем файл
    const storageRef = ref(storage, `documents/${Date.now()}_${file.name}`);
    await uploadBytes(storageRef, file);
    const fileURL = await getDownloadURL(storageRef);

    // Сохраняем в Firestore
    await addDoc(collection(db, 'documents'), {
      fio,
      docType: docTypeSelect.value,
      department: departmentInput.value.trim(),
      fileName: file.name,
      fileURL,
      status: "Сканируется",
      decisionBy: null,
      decisionDate: null,
      createdBy: currentUser.login,
      createdAt: new Date()
    });

    alert('Документ загружен');
    fioInput.value = '';
    departmentInput.value = '';
    fileInput.value = '';
    loadDocuments();

  } catch(e) {
    alert('Ошибка загрузки: ' + e.message);
  }
};

async function loadDocuments() {
  docsDiv.innerHTML = '<h3>Документы</h3>';
  const q = query(collection(db, 'documents'));
  const snapshot = await getDocs(q);

  snapshot.forEach(docSnap => {
    const docData = docSnap.data();
    const div = document.createElement('div');
    div.className = 'doc';

    div.innerHTML = `
      <b>${docData.docType}</b><br>
      ФИО: ${docData.fio}<br>
      Отдел: ${docData.department}<br>
      Статус: <span id="status-${docSnap.id}">${docData.status}</span><br>
      Принято: ${docData.decisionBy ? docData.decisionBy : '–'} ${docData.decisionDate ? new Date(docData.decisionDate.seconds * 1000).toLocaleString() : ''}<br>
      <a href="${docData.fileURL}" target="_blank">Открыть файл</a><br>
      <div id="qrcode-${docSnap.id}"></div>
    `;

    if (["minjust", "president"].includes(currentUser.role)) {
      const adminButtons = document.createElement('div');
      adminButtons.className = 'admin-buttons';
      const approveBtn = document.createElement('button');
      approveBtn.textContent = 'Одобрить';
      approveBtn.onclick = () => updateStatus(docSnap.id, 'Одобрен');

      const rejectBtn = document.createElement('button');
      rejectBtn.textContent = 'Отклонить';
      rejectBtn.onclick = () => updateStatus(docSnap.id, 'Не одобрен');

      adminButtons.appendChild(approveBtn);
      adminButtons.appendChild(rejectBtn);
      div.appendChild(adminButtons);
    }

    docsDiv.appendChild(div);

    // Генерируем QR-код на ссылку файла
    new QRCode(document.getElementById(`qrcode-${docSnap.id}`), {
      text: docData.fileURL,
      width: 128,
      height: 128
    });
  });
}

async function updateStatus(docId, newStatus) {
  const docRef = doc(db, 'documents', docId);
  await updateDoc(docRef, {
    status: newStatus,
    decisionBy: currentUser.name,
    decisionDate: new Date()
  });
  loadDocuments();
}

onUserLoggedOut();

</script>

</body>
</html>
