# Datos de partidas — formato `taller-partida/1`

Desde la **V72**, el juego guarda cada trabajo (roseta, NAP, botella, cableado, guardia, niveles) y cada minijuego en el `localStorage` del navegador:

| Clave de localStorage | Contenido |
|---|---|
| `taller_partidas` | Array con las últimas 300 partidas (formato de [`partida.schema.json`](partida.schema.json)) |
| `taller_dev` | Id aleatorio del dispositivo |
| `taller_sync_url` | (opcional) URL `https://` de un servidor que reciba las partidas pendientes |

## Importante: localStorage es de cada navegador

El `localStorage` vive **solo en el navegador de cada jugador**. GitHub Pages sirve archivos estáticos: no puede leer el localStorage de nadie ni guardar datos que manden los jugadores. Para juntar los datos de todos hay tres caminos:

1. **Exportar / importar (ya funciona):** en *Mis partidas* cada jugador baja su historial en JSON (`partidas-<clave>-<fecha>.json`). Esos archivos se pueden subir a esta carpeta (`data/partidas/`) con un commit o un pull request.
2. **Versión de Claude (ya funciona):** el artefacto tiene una nube compartida; ahí se guarda un resumen de las últimas 20 partidas de cada jugador junto con su cuenta.
3. **Servidor para el multijugador (futuro):** con un backend (por ejemplo Supabase, Firebase o un Cloudflare Worker) que exponga un endpoint `POST`. Si se configura `localStorage.taller_sync_url`, el juego le manda las partidas pendientes así:

```json
{ "schema": "taller-partida/1", "partidas": [ { "id": "…", "tipo": "trabajo", "modo": "roseta", "puntos": 870, "...": "..." } ] }
```

Si responde `2xx`, las marca como `"sync": "servidor"`. **Nunca** pongas un token de GitHub dentro del `index.html`: cualquiera podría leerlo.

## API en el navegador

```js
TallerPartidas.todas()          // todas las partidas guardadas
TallerPartidas.pendientes()     // las que todavía no se subieron
TallerPartidas.marcar(ids)      // marcarlas como subidas
TallerPartidas.exportar()       // descarga el JSON
TallerPartidas.sincronizar()    // intenta subir las pendientes
```

Cada partida tiene un `id` único (UUID), así que juntar archivos de varios jugadores o importar dos veces no duplica nada.
