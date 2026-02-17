diff --git a/README.md b/README.md
index 5162e6f6f73aca10961dcb0ad2c2e4b7b4c51051..b8e8c82befb9b96c917132d9f1579f8519a6f3c7 100644
--- a/README.md
+++ b/README.md
@@ -1 +1,120 @@
-# SpecApp3.0
\ No newline at end of file
+# MedSpecAPP v5.0 (SpecApp3.0)
+
+Applicazione web single-file per ripasso e simulazione quiz in area medico-sanitaria, con modalità **Studio**, **Quiz** ed **Esame**.
+
+## Panoramica
+
+- Interfaccia responsive (sidebar desktop + bottom nav mobile).
+- Setup sessione con selezione:
+  - banca dati,
+  - numero domande,
+  - materie,
+  - distribuzione,
+  - timer,
+  - modalità.
+- Banche dati integrate in `index.html`:
+  - `BANK_GENERALI_RAW` (domande generali),
+  - `BANK_FEB2026_RAW` (Primo Appello / Febbraio 2026).
+- Persistenza locale tramite `localStorage` con migrazione schema stato.
+
+## Requisiti
+
+- Un browser moderno (Chrome, Edge, Firefox, Safari).
+- Nessuna dipendenza esterna obbligatoria per l'esecuzione.
+
+## Avvio rapido
+
+### Opzione 1 — apertura diretta
+Apri `index.html` nel browser.
+
+### Opzione 2 — server locale consigliato
+```bash
+python3 -m http.server 4173
+```
+Poi apri:
+
+- `http://127.0.0.1:4173`
+
+## Flusso d'uso
+
+1. Apri l'app e premi **Entra** nella splash.
+2. Vai su **Studio** o **Primo appello**.
+3. Apri **Configurazione quiz**.
+4. Seleziona banca dati e filtri.
+5. Avvia la sessione.
+
+## Struttura progetto
+
+```text
+SpecApp3.0/
+├── index.html   # app completa (UI + logica + dataset)
+└── README.md
+```
+
+## Dati e normalizzazione
+
+Il motore converte schemi eterogenei in uno schema unico con:
+
+- `id`
+- `topic`
+- `question`
+- `answers`
+- `correctIndices`
+- `explanation`
+
+La normalizzazione viene applicata a entrambe le banche prima del filtraggio in setup.
+
+## Persistenza stato
+
+Lo stato applicativo viene salvato in `localStorage` con chiave principale:
+
+- `medspecapp.v3`
+
+Sono gestite anche chiavi legacy per migrazione:
+
+- `medspecapp.v2`
+- `medspecapp.v1`
+- `medspecapp`
+
+## Controlli consigliati prima del rilascio
+
+### 1) Validazione HTML
+```bash
+python3 - <<'PY'
+from html.parser import HTMLParser
+from pathlib import Path
+HTMLParser().feed(Path('index.html').read_text())
+print('HTML OK')
+PY
+```
+
+### 2) Verifica sintassi JavaScript inline
+```bash
+python3 - <<'PY'
+from pathlib import Path
+import re, subprocess, tempfile
+s=Path('index.html').read_text()
+js='\n'.join(re.findall(r'<script[^>]*>(.*?)</script>', s, flags=re.S))
+with tempfile.NamedTemporaryFile('w', suffix='.js', delete=False) as f:
+    f.write(js)
+    p=f.name
+r=subprocess.run(['node','--check',p],capture_output=True,text=True)
+print('JS CHECK OK' if r.returncode==0 else r.stderr)
+PY
+```
+
+### 3) Conteggio banche dati
+```bash
+node - <<'NODE'
+const fs=require('fs');
+const s=fs.readFileSync('index.html','utf8');
+const ex=(n)=>{const m=s.match(new RegExp(`const ${n} = \\[(.*?)\\n\\s*\\];`,'s')); return eval('['+m[1]+'\n]')};
+console.log('BANK_GENERALI_RAW', ex('BANK_GENERALI_RAW').length);
+console.log('BANK_FEB2026_RAW', ex('BANK_FEB2026_RAW').length);
+NODE
+```
+
+## Note
+
+- Il progetto è volutamente in vanilla HTML/CSS/JS per massima portabilità.
+- Tutti i contenuti e la logica principali sono nel file `index.html`.
