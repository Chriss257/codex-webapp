# codex-webapp<!DOCTYPE html>
<html lang="de">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Codex Observandi</title>
  <style>
    body { background:#000; color:#b30000; font-family:'Courier New'; padding:1rem; }
    input, textarea, select, button {
      background:#111; color:#b30000; border:1px solid #b30000;
      padding:0.5rem; margin:0.3rem 0; width:100%;
    }
    .proband { border:1px solid #b30000; padding:0.5rem; margin-bottom:1rem; }
  </style>
</head>
<body>
  <h1>Codex Observandi</h1>
  <h2>Neuen Probanden anlegen</h2>
  <input id="codename" placeholder="Codename" />
  <input id="realname" placeholder="Echter Name (optional)" />
  <label><input type="checkbox" id="protected" checked /> Identität geschützt</label>
  <button onclick="addProband()">Proband speichern</button>
  <h2>Alle Probanden</h2>
  <div id="probandList"></div>
  <script>
    const probanden = JSON.parse(localStorage.getItem('probanden') || '[]');
    function saveData() { localStorage.setItem('probanden', JSON.stringify(probanden)); }
    function addProband() {
      const codename = document.getElementById('codename').value;
      const realname = document.getElementById('realname').value;
      const protectedState = document.getElementById('protected').checked;
      if (!codename) return alert("Codename erforderlich");
      probanden.push({ id: Date.now(), codename, realname, protected: protectedState, entries: [] });
      saveData(); render(); document.getElementById('codename').value = ''; document.getElementById('realname').value = '';
    }
    function addEntry(i) {
      const cat = document.getElementById(`cat-${i}`).value;
      const note = document.getElementById(`note-${i}`).value;
      if (!note) return;
      probanden[i].entries.push({ category: cat, note, timestamp: new Date().toISOString() });
      saveData(); render();
    }
    function render() {
      const c = document.getElementById('probandList'); c.innerHTML = '';
      probanden.forEach((p, i) => {
        const div = document.createElement('div'); div.className = 'proband';
        div.innerHTML = `<strong>Codename:</strong> ${p.codename}<br>${p.protected ? '<em>(Geschützt)</em>' : `<strong>Name:</strong> ${p.realname || '-'}`}<br><br>
          <select id="cat-${i}">
            <option>Handlungsmotiv 1</option><option>Handlungsmotiv 2</option>
            <option>Handlungsmotiv 3</option><option>Körpersprache</option>
            <option>Sprachmuster</option><option>Bindungsverhalten</option>
          </select><br>
          <textarea id="note-${i}" placeholder="Beobachtung oder Notiz"></textarea>
          <button onclick="addEntry(${i})">Eintrag speichern</button>
          <div><h4>Einträge:</h4><ul>${
            p.entries.map(e => `<li><strong>[${e.category}]</strong> ${e.note} <em>${new Date(e.timestamp).toLocaleString()}</em></li>`).join('')
          }</ul></div>`;
        c.appendChild(div);
      });
    }
    render();
  </script>
</body>
</html>