<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8" />
  <title>Puntaje del Delegado</title>
  <style>
    body { font-family: Arial, sans-serif; padding: 30px; background: #f0f2f5; }
    .card { background: #fff; padding: 20px; border-radius: 8px; box-shadow: 0 2px 10px rgba(0,0,0,0.1); max-width: 500px; margin: auto; }
    h1, h2 { text-align: center; }
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

        // Separar filas y columnas por salto de línea y coma
        const filas = texto.trim().split('\n').map(f => f.split(','));

        // La fila 3 (índice 2) contiene las categorías
        const categorias = filas[2];

        // Los datos empiezan en la fila 4 (índice 3)
        // Buscamos la fila cuyo ID en columna 0 sea igual al id buscado
        const delegado = filas.slice(3).find(f => f[0].trim() === id);

        const contenedor = document.getElementById('contenido');
        if (!delegado) {
          contenedor.innerHTML = "<p><strong>Delegado no encontrado.</strong></p>";
          return;
        }

        // Nombre del delegado (columna C, índice 2)
        const nombre = delegado[2];
        // Delegacion (columna B, índice 1)
        const delegacion = delegado[1];

        let html = `<h2>${nombre} (${id})</h2>`;
        html += `<h3>Delegación: ${delegacion}</h3>`;
        html += '<ul>';

        // Las puntuaciones están desde columna D (índice 3) hasta el final de las columnas relevantes
        // Mostramos categoría (categorias[i]) y su puntuación delegado[i]
        for (let i = 3; i < categorias.length; i++) {
          html += `<li><strong>${categorias[i]}:</strong> ${delegado[i]}</li>`;
        }
        html += '</ul>';

        contenedor.innerHTML = html;

      } catch (error) {
        document.getElementById('contenido').innerHTML = `<p>Error al cargar los datos. Intenta más tarde.</p>`;
        console.error(error);
      }
    }

    setInterval(cargarDatos, 60000); // Actualiza cada minuto
    window.onload = cargarDatos;
  </script>
</head>
<body>
  <div class="card">
    <h1>Resultados del Delegado</h1>
    <div id="contenido">Cargando datos...</div>
  </div>
</body>
</html>
