<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>StoneVerse – Placa Digital</title>
  <style>
    body { font-family: 'Segoe UI', sans-serif; max-width: 900px; margin: 40px auto; padding: 20px; text-align: center; }
    h1 { color: #2e7d32; }
    img { max-width: 100%; height: auto; border-radius: 12px; margin: 20px 0; box-shadow: 0 4px 8px rgba(0,0,0,0.1); }
    .info { background: #f5f5f5; padding: 15px; border-radius: 8px; margin: 20px 0; text-align: left; display: inline-block; }
    .btn { background: #2e7d32; color: white; padding: 12px 24px; text-decoration: none; border-radius: 6px; margin: 10px; display: inline-block; font-weight: bold; }
    .btn-ar { background: #1976d2; }
  </style>
</head>
<body>
  <h1>🪨 Placa Digitalizada</h1>
  <div id="content">
    <!-- Carregado dinamicamente -->
  </div>

  <script>
    // Extrai o ID da URL: ?id=STONE-A3F9C2
    const urlParams = new URLSearchParams(window.location.search);
    const stoneID = urlParams.get('id');

    if (!stoneID) {
      document.getElementById('content').innerHTML = '<p>ID não fornecido.</p>';
    } else {
      document.getElementById('content').innerHTML = `
        <img src="fotos/${stoneID}.jpg" alt="Placa ${stoneID}" onerror="this.src='https://via.placeholder.com/600x400?text=Foto+Indisponível'">
        <div class="info">
          <p><strong>ID Único:</strong> ${stoneID}</p>
          <p><strong>Status:</strong> Disponível para instalação</p>
          <p><strong>Localização:</strong> Depósito Central, Setor B</p>
        </div>
        <a href="exp://your-app-link-here?id=${stoneID}" class="btn btn-ar">🕶️ Ver em Realidade Aumentada</a>
      `;
    }
  </script>
</body>
</html>
