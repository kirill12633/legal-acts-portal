<!DOCTYPE html>
<html lang="ru">
<head>
  <meta charset="UTF-8" />
  <title>Legal Acts Portal</title>
  <style>
    body { max-width: 600px; margin: 20px auto; font-family: Arial; }
    input, button { padding: 8px; margin: 5px 0; width: 100%; }
    #docs { margin-top: 20px; }
    .doc { border: 1px solid #ccc; padding: 10px; margin-bottom: 10px; }
  </style>
</head>
<body>

<h2>Вход / Регистрация</h2>
<input id="email" type="email" placeholder="Email" />
<input id="password" type="password" placeholder="Пароль" />
<button id="registerBtn">Регистрация</button>
<button id="loginBtn">Войти</button>
<button id="logoutBtn" style="display:none;">Выйти</button>

<h2>Загрузка документа</h2>
<input id="fio" placeholder="ФИО (3 слова)" />
<select id="docType">
  <option value="Постановление">Постановление</option>
  <option value="Указ">Указ</option>
</select>
<input id="department" placeholder="Отдел" />
<input id="fileInput" type="file" />
<button id="uploadBtn" disabled>Загрузить документ</button>

<div id="docs"></div>

<script type="module">
  import { initializeApp } from "https://www.gstatic.com/firebasejs/11.9.1/firebase-app.js";
  import { getAuth, createUserWithEmailAndPassword, signInWithEmailAndPassword, signOut, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/11.9.1/firebase-auth.js";
  import { getFirestore, collection, addDoc, query, where, getDocs } from "https://www.gstatic.com/firebasejs/11.9.1/firebase-firestore.js";
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

  const emailInput = document.getElementById('email');
  const passwordInput = document.getElementById('password');
  const registerBtn = document.getElementById('registerBtn');
  const loginBtn = document.getElementById('loginBtn');
  const logoutBtn = document.getElementById('logoutBtn');

  const fioInput = document.getElementById('fio');
  const docTypeSelect = document.getElementById('docType');
  const departmentInput = document.getElementById('department');
  const fileInput = document.getElementById('fileInput');
  const uploadBtn = document.getElementById('uploadBtn');

  const docsDiv = document.getElementById('docs');

  registerBtn.onclick = async () => {
    try {
      const userCredential = await createUserWithEmailAndPassword(auth, emailInput.value, passwordInput.value);
      alert('Пользователь зарегистрирован');
    } catch(e) {
      alert('Ошибка регистрации: ' + e.message);
    }
  };

  loginBtn.onclick = async () => {
    try {
      const userCredential = await signInWithEmailAndPassword(auth, emailInput.value, passwordInput.value);
      alert('Вход выполнен');
    } catch(e) {
      alert('Ошибка входа: ' + e.message);
    }
  };

  logoutBtn.onclick = async () => {
    await signOut(auth);
  };

  onAuthStateChanged(auth, user => {
    if(user) {
      logoutBtn.style.display = 'block';
      uploadBtn.disabled = false;
      registerBtn.disabled = true;
      loginBtn.disabled = true;
      emailInput.disabled = true;
      passwordInput.disabled = true;
      loadDocuments(user.uid);
    } else {
      logoutBtn.style.display = 'none';
      uploadBtn.disabled = true;
      registerBtn.disabled = false;
      loginBtn.disabled = false;
      emailInput.disabled = false;
      passwordInput.disabled = false;
      docsDiv.innerHTML = '';
    }
  });

  uploadBtn.onclick = async () => {
    const fio = fioInput.value.trim();
    if(fio.split(' ').length !== 3) {
      alert('ФИО должно содержать 3 слова');
      return;
    }
    if (!fileInput.files.length) {
      alert('Выберите файл');
      return;
    }
    const file = fileInput.files[0];

    try {
      // Загрузка файла в Storage
      const storageRef = ref(storage, `documents/${Date.now()}_${file.name}`);
      await uploadBytes(storageRef, file);
      const fileURL = await getDownloadURL(storageRef);

      // Сохранение метаданных в Firestore
      await addDoc(collection(db, 'documents'), {
        uid: auth.currentUser.uid,
        fio,
        docType: docTypeSelect.value,
        department: departmentInput.value.trim(),
        fileName: file.name,
        fileURL,
        status: 'Сканируется',
        createdAt: new Date()
      });

      alert('Документ загружен');
      fioInput.value = '';
      departmentInput.value = '';
      fileInput.value = '';
      loadDocuments(auth.currentUser.uid);
    } catch(e) {
      alert('Ошибка загрузки: ' + e.message);
    }
  };

  async function loadDocuments(uid) {
    docsDiv.innerHTML = '<h3>Ваши документы</h3>';
    const q = query(collection(db, 'documents'), where('uid', '==', uid));
    const querySnapshot = await getDocs(q);
    querySnapshot.forEach(doc => {
      const data = doc.data();
      const div = document.createElement('div');
      div.className = 'doc';
      div.innerHTML = `
        <b>${data.docType}</b><br>
        ФИО: ${data.fio}<br>
        Отдел: ${data.department}<br>
        Статус: ${data.status}<br>
        <a href="${data.fileURL}" target="_blank">Открыть файл</a>
      `;
      docsDiv.appendChild(div);
    });
  }
</script>

</body>
</html>
