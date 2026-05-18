# facturadirecta-help-center-screenshots

Repositorio público con las **capturas de pantalla** que aparecen
en los artículos del [Help Center de FacturaDirecta](https://help.facturadirecta.com/).

## Por qué este repo está separado

El contenido editorial de los artículos vive en
[`facturadirecta-help-center`](https://github.com/facturadirecta/facturadirecta-help-center),
que es un repo **privado** (los artículos, metadatos, sync-state
con Intercom y configuración no deben ser públicos).

Este repo sibling, en cambio, es **público**. Aloja solo los
PNGs de las capturas. La razón es técnica: cuando un artículo
nuevo se publica a Intercom, el publisher inyecta una
`<img src="https://raw.githubusercontent.com/.../<hash>.png" />`
y el servidor de Intercom descarga la imagen para subirla a su
propio CDN. Eso solo es posible si la imagen es accesible
públicamente en el momento de la sincronización.

Una vez Intercom procesa la imagen, los artículos publicados
apuntan al **CDN de Intercom** (`intercom-attachments-*.com`),
no a este repo. Este repo solo se consulta durante la **primera
sincronización** de cada PNG nuevo.

## Convenciones

- **Una sola carpeta raíz** con los PNGs. Sin sub-directorios.
- **Nombre del fichero**: `<hash>.png`, donde `<hash>` son 12
  caracteres hexadecimales generados de forma determinística a
  partir del `slug` del artículo y el `hint` de la captura. El
  cálculo lo hace `scripts/docs/screenshots-lib.ts` en
  facturadirecta3.
- **No subir nada que no sea un PNG** con el patrón de nombre
  anterior. Cualquier otro fichero será rechazado por CI (al
  configurarla).
- **Antes de cada commit**, revisar que la imagen no contiene
  datos sensibles de clientes ni montos reales. Las capturas
  deben hacerse desde un entorno de pruebas o con datos
  ficticios.

## Cómo se añade una captura nueva

El flujo está documentado en
[`.claude/skills/front-docs/SKILL.md`](https://github.com/facturadirecta/facturadirecta3/blob/main/.claude/skills/front-docs/SKILL.md)
del repo principal. Resumen:

1. Insertar el marcador `<!-- screenshot:<hash> hint="..." -->`
   en el `.md` editorial de facturadirecta3.
2. `yarn docs:list-pending-screenshots` muestra el hash que
   queda pendiente.
3. Tomar la captura y guardarla como `<hash>.png` en este repo.
4. Commit + push a este repo **antes** de ejecutar
   `intercom-sync push` sobre el artículo correspondiente.

## Mantenimiento

Periódicamente (no urgente) se pueden eliminar PNGs cuyo
`intercom_url` en el manifest del repo privado esté poblado y
`file_sha256` coincida: el PNG ya está copiado al CDN de
Intercom y este repo no se necesita para servirlo.
