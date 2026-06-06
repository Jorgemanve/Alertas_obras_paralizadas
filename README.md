# Alertas de Obras Publicas Paralizadas

Prototipo visual de dashboard para el seguimiento de obras publicas paralizadas en el Peru, inspirado en el estilo del portal de GFP Subnacional.

## Contenido

- `index.html`: dashboard completo en una sola pagina.
- `assets/`: logos institucionales usados en la cabecera.

## Funcionalidades

- Mapa interactivo con 700 obras de ejemplo distribuidas en las 25 jurisdicciones del Peru.
- Filtros por departamento, nivel de gobierno, sector, causa principal y costo de inversion.
- Indicadores clave que se actualizan segun los filtros activos.
- Causales de paralizacion dinamicas segun la seleccion.
- Popups con datos generales al seleccionar una obra en el mapa.
- Mapas base satelital, topografico y calles.
- Limites departamentales solo para visualizacion, no seleccionables.
- Ranking de priorizacion por mayor costo actualizado de inversion.

## Como verlo localmente

Abrir `index.html` en el navegador.

## Como publicarlo en GitHub Pages

1. Crear un repositorio en GitHub.
2. Subir todos los archivos de esta carpeta.
3. Ir a `Settings > Pages`.
4. En `Build and deployment`, elegir `Deploy from a branch`.
5. Seleccionar la rama `main` y la carpeta `/root`.
6. Guardar. GitHub generara una URL publica para el dashboard.

## Nota

Los datos incluidos son referenciales y sirven para visualizar la maqueta. La version final puede conectarse a fuentes oficiales o bases internas.
