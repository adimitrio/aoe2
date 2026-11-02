# Rival AoE2

Muestra en tiempo real contra quién estás jugando en **Age of Empires II: Definitive Edition**, con el ELO de RM 1v1 de cada rival.

**Online:** https://TUUSUARIO.github.io/aoe2/?id=2575121

## Qué muestra

Cada partida es una tarjeta con el mismo formato: tu equipo a la izquierda y los rivales a la derecha, cada uno ordenado por rating y emparejando al más fuerte con el más fuerte. Las barras crecen desde el centro, y entre cada par aparece la diferencia (tu jugador menos su rival directo). Arriba de las barras va el promedio de cada equipo.

- El selector **1v1 / Team Game**, arriba, cambia el rating de todas las tarjetas a la vez y se guarda en el navegador. En 1v1 se muestra el RM 1v1 actual de cada jugador; en Team Game, el rating que tenía en esa partida. Todas las barras usan la misma escala.
- Cada jugador lleva el ícono de su civilización con el color de jugador, su nombre y la civilización. Tocando el nombre se abre la partida de ese jugador.
- La primera tarjeta es la partida en curso (**Jugando ahora**, con el tiempo transcurrido y **Ver en el juego**, que la abre en Age of Empires II como espectador) o, si no estás jugando, la última partida con su resultado y duración.
- Debajo van las dos partidas anteriores con el mismo formato (resultado, mapa, duración y hace cuánto).
- La grabación (rec) de cada partida terminada, para descargar: un `.zip` con el archivo `.aoe2record`. Microsoft las guarda unos meses; si ya no está, el link avisa **No disponible**.
- En la pestaña del navegador aparece `⚔ Rival (ELO)`, así se ve sin cambiar de ventana.

El score, el eAPM y el MVP todavía no se muestran: la API de aoe2companion no los trae y habría que sacarlos del archivo de la partida (rec).

**Sección Clan** (`#clan`)
- Todos los integrantes de un clan con su ELO de RM 1v1 y de TG, país y profile ID.
- Quién está jugando en este momento: mapa y rival en 1v1, o formato y compañeros de clan en partidas por equipos. Tocando el estado se abre la partida en el juego para verla como espectador.
- Para el resto, hace cuánto jugó su última partida ranked.
- Se ordena por 1v1, TG, nombre o estado tocando el encabezado de cada columna.
- Tocando el nombre de un integrante se abre su partida en la sección Rival, y tocando su profile ID, su perfil de aoe2companion.

## Uso

1. Abrí la página.
2. En el campo **Jugador** escribí el nombre o el profile ID y tocá **Ver**. Si el nombre coincide con varios jugadores, aparece una lista para elegir. También se puede pasar el ID directo en la URL con `?id=PROFILE_ID`.
3. Dejala abierta: cuando arranca una partida, la pantalla se actualiza sola.

Tocando el nombre de cualquier jugador, en una partida o en la sección Clan, se abre su partida: la que está jugando o la última. Con **Atrás** se vuelve al jugador anterior.

El Profile ID se guarda en tu navegador, así que la próxima vez ya aparece cargado. Abrir a otro jugador así no cambia el guardado: eso se hace desde el campo **Jugador**.

Para encontrar tu Profile ID, buscá tu nombre en [aoe2companion.com](https://www.aoe2companion.com). Es el número que aparece al final de la URL de tu perfil, por ejemplo `aoe2companion.com/profile/2575121`.

También funciona como **Browser Source en OBS** para mostrarlo en un stream.

Tocando **Aviso**, arriba de todo, suena una campanita cada vez que el jugador seguido arranca una partida. La elección queda guardada en el navegador. Como los navegadores no dejan sonar una página hasta que se la toca, después de abrirla hay que tocarla una vez; en OBS suena sin tocar nada.

## Cómo funciona

Es un único `index.html`, sin dependencias ni backend. Todas las consultas las hace el navegador de quien abre la página, contra la API de [aoe2companion](https://www.aoe2companion.com) y, para las grabaciones, contra la de Microsoft:

| Qué | Endpoint | Cuándo |
|---|---|---|
| Partidas en curso | `wss://socket.aoe2companion.com/listen?handler=ongoing-matches&profile_ids=…` | En tiempo real (WebSocket) |
| Última partida | `GET https://data.aoe2companion.com/api/matches?profile_ids=…&per_page=1` | Al abrir y cada 30 s como respaldo |
| Partidas anteriores | `GET https://data.aoe2companion.com/api/matches?profile_ids=…&per_page=10` | Al abrir y cuando termina una partida |
| ELO de RM 1v1 y de Team Game | `GET https://data.aoe2companion.com/api/profiles/{id}` | Por jugador, con caché de 10 min |
| Búsqueda por nombre | `GET https://data.aoe2companion.com/api/profiles?search=NOMBRE` | Al buscar un jugador por nombre |
| Integrantes de un clan | `GET https://data.aoe2companion.com/api/profiles?clan=TAG` | Al abrir la sección Clan y cada 5 min |
| ELO del clan | `GET https://data.aoe2companion.com/api/leaderboards/rm_1v1?clan=TAG` (y `rm_team`) | Al abrir la sección Clan y cada 5 min |
| Partidas en curso del clan | El mismo WebSocket, con todos los profile IDs del clan | En tiempo real |
| Grabación de una partida | `GET https://api.ageofempires.com/api/GameStats/AgeII/GetMatchReplay/?gameId=…&profileId=…` | Al tocar **Descargar rec** o **rec** |

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
