<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CONTROL DE NICHOS DEL CEMENTERIO GENERAL DE HUERTAS</title>
    <style>
        body { font-family: 'Georgia', serif; text-align: center; background-color: #f0fff0; color: #333; margin: 0; padding: 0; }
        .pabellon { margin: 20px auto; padding: 20px; border: 3px solid #228B22; width: 90%; max-width: 800px; background-color: #98FB98; border-radius: 12px; box-shadow: 5px 5px 15px rgba(0, 0, 0, 0.2); }
        h1 { color: #006400; font-size: 28px; margin-top: 20px; text-transform: uppercase; }
        h2 { background-color: #2E8B57; color: white; padding: 12px; border-radius: 8px; font-size: 22px; }
        table { width: 100%; border-collapse: collapse; margin: 20px 0; background-color: white; }
        th, td { border: 1px solid #bbb; padding: 12px; text-align: center; font-size: 16px; }
        th { background-color: #228B22; color: white; }
        .nicho { cursor: pointer; background-color: #ADFF2F; font-weight: bold; transition: background 0.3s ease, transform 0.2s ease; border-radius: 6px; position: relative; font-family: 'Arial', sans-serif; }
        .nicho:hover { background-color: #32CD32; transform: scale(1.05); }
        .ocupado { background-color: #006400 !important; color: white; font-weight: bold; border-radius: 6px; }
        .precio { background-color: #556B2F !important; color: white !important; font-weight: bold; border-radius: 6px; }
        .btn-eliminar { position: absolute; top: 3px; right: 3px; background: #8B0000; color: white; border: none; padding: 6px; font-size: 12px; cursor: pointer; border-radius: 5px; display: none; }
        .ocupado .btn-eliminar { display: block; }

        @media (max-width: 600px) {
            h1 { font-size: 24px; }
            h2 { font-size: 18px; }
            th, td { padding: 8px; font-size: 14px; }
            .pabellon { width: 95%; padding: 15px; }
        }
    </style>
</head>
<body>
    <h1>CONTROL DE NICHOS DEL CEMENTERIO GENERAL DE HUERTAS</h1>
    <div id="cementerio"></div>
    <script>
        const pabellonesNombres = ["Pabellón Entrada", "Pabellón Pasadizo", "Pabellón Mausoleos", "Pabellón Perfil al cerro Uchpas"];
        const filas = ['1', '2', '3', '4', '5', '6', '7', '8', '9', '  '];
        const filas1 = ['1', '2', '3', '4', '5', ' '];
        const columnas = ['E', 'D', 'C', 'B', 'A'];
        const precios = { 'A': 1850, 'E': 1850, 'B': 2000, 'D': 2000, 'C': 2400 };
        const cementerioDiv = document.getElementById("cementerio");

        function guardarDatos() {
            localStorage.setItem("nichos", JSON.stringify(nichos));
        }

        function cargarDatos() {
            return JSON.parse(localStorage.getItem("nichos")) || {};
        }

        let nichos = cargarDatos();

        for (let p = 0; p < pabellonesNombres.length; p++) {
            let pabellonDiv = document.createElement("div");
            pabellonDiv.className = "pabellon";
            pabellonDiv.innerHTML = `<h2>${pabellonesNombres[p]}</h2>`;

            let columnasUsadas = columnas;
            let filasUsadas = (p < 2) ? filas : filas1;

            let table = document.createElement("table");
            let thead = document.createElement("thead");
            let headerRow = document.createElement("tr");
            headerRow.innerHTML = `<th>X</th>` + filasUsadas.map(fila => `<th>${fila}</th>`).join("");
            thead.appendChild(headerRow);
            table.appendChild(thead);

            let tbody = document.createElement("tbody");
            columnasUsadas.forEach(col => {
                let row = document.createElement("tr");
                row.innerHTML = `<td><b>${col}</b></td>`;
                filasUsadas.forEach(fila => {
                    let cell = document.createElement("td");
                    cell.className = "nicho";
                    cell.style.position = "relative";
                    let key = `${p}-${col}-${fila}`;

                    if (fila === '10' || (fila === '6' && p >= 2)) {
                        cell.innerText = `S/ ${precios[col]}`;
                        cell.classList.add("precio");
                    } else {
                        if (nichos[key]) {
                            cell.innerText = nichos[key];
                            cell.classList.add("ocupado");
                            agregarBotonEliminar(cell, key);
                        }

                        cell.onclick = function() {
                            let nombre = prompt("Ingrese el nombre del difunto:");
                            if (nombre) {
                                cell.innerText = nombre;
                                cell.classList.add("ocupado");
                                nichos[key] = nombre;
                                guardarDatos();
                                agregarBotonEliminar(cell, key);
                            }
                        };
                    }
                    row.appendChild(cell);
                });
                tbody.appendChild(row);
            });
            table.appendChild(tbody);
            pabellonDiv.appendChild(table);
            cementerioDiv.appendChild(pabellonDiv);
        }

        function agregarBotonEliminar(cell, key) {
            let btn = document.createElement("button");
            btn.className = "btn-eliminar";
            btn.innerText = "X";
            btn.onclick = function(event) {
                event.stopPropagation();
                if (confirm("¿Desea eliminar el nombre de este nicho?")) {
                    cell.innerText = " ";
                    cell.classList.remove("ocupado");
                    delete nichos[key];
                    guardarDatos();
                }
            };
            cell.appendChild(btn);
        }
    </script>
</body>
</html>
