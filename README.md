<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no" />
  <title>Delegado - Resultados y Ficha Médica</title>
  <style>
    /* Estilos generales */
    body, html {
      margin: 0;
      padding: 0;
      height: 100%;
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
      background: url('https://i.postimg.cc/YCxg0JvK/Dise-o-sin-t-tulo-2.png') no-repeat center center fixed;
      background-size: cover;
      position: relative;
    }

    /* Eliminar difuminado */
    body::before {
      content: none;
    }

    /* Contenedor principal */
    .container {
      position: relative;
      z-index: 1;
      display: flex;
      flex-direction: column;
      align-items: center;
      padding: 20px;
      min-height: 100vh;
    }

    /* Menú de navegación */
    .navbar {
      width: 100%;
      background-color: #004080;
      padding: 10px 20px;
      display: flex;
      justify-content: center;
      align-items: center;
      border-radius: 5px;
      margin-bottom: 20px;
      flex-wrap: wrap;
    }

    .nav-list {
      list-style: none;
      display: flex;
      flex-wrap: wrap;
      justify-content: center;
      margin: 0;
      padding: 0;
      width: 100%;
      max-width: 600px;
    }

    .nav-list li {
      margin: 5px 15px;
    }

    .nav-list li a {
      color: #ffffff;
      text-decoration: none;
      font-weight: bold;
      transition: color 0.3s;
      white-space: nowrap;
    }

    .nav-list li a:hover {
      color: #ffcc00;
    }

    /* Tarjeta de contenido */
    .card {
      background-color: rgba(255, 255, 255, 0.95);
      padding: 30px;
      border-radius: 10px;
      box-shadow: 0 4px 15px rgba(0, 0, 0, 0.2);
      max-width: 600px;
      width: 100%;
      box-sizing: border-box;
    }

    h2 {
      text-align: center;
      margin-bottom: 20px;
      color: #333;
    }

    h2.ficha-titulo {
      color: red;
    }

    ul {
      list-style: none;
      padding: 0;
      text-align: center;
    }

    li {
      margin-bottom: 10px;
      font-size: 16px;
      color: #555;
      word-wrap: break-word;
    }

    .registrado {
      color: green;
      font-weight: bold;
    }

    .no-checkin {
      color: red;
      font-weight: bold;
    }

    /* Responsividad */
    @media (max-width: 600px) {
      .card {
        padding: 20px;
      }

      .nav-list li {
        margin: 5px 10px;
      }
    }
  </style>
</head>
<body>
  <div class="container">
    <!-- Menú de navegación -->
    <nav class="navbar">
      <ul class="nav-list">
        <li><a href="#" onclick="mostrarSeccion('resultados')">Resultados del Delegado</a></li>
        <li><a href="#" onclick="mostrarSeccion('ficha')">Ficha Médica</a></li>
      </ul>
    </nav>

    <!-- Tarjeta de contenido -->
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
        titulo.classList.remove('ficha-titulo');
        cargarDatos(urlResultados, id, 'resultados');
      } else if (seccion === 'ficha') {
        titulo.textContent = "Ficha Médica";
        titulo.classList.add('ficha-titulo');
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

        let html = '<ul>';

        if (tipo === 'resultados') {
          const delegacion = delegado[1];
          const nombre = delegado[2];
          const checkin = delegado[3];

          html += `<li><strong>Nombre:</strong> ${nombre}</li>`;
          html += `<li><strong>Delegación:</strong> ${delegacion}</li>`;

          if (checkin.toUpperCase() === "REGISTRADO") {
            html += `<li><strong>Check-In:</strong> <span class="registrado">REGISTRADO</span></li>`;
          } else {
            html += `<li><strong>Check-In:</strong> <span class="no-checkin">NO CHECK-IN</span></li>`;
          }

          for (let i = 4; i < encabezados.length; i++) {
            const categoria = encabezados[i]?.trim() || `Categoría ${i + 1}`;
            const puntuacion = delegado[i]?.trim() || '0';
            html += `<li><strong>${categoria}:</strong> ${puntuacion}</li>`;
          }
        } else if (tipo === 'ficha') {
          for (let i = 1; i < encabezados.length; i++) {
            const campo = encabezados[i]?.trim() || `Campo ${i + 1}`;
            const valor = delegado[i]?.trim() || 'N/A';
            html += `<li><strong>${campo}:</strong> ${valor}</li>`;
          }
        }

        html += '</ul>';
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

