# LEEME: agregar juegos al arcade con etiquetas NFC

Guía paso a paso. Todo lo nuevo en `arcade_unico.ino` está marcado con `>>> NUEVO`.
El cableado y los materiales están en `CONEXIONES_Y_MATERIALES.md`.

## Cómo funciona
- La **etiqueta NFC es el cartucho**: solo guarda un ID o una URL (unos 40 bytes).
- El **juego es un archivo `.agm`** (texto) alojado en internet. El arcade lo descarga por WiFi y lo guarda en su memoria (LittleFS).
- Los motores **Sokoban** y **Snake** ya están en el firmware: el `.agm` solo trae niveles, color y portada.
- Hay otra etiqueta especial, la de **WiFi**, que guarda tu red en el arcade (no hay teclado).

## Paso 1. Prepara el código (una sola vez)
En el bloque `red_descarga.h` del `.ino` edita:

```cpp
#define ARC_HOST   "TU-USUARIO.github.io"   // dominio donde alojas los juegos
#define ARC_RUTA   "/arcade/"               // carpeta dentro del dominio
#define ARC_TLS_SIN_VERIFICAR 1             // 1 = pruebas, 0 = validar certificado
static const char ARC_CA_PEM[] = "";        // CA raíz del dominio (solo si validas)
```

- `DOMINIOS_OK` es la lista blanca: solo se descarga de esos dominios, por https y sin redirecciones.
- Empieza con `ARC_TLS_SIN_VERIFICAR 1` para probar. Para uso normal pega la CA raíz de tu dominio en `ARC_CA_PEM` y vuelve a `0`. Con `0` y la CA vacía el arcade se niega a descargar ("FALTA EL CERTIFICADO").
- Compila y sube el programa.

## Paso 2. Aloja los juegos (gratis, con GitHub Pages)
1. Crea una cuenta y un repositorio en GitHub, y activa **Pages** en sus ajustes.
2. Sube una carpeta `arcade/` con tus archivos `.agm` (hay dos de ejemplo en `ejemplos/`).
3. La URL de un juego queda así: `https://TU-USUARIO.github.io/arcade/cajas01.agm`.

## Paso 3. Configura las etiquetas NFC
Necesitas:
- Etiquetas **NTAG213 o NTAG215** (stickers, tarjetas o llaveros). **No sirven las MIFARE Classic** de 1K que suelen venir con el RC522.
- La app **NFC Tools** en tu celular (Android o iPhone).

Para escribir cada etiqueta: abre NFC Tools → **Escribir** → **Añadir un registro** → elige el tipo → **Escribir** → acerca la etiqueta a la parte trasera del celular.

| Etiqueta | Tipo de registro | Contenido |
|---|---|---|
| **WiFi** | Texto | `WIFI:S:MiRed;P:MiClave;;` |
| **Juego por ID** | Texto | `cajas01` (también vale `juego:cajas01`) |
| **Juego por URL** | URL / URI | `https://TU-USUARIO.github.io/arcade/cajas01.agm` |

Reglas:
- Un solo registro por etiqueta.
- El ID solo admite `a-z 0-9 _ -` (máx. 24 caracteres) y debe existir `ID.agm` en tu carpeta de juegos.
- Si el nombre de la red o la clave llevan `;` `:` `,` o `\`, escápalos con `\` delante.
- La red debe ser de **2.4 GHz**.
- La clave WiFi queda en texto plano en la etiqueta: guárdala tú.

## Paso 4. Úsalo en el arcade
1. En el menú, ve a la **última carátula: AGREGAR** y pulsa OK ("NUEVO").
2. **Primero la etiqueta WiFi**: acércala al lector (hasta ~3 cm). Sale "WIFI GUARDADO".
3. Después la etiqueta de un juego: conecta, descarga, valida y guarda. La carátula nueva aparece en el menú, ya seleccionada.
4. Controles en AGREGAR: **IZQUIERDA** sale, **ARRIBA** abre BORRAR JUEGO (IZQ/DER elegir, OK confirmar, ARRIBA volver).
- Máximo 6 juegos descargados, 12 KB cada uno.
- El WiFi y el lector solo se encienden en esta pantalla.

## Cómo crear tus propios juegos (`.agm`)
Archivo de texto, ASCII, máximo 12 KB:

```
ARCGAME1
tipo=sokoban            (sokoban | snake)
titulo=CAJAS            (1 a 8: A-Z 0-9 espacio -)
color=FF8800            (RRGGBB)
portada=r,x,y,w,h,col;c,x,y,r,col;l,x0,y0,x1,y1,col;t,x0,y0,x1,y1,x2,y2,col
vel=150                 (solo snake: 60 a 500 ms por paso)
meta=6                  (solo snake: 3 a 50 manzanas por nivel)
[nivel]
...mapa...
[nivel]
...otro mapa...
```

- `portada` es opcional. Dibuja sobre un lienzo de 80x80: `r` rectángulo, `c` círculo, `l` línea, `t` triángulo. Los colores van en RRGGBB. Máx. 24 formas.
- Mapas de hasta **13x13**, hasta **12 niveles**.
- **Sokoban:** `#` pared, `@` jugador, `+` jugador sobre meta, `$` caja, `.` meta, `*` caja en meta, espacio suelo. Debe haber tantas cajas como metas y un solo jugador.
- **Snake:** `#` pared, `S` inicio (uno solo), espacio o `.` suelo.
- Controles: Sokoban se mueve con las flechas, OK = pausa (ARRIBA reinicia el nivel, IZQ sale). Snake gira con las flechas, OK = pausa.
- SONIDO, SKIN y DIFICULTAD de OPCIONES también valen para estos juegos.
- Si el archivo no cumple el formato, el arcade lo rechaza al descargar.

## Errores en pantalla
| Mensaje | Qué revisar |
|---|---|
| SIN LECTOR | Cableado del RC522 (3.3 V, MISO=13, SS=7, RST=43) |
| ETIQUETA NO VALIDA / VACIA | Usar NTAG; reescribir con NFC Tools |
| ERROR DE LECTURA | Acercar más la etiqueta y mantenerla quieta |
| NO ES UN JUEGO | Formato del registro (ver tabla del Paso 3) |
| SIN WIFI GUARDADO | Leer antes la etiqueta WiFi |
| NO CONECTA | Red 2.4 GHz y clave correcta |
| SIN HORA DE INTERNET | La red no tiene salida o bloquea NTP |
| DOMINIO NO PERMITIDO | Agregar el dominio a `DOMINIOS_OK` |
| FALTA EL CERTIFICADO | Pegar `ARC_CA_PEM` o usar `ARC_TLS_SIN_VERIFICAR 1` para probar |
| NO SE ENCONTRO (HTTP 404) | El archivo no existe en esa ruta |
| ARCHIVO / CABECERA / NIVELES INVALIDOS | Formato `.agm` |
| LISTA LLENA / SIN ESPACIO | Borrar un juego con ARRIBA |

## Notas
- La primera vez que arranca, LittleFS se formatea (puede tardar unos segundos con la pantalla negra).
- Si el compilador dice "sketch too big", elige un esquema de particiones con más espacio de aplicación.
