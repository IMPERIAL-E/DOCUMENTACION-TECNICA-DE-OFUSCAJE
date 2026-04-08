````markdown
# DOCUMENTACIÓN TÉCNICA: Cómo Ofuscar y Minificar una Página Web Profesional

* **Inicio y fin:** 23/3/26 – 24/3/26.
* **Título del Proyecto:** Ofuscación y Despliegue de Alta Seguridad
* **Herramientas:** Vite (Build Tool), Terser (Minificador), Firebase (Hosting).
* **Estado Final Deseado:** Código HTML/CSS/JS comprimido en una sola línea INFINITA, con lógica incomprensible y encriptación de textos.

## ÍNDICE
1. PRERREQUISITOS: Arquitectura y Modularización.
2. PASO 1: Estructura de Carpetas y Configuración de Hosting (Firebase).
3. PASO 2: Instalación del Entorno de Construcción (Node.js).
4. PASO 3: Evolución de la Configuración de Vite (vite.config.js).
5. PASO 4: El Proceso de Construcción (The Build).
6. PASO 5: Despliegue y Verificación Final.

---

## 1. PRERREQUISITOS: Arquitectura y Modularización

Antes de iniciar cualquier proceso de ofuscación, es **obligatorio** que el proyecto esté modularizado. El ofuscador no funciona correctamente si el HTML tiene JavaScript embebido (dentro de etiquetas `<script>`). 

*(Énfasis: El nivel de JS que se puede tener en el documento HTML es mínimo para evitar que en la compresión de texto por la herramienta (Vite) sea tan fuerte que afecte la funcionalidad de los documentos).*

A continuación, un ejemplo de lo máximo permitido a nivel de scripts por documento HTML antes de ofuscar:

```html
<script type="text/javascript">
  function googleTranslateElementInit() {
    new google.translate.TranslateElement({
      pageLanguage: 'es',
      includedLanguages: 'es,en,fr,pt,ht,ru,zh-CN',
      layout: google.translate.TranslateElement.InlineLayout.SIMPLE,
      autoDisplay: false
    }, 'google_translate_element');
  }
</script>
````

**Regla de Oro:** 1 Documento Raíz (HTML) = 1 Hoja de Estilos (CSS) + 1 Hoja de Funciones Motrices (JS).

Esta estructura de jerarquías de documentos es 100% OBLIGATORIA PARA EL CORRECTO FUNCIONAMIENTO. Cada página debe estar separada en tres archivos con el mismo nombre para mantener el orden.

**Ejemplo de Estado Requerido:**

  * `login.html`
  * `login.js`
  * `login.css` (Vinculado en el HTML como `<script type="module" src="login.js"></script>`)

-----

## 2\. PASO 1: Estructura de Carpetas y Configuración de Hosting (Firebase)

En este proyecto, utilizamos Firebase como motor de Hosting. Es crucial configurar la estructura de carpetas correcta para que Vite y Firebase no choquen.

### El Rol de la Carpeta public

Vite utiliza una carpeta especial llamada `public`. Todo lo que esté adentro se copiará a la carpeta final de producción (`dist`) **sin ser tocado, renombrado ni comprimido**.

Todos los documentos de tu proyecto original (HTML, CSS, JS modularizados) debes moverlos a la carpeta raíz para que Vite pueda escanearlos y procesarlos.

Vite copiará todos los documentos escaneados y creará una nueva carpeta llamada `dist`, donde los archivos vendrán con el nombre ofuscado (una "sopa de letras").

> **Punto Crítico (Lección Aprendida):** Si tienes una carpeta de iconos llamada `icons`, debes moverla a la CARPETA `public`.
>
>   * **Estructura Correcta:** `public/icons/imagen.png`
>   * **Referencia en Código:** `<img src="public/icons/imagen.png">` (Debes tener la ruta completa en el HTML).

### Archivos Requeridos de Firebase

Un proyecto listo para Firebase debe tener estos archivos en la raíz:

  * `.firebaserc`
  * `firebase.json`
  * `firestore.rules` / `database.rules.json` (Si aplica).
  * Carpeta `functions/` (Si utilizas Cloud Functions).

-----

## 3\. PASO 2: Instalación del Entorno de Construcción (Node.js)

Para compilar, ofuscar y minificar, es obligatorio tener Node.js instalado.

### Verificación de Instalación

En la terminal de tu editor, verifica la instalación ejecutando:

```bash
node -v
npm -v
```

*(El sistema debe devolverte los números de las versiones instaladas, por ejemplo: v22.13.1).*

### Instalación de Dependencias

1.  **Crear el archivo `package.json` por defecto:**

<!-- end list -->

```bash
npm init -y
```

2.  **Instalar el motor y los plugins (Vite, Ofuscador, Minificador HTML y Terser):**

<!-- end list -->

```bash
npm install vite vite-plugin-javascript-obfuscator vite-plugin-html terser --save-dev
```

3.  **Analizar y corregir vulnerabilidades de dependencias:**

<!-- end list -->

```bash
npm audit fix
```

-----

## 4\. PASO 3: Evolución de la Configuración de Vite (vite.config.js)

Este es el cerebro de la operación. Crea o edita el archivo `vite.config.js` en la raíz de tu proyecto.

### Configuración de Alta Seguridad y Línea Infinita (RECOMENDADA)

*Uso: Máxima protección. Encripta textos, usa nombres hexadecimales, desordena la lógica, protege contra depuración (F12) y aplasta todo en una sola línea.*

```javascript
// vite.config.js - VERSIÓN B (Máxima Seguridad)
import { defineConfig } from 'vite';
import javascriptObfuscator from 'vite-plugin-javascript-obfuscator';
import { createHtmlPlugin } from 'vite-plugin-html';
import { resolve } from 'path';

export default defineConfig({
  plugins: [
    // 1. MINIFICACIÓN AGRESIVA DE HTML
    createHtmlPlugin({
      minify: {
        collapseWhitespace: true,
        removeComments: true,
        minifyCSS: true,
        minifyJS: true,
        removeRedundantAttributes: true,
        useShortDoctype: true,
      }
    }),

    // 2. OFUSCACIÓN AGRESIVA DE JS
    javascriptObfuscator({
      compact: true,
      stringArray: true,
      stringArrayEncoding: ['base64', 'rc4'],
      stringArrayThreshold: 1,
      identifierNamesGenerator: 'hexadecimal',
      renameGlobals: true,
      debugProtection: true,
      debugProtectionInterval: 4000,
      disableConsoleOutput: true,
      controlFlowFlattening: true,
      controlFlowFlatteningThreshold: 0.5,
      reservedNames: ['^fb', '^firebase', '^Vite', 'require']
    }),
  ],
  build: {
    target: 'esnext',
    // 3. MINIFICACIÓN EXTREMA FINAL
    minify: 'terser',
    terserOptions: {
      format: { comments: false },
      compress: { drop_console: true, drop_debugger: true }
    },
    rollupOptions: {
      input: {
        // IMPORTANTE: Listar TODOS los HTML del proyecto aquí
        main: resolve(__dirname, 'index.html'),
        login: resolve(__dirname, 'login.html'),
        panel: resolve(__dirname, 'panel.html'),
        // ... añadir el resto de documentos
      }
    }
  }
});
```

-----

## 5\. PASO 4: Configuración del Motor de Hosting y Construcción

### Ajustar Firebase

Antes de construir, debes indicarle a Firebase que suba la carpeta compilada (`dist`) en lugar de `public`. Abre `firebase.json` y cambia el valor de `"public"` a `"dist"`.

### The Build (Construcción)

Ejecuta el proceso que aplica la ofuscación extrema, comprime el HTML y minifica el CSS:

```bash
npx vite build
```

*(Si la terminal lanza un error Unable to parse HTML, significa que tu HTML tiene un error de sintaxis como una comilla faltante. Corrígelo y vuelve a correr el build).*

-----

## 6\. PASO 5: Despliegue y Verificación Final

Último paso: decirle a Firebase que suba la carpeta `dist` a internet.

```bash
firebase deploy --only hosting
```

**Verificación de la "Línea Infinita":**
Entra a tu página web ya subida, haz clic derecho y selecciona "Ver código fuente de la página". Debes ver toda tu sintaxis comprimida en una sola línea de texto.

-----

*Documento creado por: (VAR.P-R233)*

```

¿Prefieres organizar este documento como el archivo principal `README.md` del repositorio, o tienes planeado crear una Wiki dedicada en GitHub para tu proyecto?
```
