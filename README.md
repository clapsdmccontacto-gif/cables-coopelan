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

El código de barras de la cooperativa trae los tres datos **en este orden:
código, metraje y folio**. Así, `F0157 1000 1053` son 1000 metros del carrete
1053. Cuando los datos vienen rotulados (`N°1053`, `1000 MTS`) manda el rótulo,
no la posición:

| Etiqueta | Código | Folio | Metros |
|---|---|---|---|
| `F0157 1000 1053` | F0157 | 1053 | 1000 |
| `F0157 N°1053 1000 MTS` | F0157 | 1053 | 1000 |
| `F0157;1000;1053` | F0157 | 1053 | 1000 |
| `F0270 CARRETE 8891 1.250,5 M` | F0270 | 8891 | 1250,5 |
| `F0243 FOLIO 777 METROS 320` | F0243 | 777 | 320 |
| `1053` | — | 1053 | — |

Un número suelto se toma como folio: casi siempre es alguien buscando un
carrete, no declarando un metraje.

Lo que se dedujo por posición (y no por un rótulo) se marca con `?` en pantalla
para que el operario lo confirme antes de que se descuente nada. Y si con ese
folio no aparece ningún carrete pero al revés sí, la app corrige sola: una
etiqueta impresa en el otro orden encuentra igual su carrete.

Si la etiqueta solo trae el folio, el código y los metros se toman de los dos
campos de arriba de la vista de Stock.

## Reglas del inventario

- **Re-escanear un carrete no le pisa los metros.** Si se pisaran, un segundo pase
  de inventario borraría todo lo despachado. Para corregir el metraje está
  «Ajustar metros», que deja constancia de quién, cuándo y por qué.
- **No se puede despachar más de lo que hay.** El check list no cierra si los
  metros superan el saldo del carrete. Y como la etiqueta trae el metraje
  *original* del carrete (los 1000 MTS impresos), en un despacho la app propone
  como mucho lo que queda: si el carrete va por 750, propone 750 y no 1000.
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
