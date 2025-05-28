<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Delegado - Resultados y Ficha Médica</title>
  <style>
    html, body {
      margin: 0; padding: 0; height: 100%;
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
      background: url('https://i.postimg.cc/9MzpyNPL/Chat-GPT-Image-27-may-2025-21-29-56.png') no-repeat center center fixed;
      background-size: cover;
      overflow-x: hidden;
      color: #333;
    }

    /* Fondo blur detrás de la card para mejor legibilidad */
    .container {
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: flex-start;
      min-height: 100vh;
      padding: 20px;
      box-sizing: border-box;
      backdrop-filter: blur(8px);
      -webkit-backdrop-filter: blur(8px);
    }

    .navbar {
      background-color: rgba(0, 64, 128, 0.85);
      padding: 10px 20px;
      border-radius: 8px;
      margin-bottom: 20px;
      text-align: center;
      width: 100%;
      max-width: 700px;
      box-shadow: 0 0 15px rgba(0,0,0,0.3);
    }

    .nav-list {
      list-style: none;
      margin: 0; padding: 0;
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
      font-weight: 700;
      font-size: 16px;
      transition: color 0.3s;
    }

    .nav-list li a:hover {
      color: #ffcc00;
    }

    .card {
      background-color: rgba(255, 255, 255, 0.9);
      padding: 30px;
      border-radius: 12px;
      box-shadow: 0 8px 32px rgba(0, 0, 0, 0.15);
      max-width: 600px;
      width: 100%;
      overflow-y: auto;
      max-height: 80vh;
      position: relative;
    }

    h2 {
      text-align: center;
      margin-bottom: 20px;
      font-weight: 700;
      color: #003366;
    }

    ul {
      list-style: none;
      padding: 0;
      margin: 0;
    }

    li {
      margin-bottom: 10px;
      font-size: 16px;
      color: #333;
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

    /* Botón para mostrar/ocultar */
    #toggleBtn {
      background-color: #004080;
      color: white;
      border: none;
      padding: 10px 20px;
      margin-bottom: 15px;
      font-size: 16px;
      border-radius: 6px;
      cursor: pointer;
      transition: background-color 0.3s;
      display: block;
      margin-left: auto;
      margin-right: auto;
    }

    #toggleBtn:hover {
      background-color: #002f5b;
    }

    /* Contenido oculto al inicio */
    #contenido {
      display: none;
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
      body {
        background-attachment: scroll;
      }
    }
  </style>
</head>
<body>
  <div class="container">
    <nav class="navbar">
      <ul class="nav-list">
        <li><a href="#" onclick="mostrarSeccion('resultados'); return false;">Resultados del Delegado</a></li>
        <li><a href="#" onclick="mostrarSeccion('ficha'); return false;">Ficha Médica</a></li>
      </ul>
    </nav>

    <div class="card">
      <h2 id="titulo">Resultados del Delegado</h2>
      <button id="toggleBtn">Mostrar Resultado</button>
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

    let seccionActual = 'resultados';

    function mostrarSeccion(seccion) {
      seccionActual = seccion;
      const titulo = document.getElementById('titulo');
      const contenido = document.getElementById('contenido');
      const toggleBtn = document.getElementById('toggleBtn');

      toggleBtn.textContent = 'Mostrar Resultado';
      contenido.style.display = 'none';
      titulo.textContent = seccion === 'resultados' ? "Resultados del Delegado" : "Ficha Médica";
    }

    async function cargarDatos(url, id, tipo) {
      try {
        const response = await fetch(url);
        const texto = await response.text();

        // Manejar CSV con posibles comillas en campos
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

    // Control para mostrar/ocultar con botón
    document.getElementById('toggleBtn').addEventListener('click', () => {
      const contenido = document.getElementById('contenido');
      const toggleBtn = document.getElementById('toggleBtn');
      const id = getDelegadoID();

      if (!id) {
        contenido.innerHTML = "<p><strong>ID de delegado no proporcionado en la URL.</strong></p>";
        contenido.style.display = 'block';
        toggleBtn.textContent = 'Ocultar Resultado';
        return;
      }

      if (contenido.style.display === 'none') {
        // Cargar datos y mostrar
        if (seccionActual === 'resultados') {
          cargarDatos(urlResultados, id, 'resultados');
        } else {
          cargarDatos(urlFichaMedica, id, 'ficha');
        }
        contenido.style.display = 'block';
        toggleBtn.textContent = 'Ocultar Resultado';
      } else {
        contenido.style.display = 'none';
        toggleBtn.textContent = 'Mostrar Resultado';
      }
    });

    // Inicialización al cargar página
    window.onload = () => {
      mostrarSeccion('resultados');
    };
  </script>
</body>
</html>
