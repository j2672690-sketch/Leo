<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>FABM Accounting Quiz — Student & Admin</title>
  <style>
    :root{font-family:Inter, system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial; color:#0f172a}
    body{background:#eef2ff;padding:20px;display:flex;justify-content:center;align-items:flex-start;min-height:100vh}
    .container{max-width:1000px;width:100%;background:#fff;padding:22px;border-radius:10px;box-shadow:0 6px 18px rgba(15,23,42,0.06)}
    h1{color:#1e3a8a;text-align:center;margin:0 0 12px}
    label{display:block;margin-top:8px;font-weight:600}
    input[type="text"], input[type="password"], select, button{padding:8px;margin-top:6px;width:100%;box-sizing:border-box;border-radius:6px;border:1px solid #cbd5e1}
    .row{display:grid;grid-template-columns:1fr 180px;gap:12px;align-items:end}
    .meta{margin-bottom:12px}
    .question{margin-top:18px;font-weight:700}
    .options label{display:block;margin:6px 0;cursor:pointer}
    button{background:#1d4ed8;color:#fff;border:none;border-radius:6px;cursor:pointer;padding:10px}
    button.secondary{background:#475569}
    #result{margin-top:12px}
    #recordsTable{width:100%;border-collapse:collapse;margin-top:12px}
    #recordsTable th,#recordsTable td{border:1px solid #e2e8f0;padding:8px;text-align:left}
    .controls{display:flex;gap:8px;margin-top:12px;flex-wrap:wrap}
    .topbar{display:flex;justify-content:space-between;align-items:center;margin-bottom:8px;gap:12px}
    .admin-link{font-size:0.9rem;color:#1d4ed8;text-decoration:underline;cursor:pointer;background:none;border:0;padding:0}
    .sr-only{position:absolute;width:1px;height:1px;padding:0;margin:-1px;overflow:hidden;clip:rect(0,0,0,0);white-space:nowrap;border:0}
    .feedback{margin-top:14px;border-top:1px solid #e6edf8;padding-top:12px}
    .fb-item{padding:8px;border-radius:6px;margin-bottom:8px}
    .fb-correct{background:#ecfdf5;border:1px solid #bbf7d0}
    .fb-wrong{background:#fff7f5;border:1px solid #fecaca}
    .small{font-size:0.9rem;color:#475569}
    .detailsButton{background:#0b5ed7;color:#fff;padding:6px 8px;border-radius:6px;border:0;cursor:pointer}
    .admin-panel{margin-top:18px;padding-top:12px;border-top:1px dashed #e2e8f0}
    .ghost{background:#475569}
    @media (max-width:640px){.row{grid-template-columns:1fr}}
  </style>
</head>
<body>
  <main class="container" id="app">
    <div class="topbar">
      <h1 id="quizTitle">FABM Accounting Quiz</h1>
      <div style="min-width:220px">
        <div style="display:flex;gap:8px;align-items:center">
          <button id="adminToggle" class="secondary" type="button">Admin</button>
          <button id="helpBtn" class="ghost" type="button">Help</button>
        </div>
      </div>
    </div>

    <!-- Student form -->
    <form id="quizForm" aria-labelledby="quizTitle" novalidate>
      <div class="meta">
        <label for="studentName">Student Name</label>
        <input id="studentName" name="studentName" type="text" autocomplete="name" required aria-required="true" />

        <div class="row" style="margin-top:6px">
          <div>
            <label for="section">Section</label>
            <input id="section" name="section" type="text" required aria-required="true" />
          </div>
          <div>
            <label for="level">Grade Level</label>
            <select id="level" name="level" required aria-required="true">
              <option value="">Select</option>
              <option value="11">Grade 11</option>
              <option value="12">Grade 12</option>
            </select>
          </div>
        </div>
      </div>

      <div id="quiz" aria-live="polite"></div>

      <div class="controls">
        <button id="submitBtn" type="submit">Submit Quiz</button>
        <button id="viewBtn" type="button" class="secondary">View My Saved Results</button>
        <button id="exportBtn" type="button" class="secondary">Export My Results (CSV)</button>
        <button id="resetAnswersBtn" type="button" class="ghost">Reset Answers</button>
      </div>

      <div id="result" role="status" aria-atomic="true" aria-live="polite" style="margin-top:8px"></div>
    </form>

    <!-- Student records + details -->
    <section id="records" style="margin-top:18px;display:none">
      <h2>My Saved Results</h2>
      <table id="recordsTable" aria-describedby="recordsDesc">
        <thead>
          <tr><th>Date</th><th>Name</th><th>Section</th><th>Level</th><th>Score</th><th>Details</th></tr>
        </thead>
        <tbody></tbody>
      </table>
      <div style="margin-top:8px">
        <button id="clearBtn" class="secondary" type="button">Clear My Saved Results</button>
      </div>

      <div id="recordDetails" style="margin-top:12px;display:none"></div>
    </section>

    <!-- Per-submission feedback (visible after submit) -->
    <div id="feedback" class="feedback" aria-live="polite"></div>

    <!-- Admin panel (hidden until toggled) -->
    <div id="adminPanel" class="admin-panel" style="display:none">
      <h2>Admin — Results Viewer</h2>

      <div id="authArea">
        <p class="small">Enter admin password to view all saved results. (Client-side demo gate only.)</p>
        <label for="adminPass">Admin password</label>
        <input id="adminPass" type="password" autocomplete="current-password" />
        <div style="display:flex;gap:8px;margin-top:8px">
          <button id="loginBtn">Login</button>
          <button id="adminCloseBtn" class="ghost">Close</button>
        </div>
        <div id="authMsg" class="small" style="margin-top:8px;color:#475569"></div>
      </div>

      <section id="recordsSection" style="margin-top:12px;display:none">
        <div class="controls">
          <button id="exportAllBtn">Export All Results (CSV)</button>
          <button id="clearAllBtn" class="ghost">Clear All Results</button>
          <button id="logoutBtn" class="ghost">Logout</button>
        </div>

        <table id="adminRecordsTable" aria-describedby="recordsDesc" style="margin-top:12px">
          <thead>
            <tr><th>Date (ISO)</th><th>Date (local)</th><th>Name</th><th>Section</th><th>Level</th><th>Score</th><th>ID</th><th>Details</th></tr>
          </thead>
          <tbody></tbody>
        </table>

        <div id="adminRecordDetails" style="margin-top:12px;display:none"></div>
      </section>
    </div>
  </main>

  <script>
  (function(){
    'use strict';

    // CONFIG
    const STORAGE_KEY = 'fabmResults';
    const MAX_RECORDS = 500;
    // NOTE: client-side admin password is only a demo gate.
    // Change this value before use, or replace with server-side auth.
    const ADMIN_PASSWORD = 'secret123';

    // Quiz data
    const quizData = {
      11: [
        {q:"Accounting is best defined as:", o:["Recording profits only","Identifying and recording financial transactions","Managing employees"], a:1},
        {q:"Who uses accounting information?", o:["Owners","Managers","All of the above"], a:2},
        {q:"Accounting helps in decision making.", o:["True","False"], a:0},
        {q:"Which business is owned by one person?", o:["Corporation","Partnership","Sole proprietorship"], a:2},
        {q:"Accounting information is expressed in:", o:["Words","Money","Pictures"], a:1},
        {q:"Which is an internal user?", o:["Customer","Manager","Supplier"], a:1},
        {q:"Bookkeeping is part of accounting.", o:["True","False"], a:0},
        {q:"Which is NOT a business activity?", o:["Selling goods","Buying supplies","Personal shopping"], a:2},
        {q:"Accounting follows rules called:", o:["Policies","Standards","Principles"], a:2},
        {q:"Which is a service business?", o:["Sari-sari store","Salon","Trading company"], a:1}
      ],
      12: [
        {q:"Basic accounting equation is:", o:["A = L + E","A = I - E","A + E = L"], a:0},
        {q:"Cash is classified as:", o:["Asset","Liability","Equity"], a:0},
        {q:"Accounts Payable is a:", o:["Asset","Liability","Expense"], a:1},
        {q:"Owner’s investment increases:", o:["Assets & Equity","Assets & Liabilities","Expenses"], a:0},
        {q:"Which normally has a debit balance?", o:["Revenue","Capital","Assets"], a:2},
        {q:"Withdrawal by owner decreases:", o:["Assets","Equity","Liabilities"], a:1},
        {q:"Which is an expense?", o:["Rent","Cash","Accounts Receivable"], a:0},
        {q:"Revenue increases:", o:["Assets","Liabilities","Equity"], a:2},
        {q:"Purchasing supplies on credit increases:", o:["Assets & Liabilities","Assets only","Equity"], a:0},
        {q:"Which account is affected by profit?", o:["Assets","Capital","Liabilities"], a:1}
      ]
    };

    // Elements
    const form = document.getElementById('quizForm');
    const levelEl = document.getElementById('level');
    const quizDiv = document.getElementById('quiz');
    const resultDiv = document.getElementById('result');
    const viewBtn = document.getElementById('viewBtn');
    const recordsSection = document.getElementById('records');
    const recordsTableBody = document.querySelector('#recordsTable tbody');
    const clearBtn = document.getElementById('clearBtn');
    const exportBtn = document.getElementById('exportBtn');
    const feedbackDiv = document.getElementById('feedback');
    const recordDetailsDiv = document.getElementById('recordDetails');
    const resetAnswersBtn = document.getElementById('resetAnswersBtn');

    // Admin elements
    const adminToggle = document.getElementById('adminToggle');
    const adminPanel = document.getElementById('adminPanel');
    const adminCloseBtn = document.getElementById('adminCloseBtn');
    const loginBtn = document.getElementById('loginBtn');
    const adminPass = document.getElementById('adminPass');
    const authMsg = document.getElementById('authMsg');
    const authArea = document.getElementById('authArea');
    const recordsSectionAdmin = document.getElementById('recordsSection');
    const adminRecordsTableBody = document.querySelector('#adminRecordsTable tbody');
    const exportAllBtn = document.getElementById('exportAllBtn');
    const clearAllBtn = document.getElementById('clearAllBtn');
    const logoutBtn = document.getElementById('logoutBtn');
    const adminRecordDetails = document.getElementById('adminRecordDetails');

    // Render quiz questions for chosen level
    function renderQuiz() {
      const level = levelEl.value;
      quizDiv.innerHTML = '';
      feedbackDiv.innerHTML = '';
      recordDetailsDiv.style.display = 'none';
      if (!level) return;

      const questions = quizData[level];
      questions.forEach((item, i) => {
        const fieldset = document.createElement('fieldset');
        const legend = document.createElement('legend');
        legend.className = 'question';
        legend.textContent = `${i+1}. ${item.q}`;
        fieldset.appendChild(legend);

        const opts = document.createElement('div');
        opts.className = 'options';
        item.o.forEach((opt, j) => {
          const id = `lvl${level}_q${i}_o${j}`;
          const label = document.createElement('label');
          const input = document.createElement('input');
          input.type = 'radio';
          input.name = `q${i}`;
          input.value = String(j);
          input.id = id;
          if (j === 0) input.required = true;
          label.appendChild(input);

          const span = document.createElement('span');
          span.textContent = ' ' + opt;
          label.appendChild(span);

          opts.appendChild(label);
        });
        fieldset.appendChild(opts);
        quizDiv.appendChild(fieldset);
      });
    }

    // Helpers
    function setResult(msg, type) {
      resultDiv.textContent = msg;
      if (type === 'error') {
        resultDiv.style.color = 'crimson';
        resultDiv.setAttribute('aria-invalid', 'true');
      } else {
        resultDiv.style.color = '#0f5132';
        resultDiv.removeAttribute('aria-invalid');
      }
    }

    function escapeHtml(s) {
      if (s == null) return '';
      return String(s).replace(/[&<>"']/g, function (m) {
        return ({'&':'&amp;','<':'&lt;','>':'&gt;','"':'&quot;',"'":"&#39;"})[m];
      });
    }

    function cryptoRandomId() {
      try {
        const arr = new Uint32Array(4);
        crypto.getRandomValues(arr);
        return Array.from(arr).map(n => n.toString(16)).join('-');
      } catch (e) {
        return 'id-' + Date.now();
      }
    }

    // Storage operations
    function saveRecord(record) {
      const all = JSON.parse(localStorage.getItem(STORAGE_KEY) || '[]');
      all.push(record);
      const trimmed = all.slice(-MAX_RECORDS);
      localStorage.setItem(STORAGE_KEY, JSON.stringify(trimmed));
    }

    function getAllRecords() {
      return JSON.parse(localStorage.getItem(STORAGE_KEY) || '[]');
    }

    // Student: validate
    function validateBeforeSubmit(level) {
      const name = document.getElementById('studentName').value.trim();
      const section = document.getElementById('section').value.trim();
      if (!name || !section || !level) {
        setResult('Please complete Name, Section, and Grade Level.', 'error');
        return false;
      }
      const questions = quizData[level];
      for (let i = 0; i < questions.length; i++) {
        const ans = document.querySelector(`input[name="q${i}"]:checked`);
        if (!ans) {
          setResult(`Please answer question ${i+1}.`, 'error');
          const firstRadio = document.querySelector(`input[name="q${i}"]`);
          if (firstRadio) firstRadio.focus();
          return false;
        }
      }
      return true;
    }

    // Render per-question feedback on the page
    function renderFeedback(record) {
      feedbackDiv.innerHTML = '';
      const header = document.createElement('div');
      header.innerHTML = `<strong>Score: ${record.score}</strong> — <span class="small">submitted ${new Date(record.date).toLocaleString()}</span>`;
      feedbackDiv.appendChild(header);

      record.answers.forEach((ansObj, idx) => {
        const item = document.createElement('div');
        item.className = 'fb-item ' + (ansObj.correct ? 'fb-correct' : 'fb-wrong');

        const qTitle = document.createElement('div');
        qTitle.innerHTML = `<strong>${idx+1}. ${escapeHtml(ansObj.question)}</strong>`;
        item.appendChild(qTitle);

        const userText = document.createElement('div');
        const userChoiceText = ansObj.userChoice != null ? ansObj.options[ansObj.userChoice] : '(no answer)';
        userText.innerHTML = `<span class="small">Your answer: </span><strong>${escapeHtml(userChoiceText)}</strong>`;
        item.appendChild(userText);

        const correctText = document.createElement('div');
        correctText.innerHTML = `<span class="small">Correct answer: </span><strong>${escapeHtml(ansObj.options[ansObj.correctIndex])}</strong>`;
        item.appendChild(correctText);

        const choicesList = document.createElement('ul');
        choicesList.className = 'small';
        ansObj.options.forEach((opt, j) => {
          const li = document.createElement('li');
          let prefix = '';
          if (j === ansObj.correctIndex) prefix = '✔ ';
          if (j === ansObj.userChoice && j !== ansObj.correctIndex) prefix = '✖ ';
          li.textContent = prefix + opt;
          choicesList.appendChild(li);
        });
        item.appendChild(choicesList);

        feedbackDiv.appendChild(item);
      });

      feedbackDiv.scrollIntoView({behavior:'smooth'});
    }

    // Student: show saved attempts filtered by name/section/level
    function loadRecordsFiltered(name, section, level) {
      const all = getAllRecords();
      const filtered = all.filter(r => r.name === name && r.section === section && r.level === level);
      recordsTableBody.innerHTML = '';
      filtered.forEach(r => {
        const tr = document.createElement('tr');
        const dateTd = document.createElement('td'); dateTd.textContent = new Date(r.date).toLocaleString(); tr.appendChild(dateTd);
        const nameTd = document.createElement('td'); nameTd.textContent = r.name; tr.appendChild(nameTd);
        const sectionTd = document.createElement('td'); sectionTd.textContent = r.section; tr.appendChild(sectionTd);
        const levelTd = document.createElement('td'); levelTd.textContent = r.level; tr.appendChild(levelTd);
        const scoreTd = document.createElement('td'); scoreTd.textContent = r.score; tr.appendChild(scoreTd);

        const detTd = document.createElement('td');
        const btn = document.createElement('button');
        btn.className = 'detailsButton';
        btn.textContent = 'Details';
        btn.addEventListener('click', () => {
          showRecordDetails(r);
        });
        detTd.appendChild(btn);
        tr.appendChild(detTd);

        recordsTableBody.appendChild(tr);
      });
      recordsSection.style.display = filtered.length ? 'block' : 'none';
      return filtered;
    }

    function showRecordDetails(record) {
      recordDetailsDiv.style.display = 'block';
      recordDetailsDiv.innerHTML = '';
      const header = document.createElement('div');
      header.innerHTML = `<strong>Attempt:</strong> ${new Date(record.date).toLocaleString()} — <strong>${record.score}</strong>`;
      recordDetailsDiv.appendChild(header);

      record.answers.forEach((ansObj, idx) => {
        const item = document.createElement('div');
        item.className = 'fb-item ' + (ansObj.correct ? 'fb-correct' : 'fb-wrong');

        const qTitle = document.createElement('div');
        qTitle.innerHTML = `<strong>${idx+1}. ${escapeHtml(ansObj.question)}</strong>`;
        item.appendChild(qTitle);

        const userText = document.createElement('div');
        const userChoiceText = ansObj.userChoice != null ? ansObj.options[ansObj.userChoice] : '(no answer)';
        userText.innerHTML = `<span class="small">Your answer: </span><strong>${escapeHtml(userChoiceText)}</strong>`;
        item.appendChild(userText);

        const correctText = document.createElement('div');
        correctText.innerHTML = `<span class="small">Correct answer: </span><strong>${escapeHtml(ansObj.options[ansObj.correctIndex])}</strong>`;
        item.appendChild(correctText);
        recordDetailsDiv.appendChild(item);
      });

      recordDetailsDiv.scrollIntoView({behavior:'smooth'});
    }

    // Student: export filtered results (summary)
    function exportCSVForFiltered(filtered) {
      if (!filtered.length) { alert('No saved results to export for the current student.'); return; }
      const header = ['date','name','section','level','score','id'];
      const rows = filtered.map(r => [r.date, r.name, r.section, r.level, r.score, r.id || '']);
      const csv = [header, ...rows].map(r => r.map(cell => `"${String(cell).replace(/"/g,'""')}"`).join(',')).join('\n');
      const bom = '\uFEFF';
      const blob = new Blob([bom + csv], {type:'text/csv;charset=utf-8;'});
      const url = URL.createObjectURL(blob);
      const a = document.createElement('a');
      a.href = url;
      const ts = new Date().toISOString().replace(/[:.]/g,'-');
      a.download = `fabm_results_${ts}.csv`;
      document.body.appendChild(a);
      a.click();
      a.remove();
      URL.revokeObjectURL(url);
    }

    // Student: clear filtered
    function clearFilteredRecords(name, section, level) {
      if (!confirm('Clear your saved results? This cannot be undone.')) return;
      const all = getAllRecords();
      const remaining = all.filter(r => !(r.name === name && r.section === section && r.level === level));
      localStorage.setItem(STORAGE_KEY, JSON.stringify(remaining));
      loadRecordsFiltered(name, section, level);
      setResult('Your saved results cleared.', 'ok');
      recordDetailsDiv.style.display = 'none';
      feedbackDiv.innerHTML = '';
    }

    // Admin: auth
    function 
