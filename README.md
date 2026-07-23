<p align="center">
  <img src="images/logo.png" alt="La Mesa" width="180">
</p>

# La Mesa — Coach de Poker Texas Hold'em

Una webapp de entrenamiento de Texas Hold'em en la que juegas contra 5 rivales controlados por IA, cada uno con su propio estilo, mientras un "coach" analiza tu forma de jugar mano a mano y te dice qué haces bien y qué deberías mejorar.

Es un único archivo HTML autocontenido (HTML + CSS + JavaScript), pensado para funcionar como página estática en GitHub Pages, sin backend propio.

## Características

**Mesa de juego**
- **Cash Game** con ciegas fijas (1/2), y **Torneo** sin recompras donde las ciegas suben cada 10 manos (se doblan: 1/2 → 2/4 → 4/8...) hasta que solo queda un jugador.
- Tamaño de mesa seleccionable: **Heads-Up** (1 rival), **6-Max** (5 rivales, por defecto) y **9-Max** (8 rivales) — cada uno con sus propios nombres de posición (en 9-max se añaden UTG+1, UTG+2, LJ y HJ) y disposición de asientos.
- Motor de póker completo: evaluador de manos de 7 cartas, botes secundarios (side pots) para situaciones de all-in múltiple entre varios jugadores.
- Al terminar cada mano se destaca al ganador principal y la combinación exacta de cartas ganadora; si hay reparto de bote, el resto de implicados aparece con una etiqueta de "Reparto" y la cantidad exacta que recibe cada uno.

**Rivales con IA**
- 8 personalidades distintas (roca, sólido/TAG, suelta/LAG, pasivo, maníaco, nit extremo, shark ajustada, recreativo), cada una con su propio rango de apertura, frecuencia de farol y de 3-bet. Según el tamaño de mesa elegido, se usan tantas como rivales haga falta.
- Cada rival deriva su estilo lentamente con el tiempo y reacciona a rachas recientes (más agresivo tras ganar, más conservador tras perder), para que no sean 100% predecibles ni siempre iguales, incluso en partidas largas 1 contra 1.

**Cuaderno del Coach**
- **Mi perfil**: barras de Tight/Loose, Pasivo/Agresivo y Frecuencia de farol, con una etiqueta de estilo (Roca, TAG, LAG, Maníaco, Estación de pago).
- **Estadísticas**: VPIP, PFR, 3-Bet, factor de agresión, % de farol, % que llegas a showdown y % que ganas ahí, balance de la sesión, y una **gráfica de evolución de fichas** mano a mano.
- **Consejos**: avisos accionables sobre patrones a corregir (limpear en vez de subir, jugar demasiado suelto en posiciones tempranas, pagar de más...) y también aciertos a reforzar (saber retirarte a tiempo, farolear en los momentos correctos, cobrar bien las manos fuertes, buen ajuste según la posición). Todo generado por reglas fijas basadas en tus propias estadísticas — sin IA generativa ni llamadas externas.
- **Historial**: cada mano jugada, con un botón "Ver mano" que despliega la transcripción completa de la acción (ciegas, subidas, calles, resultado) para repasarla con calma.
- **Jugadores**: ranking de los usuarios registrados con su tipo de jugador calculado, para comparar estilos.
- Botón de términos (❓) con un glosario de todas las estadísticas, posiciones y tipos de jugador, pensado para quien no conozca la jerga del póker.

**Cuentas y progreso en la nube**
- Registro e inicio de sesión por email y contraseña (Firebase Authentication).
- Fichas, historial de manos y estadísticas se guardan en Firestore y se recuperan al iniciar sesión desde cualquier dispositivo.
- Botones separados para reiniciar solo las fichas o solo las estadísticas — ninguna otra acción de la app borra datos sin que el usuario lo pida explícitamente.
- Ventana de "Privacidad y datos" explicando qué se guarda, para qué, dónde y cómo borrarlo.

**Como app**
- Interfaz optimizada para móvil.
- Logo e icono propios, favicon, y soporte para "Añadir a pantalla de inicio" (manifest + iconos, se comporta como una app instalada).
- Vista previa cuidada al compartir el enlace (Open Graph / Twitter Card con imagen propia).
- Tema visual propio (mesa de fieltro, tipografía serif/mono, sin plantillas genéricas).

## Cómo funciona (y qué NO es)

Los consejos del Coach se generan con reglas fijas basadas en umbrales estadísticos habituales en el análisis de poker (VPIP, PFR, factor de agresión, etc.) — no hay ninguna IA generativa ni llamada a un servicio externo implicada en los consejos. Toda la lógica corre en el propio navegador.

Los rivales tampoco son un solver GTO: son una IA heurística con reglas, deriva de personalidad y algo de aleatoriedad, pensada para practicar fundamentos y detectar patrones propios, no para ser matemáticamente óptima.

## Puesta en marcha

Este proyecto necesita un proyecto propio y gratuito de Firebase para las cuentas y el guardado en la nube.

1. **Crea un proyecto en Firebase**
   Ve a [console.firebase.google.com](https://console.firebase.google.com) → *Agregar proyecto*.

2. **Activa el inicio de sesión por email/contraseña**
   *Compilación → Authentication → Sign-in method → Correo electrónico/contraseña.*

3. **Crea la base de datos**
   *Compilación → Firestore Database → Crear base de datos* (modo de producción). En la pestaña *Reglas*, usa:
   ```
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /players/{uid} {
         allow read: if request.auth != null;
         allow write: if request.auth != null && request.auth.uid == uid;
       }
     }
   }
   ```

4. **Registra una app web y copia tu configuración**
   *Configuración del proyecto → Tus apps → icono `</>`.* Pega el objeto `firebaseConfig` que te da Firebase dentro de `FIREBASE_CONFIG`, cerca del principio del `<script>` del archivo.

5. **Autoriza tu dominio**
   *Authentication → Sign-in method → Authorized domains* → añade el dominio donde publiques la app (por ejemplo `tu-usuario.github.io`).

## Desplegar en GitHub Pages

1. Sube el archivo `.html`, la carpeta `images/` y `manifest.json` a la raíz del repositorio, manteniendo esta estructura:
   ```
   ├── index.html         (o poker_coach.html)
   ├── manifest.json
   └── images/
       ├── logo.png
       ├── icon-192.png
       ├── icon-512.png
       └── og-image.png
   ```
   Si el archivo HTML no se llama `index.html`, la URL raíz del sitio no lo cargará automáticamente — o lo renombras, o entras escribiendo el nombre completo del archivo al final de la URL.
2. En el repositorio: *Settings → Pages → Source: Deploy from a branch* → elige la rama y la carpeta `/ (root)`.
3. Espera un par de minutos y entra en la URL que te da GitHub (`https://tu-usuario.github.io/nombre-repo/`).

## Estructura del proyecto

```
├── index.html       # Toda la aplicación: motor de juego, IA, coach, cuentas y estilos
├── manifest.json     # Metadatos para "Añadir a pantalla de inicio"
├── images/
│   ├── logo.png       # Logo principal (usado en la cabecera y el login)
│   ├── icon-192.png    # Icono para pantalla de inicio (Android/PWA)
│   ├── icon-512.png    # Icono para pantalla de inicio (alta resolución)
│   └── og-image.png    # Imagen de vista previa al compartir el enlace
├── README.md
└── LICENSE
```

## Notas de seguridad

- La `apiKey` de Firebase es una clave de cliente pensada para ser pública; la protección real de los datos la dan las reglas de Firestore, no el secreto de esa clave.
- No hay contraseñas de administrador ni credenciales de servidor en este repositorio. No subas claves de tipo "cuenta de servicio" (por ejemplo, las que empiezan por `AQ.`) — no son necesarias ni compatibles con este proyecto.

## Licencia

Este proyecto está bajo licencia [CC BY-NC 4.0](https://creativecommons.org/licenses/by-nc/4.0/deed.es) —
puedes compartirlo y modificarlo dando crédito a KaiWolven, pero no para uso comercial.
Consulta el archivo [LICENSE](LICENSE) para más detalles.
