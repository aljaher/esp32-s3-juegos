# Arcade ESP32-S3: conexiones y materiales

## Materiales

| Cant. | Material | Notas |
|---|---|---|
| 1 | ESP32-S3 Super Mini | Cerebro del arcade (flash 4 MB) |
| 1 | Pantalla redonda GC9A01A 240x240 (1.28") | Módulo SPI de 7-8 pines |
| 5 | Pulsadores (6x6 mm o los de caja) | ARRIBA, ABAJO, IZQUIERDA, DERECHA, OK |
| 1 | Buzzer **pasivo** | Pasivo (con tonos), no el activo |
| 1 | Lector RFID **RC522** (13.56 MHz) | Incluye normalmente tarjeta y llavero MIFARE, que **no sirven** para los juegos |
| 3 o más | Etiquetas **NTAG213 o NTAG215** | Una para el WiFi y una por juego (stickers, tarjetas o llaveros) |
| 1 | Protoboard o placa perforada | Con cables dupont |
| 1 | Cable USB-C de datos | Para programar y alimentar |
| Opcional | Capacitor de 100 µF | Entre 3V3 y GND, cerca del RC522 (por si hay reinicios al conectar WiFi) |

Software y servicios:
- ArduinoDroid o Arduino IDE con el core ESP32 3.3.5 y las librerías Adafruit GFX, Adafruit GC9A01A y BusIO.
- App **NFC Tools** en el celular (para escribir las etiquetas).
- Una red WiFi de 2.4 GHz.
- Un lugar para alojar los juegos (por ejemplo GitHub Pages, gratis).

## Mapa de pines del ESP32-S3 Super Mini

| Pin | Uso |
|---|---|
| 1 | Botón ARRIBA |
| 2 | Botón ABAJO |
| 3 | Botón IZQUIERDA |
| 4 | Botón DERECHA |
| 5 | Botón OK |
| 6 | Buzzer |
| 7 | RC522 SDA (SS) |
| 8 | Pantalla DC |
| 9 | Pantalla RST |
| 10 | Pantalla CS |
| 11 | MOSI: pantalla SDA y RC522 MOSI |
| 12 | SCK: pantalla SCL y RC522 SCK |
| 13 | RC522 MISO |
| 43 (TX) | RC522 RST |
| 44 (RX) | Libre |
| 3V3 | Alimentación de pantalla, RC522 y BLK |
| GND | Común a todo |

## Pantalla GC9A01A

| Pantalla | ESP32-S3 |
|---|---|
| VCC | 3V3 |
| GND | GND |
| SCL (SCK) | 12 |
| SDA (MOSI) | 11 |
| CS | 10 |
| DC | 8 |
| RST | 9 |
| BLK (luz de fondo) | 3V3 |

## Botones (5)
Un lado de cada botón al pin y el otro lado a GND. No hacen falta resistencias (se usa la resistencia interna).

| Botón | Pin |
|---|---|
| ARRIBA | 1 |
| ABAJO | 2 |
| IZQUIERDA | 3 |
| DERECHA | 4 |
| OK | 5 |

## Buzzer pasivo
- Un terminal al pin **6**, el otro a **GND**.

## Lector RC522 (3.3 V, nunca 5 V)

| RC522 | ESP32-S3 |
|---|---|
| 3.3V | 3V3 |
| GND | GND |
| SCK | 12 (compartido con la pantalla) |
| MOSI | 11 (compartido con la pantalla) |
| MISO | 13 |
| SDA (SS) | 7 |
| RST | 43 |
| IRQ | sin conectar |

El RC522 y la pantalla comparten el bus SPI (SCK y MOSI). El lector usa su propio SS, MISO y RST, y va a 4 MHz, así que no se estorban.

## Notas de montaje
- Todo funciona a **3.3 V**. El pin de 5 V no se usa.
- Todos los GND van unidos.
- Para que el RC522 lea bien, coloca la etiqueta a unos 3 cm o menos de la antena del lector.
- Si el arcade se reinicia al conectar el WiFi, agrega el capacitor de 100 µF cerca del RC522.
- Para probar los botones, la pantalla debe mostrar el menú de carátulas con IZQUIERDA/DERECHA.
