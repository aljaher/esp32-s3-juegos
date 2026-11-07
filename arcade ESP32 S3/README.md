<div align="center">

# 🕹️ Arcade ESP32-S3

**Consola arcade en miniatura con pantalla redonda, cuatro juegos clásicos y cartuchos NFC para descargar juegos nuevos por WiFi.**

![Plataforma](https://img.shields.io/badge/ESP32--S3-Super%20Mini-E7352C?logo=espressif&logoColor=white)
![Framework](https://img.shields.io/badge/Arduino-C%2B%2B-00979D?logo=arduino&logoColor=white)
![Pantalla](https://img.shields.io/badge/GC9A01A-240%C3%97240-2b2b2b)
![NFC](https://img.shields.io/badge/NFC-RC522%20%2F%20NTAG-F5A623)
![Contribuciones](https://img.shields.io/badge/contribuciones-bienvenidas-2ea44f)
![Licencia](https://img.shields.io/badge/licencia-MIT-blue)

[![Hecho con Claude](https://img.shields.io/badge/Hecho%20en%20colaboraci%C3%B3n%20con-Claude-D97757?logo=anthropic&logoColor=white)](https://www.anthropic.com/claude)

Creado por **L Code** en colaboración con **Claude** (Anthropic).

</div>

---

## Descripción

**Arcade ESP32-S3** es un videojuego portátil hecho con un ESP32-S3 Super Mini, una pantalla redonda GC9A01A de 240×240, cinco botones y un buzzer. Todo el firmware vive en **un solo archivo `.ino`**, sin librerías externas más allá de Adafruit GFX y el driver de la pantalla.

Incluye cuatro juegos con menú de carátulas a pantalla completa y, además, un sistema de **cartuchos NFC**: acercas una etiqueta al lector, el arcade descarga un juego por WiFi, lo guarda en su memoria y aparece como una carátula nueva.

## Características

### Juegos incluidos
| Juego | Descripción |
|---|---|
| **Pac-Man** | Laberinto, fantasmas y puntos |
| **Tetris** | Piezas, líneas y niveles de velocidad |
| **Invaders** | Naves invasoras al estilo clásico |
| **Dino Run** | Corredor de obstáculos a color, estilo dinosaurio de Chrome |

### Experiencia de uso
- **Menú de carátulas** a pantalla completa: IZQUIERDA / DERECHA cambian de juego, OK entra.
- **Menú propio en cada juego:** JUGAR y OPCIONES (sonido, skin y dificultad), que se guardan en memoria.
- **Skins** para cambiar la paleta de colores y **tres niveles de dificultad**.
- **Records guardados** por juego en Preferences.
- **Sonido** con buzzer pasivo, con efectos y melodías por juego.

### Cartuchos NFC y juegos descargables
- **AGREGAR JUEGO:** última carátula del menú, con animación de espera, confirmación y mensajes de error.
- **Lector RC522** con driver propio mínimo (SPI), sin librerías externas. Lee etiquetas NTAG/Ultralight con mensajes NDEF.
- **Configuración WiFi sin teclado:** una etiqueta NFC con las credenciales las guarda en el arcade.
- **Descarga por HTTPS** hacia LittleFS, con lista blanca de dominios.
- **Motores de juego incluidos en el firmware:** **Sokoban** y **Snake con mapas**. Los juegos descargables son archivos de datos (`.agm`) con niveles, color y portada.
- Hasta **6 juegos descargados**, que se pueden borrar desde el propio arcade.

## Cómo funciona

```
 Etiqueta NFC ──► Lector RC522 ──► ESP32-S3 ──► WiFi + HTTPS ──► archivo .agm
 (ID o URL)                           │                              (en tu servidor)
                                      ▼
                              LittleFS (memoria)
                                      │
                                      ▼
                      Motor del firmware (Sokoban / Snake)
                                      │
                                      ▼
                          Nueva carátula en el menú
```

- **La etiqueta es el cartucho:** guarda solo un ID o una URL (decenas de bytes). Una etiqueta no puede contener un juego completo.
- **El juego es un dato, no código:** un ESP32 no puede ejecutar código nativo descargado sin OTA, así que los juegos son tablas de datos que interpreta un motor ya incluido en el firmware.
- **La memoria se cuida:** el WiFi y el lector solo se encienden en la pantalla AGREGAR JUEGO y se apagan al salir.

## Seguridad

- Solo **HTTPS**, sin seguir redirecciones.
- **Lista blanca de dominios** (`DOMINIOS_OK`) definida en el firmware.
- Validación de **tamaño** (máx. 12 KB) y de **formato** antes de guardar: cabecera, caracteres, niveles, jugadores, cajas y metas.
- Los IDs solo admiten `a-z 0-9 _ -`, por lo que no pueden escapar de su carpeta.
- Validación de certificado con **CA raíz** propia (`ARC_CA_PEM`) y hora por NTP.
- Las credenciales WiFi se guardan en Preferences y no se muestran en pantalla, salvo el nombre de la red al guardarla.

## Hardware

| Componente | Detalle |
|---|---|
| Microcontrolador | ESP32-S3 Super Mini (flash 4 MB) |
| Pantalla | GC9A01A redonda 240×240, SPI |
| Entradas | 5 pulsadores (ARRIBA, ABAJO, IZQUIERDA, DERECHA, OK) |
| Sonido | Buzzer pasivo |
| NFC | Lector RC522 + etiquetas NTAG213 / NTAG215 |

Todo funciona a **3.3 V**. Cableado, mapa de pines y lista completa de materiales: [`docs/CONEXIONES_Y_MATERIALES.md`](docs/CONEXIONES_Y_MATERIALES.md).

## Instalación

1. Instala el **core ESP32** (3.3.5) y las librerías **Adafruit GFX**, **Adafruit GC9A01A** y **Adafruit BusIO**.
2. Abre `arcade_unico/arcade_unico.ino`.
3. Elige la placa **ESP32S3 Dev Module** (flash 4 MB, PSRAM deshabilitada).
4. Edita las 4 líneas de configuración del bloque `red_descarga.h`: dominio, ruta, modo de certificado y CA.
5. Compila y sube.

La guía para alojar juegos, escribir etiquetas NFC y crear tus propios juegos está en [`docs/LEEME_AGREGAR_JUEGOS.md`](docs/LEEME_AGREGAR_JUEGOS.md).

## Estructura del repositorio

```
.
├── arcade_unico/
│   └── arcade_unico.ino          # Firmware completo (un solo archivo)
├── docs/
│   ├── LEEME_AGREGAR_JUEGOS.md   # Etiquetas NFC, WiFi, formato .agm
│   └── CONEXIONES_Y_MATERIALES.md
├── ejemplos/
│   ├── cajas01.agm               # Sokoban de ejemplo (3 niveles)
│   └── serpiente01.agm           # Snake de ejemplo (2 mapas)
├── CONTRIBUTING.md
├── LICENSE
└── README.md
```

El firmware está organizado en bloques marcados con `// ===== inicio de X.h =====`, uno por juego o módulo (por ejemplo `rfid_rc522.h`, `ndef.h`, `juegos_datos.h`, `red_descarga.h`, `motor_sokoban.h`, `motor_snake.h`, `agregar_juego.h`, `arcade_menu.h`).

## Estado del proyecto

- Los cuatro juegos y el menú de carátulas son la base original del proyecto, ya probada por su autor.
- La integración **NFC + WiFi + juegos descargables** es una versión inicial. Su lógica (formato, validación, NDEF, motores) pasó pruebas en PC y compila de forma sintáctica, pero **está pendiente de validar en hardware real**, en especial el RC522, el WiFi y HTTPS.

## Ideas para el futuro

- Motor de **Quiz** con preguntas y respuestas definidas en datos.
- **Portal cautivo** como alternativa para configurar el WiFi.
- Más motores de juego y packs de skins descargables.
- Descarga con comprobación de integridad (hash) además del tamaño y el formato.

## 🤝 Contribuciones

**¡Las contribuciones son bienvenidas!** Puedes ayudar de varias formas:

- Probando el proyecto en tu hardware y reportando lo que encuentres.
- Creando **juegos nuevos** en formato `.agm` o proponiendo nuevos motores.
- Mejorando el driver, la interfaz, el sonido o la documentación.
- Corrigiendo errores o traduciendo la documentación.

Lee la guía en [`CONTRIBUTING.md`](CONTRIBUTING.md) antes de abrir un Pull Request.

## Créditos

Proyecto creado por **L Code**, desarrollado en colaboración con **[Claude](https://www.anthropic.com/claude)**, el asistente de IA de Anthropic.

## Licencia

Distribuido bajo la licencia **MIT**. Consulta el archivo [`LICENSE`](LICENSE).
