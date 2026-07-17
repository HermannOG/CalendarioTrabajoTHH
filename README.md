# Traslados Aeropuerto

App web (PWA) para gestionar los traslados de pasajeros: aeropuerto → hotel,
aeropuerto → empresa, y viceversa. Se puede editar desde cualquier lugar
(celular, computadora, tablet) porque los datos viven en Firebase Firestore
y se sincronizan en tiempo real entre todos los que abran el enlace.

## Qué incluye

- **Próximos**: lista de los traslados pendientes desde hoy en adelante, ordenados por fecha/hora.
- **Calendario**: vista mensual, toca un día para ver los traslados de esa fecha.
- **Todos**: historial completo (pendientes, completados, cancelados).
- Formulario para cada traslado con: pasajero(s), tipo de traslado, número de vuelo,
  fecha, hora, origen, destino, conductor, vehículo, estado y notas.
- Buscador por pasajero, vuelo, origen/destino, conductor o notas.
- Funciona como app instalable en el celular (PWA) — "Agregar a pantalla de inicio".

## 1. Crear el proyecto de Firebase

1. Ve a https://console.firebase.google.com y crea un proyecto nuevo (gratis, plan Spark).
2. Dentro del proyecto, ve a **Compilación → Firestore Database → Crear base de datos**.
   Elige modo **producción** (ya trae reglas seguras propias) y la región más cercana.
3. Ve a **Compilación → Authentication → Comenzar** y activa el proveedor **Anónimo**.
   (La app inicia sesión anónima automáticamente para que solo quien tenga el enlace
   pueda leer/escribir los datos — no requiere que la gente cree cuentas ni contraseñas.)
4. Ve a **Configuración del proyecto (⚙️) → General → Tus apps → Agregar app → Web (`</>`)**.
   Ponle un nombre (ej. "traslados-web") y copia el objeto `firebaseConfig` que te muestra.

## 2. Conectar la app a tu proyecto

Abre `index.html` y busca esta sección (cerca de la mitad del archivo):

```js
const firebaseConfig = {
  apiKey:            "TU_API_KEY",
  authDomain:        "tu-proyecto.firebaseapp.com",
  projectId:         "tu-proyecto",
  storageBucket:     "tu-proyecto.firebasestorage.app",
  messagingSenderId: "000000000000",
  appId:             "1:000000000000:web:xxxxxxxxxxxxxxxxxxxxxx"
};
```

Reemplázala con los valores que copiaste en el paso 1.4. Esta `apiKey` es pública
por diseño (así funcionan las apps web de Firebase); lo que realmente protege tus
datos son las reglas de `firestore.rules` y el login anónimo.

## 3. Subir las reglas de seguridad de Firestore

Necesitas Node.js instalado. Luego, desde la carpeta del proyecto:

```bash
npm install -g firebase-tools
firebase login
firebase init
```

En `firebase init`, selecciona **Firestore** y **Hosting**, elige tu proyecto existente,
y cuando pregunte por el archivo de reglas usa el `firestore.rules` que ya viene aquí
(no lo sobrescribas). Para Hosting, indica que la carpeta pública es `.` (la raíz del
proyecto) y responde "No" a configurarlo como single-page app.

Publica las reglas:

```bash
firebase deploy --only firestore:rules
```

## 4. Subir el código a GitHub

```bash
git init
git add .
git commit -m "Primera versión: app de traslados"
git branch -M main
git remote add origin https://github.com/TU_USUARIO/traslados.git
git push -u origin main
```

**Importante:** este `.gitignore` ya excluye archivos sensibles como
`firebase_key.json` (clave de cuenta de servicio). Nunca subas ese tipo de
archivo a un repositorio, ni siquiera privado.

## 5. Publicar (Firebase Hosting)

```bash
firebase deploy --only hosting
```

Te dará una URL tipo `https://tu-proyecto.web.app` — ese es el enlace que
compartes con tu equipo. Cualquiera que lo abra puede ver y editar los
traslados en tiempo real, desde el celular o la computadora.

## Alternativa: publicar con GitHub Pages en vez de Firebase Hosting

Si prefieres no usar Firebase Hosting, puedes activar **GitHub Pages** en la
configuración del repositorio (Settings → Pages → Deploy from branch → main).
Firestore (la base de datos) sigue funcionando igual porque la conexión se hace
desde el navegador del usuario, independientemente de dónde esté alojado el HTML.

## Estructura de datos (colección `traslados` en Firestore)

Cada documento representa un viaje:

```json
{
  "pasajeros": "Juan Pérez, María Gómez",
  "tipo": "aeropuerto-hotel",
  "vuelo": "AA1234",
  "fecha": "2026-07-20",
  "hora": "14:30",
  "origen": "Aeropuerto SJO",
  "destino": "Hotel Presidente",
  "conductor": "Carlos R.",
  "vehiculo": "Van placa CRC123",
  "estado": "pendiente",
  "notas": "Grupo de 3 personas, equipaje de golf"
}
```

## Personalización rápida

- **Colores/tema**: variables CSS al inicio de `index.html` (`:root { --bg: ... }`).
- **Tipos de traslado**: objeto `TIPOS` dentro del `<script>` de `index.html`.
- **Icono de la app / splash**: agrega tus propios íconos en la carpeta `icons/`
  (192x192 y 512x512 px) referenciados en `manifest.json`.
