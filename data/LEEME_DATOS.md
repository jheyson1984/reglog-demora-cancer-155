# Datos

La base no se publica en este repositorio.

## Cómo obtenerla

1. Ingrese a https://portalsivigila.ins.gov.co/Paginas/Buscador.aspx (Instituto Nacional de Salud, búsqueda de microdatos).
2. Seleccione el evento "Cáncer de la mama y cuello uterino" y el año 2025, y diligencie el formulario de registro del INS.
3. Guarde el archivo descargado con el nombre `Datos_2025_155.xlsx`.
4. Súbalo en la primera celda del cuaderno `notebooks/RegLog_demora_cancer_155.ipynb`.

## Descripción

Base nominal depurada, sin datos de identificación personal: 18.717 registros y 69 columnas.

Variables usadas: `INI_SIN`, `FEC_CON`, `Departamento_residencia`, `EDAD`, `TIP_SS`, `estrato` y `AREA`.
