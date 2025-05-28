<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Delegado - Resultados y Ficha Médica</title>
  <style>
    html, body {
      margin: 0;
      padding: 0;
      height: 100%;
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
      overflow-x: hidden;
    }

    .bg {
      position: fixed;
      top: 0;
      left: 0;
      width: 100vw;
      height: 100vh;
      background: url('https://i.postimg.cc/FHGqFhdk/Chat-GPT-Image-27-may-2025-21-08-37.png') no-repeat center center;
      background-size: cover;
      z-index: -1;
    }

    .container {
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: flex-start;
      min-height: 100vh;
      padding: 20px;
      box-sizing: border-box;
    }

    .navbar {
      background-color: rgba(0, 64, 128, 0.8);
      padding: 10px 20px;
      border-radius: 5px;
      margin-bottom: 20px;
      width: 100%;
      max-width: 600px;
    }

    .nav-list {
      list-style: none;
      margin: 0;
      padding: 0;
      display: flex;
      justify-content: center;
      flex-wrap: wrap;
    }

    .nav-list li {
      margin: 0 15px;
    }

    .nav-list li a {
      color: #fff;
      text-decoration: none;
      font-weight: bold;
      transition: color 0.3s;
    }

    .nav-list li a:hover {
      color: #ffcc00;
    }

    .card {
      background-color: rgba(255, 255, 255, 0.95);
      padding: 30px;
      border-radius: 10px;
      box-shadow: 0 4px 15px rgba(0, 0, 0, 0.2);
      max-width: 600px;
      width: 100%;
      overflow-y: auto;
      max-height: 80vh;
    }

    h2 {
      text-align: center;
      margin-bottom: 20px;
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

    .verde {
      color: green;
      font-weight: bold;
    }

    .rojo {
      color: red;
      font-weight: bold;
    }

    .negrita {
      font-weight: bold;
    }

    @media (max-width: 600px) {
      .nav-list {
        flex-direction: column;
        align-items: center;
      }

      .nav-list li {
        margin: 5px 0;
      }

      .card {
        padding: 20px;
        max-height: none;
      }
    }
  </style>
</head>
<body>
  <div class="bg"></div>
  <div class="container">
    <nav class="navbar">
      <ul class="nav-list">
        <li><a href="#" onclick="mostrarSeccion('resultados')">Resultados del Delegado</a></li>
        <li><a href="#" onclick="mostrarSeccion('ficha')">Ficha Médica</a></li>
      </ul>
    </nav>

    <div class="card">
      <h2 id="titulo">Resultados del Delegado</h2>
      <div id="contenido">Cargando datos...</div>
    </div>
  </div>

  <script>
    function getDelegadoID() {
      const params = new URLSearchParams(window.location.search);
      return params.get("id");
    }

    const urlResultados = "https://docs.google.com/spreadsheets/d/e/2PACX-1vT6fLIskyoJRS2F82f4Sb1oaxpvr2oro_-nyWKy3fDEN6VEtKY0mdrH9Pd5qyGLRpQF5GDVTgHVxCBT/pub?gid=0&single=true&output=csv";
    const urlFichaMedica = "https://docs.google.com/spreadsheets/d/e/2PACX-1vT6fLIskyoJRS2F82f4Sb1oaxpvr2oro_-nyWKy3fDEN6VEtKY0mdrH9Pd5qyGLRpQF5GDVTgHVxCBT/pub?gid=2052349342&single=true&output=csv";

    function mostrarSeccion(seccion) {
      const id = getDelegadoID();
      const titulo = document.getElementById('titulo');
      const contenido = document.getElementById('contenido');

      if (!id) {
        contenido.innerHTML = "<p><strong>ID de delegado no proporcionado en la URL.</strong></p>";
        return;
      }

      if (seccion === 'resultados') {
        titulo.textContent = "Resultados del Delegado";
        cargarDatos(urlResultados, id, 'resultados');
      } else if (seccion === 'ficha') {
        titulo.textContent = "Ficha Médica";
        cargarDatos(urlFichaMedica, id, 'ficha');
      }
    }

    async function cargarDatos(url, id, tipo) {
      try {
        const response = await fetch(url);
        const texto = await response.text();

        const filas = texto.trim().split('\n').map(fila => fila.split(/,(?=(?:(?:[^"]*"){2})*[^"]*$)/));
        const encabezados = filas[2];
        const datos = filas.slice(3);

        const delegado = datos.find(fila => fila[0].trim() === id);
        const contenedor = document.getElementById('contenido');

        if (!delegado) {
          contenedor.innerHTML = "<p><strong>Delegado no encontrado.</strong></p>";
          return;
        }

        let html = "<ul>";

        if (tipo === 'resultados') {
          const delegacion = delegado[1];
          const nombre = delegado[2];
          const checkin = delegado[3];

          html += `<li><strong>Nombre:</strong> ${nombre}</li>`;
          html += `<li><strong>Delegación:</strong> ${delegacion}</li>`;
          html += `<li><strong>Check-In:</strong> ${
            checkin.toUpperCase() === "REGISTRADO"
              ? `<span class="verde">REGISTRADO</span>`
              : `<span class="rojo">NO CHECK-IN</span>`
          }</li>`;

          for (let i = 4; i < encabezados.length; i++) {
            const categoria = encabezados[i]?.trim() || `Categoría ${i + 1}`;
            const puntuacion = delegado[i]?.trim() || '0';
            html += `<li><strong>${categoria}:</strong> ${puntuacion}</li>`;
          }
        } else if (tipo === 'ficha') {
          for (let i = 1; i < encabezados.length; i++) {
            const campo = encabezados[i]?.trim() || `Campo ${i + 1}`;
            const valor = delegado[i]?.trim() || 'N/A';
            html += `<li><strong>${campo}:</strong> <span class="negrita">${valor}</span></li>`;
          }
        }

        html += "</ul>";
        contenedor.innerHTML = html;

      } catch (error) {
        console.error(error);
        document.getElementById('contenido').innerHTML = "<p><strong>Error al cargar los datos.</strong></p>";
      }
    }

    window.onload = () => {
      mostrarSeccion('resultados');
    };
  </script>
</body>
</html>
