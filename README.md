# Alertas de Obras Públicas Paralizadas

Prototipo visual de dashboard para el seguimiento de obras públicas paralizadas en el Perú, inspirado en el estilo del portal de GFP Subnacional.

## Contenido

- `index.html`: dashboard completo en una sola página.
- `assets/`: logos institucionales usados en la cabecera.

## Funcionalidades

- Mapa interactivo con 1,248 obras públicas cargadas desde `BD_ANEXO_OP-MAYO26.xlsx`, distribuidas en las 25 jurisdicciones del Perú.
- Filtros por departamento, nivel de gobierno, sector, causa principal y costo de inversión.
- Indicadores clave que se actualizan según los filtros activos.
- Causales de paralización dinámicas según la selección.
- Popups con datos generales al seleccionar una obra en el mapa.
- Mapas base satelital, topográfico y calles.
- Límites departamentales solo para visualización, no seleccionables.
- Ranking de priorización por mayor costo actualizado de inversión.

## Cómo verlo localmente

Abrir `index.html` en el navegador.

## Cómo publicarlo en GitHub Pages

1. Crear un repositorio en GitHub.
2. Subir todos los archivos de esta carpeta.
3. Ir a `Settings > Pages`.
4. En `Build and deployment`, elegir `Deploy from a branch`.
5. Seleccionar la rama `main` y la carpeta `/root`.
6. Guardar. GitHub generará una URL pública para el dashboard.

## Nota

Los datos de obras provienen del archivo `BD_ANEXO_OP-MAYO26.xlsx`. La ubicación en el mapa se muestra de forma referencial cuando la base no incluye coordenadas explícitas.
