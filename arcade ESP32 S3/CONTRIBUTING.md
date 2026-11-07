# Guía de contribución

¡Gracias por querer mejorar **Arcade ESP32-S3**! Toda contribución es bienvenida: código, juegos, documentación, pruebas o ideas.

## Formas de contribuir

1. **Reportar errores:** abre un *Issue* con el problema, la placa y versión del core que usas, el módulo de pantalla y lector, y los pasos para reproducirlo. Si es posible, incluye el mensaje que aparece en pantalla.
2. **Proponer mejoras:** abre un *Issue* describiendo la idea antes de programarla, para coordinar el trabajo.
3. **Crear juegos (`.agm`):** comparte tus archivos en `ejemplos/`. Deben seguir el formato de [`docs/LEEME_AGREGAR_JUEGOS.md`](docs/LEEME_AGREGAR_JUEGOS.md) y funcionar al descargarse en el arcade.
4. **Mejorar el código o la documentación:** envía un *Pull Request*.

## Pull Requests

1. Haz un *fork* y crea una rama con un nombre claro (`mejora-sonido`, `motor-quiz`).
2. Mantén el estilo del proyecto:
   - Un solo archivo `.ino`, organizado en bloques `// ===== inicio de X.h =====` / `// ===== fin de X.h =====`.
   - Identificadores y comentarios en español.
   - Un `namespace` por juego o módulo.
   - Sin librerías externas adicionales salvo que se justifique.
3. Respeta las restricciones de memoria: el menú de carátulas usa alrededor de 140 KB de heap y el WiFi solo se enciende en la pantalla AGREGAR JUEGO.
4. No debilites la seguridad de la descarga: HTTPS, lista blanca de dominios, validación de tamaño y de formato.
5. Prueba tus cambios en hardware real y anótalo en el PR (placa, pantalla, lector y qué probaste).
6. Describe qué cambia y por qué.

## Código de conducta

Sé respetuoso y constructivo. Se aceptan críticas técnicas, no ataques personales.

## Créditos

El proyecto lo creó **L Code** en colaboración con **Claude** (Anthropic). Los contribuyentes se agradecen en el historial del repositorio.
