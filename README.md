<!doctype html>
<html lang="pt-BR">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>StoneVerse – Placa Digital</title>
  <meta http-equiv="Content-Security-Policy" content="default-src 'self' data:; img-src 'self' data: https:; style-src 'self' 'unsafe-inline'; script-src 'self' 'unsafe-inline'">
  <style>
    :root { --max-width:900px; --brand-green:#2e7d32; --brand-blue:#1976d2; --muted:#666; --card-bg:#f5f5f5; --page-bg:#f9f9f9; }
    html,body{height:100%;margin:0;padding:0;background:var(--page-bg);font-family:"Segoe UI",system-ui,-apple-system,"Helvetica Neue",Arial,sans-serif;color:#111;}
    .container{max-width:var(--max-width);margin:40px auto;padding:20px;text-align:center;}
    h1{color:var(--brand-green);margin:0 0 18px;font-size:1.5rem;}
    img.stone-photo{max-width:100%;height:auto;border-radius:12px;margin:20px 0;box-shadow:0 4px 8px rgba(0,0,0,0.08);border:3px solid #ddd;display:block;}
    .info{background:var(--card-bg);padding:15px;border-radius:8px;margin:20px 0;text-align:left;display:inline-block;width:100%;box-sizing:border-box;}
    .btn{background:var(--brand-green);color:#fff;padding:12px 24px;text-decoration:none;border-radius:6px;margin:10px;display:inline-block;font-weight:700;font-size:16px;border:none;cursor:pointer;}
    .btn-ar{background:var(--brand-blue);}
    .loading{color:var(--muted);font-style:italic;}
    .error{color:#b00020;font-weight:600;}
    .debug{font-size:0.9rem;color:#444;margin-top:10px;}
    @media (max-width:520px){ .container{margin:18px 12px;padding:12px;} h1{font-size:1.25rem;} }
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
        <p><strong>Atenção:</strong> JavaScript desativado. Abra a imagem diretamente: fotos/SEU_ID.jpg</p>
      </div>
    </noscript>
  </div>

  <script>
    (function () {
      const content = document.getElementById('content');
      function setBusy(isBusy) { content.setAttribute('aria-busy', isBusy ? 'true' : 'false'); }
      function showMessage(text, className) { setBusy(false); content.innerHTML = ''; const p = document.createElement('p'); p.textContent = text; if (className) p.className = className; content.appendChild(p); }

      const params = new URLSearchParams(window.location.search);
      const rawId = params.get('id');
      if (!rawId) { showMessage('ID não fornecido. Verifique o link.', 'error'); console.warn('Parâmetro id ausente na URL'); return; }

      let decoded;
      try { decoded = decodeURIComponent(rawId); } catch (e) { showMessage('ID inválido. Erro ao decodificar.', 'error'); console.error('Erro decodeURIComponent:', e); return; }
      if (!/^[A-Za-z0-9_-]+$/.test(decoded)) { showMessage('ID inválido. Caracteres não permitidos.', 'error'); console.warn('ID contem caracteres inválidos:', decoded); return; }

      const stoneID = decoded;

      // ALTERAÇÃO: usar o domínio raw.githubusercontent.com para carregar a imagem diretamente
      const RAW_BASE = 'https://raw.githubusercontent.com/stoneversebr/stoneverse/main/fotos';
      const imgUrl = `${RAW_BASE}/${encodeURIComponent(stoneID)}.jpg`;
      // Se preferir usar caminho relativo quando hospedado no mesmo domínio, comente a linha acima e descomente a linha abaixo:
      // const imgUrl = `fotos/${encodeURIComponent(stoneID)}.jpg`;

      // Mostra link direto para testes rápidos
      function appendDebugLink(url) {
        const dbg = document.createElement('div');
        dbg.className = 'debug';
        dbg.innerHTML = `Link direto para teste: <a href="${url}" target="_blank" rel="noopener noreferrer">${url}</a>`;
        content.appendChild(dbg);
      }

      function createInfoParagraph(labelText, valueText) {
        const p = document.createElement('p');
        const strong = document.createElement('strong');
        strong.textContent = labelText;
        p.appendChild(strong);
        p.appendChild(document.createTextNode(' ' + valueText));
        return p;
      }

      function renderWithImage(url) {
        setBusy(false);
        content.innerHTML = '';

        const img = document.createElement('img');
        img.className = 'stone-photo';
        img.src = url;
        img.alt = `Foto da placa ${stoneID}`;
        img.loading = 'lazy';
        img.width = 1200;
        img.onerror = function () { showImageNotFound(); };

        const info = document.createElement('div');
        info.className = 'info';
        info.appendChild(createInfoParagraph('ID Único:', stoneID));
        info.appendChild(createInfoParagraph('Status:', 'Disponível para instalação'));
        info.appendChild(createInfoParagraph('Localização:', 'Depósito Central, Setor B'));

        const arButton = document.createElement('a');
        arButton.className = 'btn btn-ar';
        arButton.textContent = '🕶️ Ver em Realidade Aumentada';
        arButton.href =  `https://stoneversear.8thwall.app/stone-ar-viewer?id=${encodeURIComponent(stoneID)}`;
        arButton.target = '_blank';
        arButton.rel = 'noopener noreferrer';
        arButton.title = 'Abrir visualizador de Realidade Aumentada em nova aba';

        content.appendChild(img);
        content.appendChild(info);
        content.appendChild(arButton);
        appendDebugLink(url);
      }

      function showImageNotFound() {
        setBusy(false);
        content.innerHTML = '';
        const p = document.createElement('p');
        p.className = 'error';
        p.textContent = `❌ Foto não encontrada para ${stoneID}`;
        const p2 = document.createElement('p');
        p2.textContent = 'Abra o link direto para ver o erro (404/403) e confira o caminho/nomes.';
        content.appendChild(p);
        content.appendChild(p2);
        appendDebugLink(imgUrl);
      }

      // Primeiro tentamos HEAD para detectar status; se falhar (CORS/HEAD bloqueado), usamos preload com Image()
      function tryFetchHeadThenPreload(url) {
        if (!window.fetch) {
          console.warn('fetch não disponível, usando preload de imagem');
          preloadImage(url);
          return;
        }

        // Use cache: "no-store" durante debug se quiser forçar o servidor
        fetch(url, { method: 'HEAD', cache: 'reload' })
          .then(function (resp) {
            console.log('HEAD response', resp);
            const ct = resp.headers.get('content-type') || '';
            if (resp.ok && ct.toLowerCase().startsWith('image')) {
              console.log('HEAD ok e content-type imagem, indo carregar');
              preloadImage(url);
            } else {
              console.warn('HEAD não retornou image content-type ou não ok. status=', resp.status, 'content-type=', ct);
              preloadImage(url);
            }
          })
          .catch(function (err) {
            console.warn('fetch HEAD falhou (CORS/HEAD bloqueado ou rede). fallback para preload:', err);
            preloadImage(url);
          });
      }

      // Preload seguro usando um objeto Image — esperamos onload/onerror antes de inserir
      function preloadImage(url) {
        setBusy(true);
        const tester = new Image();
        tester.onload = function () {
          console.log('Image carregada com sucesso:', url);
          renderWithImage(url);
        };
        tester.onerror = function (ev) {
          console.error('Erro ao carregar imagem diretamente via Image():', ev);
          showImageNotFound();
        };
        tester.src = url;
        setTimeout(function () {
          appendDebugLink(url);
        }, 600);
      }

      console.log('Tentando carregar imagem para stoneID=', stoneID, 'url=', imgUrl);
      tryFetchHeadThenPreload(imgUrl);

    })();
  </script>
</body>
</html>
