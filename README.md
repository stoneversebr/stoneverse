<!doctype html>
<html lang="pt-BR">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>StoneVerse – Placa Digital</title>

  <!-- Basic security / UX -->
  <meta http-equiv="Content-Security-Policy" content="default-src 'self' data:; img-src 'self' data: https:; style-src 'self' 'unsafe-inline'; script-src 'self' 'unsafe-inline'">
  <style>
    :root {
      --max-width: 900px;
      --brand-green: #2e7d32;
      --brand-blue: #1976d2;
      --muted: #666;
      --card-bg: #f5f5f5;
      --page-bg: #f9f9f9;
    }

    html,body{
      height:100%;
      margin:0;
      padding:0;
      background:var(--page-bg);
      font-family: "Segoe UI", system-ui, -apple-system, "Helvetica Neue", Arial, sans-serif;
      color: #111;
    }

    .container{
      max-width:var(--max-width);
      margin:40px auto;
      padding:20px;
      text-align:center;
    }

    h1{ color:var(--brand-green); margin:0 0 18px; font-size:1.5rem; }
    img.stone-photo{
      max-width:100%;
      height:auto;
      border-radius:12px;
      margin:20px 0;
      box-shadow:0 4px 8px rgba(0,0,0,0.08);
      border:3px solid #ddd;
      display:block;
    }

    .info{
      background:var(--card-bg);
      padding:15px;
      border-radius:8px;
      margin:20px 0;
      text-align:left;
      display:inline-block;
      width:100%;
      box-sizing:border-box;
    }

    .btn{
      background:var(--brand-green);
      color:#fff;
      padding:12px 24px;
      text-decoration:none;
      border-radius:6px;
      margin:10px;
      display:inline-block;
      font-weight:700;
      font-size:16px;
      border:none;
      cursor:pointer;
    }

    .btn-ar{ background:var(--brand-blue); }

    .loading{ color:var(--muted); font-style:italic; }
    .error{ color:#b00020; font-weight:600; }

    @media (max-width:520px){
      .container{ margin:18px 12px; padding:12px; }
      h1{ font-size:1.25rem; }
    }
  </style>
</head>
<body>
  <div class="container" role="main">
    <h1>🪨 Placa Digitalizada</h1>

    <div id="content" aria-live="polite" aria-busy="true">
      <p class="loading">Carregando dados...</p>
    </div>

    <noscript>
      <div class="info">
        <p><strong>Atenção:</strong> JavaScript está desativado. Para ver a placa, ative o JavaScript ou abra o arquivo de imagem diretamente.</p>
      </div>
    </noscript>
  </div>

  <script>
    (function () {
      const content = document.getElementById('content');

      function setBusy(isBusy) {
        content.setAttribute('aria-busy', isBusy ? 'true' : 'false');
      }

      function showMessage(text, className) {
        setBusy(false);
        content.innerHTML = '';
        const p = document.createElement('p');
        p.textContent = text;
        if (className) p.className = className;
        content.appendChild(p);
      }

      // Extract and validate id param
      const params = new URLSearchParams(window.location.search);
      const rawId = params.get('id');

      if (!rawId) {
        showMessage('ID não fornecido. Verifique o link.', 'error');
        return;
      }

      // Decode and sanitize: allow only letters, numbers, underscore and hyphen (adjust if necessary)
      const decoded = decodeURIComponent(rawId);
      const valid = /^[A-Za-z0-9_-]+$/.test(decoded);
      if (!valid) {
        showMessage('ID inválido. Caracteres não permitidos.', 'error');
        return;
      }

      const stoneID = decoded;
      const imgUrl = `fotos/${encodeURIComponent(stoneID)}.jpg`;

      // Create DOM elements safely (avoid innerHTML with untrusted data)
      function renderWithImage(url) {
        setBusy(false);
        content.innerHTML = '';

        const img = document.createElement('img');
        img.className = 'stone-photo';
        img.src = url;
        img.alt = Foto da placa ${stoneID};
        img.loading = 'lazy';
        img.width = 1200; // hint for layout (optional)
        img.onerror = function () {
          // If the image failed to load after we tried to render it, show fallback
          showImageNotFound();
        };

        const info = document.createElement('div');
        info.className = 'info';

        const pId = document.createElement('p');
        pId.innerHTML = `<strong>ID Único:</strong> ${escapeHtml(stoneID)}`;

        const pStatus = document.createElement('p');
        pStatus.innerHTML = `<strong>Status:</strong> Disponível para instalação`;

        const pLoc = document.createElement('p');
        pLoc.innerHTML = `<strong>Localização:</strong> Depósito Central, Setor B`;

        info.appendChild(pId);
        info.appendChild(pStatus);
        info.appendChild(pLoc);

        const arButton = document.createElement('a');
        // open AR url in a new tab/window for safety; add noopener,noreferrer
        arButton.className = 'btn btn-ar';
        arButton.textContent = '🕶️ Ver em Realidade Aumentada';
        arButton.href = `https://stoneversebr.8thwall.app/stone-ar-viewer?id=${encodeURIComponent(stoneID)}`;
        arButton.target = '_blank';
        arButton.rel = 'noopener noreferrer';

        content.appendChild(img);
        content.appendChild(info);
        content.appendChild(arButton);
      }

      function showImageNotFound() {
        setBusy(false);
        content.innerHTML = '';
        const p = document.createElement('p');
        p.className = 'error';
        p.textContent = ❌ Foto não encontrada para ${stoneID};
        const p2 = document.createElement('p');
        p2.textContent = 'Verifique se a placa foi digitalizada corretamente.';
        content.appendChild(p);
        content.appendChild(p2);
      }

      // Simple HTML escape for text inserted as HTML snippets (used only for small controlled parts)
      function escapeHtml(str) {
        return String(str).replace(/[&<>"'`=\/]/g, function (s) {
          return ({
            '&': '&amp;',
            '<': '&lt;',
            '>': '&gt;',
            '"': '&quot;',
            "'": '&#39;',
            '/': '&#x2F;',
            '`': '&#x60;',
            '=': '&#x3D;'
          })[s];
        });
      }

      // Try a lightweight HEAD fetch to check if image exists.
      // Note: Some servers block HEAD or CORS may prevent this; fallback to img onload/onerror.
      if (window.fetch) {
        fetch(imgUrl, { method: 'HEAD', cache: 'reload' })
          .then(function (resp) {
            if (resp.ok && resp.headers.get('content-type')?.startsWith('image')) {
              renderWithImage(imgUrl);
            } else {
              // Not OK — render and rely on img.onerror to show fallback
              // (some servers return 200 with HTML page; we still try to load the image)
              renderWithImage(imgUrl);
            }
          })
          .catch(function () {
            // Network/CORS/HEAD not allowed — try to render image and let onerror handle it
            renderWithImage(imgUrl);
          });
      } else {
        // No fetch support: fallback to image-based existence detection
        renderWithImage(imgUrl);
      }
    })();
  </script>
</body>
</html>
