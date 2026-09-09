# Trazabilidad de Cables · Coopelan

App de bodega para **cables**: un solo archivo `index.html`, sin instalación y sin
servidor propio. Se abre en el teléfono, se escanea el carrete y el inventario se
va descontando solo.

Es la hermana de la app de transformadores, pero **independiente**: no comparte
datos con ella (ver *Separación de datos*).

## Qué hace

**Escanear / Despacho** — Se lee la etiqueta del carrete y la app saca de ahí tres
cosas: el **código del cable** (F0152), el **N° de folio** del carrete y los
**metros**. Encuentra el carrete en el inventario, se completa el check list y al
cerrarlo **descuenta los metros** despachados. Sale el correo con el informe y el
PDF adjunto, igual que en la app de transformadores.

También registra **entradas**: cable que vuelve de terreno suma metros al carrete,
y un carrete que recién llega se da de alta desde el mismo escaneo.

**Stock** — Acá se carga todo el inventario, escaneando carrete por carrete o
subiendo un Excel. No hay que elegir bodega: un carrete está o no está, y lo que
importa es cuántos metros le quedan. Se puede exportar a Excel o CSV para
inventarios e informes, y volver a subir ese mismo archivo corregido.

**Historial** — Todos los movimientos con su check list, y un informe en Excel con
tres hojas: movimientos, consumo por código de cable y el detalle de la inspección.

**Base de datos** — Los 50 códigos de la familia F vienen dentro de la app. Si
aparecen códigos nuevos se cargan con un CSV/XLSX de dos columnas
(`CODIGO`, `DESCRIPCION`).

## Cómo lee las etiquetas

No todas las etiquetas vienen iguales, así que se prueban tres formas, en orden:

| Etiqueta | Código | Folio | Metros |
|---|---|---|---|
| `F0152 FOLIO 12345 500 MTS` | F0152 | 12345 | 500 |
| `F0152;12345;500` | F0152 | 12345 | 500 |
| `F0270 CARRETE 8891 1.250,5 M` | F0270 | 8891 | 1250,5 |
| `CABLE F0243 N 777 METROS 320` | F0243 | 777 | 320 |
| `12345` | — | 12345 | — |

Lo que se dedujo por posición (y no por un rótulo) se marca con `?` en pantalla
para que el operario lo confirme antes de que se descuente nada.

Si la etiqueta solo trae el folio, el código y los metros se toman de los dos
campos de arriba de la vista de Stock.

## Reglas del inventario

- **Re-escanear un carrete no le pisa los metros.** Si se pisaran, un segundo pase
  de inventario borraría todo lo despachado. Para corregir el metraje está
  «Ajustar metros», que deja constancia de quién, cuándo y por qué.
- **No se puede despachar más de lo que hay.** El check list no cierra si los
  metros superan el saldo del carrete.
- **Eliminar un movimiento devuelve los metros** al carrete, para que el
  inventario no quede descuadrado justamente por haber corregido un error.

## Separación de datos

Esta app y la de transformadores pueden convivir en el mismo teléfono sin pisarse:

- **En el teléfono:** todas las llaves son `coopelan_cable_*`.
- **En la nube:** el prefijo es `cable_` (la de transformadores usa `trafo_`).
  Los tres sistemas comparten la misma tabla sin verse entre ellos.
- **En el correo:** conviene configurar un script de Google propio para cables,
  así los informes no se mezclan.

## Publicar

Es un archivo suelto: alcanza con subir `index.html` a cualquier hosting estático
(GitHub Pages, por ejemplo) y repartir el link.

## Ajustes

Correos, script de envío, respaldo y borrado están detrás de la misma clave de
administrador que ya usa la app de transformadores.
