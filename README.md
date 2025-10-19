<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>StoneVerse – Placa Digital</title>
  <style>
    body { 
      font-family: 'Segoe UI', sans-serif; 
      max-width: 900px; 
      margin: 40px auto; 
      padding: 20px; 
      text-align: center; 
      background: #f9f9f9;
    }
    h1 { color: #2e7d32; }
    img { 
      max-width: 100%; 
      height: auto; 
      border-radius: 12px; 
      margin: 20px 0; 
      box-shadow: 0 4px 8px rgba(0,0,0,0.1);
      border: 3px solid #ddd;
    }
    .info { 
      background: #f5f5f5; 
      padding: 15px; 
      border-radius: 8px; 
      margin: 20px 0; 
      text-align: left; 
      display: inline-block;
      width: 100%;
    }
    .btn { 
      background: #2e7d32; 
      color: white; 
      padding: 12px 24px; 
      text-decoration: none; 
      border-radius: 6px; 
      margin: 10px; 
      display: inline-block; 
      font-weight: bold; 
      font-size: 16px;
    }
    .btn-ar { 
      background: #1976d2; 
    }
    .loading { color: #666; font-style: italic; }
  </style>
</head>
<body>
  <h1>🪨 Placa Digitalizada</h1>

  <div id="content">
    <p class="loading">Carregando dados...</p>
  </div>

  <script>
    // Extrai o ID da URL: ?id=STONE-A3F9C2
    const urlParams = new URLSearchParams(window.location.search);
    const stoneID = urlParams.get('id');
    const contentDiv = document.getElementById('content');

    if (!stoneID) {
      contentDiv.innerHTML = '<p>ID não fornecido. Verifique o link.</p>';
    } else {
      // Monta a URL da imagem
      const imgUrl = fotos/${stoneID}.jpg;

      // Verifica se a imagem existe
      const img = new Image();
      img.onload = function() {
        contentDiv.innerHTML = `
          <img src="${imgUrl}" alt="Placa ${stoneID}">
          <div class="info">
            <p><strong>ID Único:</strong> ${stoneID}</p>
            <p><strong>Status:</strong> Disponível para instalação</p>
            <p><strong>Localização:</strong> Depósito Central, Setor B</p>
          </div>
          <a href="https://stoneversear.8thwall.app/stone-ar-viewer?id=${stoneID}" class="btn btn-ar">
            🕶️ Ver em Realidade Aumentada
          </a>
        `;
      };
      img.onerror = function() {
        contentDiv.innerHTML = `
          <p>❌ Foto não encontrada para ${stoneID}</p>
          <p>Verifique se a placa foi digitalizada corretamente.</p>
        `;
      };
      img.src = imgUrl;
    }
  </script>
</body>
</html>
