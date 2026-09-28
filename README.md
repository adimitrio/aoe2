# Rival AoE2

Muestra en tiempo real contra quién estás jugando en **Age of Empires II: Definitive Edition**, con el ELO de RM 1v1 de cada rival.

**Online:** https://TUUSUARIO.github.io/aoe2/?id=2575121

## Qué muestra

**Mientras jugás**
- Cada rival con su nombre, profile ID y color de jugador.
- Su ELO de RM 1v1 y la diferencia con el tuyo ("te lleva X" o "X debajo tuyo").
- Su civilización, país, winrate y cantidad de partidas en 1v1, ELO máximo y el rating del modo que se está jugando.
- Links a su perfil en aoe2companion y aoe2insights.
- El mapa, el modo, el servidor y el tiempo transcurrido desde que arrancó la partida.
- Tus aliados, con su profile ID y ELO de 1v1.
- En la pestaña del navegador aparece `⚔ Rival (ELO)`, así se ve sin cambiar de ventana.

**Cuando no estás en partida**
- Tu última partida, con el resultado y cuánto duró.

## Uso

1. Abrí la página.
2. Escribí tu Profile ID en el campo de arriba y tocá **Seguir**, o pasalo directo en la URL con `?id=TU_ID`.
3. Dejala abierta: cuando arranca una partida, la pantalla se actualiza sola.

El Profile ID se guarda en tu navegador, así que la próxima vez ya aparece cargado.

Para encontrar tu Profile ID, buscá tu nombre en [aoe2companion.com](https://www.aoe2companion.com). Es el número que aparece al final de la URL de tu perfil, por ejemplo `aoe2companion.com/profile/2575121`.

También funciona como **Browser Source en OBS** para mostrarlo en un stream.

## Cómo funciona

Es un único `index.html`, sin dependencias ni backend. Todas las consultas las hace el navegador de quien abre la página, contra la API de [aoe2companion](https://www.aoe2companion.com):

| Qué | Endpoint | Cuándo |
|---|---|---|
| Partidas en curso | `wss://socket.aoe2companion.com/listen?handler=ongoing-matches&profile_ids=…` | En tiempo real (WebSocket) |
| Última partida | `GET https://data.aoe2companion.com/api/matches?profile_ids=…&per_page=1` | Al abrir y cada 30 s como respaldo |
| ELO de RM 1v1 | `GET https://data.aoe2companion.com/api/profiles/{id}` | Por jugador, con caché de 10 min |

Si el WebSocket se corta, la página se reconecta sola y mientras tanto sigue consultando la última partida cada 30 segundos.

## Correrlo localmente

Abrí `index.html` con doble clic. Si el navegador bloquea las consultas, servilo desde un servidor local:

```bash
php -S localhost:8080
```

y abrí http://localhost:8080/.

## Créditos

Los datos vienen de [aoe2companion.com](https://www.aoe2companion.com). Su API todavía no es pública oficialmente, así que esta herramienta está pensada para uso personal y consulta cada 30 segundos para no cargar su servidor.

Age of Empires II © Microsoft Corporation. Esta herramienta fue creada bajo las [Game Content Usage Rules](https://www.xbox.com/en-US/developers/rules) de Microsoft usando assets de Age of Empires II: Definitive Edition, y no está respaldada ni afiliada a Microsoft.
