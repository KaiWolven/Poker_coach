# La Mesa — Coach de Poker Texas Hold'em
 
Una webapp de entrenamiento de Texas Hold'em en la que juegas contra 5 rivales controlados por IA, cada uno con su propio estilo, mientras un "coach" analiza tu forma de jugar mano a mano y te dice qué haces bien y qué deberías mejorar.
 
Es un único archivo HTML autocontenido (HTML + CSS + JavaScript), pensado para funcionar como página estática en GitHub Pages, sin backend propio.
 
## Características
 
**Mesa de juego**
- Cash game y modo Torneo (sin recompras, hasta que solo quede un jugador), seleccionables desde la cabecera.
- Mesa de 6 jugadores (6-max): tú y 5 rivales controlados por IA.
- Motor de póker completo: evaluador de manos de 7 cartas, botes secundarios (side pots) para situaciones de all-in múltiple, reparto de empates.
- Al terminar cada mano se destaca visualmente al ganador y la combinación exacta de cartas ganadora; si hay reparto de bote, se indica por separado con la cantidad exacta que recibe cada jugador implicado.
**Rivales con IA**
- 5 personalidades distintas (roca, sólido/TAG, suelta/LAG, pasivo, maníaco), cada una con su propio rango de apertura, frecuencia de farol y de 3-bet.
- Cada rival deriva su estilo lentamente con el tiempo y reacciona a rachas recientes (más agresivo tras ganar, más conservador tras perder), para que no sean 100% predecibles ni siempre iguales.
**Cuaderno del Coach**
- **Mi perfil**: barras de Tight/Loose, Pasivo/Agresivo y Frecuencia de farol, con una etiqueta de estilo (Roca, TAG, LAG, Maníaco, Estación de pago).
- **Estadísticas**: VPIP, PFR, 3-Bet, factor de agresión, % de farol, % que llegas a showdown y % que ganas ahí, balance de la sesión.
- **Consejos**: avisos accionables sobre patrones a corregir (limpear en vez de subir, jugar demasiado suelto en posiciones tempranas, pagar de más, etc.) y también aciertos a reforzar (saber retirarte a tiempo, farolear en los momentos correctos, cobrar bien las manos fuertes, buen ajuste según la posición).
- **Historial**: registro de cada mano jugada, con nota si hubo algo destacable.
- **Jugadores**: ranking de los usuarios registrados con su tipo de jugador calculado, para comparar estilos.
- Botón de términos (❓) con un glosario de todas las estadísticas, posiciones y tipos de jugador, pensado para quien no conozca la jerga del póker.
**Cuentas y progreso en la nube**
- Registro e inicio de sesión por email y contraseña (Firebase Authentication).
- Fichas, historial de manos y estadísticas se guardan en Firestore y se recuperan al iniciar sesión desde cualquier dispositivo.
- Botones separados para reiniciar solo las fichas o solo las estadísticas — nunca se borran datos sin que el usuario lo pida explícitamente.
**Otros**
- Interfaz optimizada para móvil.
- Tema visual propio (mesa de fieltro, tipografía serif/mono, sin plantillas genéricas).
## Cómo funciona (y qué NO es)
 
Los consejos del Coach se generan con reglas fijas basadas en umbrales estadísticos habituales en el análisis de poker (VPIP, PFR, factor de agresión, etc.) — no hay ninguna IA generativa ni llamada a un servicio externo implicada en los consejos. Toda la lógica corre en el propio navegador.
 
Los rivales tampoco son un solver GTO: son una IA heurística con reglas y algo de aleatoriedad, pensada para practicar fundamentos y detectar patrones propios, no para ser matemáticamente óptima.
 
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
 
1. Sube el archivo `.html` a la raíz del repositorio (o renómbralo a `index.html` para que cargue directamente en la raíz del sitio).
2. En el repositorio: *Settings → Pages → Source: Deploy from a branch* → elige la rama y la carpeta `/ (root)`.
3. Espera un par de minutos y entra en la URL que te da GitHub (`https://tu-usuario.github.io/nombre-repo/`).
## Estructura del proyecto
 
```
├── index.html   # Toda la aplicación: motor de juego, IA, coach, cuentas y estilos
└── README.md
```
 
## Notas de seguridad
 
- La `apiKey` de Firebase es una clave de cliente pensada para ser pública; la protección real de los datos la dan las reglas de Firestore, no el secreto de esa clave.
- No hay contraseñas de administrador ni credenciales de servidor en este repositorio. No subas claves de tipo "cuenta de servicio" (por ejemplo, las que empiezan por `AQ.`) — no son necesarias ni compatibles con este proyecto.
## Licencia
 
Añade aquí la licencia que prefieras (por ejemplo, [MIT](https://choosealicense.com/licenses/mit/)) si quieres que otros puedan reutilizar o modificar el código libremente.
