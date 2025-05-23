<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
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
    }

    /* Contenedor principal */
    .container {
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      min-height: 100vh;
      padding: 20px;
    }

    /* Menú de navegación */
    .navbar {
      background-color: rgba(0, 64, 128, 0.8);
      padding: 10px 20px;
      border-radius: 5px;
      margin-bottom: 20px;
      width: 100%;
      max-width: 600px;
      text-align: center;
    }

    .nav-list {
      list-style: none;
      margin: 0;
      padding: 0;
    }

    .nav-list li {
      display: inline;
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

    /* Tarjeta de contenido */
    .card {
      background-color: rgba(255, 255, 255, 0.9);
      padding: 30px;
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

    .rojo {
      color: red;
      font-weight: bold;
    }

    /* Responsividad */
    @media (max-width: 600px) {
      body, html {
        background-attachment: scroll;
      }

      .card {
        padding: 20px;
      }

      .nav-list li {
        display: block;
        margin: 5px 0;
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
    // Función para obtener el ID del delegado desde la URL
    function getDelegadoID() {
      const params = new URLSearchParams(window.location.search);
      return params.get("id");
    }

    // URLs de las hojas de cálculo publicadas como CSV
    const urlResultados = "https://docs.google.com/spreadsheets/d/e/2PACX-1vT6fLIskyoJRS2F82f4Sb1oaxpvr2oro_-nyWKy3fDEN6VEtKY0mdrH9Pd5qyGLRpQF5GDVTgHVxCBT/pub?gid=0&single=true&output=csv";
    const urlFichaMedica = "https://docs.google.com/spreadsheets/d/e/2PACX-1vT6fLIskyoJRS2F82f4Sb1oaxpvr2oro_-nyWKy3fDEN6VEtKY0mdrH9Pd5qyGLRpQF5GDVTgHVxCBT/pub?gid=2052349342&single=true&output=csv";

    // Función para mostrar la sección seleccionada
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

    // Función para cargar los datos desde Google Sheets
    async function cargarDatos(url, id, tipo) {
      try {
        const response = await fetch(url);
        const texto = await response.text();

        const filas = texto.trim().split('\n').map(fila => fila.split(/,(?=(?:(?:[^"]*"){2})*[^"]*$)/));
        const encabezados = filas[2]; // Fila 3 (índice 2) contiene los encabezados
        const datos = filas.slice(3); // Datos a partir de la fila 4

        const delegado = datos.find(fila => fila[0].trim() === id);
        const contenedor = document.getElementById('contenido');

        if (!delegado) {
          contenedor.innerHTML = "<p><strong>Delegado no encontrado.</strong></p>";
          return;
        }

        let html = "<ul>";

        if (tipo === 'resultados') {
          const delegacion = delegado[1]; // Columna B
          const nombre = delegado[2];     // Columna C
          const checkin = delegado[3];    // Columna D

          html += `<li><strong>Nombre:</strong> ${nombre}</li>`;
          html += `<li><strong>Delegación:</strong> ${delegacion}</li>`;

          // Estado de Check-In
          if (checkin.toUpperCase() === "REGISTRADO") {
            html += `<li><strong>Check-In:</strong> <span class="rojo">REGISTRADO</span></li>`;
          } else {
            html += `<li><strong>Check-In:</strong> <span class="rojo">NO CHECK-IN</span></li>`;
          }

          // Mostrar categorías y puntuaciones desde la columna E (índice 4)
          for (let i = 4; i < encabezados.length; i++) {
            const categoria = encabezados[i]?.trim() || `Categoría ${i + 1}`;
            const puntuacion = delegado[i]?.trim() || '0';
            html += `<li><strong>${categoria}:</strong> ${puntuacion}</li>`;
          }
        } else if (tipo === 'ficha') {
          // Mostrar todos los campos de la ficha médica
          for (let i = 1; i < encabezados.length; i++) {
            const campo = encabezados[i]?.trim() || `Campo ${i + 1}`;
            const valor = delegado[i]?.trim() || 'N/A';
            html += `<li><strong>${campo}:</strong> <span class="rojo">${valor}</span></li>`;
          }
        }

        html += "</ul>";
        contenedor.innerHTML = html;

      } catch (error) {
        console.error(error);
        document.getElementById('contenido').innerHTML = "<p><strong>Error al cargar los datos.</strong></p>";
      }
    }

    // Cargar la sección de resultados por defecto al cargar la página
    window.onload = () => {
      mostrarSeccion('resultados');
    };
  </script>
</body>
</html>

  </script>
</body>
</html>

