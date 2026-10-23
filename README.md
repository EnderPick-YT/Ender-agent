# Guía para preparar y enviar el correo del Festival de Navidad

Esta guía explica cómo hacer que el correo `email-anuncio-navidad.html` se vea correctamente (con su nieve cayendo animada) en Gmail, y cómo enviarlo.

## Qué hay en cada archivo

| Archivo | Qué es |
|---|---|
| `email-anuncio-navidad.html` | El correo completo, listo para enviar. Contiene todo el texto del anuncio en inglés y el botón que lleva a `christmasfestival.rf.gd`. |
| `assets/email-hero.gif` (dentro del ZIP) | La animación de nieve: cielo nocturno, copos cayendo, luces de Navidad y capas de nieve. |
| `festival-navidad-php.zip` | La aplicación PHP del formulario actualizada, con los nuevos estilos de invierno. |

## Paso 1 — Subir la animación a tu hosting

El correo necesita que el GIF esté alojado en internet con una dirección pública:

1. Sube el contenido del ZIP a tu hosting de `christmasfestival.rf.gd` (igual que hiciste con el formulario). El GIF queda en `assets/email-hero.gif`.
2. Comprueba que funciona abriendo en el navegador: `https://christmasfestival.rf.gd/assets/email-hero.gif`. Debes ver la escena con la nieve cayendo en movimiento.
3. Importante: cuando preparé esta guía, esa dirección todavía no respondía, así que el GIF aún no está subido. Es el único requisito externo: sin él, el correo se verá igual de bien estructurado, pero sin la escena animada.

## Paso 2 — Enviar el correo desde Gmail

### Opción A: copiar y pegar (la más sencilla)

1. Abre `email-anuncio-navidad.html` con **Chrome** o Edge (con conexión a internet, para que cargue el GIF).
2. Selecciona todo con `Ctrl+A` (Windows) o `Cmd+A` (Mac) y copia con `Ctrl+C` / `Cmd+C`.
3. Abre Gmail → **Redactar** → pega con `Ctrl+V` / `Cmd+V`.
4. Añade el asunto, por ejemplo: `The Christmas Festival is back — Sign up before November 6!`
5. Envíalo.

Este método suele conservar tablas, colores, tarjetas, el botón con su enlace y el GIF animado, pero depende de cómo pegue Gmail cada vez: por eso es imprescindible el envío de prueba del Paso 3 antes del envío real. Ojo: **adjuntar el archivo HTML no es lo mismo que insertarlo como cuerpo** — si lo adjuntas, el destinatario recibe un archivo descargable, no el correo diseñado.

### Opción B: insertar el HTML crudo (fidelidad total)

Si al pegar algo se pierde o quieres el resultado exacto al archivo:

- En Chrome, instala una extensión tipo "Insert HTML by cloudHQ" y úsala en la ventana de redacción para insertar el contenido del archivo.
- O envíalo con una herramienta de envío de correos (MailerLite, Brevo, etc.) pegando el HTML en su editor de código.

## Paso 3 — Prueba antes del envío real

1. **Envíatelo a ti mismo primero** (a tu propio Gmail) y ábrelo en la app de Gmail del móvil y en el ordenador.
2. Verifica la lista de comprobación:
   - [ ] El GIF se ve animado (nieve cayendo, luces parpadeando).
   - [ ] El botón **SIGN UP NOW** abre `https://christmasfestival.rf.gd`.
   - [ ] Las tres fechas son correctas: Festival 21 dic 2026, inscripción hasta 6 nov 2026, audiciones 9–13 nov 2026.
   - [ ] En el móvil, el correo cabe en pantalla y el texto no se corta.
3. Para enviarlo a muchos alumnos, pon sus direcciones en **CCO** (copia oculta) para que nadie vea la lista completa.

## Notas técnicas (por qué está hecho así)

- **Gmail no admite animaciones CSS**: las tablas de compatibilidad de [Can I Email sobre `@keyframes`](https://www.caniemail.com/features/css-at-keyframes/) y [Can I Email sobre la propiedad `animation`](https://www.caniemail.com/features/css-animation/) muestran que Gmail (web, iOS y Android) no las soporta, y que solo Apple Mail las reproduce. Por eso la solución elegida para la nieve animada es un **GIF**, que sí se reproduce en Gmail, en la app de iOS/Android y en casi todos los clientes de correo.
- Gmail **recorta** los mensajes muy largos (el límite más citado es de 102 KB): este correo ocupa unos 10 KB, muy por debajo, así que debería verse completo. Aun así, haz el envío de prueba.
- Si el destinatario tiene bloqueada la carga de imágenes, verá el texto del anuncio (todo el contenido importante es texto real HTML, no imagen) y el texto alternativo de la escena.
- El botón usa un `<a>` con estilos inline, el formato más compatible que existe en correo.
- En el archivo HTML hay un pequeño bloque `<style>` con un parpadeo extra: Gmail lo ignora sin romper nada, pero Apple Mail lo aprovecha. Es una mejora progresiva, no un requisito.
- Una comprobación en navegador no equivale a una prueba real en la aplicación de Gmail: ese es el motivo del envío de prueba del Paso 3.
