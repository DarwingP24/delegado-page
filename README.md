<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8" />
  <title>Puntaje del Delegado</title>
  <style>
    body { font-family: Arial, sans-serif; padding: 30px; background: #f0f2f5; }
    .card { background: #fff; padding: 20px; border-radius: 8px; box-shadow: 0 2px 10px rgba(0,0,0,0.1); max-width: 600px; margin: auto; }
    h1, h2, h3 { text-align: center; }
    ul { list-style: none; padding: 0; }
    li { padding: 5px 0; }
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

        const filas = texto.trim().split('\n').map(fila => fila.split(','));

        const categorias = filas[0]; // encabezados reales
        const datosDelegados = filas.slice(1); // resto de los datos
        const delegado = datosDelegados.find(fila => fila[0].trim() === id);

        const contenedor = document.getElementById('contenido');

        if (!delegado) {
          contenedor.innerHTML = "<p><strong>Delegado no encontrado.</strong></p>";
          return;
        }

        const nombre = delegado[2];      // columna C
        const delegacion = delegado[1]; // columna B

        let html = `<h2>${nombre} (${id})</h2>`;
        html += `<h3>Delegación: ${delegacion}</h3>`;
        html += `<ul>`;

        // desde columna 3 en adelante (índice 3)
        for (let i = 3; i < categorias.length; i++) {
          const categoria = categorias[i] || `Categoría ${i + 1}`;
          const puntuacion = delegado[i] || '0';
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
  <div class="card">
    <h1>Resultados del Delegado</h1>
    <div id="contenido">Cargando datos...</div>
  </div>
</body>
</html>
