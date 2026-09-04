# Control de Stock — Ideas Étnicas

Herramienta de un solo archivo (`index.html`, sin build, sin servidor) para comparar el stock de cada local gastronómico contra lo esperado, a partir de 4 excels de un mismo período:

- **Stock inicial** (foto al comienzo del período).
- **Compras** de insumos del período.
- **Ventas** de artículos del período.
- **Stock final** (foto al cierre del período, la que reportó el local).

Calcula, por artículo:

```
Stock Esperado = Stock Inicial + Compras − Ventas
Diferencia     = Stock Final (reportado) − Stock Esperado
```

Por ahora solo controla artículos **por unidad** (cervezas embotelladas/enlatadas, aguas con/sin gas, aguas saborizadas, gaseosas, vinos, y otros que se marquen manualmente). Los platos de comida y cualquier ítem que dependa de receta quedan afuera hasta que se implemente ese control (fase 2). La cerveza de barril/canilla también queda excluida por defecto — no es "por unidad" (se cuenta por fracción de barril, no por lata/botella).

## Uso

1. Completá el nombre del local y el rango de fechas, y subí los 4 archivos (podés arrastrarlos).
2. Apretá **Calcular stock**.
3. Si hay nombres de artículo que no coinciden exacto entre archivos (típico: "AGUA CON GAS" en Stock vs "Agua C/Gas" en Ventas) y la app no está segura, te va a preguntar solo esos casos puntuales — un clic por cada uno, o "Ninguno / no corresponde" si no aplica. La mayoría de los artículos no necesita esto.
4. Vas a ver el resultado: cuántos artículos cierran OK, cuántos faltan, cuántos sobran, y el detalle de cada uno. Se puede filtrar, ajustar la tolerancia (para redondeos) y exportar a Excel.

Lo que confirmás en el paso 3 queda guardado en el navegador — la próxima vez que subas los excels del mismo local casi no va a preguntar nada nuevo, solo lo que cambió. Si algo quedó mal vinculado, en la tabla de resultados cada fila tiene un ✎ para corregirlo ahí mismo, sin tener que rehacer todo.

## Por qué existe el paso de "confirmanos esto"

Se probó contra los 4 excels reales de Baum de Lomas, que mostraron problemas típicos de una pyme sin sistema unificado:

1. **El mismo artículo tiene nombres distintos en cada archivo** (Stock: "AGUA CON GAS", Ventas: "Agua C/Gas"). No se puede asumir texto exacto igual.
2. **Solo el Stock tiene código de producto** ("N° de Control", ej. `BEB073`) — se usa como ancla cuando está disponible.
3. **Las unidades no coinciden**: Compras registra en packs/cajas ("2 PACK X 6") pero Stock cuenta unidades sueltas ("12 BOTELLAS") — la app convierte sola, adivinando el tamaño del pack a partir del texto.

La app intenta resolver todo esto sola (nombres parecidos, abreviaturas tipo "c/gas" → "con gas", diferencias de mayúsculas, packs vs. unidades) y solo te pregunta cuando dos productos son parecidos pero no lo suficiente como para adivinar con seguridad — por ejemplo, no asume que "Coca Cola" sin más aclaración es la de 600ml o la de 1,5L, eso te lo pregunta.

## Ajustes avanzados (para cuando algo no cierra)

Al final de la tarjeta de carga hay un desplegable "Ajustes avanzados" con el mapeo de columnas de cada archivo y la tabla de conversión de unidades. Normalmente no hace falta tocarlo — la app detecta las columnas sola — pero si un local tiene un formato muy distinto (otros nombres de columna, otra fila de encabezado) y el resultado no cierra por eso, ahí se puede corregir a mano.

## Próximos locales

Cada local puede tener columnas y formato distintos. Al abordar uno nuevo: subir sus 4 excels y calcular — si la detección automática de columnas falla (columnas con nombres muy distintos a "Insumo"/"Artículo"/"Cantidad"/etc.), la app lo avisa y abre "Ajustes avanzados" para mapearlas a mano.

## Dependencias

Usa [SheetJS/xlsx](https://github.com/SheetJS/sheetjs) (Apache-2.0) vendorizado en `vendor/xlsx.full.min.js` para leer/escribir Excel en el navegador — no depende de un CDN externo.
