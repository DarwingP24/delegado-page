<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1" />
  <title>Delegado - Resultados y Ficha Médica</title>
  <style>
    /* Reset básico */
    *, *::before, *::after {
      box-sizing: border-box;
    }

    html, body {
      margin: 0; padding: 0; height: 100%;
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
      background: url('https://i.postimg.cc/9MzpyNPL/Chat-GPT-Image-27-may-2025-21-29-56.png') no-repeat center center fixed;
      background-size: cover;
      color: #333;
      -webkit-font-smoothing: antialiased;
      -moz-osx-font-smoothing: grayscale;
    }

    /* Fondo blur detrás */
    .container {
      min-height: 100vh;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: flex-start;
      padding: 1rem;
      background-color: rgba(255,255,255,0.85);
      max-width: 900px;
      margin: 0 auto;
      border-radius: 12px;
      box-shadow: 0 0 25px rgba(0,0,0,0.15);
      backdrop-filter: blur(8px);
      -webkit-backdrop-filter: blur(8px);
    }

    /* Navbar */
    .navbar {
      width: 100%;
      margin-bottom: 1.5rem;
      text-align: center;
    }

    .nav-list {
      list-style: none;
      padding: 0;
      margin: 0;
      display: flex;
      justify-content: center;
      gap: 2rem;
      flex-wrap: wrap;
      font-weight: 600;
    }

    .nav-list li a {
      color: #004080;
      text-decoration: none;
      font-size: 1.1rem;
      padding: 0.3rem 0.5rem;
      border-radius: 6px;
      transition: background-color 0.3s ease, color 0.3s ease;
      cursor: pointer;
      user-select: none;
      display: inline-block;
    }

    .nav-list li a:hover,
    .nav-list li a.active {
      background-color: #004080;
      color: #fff;
    }

    /* Card */
    .card {
      width: 100%;
      max-width: 700px;
      background: white;
      padding: 2rem;
      border-radius: 12px;
      box-shadow: 0 8px 32px rgba(0,0,0,0.12);
      overflow-y: auto;
      max-height: 75vh;
    }

    h2 {
      margin-top: 0;
      margin-bottom: 1rem;
      text-align: center;
      font-weight: 700;
      color: #003366;
      font-size: 1.8rem;
    }

    ul {
      padding: 0;
      margin: 0;
      list-style: none;
    }

    li {
      font-size: 1.05rem;
      padding: 0.4rem 0;
      border-bottom: 1px solid #eee;
      color: #222;
    }

    .verde {
      color: green;
      font-weight: 700;
    }

    .rojo {
      color: red;
      font-weight: 700;
    }

    .negrita {
      font-weight: 700;
    }

    /* Botón mostrar/ocultar */
    #toggleBtn {
      background-color: #004080;
      color: white;
      border: none;
      padding: 0.75rem 1.5rem;
      font-size: 1.1rem;
      border-radius: 8px;
      cursor: pointer;
      margin: 0 auto 1.5rem auto;
      display: block;
      transition: background-color 0.3s ease;
      user-select: none;
    }

    #toggleBtn:hover {
      background-color: #002f5b;
    }

    /* Oculto inicialmente */
    #contenido {
      display: none;
    }

    /* Responsive */
    @media (max-width: 768px) {
      .container {
        max-width: 95vw;
        padding: 1rem 1.5rem;
      }
      .card {
        max-height: none;
        padding: 1.5rem;
      }
      h2 {
        font-size: 1.5rem;
      }
      .nav-list {
        gap: 1.2rem;
      }
      .nav-list li a {
        font-size: 1rem;
      }
      #toggleBtn {
        width: 100%;
      }
    }

    @media (max-width: 400px) {
      li {
        font-size: 0.95rem;
      }
    }
  </style>
</head>
<body>
  <div class="container">
    <nav class="navbar">
      <ul class="nav-list" id="navList">
        <li><a href="#" class="active" onclick="mostrarSeccion('resultados'); return false;">Resultados del Delegado</a></li>
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

    function activarNav(seccion) {
      const links = document.querySelectorAll('.nav-list a');
      links.forEach(link => {
        if (link.textContent.toLowerCase().includes(seccion)) {
          link.classList.add('active');
        } else {
          link.classList.remove('active');
        }
      });
    }

    function mostrarSeccion(seccion) {
      seccionActual = seccion;
      activarNav(seccion);
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
            const valor = delegado[i]?.trim() || '-';
            html += `<li><strong>${campo}:</strong> ${valor}</li>`;
          }
        }

        html += "</ul>";
        contenedor.innerHTML = html;
      } catch (error) {
        document.getElementById('contenido').innerHTML = `<p>Error al cargar datos: ${error.message}</p>`;
      }
    }

    document.getElementById('toggleBtn').addEventListener('click', () => {
      const contenido = document.getElementById('contenido');
      if (contenido.style.display === 'none' || contenido.style.display === '') {
        contenido.style.display = 'block';
        document.getElementById('toggleBtn').textContent = 'Ocultar Resultado';
        const id = getDelegadoID();
        if (!id) {
          contenido.innerHTML = '<p>Falta parámetro <strong>id</strong> en la URL.</p>';
          return;
        }
        if (seccionActual === 'resultados') {
          cargarDatos(urlResultados, id, 'resultados');
        } else {
          cargarDatos(urlFichaMedica, id, 'ficha');
        }
      } else {
        contenido.style.display = 'none';
        document.getElementById('toggleBtn').textContent = 'Mostrar Resultado';
      }
    });

    // Inicializar estado
    mostrarSeccion('resultados');
  </script>
</body>
</html>
