# Control de Stock — Ideas Étnicas

Herramienta de un solo archivo (`index.html`, sin build, sin servidor) para comparar el stock de cada local gastronómico contra lo esperado, a partir de 4 excels de un mismo período:

- **Stock inicial** (foto al comienzo del período).
- **Compras** de insumos del período.
- **Ventas** de artículos del período.
- **Stock final** (foto al cierre del período, la que reportó el local).

Calcula, por artículo:

```
Stock Teórico = Stock Inicial + Compras − Ventas
Diferencia    = Stock Final (reportado) − Stock Teórico
```

Por ahora solo controla artículos **por unidad** (cervezas embotelladas/enlatadas, aguas con/sin gas, aguas saborizadas, gaseosas, vinos, y otros que se marquen manualmente). Los platos de comida y cualquier ítem que dependa de receta quedan afuera hasta que se implemente ese control (fase 2). La cerveza de barril/canilla también queda excluida por defecto — no es "por unidad" (se cuenta por fracción de barril, no por lata/botella), se puede incluir manualmente si hace falta.

## Cómo funciona (calibrado contra Baum de Lomas)

El diseño se ajustó después de mirar los 4 excels reales de Baum de Lomas, que mostraron tres problemas típicos de una pyme sin sistema unificado:

1. **El mismo artículo tiene nombres distintos en cada archivo** (Stock: "AGUA CON GAS", Compras: "AGUA CON GAS", Ventas: "Agua C/Gas"). No hay forma de asumir que van a coincidir como texto exacto.
2. **Solo el Stock tiene código de producto** ("N° de Control", ej. `BEB073`). Compras y Ventas no.
3. **Las unidades no coinciden**: Compras registra en packs/cajas ("2 PACK X 6") pero Stock cuenta unidades sueltas ("12 BOTELLAS").

Por eso la app usa el **Stock Inicial como lista maestra** de artículos controlados (son los que el local ya cuenta por unidad, con su código), y en el paso 3 ("Vincular artículos") vos confirmás manualmente qué nombres de Compras y de Ventas corresponden a cada uno — con sugerencias automáticas por similitud de texto para acelerarlo. Esa vinculación, el mapeo de columnas y los factores de conversión de unidades quedan guardados en el navegador (localStorage), así que de un mes al otro no hay que rehacerlos, solo revisar si aparecieron nombres nuevos.

## Uso

1. **Paso 1**: nombre del local, rango de fechas (filtra Compras/Ventas; Stock inicial/final no se filtran, son una foto puntual), y subir los 4 archivos.
2. **Paso 2**: por archivo, indicar en qué columna está el Código de producto (opcional pero recomendado en Stock inicial/final), el Artículo, la Unidad de medida y la Cantidad (y la Fecha en Compras/Ventas). Si el archivo mezcla varios locales, hay modos alternativos ("una columna indica el Local" / "cada hoja es un local distinto").
3. **Paso 3a**: revisar los factores de conversión de Compras (cuántas unidades de stock representa cada "Unidad de Medida" — se sugiere solo a partir del texto, ej. "PACK X 6" → 6, "CAJA X 12" → 12).
4. **Paso 3b**: por cada artículo del Stock Inicial, confirmar o agregar los nombres con que aparece en Compras y en Ventas (chips sugeridos por similitud, click para aceptar). Los de barril/canilla vienen desmarcados.
5. **Paso 4**: ver la comparación, filtrar por "solo con diferencia", ajustar tolerancia, y exportar a Excel.

## Próximos locales

Cada local puede tener columnas y formato distintos — por eso el mapeo del paso 2 es manual por archivo, no un template fijo. Al abordar un local nuevo: subir sus 4 excels, revisar/ajustar el mapeo automático que sugiere la app, y completar la vinculación de artículos del paso 3b (las sugerencias por similitud ayudan pero no hay que confiar en ellas a ciegas — conviene repasar sobre todo los artículos con diferencias grandes, suele ser porque falta vincular un nombre).

## Dependencias

Usa [SheetJS/xlsx](https://github.com/SheetJS/sheetjs) (Apache-2.0) vendorizado en `vendor/xlsx.full.min.js` para leer/escribir Excel en el navegador — no depende de un CDN externo.
