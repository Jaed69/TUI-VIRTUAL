# TIU Virtual — Carnet Virtual de Estudiante (PWA)

Aplicación web progresiva (PWA) que muestra un carnet virtual de estudiante con foto, nombre, código, carrera y campus, junto con un reloj en tiempo real. Funciona sin internet y se puede instalar en la pantalla de inicio del celular.

---

## Estructura del proyecto

| Archivo | Qué hace |
|---|---|
| `index.html` | Toda la app: HTML (interfaz), CSS (estilos) y JS (datos, reloj, PWA) |
| `manifest.json` | Configuración PWA (nombre, icono, modo pantalla completa) |
| `sw.js` | Service Worker — cachea archivos para funcionar sin internet |

No tiene dependencias externas. Solo abre `index.html` en un navegador y funciona.

---

## Cómo modificar los datos del estudiante

### Cambiar el nombre, código, carrera y campus

Abre `index.html` y busca el objeto `DATOS` (línea ~406). Edita los valores:

```javascript
const DATOS = {
    nombre:    "PEPITO ROMPECUNAS",          // ← nombre completo
    apellido1: "DE LA CRUZ",                 // ← primer apellido
    apellido2: "UGARTE",                     // ← segundo apellido
    codigo:    "202310456",                  // ← código de alumno
    id_banner: "N04080409",                  // ← ID Banner
    carrera:   "DISEÑO PROFESIONAL GRÁFICO", // ← nombre de la carrera
    campus:    "Campus San Miguel",          // ← sede/campus
};
```

> **Nota de privacidad**: Los apellidos se muestran censurados (solo la primera letra + `***`). La función `censurar()` en la línea 422 controla esto. Si quieres mostrar los apellidos completos, cambia:

```javascript
// Actual (censurado):
document.getElementById("nombre-linea2").textContent = censurar(DATOS.apellido1) + " " + censurar(DATOS.apellido2);

// Para mostrar apellidos completos:
document.getElementById("nombre-linea2").textContent = DATOS.apellido1 + " " + DATOS.apellido2;
```

### Cambiar la foto del estudiante

La foto está incrustada en base64 dentro del HTML (línea ~377):

```html
<div class="photo-wrap">
  <img src="data:image/jpeg;base64,/9j/4AAQSkZJRg..." />
</div>
```

**Opción A — Usar un archivo de imagen local:**

1. Coloca la foto (ej. `foto.jpg`) en la misma carpeta del proyecto
2. Reemplaza el `<img>` por:

```html
<div class="photo-wrap">
  <img src="foto.jpg" alt="Foto del estudiante" />
</div>
```

3. Agrega `"./foto.jpg"` a la lista `ASSETS_TO_CACHE` en `sw.js` para que se cachee.

**Opción B — Convertir una foto a base64 (recomendado para offline):**

1. Ve a https://www.base64-image.de y sube tu foto
2. Copia el código base64 generado
3. Reemplaza todo el contenido de `src="data:image/jpeg;base64,..."` con el nuevo código
4. (Opcional) Si la foto es PNG, cambia `image/jpeg` por `image/png`

---

## Cómo cambiar los colores y estilo

Busca las variables CSS en `index.html` (líneas ~45-53):

```css
:root {
    --red:      #c62828;    /* rojo de acentos */
    --dark:     #1a1a2e;    /* texto oscuro */
    --bg:       #F9FAFE;    /* fondo azul claro */
    --cloud:    rgba(199, 207, 254, 0.55);  /* nubes decorativas */
    --grey:     #9e9e9e;    /* texto gris */
    --navy:     #2B3945;    /* títulos */
    --clock-bg: #DBD9FF;    /* fondo del reloj */
}
```

Cambia los valores hexadecimales a tu gusto. Por ejemplo, para fondo blanco usa `--bg: #ffffff;`.

---

## Cómo cambiar el icono de la app (PWA)

Edita `manifest.json`, cambia la URL en `"src"` por la de tu icono. El icono se usa cuando el usuario instala la app en su celular.

```json
"icons": [
    {
        "src": "icon-192.png",
        "sizes": "192x192",
        "type": "image/png"
    },
    {
        "src": "icon-512.png",
        "sizes": "512x512",
        "type": "image/png"
    }
]
```

Coloca los archivos `icon-192.png` y `icon-512.png` en la carpeta del proyecto y agrégalos a `ASSETS_TO_CACHE` en `sw.js`.

---

## Cómo probar localmente

**Opción 1 — Abrir directo (solo visual):**
Arrastra `index.html` a Chrome/Edge. La PWA no se activa desde `file://`.

**Opción 2 — Servidor local (recomendado):**
```bash
# Con Python 3
python -m http.server 8000
# Luego abre http://localhost:8000
```

**Para probar la instalación PWA:**
- Abre Chrome DevTools (F12) → pestaña "Application" → "Manifest"
- O usa Lighthouse para verificar que la PWA cumple los requisitos

---

## Cómo agregar quitar la censura de apellidos

En `index.html` busca la función `censurar` (línea ~422):

```javascript
function censurar(t) { return t.length <= 1 ? t : t[0] + "***"; }
```

- **Mostrar apellidos completos**: Cambia la línea 433 (ver sección "Cambiar los datos" arriba).
- **Mostrar más caracteres** (ej. primeras 3 letras): Cambia la función a `return t.length <= 3 ? t : t.slice(0,3) + "***";`

---

## Cómo quitar elementos visuales

- **Quitar las nubes decorativas**: Borra el bloque `<div class="clouds">...</div>` (líneas 353-357)
- **Quitar el reloj**: Borra `<div class="clock-box">...</div>` (líneas 370-374) y la función `tick()` + `setInterval` en JS (líneas 460-485)
- **Quitar el botón volver**: Borra `<div class="app-bar">...</div>` (líneas 359-366)

---

## Resumen rápido de archivos

```
Tiu_virtual/
├── index.html       ← edita aquí: DATOS (nombres), <img> (foto), colores, CSS
├── manifest.json    ← edita aquí: nombre de la app, iconos PWA
├── sw.js            ← edita aquí: archivos a cachear offline
└── README.md        ← este archivo
```
