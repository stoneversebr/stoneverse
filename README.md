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
    .btn { background: #2e7d32; color: white; padding: 12px 24px; text-decoration: none; border-radius: 6px; margin: 10px; display: inline-block; font-weight: bold; }
    .btn-ar { background: #1976d2; }
  </style>
</head>
<body>
  <h1>🪨 Placa Digitalizada</h1>

  <div id="content">
    <p>Carregando dados...</p>
  </div>

  <script>
    const urlParams = new URLSearchParams(window.location.search);
    const stoneID = urlParams.get('id');
    const contentDiv = document.getElementById('content');

    if (!stoneID) {
      contentDiv.innerHTML = '<p>ID não fornecido.</p>';
    } else {
      const imgUrl = fotos/${stoneID}.jpg;
      const img = new Image();
      img.onload = function() {
        contentDiv.innerHTML = `
          <img src="${imgUrl}" alt="Placa ${stoneID}">
          <a href= "https://stoneversebr.8thwall.app/stone-ar-viewer?id=${stoneID}" class="btn btn-ar">
            🕶️ Ver em Realidade Aumentada
          </a>
        `;
      };
      img.onerror = function() {
        contentDiv.innerHTML = <p>Foto não encontrada para ${stoneID}</p>;
      };
      img.src = imgUrl;
    }
  </script>
</body>
</html>
