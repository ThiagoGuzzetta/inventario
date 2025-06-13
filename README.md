<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Inventario</title>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.17.0/xlsx.full.min.js"></script>
  <style>
    body { font-family: Arial, sans-serif; padding: 20px; }
    table, th, td { border: 1px solid black; border-collapse: collapse; padding: 8px; }
    table { width: 100%; margin-top: 20px; }
    input, button { margin: 5px; }
  </style>
</head>
<body>
  <h1>Inventario</h1>


  <input type="file" id="fileInput" accept=".xlsx" />


  <div>
    <input type="text" id="itemName" placeholder="Nombre del producto" />
    <input type="number" id="itemQuantity" placeholder="Cantidad" />
    <button onclick="agregar()">Agregar</button>
    <button onclick="sumar()">Sumar</button>
    <button onclick="restar()">Restar</button>
  </div>


  <table id="inventoryTable">
    <thead>
      <tr>
        <th>Producto</th>
        <th>Cantidad</th>
      </tr>
    </thead>
    <tbody></tbody>
  </table>


  <script>
    let inventario = {};


    document.getElementById('fileInput').addEventListener('change', function(e) {
      const reader = new FileReader();
reader.onload = function(e) {
  const data = new Uint8Array(e.target.result);
  const workbook = XLSX.read(data, { type: 'array' });
  const sheetName = workbook.SheetNames[0];
  const sheet = workbook.Sheets[sheetName];
  const json = XLSX.utils.sheet_to_json(sheet);

  inventario = {};
  json.forEach(row => {
    const nombre = normalizarNombre(row.Producto); // Normalización
    inventario[nombre] = parseInt(row.Cantidad) || 0;
  });

  mostrarInventario();
  guardarInventario(); // 🔥 Esto guarda en localStorage automáticamente
};
      reader.readAsArrayBuffer(e.target.files[0]);
    });


    function mostrarInventario() {
      const tbody = document.querySelector('#inventoryTable tbody');
      tbody.innerHTML = '';
      for (const [producto, cantidad] of Object.entries(inventario)) {
        const row = `<tr><td>${producto}</td><td>${cantidad}</td></tr>`;
        tbody.innerHTML += row;
      }
    }


     function normalizarNombre(nombre) {
    return nombre.trim().toLowerCase();
  }

  function agregar() {
    const nombreOriginal = document.getElementById('itemName').value;
    const cantidad = parseInt(document.getElementById('itemQuantity').value);
    if (!nombreOriginal || isNaN(cantidad)) return;

    const nombre = normalizarNombre(nombreOriginal);
    inventario[nombre] = cantidad; // reemplaza cualquier cantidad previa
    mostrarInventario();
    guardarInventario();
  }

  function sumar() {
    const nombreOriginal = document.getElementById('itemName').value;
    const cantidad = parseInt(document.getElementById('itemQuantity').value);
    if (!nombreOriginal || isNaN(cantidad)) return;

    const nombre = normalizarNombre(nombreOriginal);
    if (!inventario[nombre]) inventario[nombre] = 0;
    inventario[nombre] += cantidad;
    mostrarInventario();
    guardarInventario();
  }

  function restar() {
    const nombreOriginal = document.getElementById('itemName').value;
    const cantidad = parseInt(document.getElementById('itemQuantity').value);
    if (!nombreOriginal || isNaN(cantidad)) return;

    const nombre = normalizarNombre(nombreOriginal);
    if (!inventario[nombre]) inventario[nombre] = 0;
    inventario[nombre] -= cantidad;
    if (inventario[nombre] < 0) inventario[nombre] = 0;
    mostrarInventario();
    guardarInventario();
  }
    // Guardar en LocalStorage
function guardarInventario() {
  localStorage.setItem('inventario', JSON.stringify(inventario));
}


// Cargar desde LocalStorage
function cargarInventario() {
  const data = localStorage.getItem('inventario');
  if (data) {
    inventario = JSON.parse(data);
    mostrarInventario();
  }
}


// Llama a cargar al inicio
cargarInventario();
  </script>
</body>
</html>
