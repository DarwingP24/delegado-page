<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <title>RESULTADOS DELEGADO REG 15</title>
  <style>
    body, html {
      height: 100%;
      margin: 0;
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
      background: url('https://i.postimg.cc/YCxg0JvK/Dise-o-sin-t-tulo-2.png') no-repeat center center fixed;
      background-size: cover;
      position: relative;
    }

    /* Capa de superposición para difuminar el fondo */
    body::before {
      content: "";
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      backdrop-filter: blur(8px);
      background-color: rgba(0, 0, 0, 0.4);
      z-index: 0;
    }

    .container {
      position: relative;
      z-index: 1;
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100%;
    }

    .card {
      background-color: rgba(255, 255, 255, 0.95);
      padding: 40px;
      border-radius: 10px;
      box-shadow: 0 4px 15px rgba(0, 0, 0, 0.2);
      max-width: 600px;
      width: 100%;
    }

    h2 {
      text-align: center;
      margin-bottom: 20px;
      color: #333;
    }

    ul {
      list-style: none;
      padding: 0;
    }

    li {
      margin-bottom: 10px;
      font-size: 16px;
      color: #555;
    }

    .registrado {
      color: green;
      font-weight: bold;
    }

    .no-checkin {
      color: red;
      font-weight: bold;
    }
  </style>
</head>
<body>
  <div class="container">
    <div class="card">
      <h2>Resultados del Delegado</h2>
      <ul>
        <li><strong>Nombre:</strong> Juan Pérez</li>
        <li><strong>Delegación:</strong> Santo Domingo</li>
        <li><strong>Check-In:</strong> <span class="registrado">REGISTRADO</span></li>
        <li><strong>Categoría 1:</strong> 85</li>
        <li><strong>Categoría 2:</strong> 90</li>
        <li><strong>Categoría 3:</strong> 78</li>
        <li><strong>Categoría 4:</strong> 88</li>
      </ul>
    </div>
  </div>
</body>
</html>
