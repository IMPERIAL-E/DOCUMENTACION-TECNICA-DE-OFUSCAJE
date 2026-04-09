# DOCUMENTACIÓN TÉCNICA DE OFUSCAJE

* **Inicio y fin:** 23/3/26 – 24/3/26.
* **Título del Proyecto:** Ofuscación y Despliegue de Alta Seguridad.
* **Herramientas:** Vite (Build Tool), Terser (Minificador), Firebase (Hosting).
* **Estado Final Deseado:** Código HTML/CSS/JS comprimido en una sola línea INFINITA, con lógica incomprensible y encriptación de textos.

## ÍNDICE
1.  PRERREQUISITOS: Arquitectura y Modularización.
2.  PASO 1: Estructura de Carpetas y Configuración de Hosting (Firebase).
3.  PASO 2: Instalación del Entorno de Construcción (Node.js).
4.  PASO 3: Evolución de la Configuración de Vite (vite.config.js).
5.  PASO 4: El Proceso de Construcción (The Build).
6.  PASO 5: Despliegue y Verificación Final.

---

## 1. PRERREQUISITOS: Arquitectura y Modularización

Antes de iniciar cualquier proceso de ofuscación, es **obligatorio** que el proyecto esté modularizado. El ofuscador no funciona correctamente si el HTML tiene JavaScript embebido (dentro de etiquetas `<script>`).

*(Énfasis: El nivel de JS que se puede tener en el documento HTML es mínimo para evitar que en la compresión de texto por la herramienta (Vite) sea tan fuerte que afecte la funcionalidad de los documentos)*.

A continuación, te mostraré algunos ejemplos puntualmente ambiguos de qué tan permitido se puede tener una pequeña cantidad de JavaScript embebido (dentro de etiquetas `<script>`) en el HTML.

<img width="886" height="209" alt="image" src="https://github.com/user-attachments/assets/e96dfc26-e8aa-4e3c-b465-a7559ea3efe9" />


Y este ejemplo que ves aquí arriba es lo máximo a nivel de scripts por documento HTML que te puedes permitir tener tan siquiera antes de pensar en ofuscar y Minificar todo tu proyecto.

**Regla de Oro:** 1 Documento Raíz (HTML) = 1 Hoja de Estilos (CSS) + 1 Hoja de Funciones Motrices (JS).

Esta estructura de jerarquías de documentos es 100% OBLIGATORIA PARA EL CORRECTO FUNCIONAMIENTO. Si no está así cuando vayas modularizando cada parte del proyecto, al ofuscar te será casi imposible encontrar los errores.

**Estado Requerido del Proyecto:**
Cada página debe estar separada en tres archivos con el mismo nombre para mantener el orden:
* `login.html`
* `login.js`
* `login.css` (Vinculado en el HTML como `<script type="module" src="login.js"></script>`).

#
**Ejemplo 1 en Carpeta:**

<img width="420" height="200" alt="image" src="https://github.com/user-attachments/assets/9c821bcc-ef1f-46ba-97d9-0ae93d154c7c" />

#
**Ejemplo 2 en el Editor (Que estés Utilizando en este Momento):**

<img width="822" height="60" alt="image" src="https://github.com/user-attachments/assets/08c988f5-daf4-4868-a327-525690eecca9" />

---

## 2. PASO 1: Estructura de Carpetas y Configuración de Hosting (Firebase)

En este proyecto, utilizamos Firebase como motor de Hosting. Es crucial configurar la estructura de carpetas correcta para que Vite y Firebase no choquen.

#
### El Rol de la Carpeta public

<img width="150" height="209" alt="image" src="https://github.com/user-attachments/assets/4eee1394-a7cc-4a82-b7c4-ef5fa7e5b69e" />

#
Vite utiliza la carpeta especial desde donde haces tus deploys actuales llamada `public`. Y todo lo que esté adentro se copiará a la carpeta final de producción (`dist`) **sin ser tocado, renombrado ni comprimido por nosotros**.

Esto quiere decir que Vite hace una copia de toda tu carpeta Principal que es donde tenías todo tu proyecto inicialmente.

**¿A qué me refiero, por ejemplo?**
#
Los proyectos cuando se están creando, primero se crea una carpeta raíz. Dentro de esa carpeta se crean subcarpetas que dividen por clasificación. TODOS los documentos que se encuentran dentro de esa carpeta tú debes Seleccionarlos y moverlos a la carpeta raíz.

<img width="915" height="467" alt="image" src="https://github.com/user-attachments/assets/05c66a03-979f-4ac9-8568-e3e18d3004e8" />

#
Tienes que sacar todos esos documentos y soltarlos libres todos dentro de la carpeta raíz, porque luego Vite hace una copia y escaneo.

(Aquí: ya están todos sueltos, en la carpeta raíz).

#
<img width="922" height="447" alt="image" src="https://github.com/user-attachments/assets/c72f8e11-1b7f-4e0d-aeb1-0bacf7e1a38e" />

#
Más adelante te enseñaré cómo redirigir el escaneo. Vite copia todos los documentos y crea una nueva carpeta llamada `dist` y anexa todos los documentos dentro. Esos documentos vienen con el nombre ofuscado y vuelto una sopa de letras.

**Ejemplo visual de cómo te quedarán los archivos escaneados y copiados:**

#
<img width="211" height="145" alt="image" src="https://github.com/user-attachments/assets/4e6aa6f1-71b9-4e9a-a1d5-6e9f89ac9775" />

#
> **Punto Crítico (Lección Aprendida):** Si tienes una carpeta de iconos llamada `icons`, debes moverlas a la CARPETA de `public`
para evitar errores de lectura a la hora de llamar mediante un enlace una foto o icono de imagen que tengas en la carpeta `icons`.
> * **Estructura Correcta:** `public/icons/imagen.png`
> * **Referencia en Código:** `<img src="public/icons/imagen.png">` (Debes tener la ruta completa en el archivo HTML).

### Archivos Requeridos de Firebase
Un proyecto listo para Firebase debe tener estos archivos en la raíz:

#
> `.firebaserc`
<img width="153" height="181" alt="image" src="https://github.com/user-attachments/assets/864f8c7a-f722-4336-8222-5e04810f7a0a" />

#
> `firebase.json`
<img width="153" height="184" alt="image" src="https://github.com/user-attachments/assets/e8273c19-2277-4630-bd7b-0db103d307f2" />

#
* `firestore.rules` / `database.rules.json` (Si aplica, siempre van en la carpeta raíz).

#
<img width="158" height="206" alt="image" src="https://github.com/user-attachments/assets/004489eb-391b-44bb-bcb8-b793a082f3be" />

* Carpeta `functions/` (Si utilizas Cloud Functions).

---

## 3. PASO 2: Instalación del Entorno de Construcción (Node.js)

Para que nuestro motor pueda compilar, ofuscar y Minificar el código, es obligatorio tener instalado Node.js en la computadora.

```
*(Si ya lo tiene instalado, puede saltar este paso de inicialización)*.

### 2.1 Descarga e Instalación de Node.js
1.  Dirígete a la página oficial: nodejs.org.
2.  Descarga la versión recomendada LTS.
3.  Ejecuta el instalador.
4.  Verifica la instalación ejecutando estos comandos en la terminal CMD:

`node -v`: (este comando sirve para ver qué versión del programa tenemos instalada).
```

#
<img width="556" height="139" alt="image" src="https://github.com/user-attachments/assets/94f59ee7-24a6-4913-a9ee-d99738741de2" />

---
*(Si observas el resultado del primer comando, fíjate que en este momento la versión de node que estoy usando es la v22.13.1)*.

`npm -v`: (este comando sirve para ver qué versión del programa tenemos instalada).

<img width="268" height="63" alt="image" src="https://github.com/user-attachments/assets/be99f082-62c1-4c22-8071-1e015f166b66" />

#
### Instalación de dependencias y configuración para el despliegue

1.  **Crear el archivo `package.json` por defecto:**
    Abre la terminal de tu editor y ejecuta el siguiente comando:
    `npm init -y`: (Inicializa el entorno y genera el archivo).

<img width="964" height="243" alt="image" src="https://github.com/user-attachments/assets/6c21cae9-e05c-4a74-9547-d69851f9d3d3" />

#
    El Resultado en el explorador de archivos te va a dejar un documento así en tu explorador de archivos:

<img width="138" height="157" alt="image" src="https://github.com/user-attachments/assets/e3e52e85-e702-43bb-9954-a5f541d47d6f" />

---
2.  **Instalar el motor y los plugins:**
   `npm install vite vite-plugin-javascript-obfuscator vite-plugin-html terser --save-dev`

    *(Primera descarga)*:
    
<img width="948" height="239" alt="image" src="https://github.com/user-attachments/assets/b0372070-3895-4813-b030-806ccb570ae4" />

    *(Como podrás observar, puede decir que tiene vulnerabilidades, se actualizarán más adelante)*.

    *(Segunda descarga)*:
    
<img width="963" height="178" alt="image" src="https://github.com/user-attachments/assets/2c978e8e-ac34-46e6-8a4e-99eb1f8e5204" />

> 4. `npm audit fix`: (Analiza y corrige automáticamente las vulnerabilidades).

    *(En 1ra etapa)*:

<img width="963" height="209" alt="image" src="https://github.com/user-attachments/assets/3c378df9-2b12-4432-a359-bf6a1b643c76" />

    *(2da y última etapa)*:
 
<img width="848" height="335" alt="image" src="https://github.com/user-attachments/assets/ae3f9f2f-72f5-4a5c-a06d-6ddbced649e0" />

---

## 4. PASO 3: Evolución de la Configuración de Vite (vite.config.js)

Este es el cerebro de la operación.

### Versión A: Configuración Estándar (Estilo Google - Rápida)
**Uso:** Ofuscación básica, renombrado de variables a letras simples (a, b, c), alta compatibilidad.

```markdown
```javascript
// vite.config.js - VERSIÓN A
import { defineConfig } from 'vite';
import javascriptObfuscator from 'vite-plugin-javascript-obfuscator';
import { createHtmlPlugin } from 'vite-plugin-html';
import { resolve } from 'path';

export default defineConfig({
  plugins: [
    // Minificación de HTML básica
    createHtmlPlugin({ minify: true }),
    // Ofuscador Estilo Google (Mangling)
    javascriptObfuscator({
      compact: true,
      controlFlowFlattening: false, // Apagado para evitar conflictos iniciales
      identifierNamesGenerator: 'mangled', // Usa a, b, c
      stringArray: false, // No encripta textos
    }),
  ],
  build: {
    target: 'esnext',
    minify: 'terser', // Compresión estándar
    rollupOptions: {
      input: {
        // Lista genérica de documentos
        main: resolve(__dirname, 'index.html'),
        login: resolve(__dirname, 'login.html'),
        panel: resolve(__dirname, 'panel.html'),
      }
    }
  }
});
```

### Versión B: Configuración de Alta Seguridad y Línea Infinita (RECOMENDADA)
**Uso:** Máxima protección. Encripta textos, usa nombres hexadecimales, desordena la lógica, protege contra depuración (F12) y aplasta todo en una sola línea de texto.

```javascript
// vite.config.js - VERSIÓN B (Máxima Seguridad)
import { defineConfig } from 'vite';
import javascriptObfuscator from 'vite-plugin-javascript-obfuscator';
import { createHtmlPlugin } from 'vite-plugin-html';
import { resolve } from 'path';

export default defineConfig({
  plugins: [

    // 1. MINIFICACIÓN AGRESIVA DE HTML (Sopa de letras)
    createHtmlPlugin({
      minify: {
        collapseWhitespace: true, // Elimina espacios y saltos de línea
        removeComments: true, // Borra comentarios
        minifyCSS: true, // Comprime CSS interno
        minifyJS: true, // Comprime JS interno
        removeRedundantAttributes: true,
        useShortDoctype: true,
      }
    }),

    // 2. OFUSCACIÓN AGRESIVA DE JS
    javascriptObfuscator({
      compact: true, // Aplasta el JS
      // Encriptación de Textos (Strings)
      stringArray: true,
      stringArrayEncoding: ['base64', 'rc4'], // Doble encriptación ilegible
      stringArrayThreshold: 1, // Encriptar el 100% de los textos
      // Ofuscación de Nombres
      identifierNamesGenerator: 'hexadecimal', // Usa _0x1a2b3c
      renameGlobals: true, // Renombra variables globales
      // Protección y Desorden Lógico
      debugProtection: true, // Congela la página si abren F12
      debugProtectionInterval: 4000,
      disableConsoleOutput: true, // Elimina console.logs
      controlFlowFlattening: true, // Desordena el flujo del código
      controlFlowFlatteningThreshold: 0.5,
      // Nombres reservados para no romper APIs (Firebase, etc.)
      reservedNames: ['^fb', '^firebase', '^Vite', 'require']
    }),
  ],
  build: {
    target: 'esnext',

    // 3. MINIFICACIÓN EXTREMA FINAL (Línea Infinita)
    minify: 'terser',
    terserOptions: {
      format: {
        comments: false, // Garantiza cero comentarios
      },
      compress: {
        drop_console: true, // Elimina console.logs residuales
        drop_debugger: true
      }
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

---

## 5. Configuración del Motor de Hosting (Firebase)

Antes de construir el proyecto, debes indicarle a Firebase qué carpeta debe subir a internet. Por defecto, Firebase busca una carpeta llamada `public`, pero Vite generará el código ofuscado en una carpeta llamada `dist`.

#
Abre el archivo `firebase.json` ubicado en la raíz de tu proyecto.

<img width="152" height="184" alt="image" src="https://github.com/user-attachments/assets/54d5876e-83f3-4394-84f3-9e700243d415" />

#
Localiza la sección "hosting" y cambia el valor `"public"` por `"dist"`.

<img width="473" height="442" alt="image" src="https://github.com/user-attachments/assets/eff8ab9b-c294-4e2a-935a-9bd0ff7b7da8" />

---

## 6. Construcción, Ofuscación y Minificación Total (The Build)

`npx vite build`: Ejecuta tres procesos simultáneos:
1.  Aplica la ofuscación extrema al JavaScript.
2.  Comprime el HTML.
3.  Minifica automáticamente todos los archivos CSS. Finalmente, empaqueta todo el resultado en la nueva carpeta `dist`.

*(A Continuación, verás la compresión y Minificación de todos los archivos de tu proyecto transformados a nombres extraños, esto es totalmente normal)*.

#
**1ra parte:**

<img width="825" height="205" alt="image" src="https://github.com/user-attachments/assets/ee50d5cd-5db7-40b5-b735-41be57e071b5" />

#
**2da parte:**

<img width="589" height="180" alt="image" src="https://github.com/user-attachments/assets/efc591cc-9c56-4ac9-826f-c383b99e1c42" />

---
Al final te dejará un resultado así en la terminal:

<img width="739" height="137" alt="image" src="https://github.com/user-attachments/assets/8c1be4af-3027-463f-8e53-aac296bd09d7" />

---
En el explorador de archivos se creará automáticamente la carpeta `dist`:

<img width="141" height="191" alt="image" src="https://github.com/user-attachments/assets/0afa814c-b696-4373-a306-644aa47b0631" />

#
> **Error Común:** Si la terminal lanza un error amarillo o rojo como `Unable to parse HTML`, significa que tu HTML tiene un error de sintaxis.

<img width="909" height="189" alt="image" src="https://github.com/user-attachments/assets/70d90a38-90bf-443e-815f-f55488f4e3aa" />

#
Los archivos HTML ahora están aquí, y una carpeta `assets` contiene los JS y CSS con nombres raros.

<img width="571" height="525" alt="image" src="https://github.com/user-attachments/assets/c3ffdc82-d09b-43d7-a09a-6ef2a314c5bb" />

---

## 7. PASO 4: Despliegue y Verificación Final

Último paso: decirle a Firebase que suba la carpeta `dist` (la ofuscada).

#
**Comando de Despliegue:**
`firebase deploy --only hosting`

<img width="851" height="175" alt="image" src="https://github.com/user-attachments/assets/fc5a3a78-14d8-4aab-af2a-fe2a6c47888c" />


**Verificación de la "Línea Infinita":**
Entra a tu página web ya subida, haz clic derecho y selecciona "Ver código fuente de la página".
Obtuve una compresión del HTML llevando toda sintaxis a una sola línea infinita.

<img width="1233" height="42" alt="image" src="https://github.com/user-attachments/assets/ed7848cb-ec2a-44b2-983d-da0660938d5e" />

---
FIN DE LA DOCUMENTACIÓN
GRACIAS POR LLEGAR HASTA AQUÍ ESTE DOCUMENTO FUE CRADO POR: (IMPERIAL-E).
```
