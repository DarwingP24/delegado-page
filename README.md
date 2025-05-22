<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8" />
  <title>RESULTADOS DELEGADO REG 15</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      padding: 30px;
      background: #f0f2f5;
    }
    .header {
      display: flex;
      align-items: center;
      justify-content: space-between;
      margin-bottom: 20px;
    }
    .header img {
      height: 60px;
    }
    .header h1 {
      flex-grow: 1;
      text-align: center;
      font-size: 24px;
      margin: 0 20px;
    }
    .card {
      background: #fff;
      padding: 20px;
      border-radius: 8px;
      box-shadow: 0 2px 10px rgba(0,0,0,0.1);
      max-width: 600px;
      margin: auto;
    }
    h2, h3 {
      text-align: center;
    }
    ul {
      list-style: none;
      padding: 0;
    }
    li {
      padding: 5px 0;
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
  <script>
    function getDelegadoID() {
      const params = new URLSearchParams(window.location.search);
      return params.get("id");
    }

    async function cargarDatos() {
      const id = getDelegadoID();
      const url = "https://docs.google.com/spreadsheets/d/e/2PACX-1vT6fLIskyoJRS2F82f4Sb1oaxpvr2oro_-nyWKy3fDEN6VEtKY0mdrH9Pd5qyGLRpQF5GDVTgHVxCBT/pub?gid=0&single=true&output=csv";

      try {
        const response = await fetch(url);
        const texto = await response.text();

        const filas = texto.trim().split('\n').map(fila => fila.split(/,(?=(?:(?:[^"]*"){2})*[^"]*$)/));
        const categorias = filas[2]; // Fila 3 (índice 2) contiene los encabezados de las categorías
        const datosDelegados = filas.slice(3); // Datos a partir de la fila 4

        const delegado = datosDelegados.find(fila => fila[0].trim() === id);
        const contenedor = document.getElementById('contenido');

        if (!delegado) {
          contenedor.innerHTML = "<p><strong>Delegado no encontrado.</strong></p>";
          return;
        }

        const delegacion = delegado[1]; // Columna B
        const nombre = delegado[2];     // Columna C
        const checkin = delegado[3];    // Columna D

        let html = `<h2>Resultados del Delegado</h2>`;
        html += `<ul>`;
        html += `<li><strong>Nombre:</strong> ${nombre}</li>`;
        html += `<li><strong>Delegación:</strong> ${delegacion}</li>`;

        // Estado de Check-In
        if (checkin.toUpperCase() === "REGISTRADO") {
          html += `<li><strong>Check-In:</strong> <span class="registrado">REGISTRADO</span></li>`;
        } else {
          html += `<li><strong>Check-In:</strong> <span class="no-checkin">NO CHECK-IN</span></li>`;
        }

        // Mostrar categorías y puntuaciones desde la columna E (índice 4)
        for (let i = 4; i < categorias.length; i++) {
          const categoria = categorias[i]?.trim() || `Categoría ${i + 1}`;
          const puntuacion = delegado[i]?.trim() || '0';
          html += `<li><strong>${categoria}:</strong> ${puntuacion}</li>`;
        }

        html += `</ul>`;
        contenedor.innerHTML = html;

      } catch (error) {
        console.error(error);
        document.getElementById('contenido').innerHTML = `<p>Error al cargar los datos.</p>`;
      }
    }

    window.onload = cargarDatos;
    setInterval(cargarDatos, 60000);
  </script>
</head>
<body>
  <div class="header">
    <!-- Reemplaza la URL con la del logo de PLERD -->
    <img src="https://th.bing.com/th/id/OIP.eUvmdBETrHskQkE5VPwQSQHaHa?cb=iwc2&rs=1&pid=ImgDetMain" alt="Logo PLERD">
    <h1>DECIMO SEXTO MODELO REGIONAL 15</h1>
    <img src="https://th.bing.com/th/id/OIP.4gmmq7apwbwzRtkucTzbJAHaGS?cb=iwc2&rs=1&pid=ImgDetMain" alt="Logo ONU">
  </div>
  <div class="card">
    <div id="contenido">Cargando datos...</div>
  </div>
</body>
</html>
