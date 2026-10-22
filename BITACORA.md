### Bitácora de Aprendizaje - DevSecOps Lab

### 2026-09-19 (sábado) - · ~4 h

-  **Hice:** Instalado OrbStack + trivy/gitleaks/semgrep en el Mac. Levantado
  Juice Shop y crAPI (11 contenedores, todos healthy). Montado el pipeline
  de seguridad en GitHub Actions con 3 capas y conseguido el verde.
-  **Se rompió:**
  - (mañana, OrbStack) Los nombres .orb.local no resuelven por DNS.
    Uso localhost:3000 y sigo.
  - (tarde, GitHub Actions) Pipeline en rojo por una errata mía (`--erro`).
    El propio log decía la solución.
  - (tarde, GitHub Actions) Pipeline en rojo otra vez, este con hallazgo
    real de Semgrep. Los contenedores no se tocaron por la tarde.
-  **Aprendí:** Semgrep encontró 4 fallos **en mi propio pipeline**: las acciones
  estaban fijadas a etiquetas mutables (@v4, @v2, @master). Si comprometen esa
  cuenta, mi CI ejecuta código ajeno con mis secretos delante. Arreglado fijando
  cada acción a un SHA de 40 caracteres. Es la misma lección que los 11
  contenedores que descargué esta mañana sin saber qué había dentro:
  **cadena de suministro**.
  Distinguir "la herramienta ha fallado" de "la herramienta ha encontrado algo"
  es la habilidad clave. Los dos son rojos y no significan lo mismo.
-  **Pendiente:** Pasar Trivy a las imágenes de crAPI y ver qué sale.
  Mirar lo del DNS de .orb.local algún día (no bloquea nada).
  En /rest/user/whoami el cliente decide qué campos pide con `?fields=`.
  ¿Quién valida eso, el cliente o el servidor?

### 2026-09-21 (lunes) — · ~50 min

-  **Hice:** Primer apartado "Fundamentos de Docker" del curso de Platzi. 
-  **Se rompió:** Nada.
-  **Aprendí:** Los comandos básicos de Docker (`--version`, `images`, `run`,
  `info`, `ps`) y sobre todo el reflejo del `--help`, que sirve igual para
  trivy, semgrep, terraform y kubectl.  
-  **Pendiente:** `docker info` tiene un apartado de Security Options.

### 2026-09-22 (martes) — · ~50 min

-  **Hice:** Descargar el dotnet en brew, crear la API, y el archivo "Dockerfile" 
-  **Se rompió:** El build falló (NETSDK1045): mi SDK local es .NET 10 y el
           contenedor traía el SDK 8. Deriva de versiones entre mi máquina
           y el entorno de build. Arreglado alineando la imagen base.
-  **Aprendí:** Los comandos básicos de Docker para compilar la imagen(`docker build .`, `images`, `docker build -t nombreImagen:latest .`)  y para eliminar la imagen `docker rmi -f` nombre de la imagen   
-  **Pendiente:** Nada.

### 2026-09-23 (miércoles) — · ~40 min

-  **Hice:** Ejecutar trivy image mi-api:latest, para detectar los CVE críticos y altos. 
-  **Se rompió:** Trivy cortó al descargar su BD de vulnerabilidades (GOAWAY del  servidor). Reintenté y fue. Lección: el escáner depende de una BD externa; en un pipeline real eso hay que cachearlo.
-  **Aprendí:** Imagen base: 40 CVE (5 low, 35 medium, 0 high, 0 critical)
           y 1,36 GB. De ese peso, mi código son unos pocos KB; el resto
           es SDK que nunca se ejecuta en producción.
           Cero críticas NO es seguro: Trivy no ve mi código, ni que el
           contenedor corre como root.  
-  **Pendiente:** Nada.

### 2026-09-24 (jueves) — · ~40 min

-  **Hice:** Multi-stage build con imagen de runtime y usuario no root. 
-  **Se rompió:** Nada. 
-  **Aprendí:** 40 → 13 CVE y 1,36 GB → 369 MB. Las 5 bajas no se movieron:
           son del sistema base, común a ambas. Lo que quité fue el
           toolchain. Reducir superficie > parchear hallazgos.
           Lo de no correr como root no lo cuenta Trivy y es lo que más
           importa de hoy.  
-  **Pendiente:** Nada.

### 2026-09-25 (viernes) — · ~30 min

-  **Hice:** Comparar los CVE de los dos días anteriores y realizar una tabla de comparación en el README del proyecto. 
-  **Se rompió:** Nada. 
-  **Aprendí:** Crear tablas en el archivo README del proyecto. 
-  **Pendiente:** Nada.

### 2026-09-26 (sábado) — · ~3 h

-  **Hice:** Integrada la construcción y el escaneo de la imagen en el pipeline.
           5 capas automáticas en cada push. Verde. 
-  **Se rompió:** 8 veces. Por orden: copiar sin adaptar / "rum" en vez de "run" /
           indentación y comillas al pegar / parámetro "ref" inventado por
           leer una palabra del error en vez de la frase / uses con un comando
           dentro / ruta del Dockerfile (contexto de build) / quitar el bloque
           with al arreglar el uses. 
-  **Aprendí:** Una acción es un envoltorio: lee en el log qué comando construyó
           y trabaja hacia atrás hasta el parámetro que lo produjo.
           Arreglar una cosa y romper otra en el mismo cambio = por eso
           los commits pequeños.  
-  **Pendiente:** Nada.

### 2026-09-28 (lunes) — · ~20 min

- **Hice:** Ver los vídeo de Platzi, convertir imagen en servicio web y gestión de imagenes (videos: 8-9)
- **Se rompió:** Nada.
- **Aprendí:** aprendí el comando -> docker run -it --rm -d -p 8080:80 --name mi-api mi-api
- **Pendiente:** PAUSA de 5 días por viaje. Al volver: clases 8-11 de Docker
  (gestión de imágenes y contenedores, capas y caché).

### 2026-09-29 a 10-03 · PAUSA PLANIFICADA

  - Viaje. Sin sesiones. No es ruptura de cadena: pausa anunciada y retomada.

### 2026-10-05 (lunes) — · ~45 min · vuelta de la pausa

- **Hice:** Clases 8 y 9 de Docker (gestión de contenedores e imágenes).
  Levantado el contenedor de mi API endurecida y accedido a
  `/weatherforecast`. Renombrado una imagen con `docker image tag`.

- **Se rompió:** El navegador no mostraba nada aunque el contenedor corría.
  Copié `-p 8080:80` del vídeo sin adaptarlo — ese 80 era de nginx, y mi API
  escucha en el 8080. `docker logs` me lo dijo en la primera línea:
  *Now listening on: http://[::]:8080*. Un cambio y a funcionar.

- **Aprendí:**
  - `docker logs` antes de tocar nada. Preguntarle al contenedor qué hace
    en vez de adivinar.
  - `docker image tag` NO copia nada: crea un segundo nombre sobre la misma
    imagen, con el mismo IMAGE ID. Una etiqueta es un puntero que se puede
    mover; un digest es contenido. **Es lo mismo que el hallazgo de Semgrep
    del día 1 con `trivy-action@master`, visto desde el otro lado.**
  - Swagger viene desactivado en Production a propósito: exponer la
    documentación interactiva es regalar el mapa de endpoints.
  - Volver a copiar un ejemplo sin adaptarlo. Error nº1 de mi lista de ocho,
    repetido dos semanas después.

- **Pendiente:**
  - Clase 11 (desplegar una API en Docker) — mañana por la mañana.
  - Los datos de la plantilla son absurdos: 38 °C etiquetado como "Chilly".
    Fallo de lógica de negocio: válido en tipos, imposible en significado.
    Ningún escáner lo detecta. Guardar para la semana 10 (IDOR).

### 2026-10-06 (martes) —· ~2h15

- Hice: Vídeos 10-13 de Docker. Creada red propia (lab-net) y volumen con
  nombre (pgdata). Postgres y mi API en la misma red, comunicándose por
  nombre. Verificado que los datos sobreviven a destruir el contenedor.
- Se rompió: Paré pgdb en vez de mi-api. Leí el error, docker ps, y lo vi.
- Aprendí:
  - Los contenedores se encuentran POR NOMBRE dentro de una red propia, no
    por localhost. localhost dentro de un contenedor es ese contenedor.
  - El porqué: dentro de la red responde el DNS interno de Docker
    (127.0.0.11); fuera responde otro resolvedor que no conoce esos nombres.
    Cada red es su propio universo de nombres.
  - -p 127.0.0.1:... restringe la publicación en el host. No tiene nada que
    ver con que dos contenedores se vean.
  - Postgres sin -p: una base de datos no necesita puerta al exterior.
    En docker ps se ve: la flecha -> solo aparece en lo publicado.
  - Vídeos 10 y 11 poco aportaron: ya lo había hecho en la práctica.
- Pendiente: El -e POSTGRES_PASSWORD queda en el historial del shell y en
  docker inspect. Comprobarlo mañana.

### 2026-10-07 (miércoles) — · ~45 min

- Hice: Escaneo de secretos con trivy sobre mi-api:hardened (limpia) y
  postgres:14 (1 hallazgo). Inspección de imagen vs contenedor.
- Se rompió: Errata al copiar el nombre de la imagen. La leí en el error.
  Tercera errata del mes resuelta sola; ya no me bloquean.
- Aprendí:
  - Trivy escanea IMÁGENES. Mi contraseña está en el CONTENEDOR
    (docker inspect pgdb → Config.Env), en texto plano. El escáner no la ve.
  - El hallazgo de postgres no era mi clave: era ssl-cert-snakeoil.key,
    una clave de relleno de Debian. HIGH, real, y sin importancia: no
    protege nada y la tiene todo el mundo. Primer falso positivo triado.
  - Trivy enmascara el valor del secreto a propósito: un informe que
    imprime secretos en los logs del CI es una vulnerabilidad.
  - history vacío NO prueba que no esté: zsh no vuelca hasta cerrar sesión.
    Resultado negativo de herramienta que no domino = no sé, no "no está".
- Pendiente: ¿cómo se pasa entonces una contraseña a un contenedor sin
  que acabe en docker inspect? (Docker secrets, ficheros montados, Key Vault).

### 2026-10-08 (jueves) — · ~2 h

- **Hice:** Vídeos 14 a 19 de Docker. **Curso terminado.** Inspeccionadas las
  capas de mi imagen con `docker history`. Primer intento de escribir un
  `docker-compose.yml`.

- **Se rompió:** El compose. Lo escribí como un script, metiendo comandos
  `docker` dentro de `command:`. No es un fallo de sintaxis: Compose es
  **declarativo**, no imperativo. No se le dice qué hacer, se le describe
  qué debe existir. Cada `docker run` es un **servicio**, no una línea de
  guion. Y `command:` es lo que se ejecuta DENTRO del contenedor — lo mismo
  que iba después del nombre de la imagen en `busybox nslookup pgdb`.

- **Aprendí:**
  - `docker history` muestra el comando que creó cada capa. En mi imagen:
    934 kB de código propio sobre 186 MB; el resto son runtimes necesarios.
    En septiembre esto era una estimación; hoy lo he leído línea por línea.
  - Las variables de entorno se ven **en texto plano** en el historial de
    capas, aunque la capa pese 0 B.
  - `docker save` produce un **tar** (no un rar) que se puede abrir y
    recorrer: así se extrae un secreto de la imagen de otro.
  - Publicar en un registro es **irreversible**: puedes borrar la imagen del
    registro, no de las máquinas que ya la descargaron. Si sube un secreto,
    se rota, no se borra.
  - **Tercera vez que aparece el mismo principio** (historial de Git, capas
    de Docker, etiquetas mutables): borrar no es eliminar.
  - Correcciones a lo que decía el curso: es `docker compose build`, y el
    atributo `version:` del compose está obsoleto.

- **Pendiente:**
  - Terminar el `docker-compose.yml` traduciendo los comandos del martes:
    cada `docker run` → un servicio; red y volumen declarados arriba y
    referenciados en cada servicio; Postgres sin `ports:`.
  - **Aviso:** al pasar `POSTGRES_PASSWORD` al compose y hacer commit, el
    secreto pasa de mi shell a GitHub, público y permanente en el historial.
    Empeora respecto a ayer. Gitleaks debería pillármelo en el pipeline.
    ¿Cómo se hace bien? (.env fuera de Git, Docker secrets, Key Vault).

### 2026-10-09/10 (viernes/sábado) — · ~1h30

- Hice: docker-compose.yml completo y funcionando. Red, volumen, Postgres
  y mi API levantados con un solo comando.
- Se rompió:
  - Flags del CLI dentro del YAML (-e, -v, --network): el nombre de la
    clave ya hace el trabajo del flag.
  - = en vez de : en environment. Faltaban los guiones de las listas.
  - Indentación, otra vez. Transcribí en vez de copiar.
  - Puerto 8080 ocupado por el contenedor que creé a mano el martes.
- Aprendí:
  - Compose es DECLARATIVO: describes qué debe existir, no qué hacer.
  - Compose solo gestiona lo que él crea. Lo levantado a mano le es
    invisible y choca con él. Al pasar a declarativo hay que limpiar.
  - Prefija todo con el nombre del proyecto (mi-api_pgdata), así que no
    son los mismos volúmenes ni redes que los del martes.
  - docker compose config valida sin levantar nada. Usarlo siempre antes.
  - Subí el compose con POSTGRES_PASSWORD=ejemplo123 y gitleaks lo
    dejó pasar. Detecta por PATRÓN (AKIA..., ghp_..., BEGIN KEY...),
    no entiende contexto. Una contraseña humana corriente es
    invisible para él. Verde ≠ limpio: verde = sin secretos con
    formato reconocible. Tercera vez este mes que un cero significa
    ceguera y no limpieza.
- Pendiente:
  - El context está duplicado (mi-api/mi-api) y la imagen es mi-api:latest,
    no la endurecida. Revisar.
  - El compose vive en mi-api/ pero describe dos servicios: su sitio es la
    raíz del repo.
  - POSTGRES_PASSWORD en texto plano en un fichero que va a Git.
    Si hago commit, gitleaks debería pillarme a mí.
- Mañana sábado: crear cuenta en PortSwigger Web Security Academy y hacer
  el bloque completo de SQL injection. Labs, no solo teoría.

###  2026-10-10 (sábado) — · ~3 h · primer día de PortSwigger / AppSec

- Hice: Teoría de arranque de SQLi + 3 labs resueltos.
  Lab 1 (Apprentice) — Retrieving hidden data
  Lab 2 (Apprentice) — Login bypass
  Lab 3 (PRACTITIONER) — UNION, contar columnas

- Lab 1: SELECT ... WHERE category='...' AND released=1
  Payload: '--  (comilla + espacio + dos guiones)
  Comenté el AND released=1 para ver productos ocultos.
  Tropiezo: -- sin espacio detrás no comenta.

- Lab 2: SELECT ... WHERE username='...' AND password='...'
  Inyecté en el campo USUARIO: administrator'--
  Comenté la comprobación de contraseña → entro como admin sin saberla.
  La contraseña da igual porque queda comentada.

- Lab 3: filtro de categoría (en la URL, como el lab 1)
  Gifts' UNION SELECT NULL,NULL,NULL--  → 3 columnas.
  NULL porque encaja en cualquier tipo: así mido SOLO el nº de columnas,
  no el tipo (misma idea que la sonda neutra).
  Tropiezo: olvidé la ' antes de UNION → error de servidor. La comilla
  es lo que rompe la cadena y te mete en el SQL.

- Aprendí: el enunciado de cada lab NOMBRA el punto de inyección
  ("category filter", "login function"). Leerlo dos veces antes de buscar.
  Inyectas DONDE tu dato entra en la consulta, no "en la URL o en el form"
  por norma.

- Pendiente: lunes → seguir en UNION. Ya sé que son 3 columnas; toca
  encontrar cuál admite texto y sacar datos de otras tablas.

### 2026-10-12 (lunes) — · ~2h · mañana · UNION completo + OWASP

- **Hice:** Labs 4, 5 y 6 de SQLi (sección UNION cerrada) + lectura de
  OWASP Top 10 2025: A01 Broken Access Control y A05 Injection.

- **Lab 4 — UNION, columna de tipo texto** (Practitioner)
  Payload: Gifts' UNION SELECT NULL,'Gb1lYr',NULL--
  Ya sabía que eran 3 columnas (lab 3). Probé el string en cada posición
  con NULL en las demás → la columna 2 admite texto.

- **Lab 5 — UNION, datos de otra tabla** (Practitioner)
  Payload: ' UNION SELECT username, password FROM users--
  OJO: este lab eran 2 columnas, no 3. El nº de columnas es de cada app.
  Volqué la tabla users y me logueé como administrator.

- **Lab 6 — UNION, varios valores en una sola columna** (Practitioner)
  Solo UNA columna admite texto, pero necesito DOS valores (user y pass).
  Paso 1: ' UNION SELECT NULL,'administrator'--  → confirmo la columna texto.
  Paso 2: `' UNION SELECT NULL, username || '~' || password FROM users--`
  Concatené ambos valores en esa columna separados por `~`. Login con el
  admin → resuelto.

- **Ayuda:** En el lab 6, tras 15-20 min atascado, miré la pista. Lo que me
  faltaba era concatenar con || en una sola columna. El resto lo hice yo.

- **Aprendí:**
  - `||` concatena valores en una columna (Oracle/PostgreSQL; en MySQL `CONCAT()`).
    El separador (`~`) distingue los dos valores en la salida.
  - El nº de columnas se mide de cero en cada app, nunca se asume.
  - NULL = sonda neutra (4ª vez que aparece la idea).
  - OWASP: lo que acabo de explotar tiene nombre. A05 Injection = mis labs
    de SQLi. A01 Broken Access Control = convertirme en administrator sin
    permiso. Y la nueva A03 2025 (Software Supply Chain) = lo que vi con
    Docker (etiquetas mutables, capas). Mi trabajo de las últimas semanas
    ya era OWASP sin saberlo.

- **Pendiente:**
  - Siguiente sección: "Examining the database" — tipo y versión de la BD,
    listar tablas y columnas. Dejo de conocer la estructura de antemano y
    aprendo a descubrirla yo.

### 2026-10-13 (martes) — PortSwigger SQLi · "Examining the database"

**Tiempo:** ~60 min (de ellos ~45 de atasco antes de pedir ayuda)
**Lab:** *SQL injection attack, querying the database type and version on MySQL and Microsoft* → **SOLVED**
**Objetivo:** hacer que la BBDD devuelva su versión → `8.0.42-0ubuntu0.20.04.1`

### Qué hice
- Conté columnas con UNION (2 columnas de texto) y sustituí una por la consulta de versión.
- Payload final: `' UNION SELECT @@version,'def'#`
- En la URL, el `#` codificado: `...SELECT @@version,'def'%23`

### Dónde me atasqué y por qué
Todos mis intentos daban **internal server error**, probara 1, 2, 3 o 4 columnas.
Incluso la solución oficial de PortSwigger "no funcionaba"… porque ellos la lanzan desde Burp.

### Lecciones (las de verdad)
1. **Capa de transporte.** El `#` es comentario en SQL pero **delimitador de fragmento en una URL**: el navegador corta y NO envía nada después del `#`. Por eso mi comentario nunca llegaba a la BBDD y la comilla original quedaba sin cerrar → error. Solución en navegador: codificarlo como `%23`. En Burp viaja literal, sin codificar.
2. **Diagnóstico.** Si *todos* los intentos fallan igual, el fallo es **estructural, no del número de columnas**. Si fuera de columnas, exactamente uno funcionaría.
3. **Verde ≠ aprendido.** Mi primer "solved" lo saqué escribiendo el string de versión a mano (`'8.0.42...'`) como literal. El checker se pone verde porque solo busca esa cadena en la página, pero eso no demuestra nada: en un pentest real NO conoces la versión. Lo correcto es `@@version`, que **la resuelve el servidor**, no yo. (4ª vez que un verde significa otra cosa de la que parece.)

### Decisión
Me descargo e instalo **Burp Suite Community** en el Mac. A partir del próximo lab de inyección, todo por Burp (Proxy → Send to Repeater). Mañana lo configuro.

### Pendiente
- Configurar Burp y validar el flujo Proxy → Repeater (primeros 10 min de la próxima sesión).
- Terminar "Examining the database": lab de listar tablas/columnas.
- Después: labs de Access Control / IDOR (aplazados, no saltados).

### 2026-10-14 (miércoles) — PortSwigger SQLi · "Listing database contents" (PostgreSQL) · estreno de Burp

**Tiempo:** sesión nocturna, cerrada pasada la medianoche (más larga de lo previsto)
**Lab:** *SQL injection attack, listing the database contents on non-Oracle databases* → **SOLVED** (login como `administrator`)
**Primera sesión real con Burp Suite** (Proxy → Repeater), tras configurarlo hoy.

### Qué hice
1. Listar tablas: `' union select table_name, null from information_schema.tables --`
2. Listar columnas de la tabla de usuarios: `' union SELECT COLUMN_NAME, null FROM information_schema.columns WHERE table_name = 'users_teorfa' --`
3. Extraer credenciales: `' union SELECT username_sjciua, password_zmwajc FROM users_teorfa --`

Resultado → `administrator` / `paw9dv6oosx5fxi77mmk` (en texto plano) → login → lab resuelto.

### Lecciones (las de verdad)
1. **Tablas del motor ≠ tablas de la app.** Me metí primero en `pg_user` y las claves salían cifradas: `pg_*` e `information_schema` son el catálogo de sistema de PostgreSQL (las cuentas del propio servidor), no el login de la web. Los usuarios de la aplicación viven en el esquema `public`. Mismo concepto que `sys.sql_logins` en SQL Server.
2. **No escanear a ojo, filtrar con la BBDD.** Buscar la tabla entre 220 nombres a mano es la técnica equivocada. Con `WHERE table_schema = 'public'` el motor me deja solo 2 tablas (`products` y `users_teorfa`). Recon = hacer que la base de datos trabaje por mí.
3. **PortSwigger aleatoriza nombres de tabla Y de columna** (`users_teorfa`, `username_sjciua`, `password_zmwajc`) justo para que no se puedan memorizar y haya que enumerar. Recon de verdad.
4. **Un resultado vacío puede ser la herramienta fallando, no un "no hay datos".** Esto me costó tiempo: acerté la tabla `users_teorfa` al 2º intento, pero Burp no renderizó bien la respuesta y la di por mala. Me volví a la URL y me puse a probar otras tablas… para acabar en la misma. Lección: antes de descartar, confirmar que la herramienta está mostrando de verdad la respuesta. (Es el mismo patrón de siempre: distinguir fallo de herramienta de resultado real. Vacío ≠ negativo.)

### Reflexión AppSec (lo que me llevo a mi trabajo)
La contraseña estaba en **texto plano** en la tabla — por eso con leerla ya entraba. Esa es la vulnerabilidad *encima* de la SQLi: `pg_user` sí las tenía hasheadas, la app no. En mi código .NET: nunca almacenar contraseñas en claro; hash + salt siempre.

### Pendiente
- Afinar el flujo en Burp para no volver a confundir un fallo de render con un resultado vacío.
- Siguiente bloque de "Examining the database" o pasar a los labs de UNION restantes según toque en la ruta.

### 2026-10-15 (jueves) — PortSwigger · Blind SQLi con respuestas condicionales

**Tiempo:** sesión nocturna larga, cerrada ~02:00 (el Intruder estrangulado de la Community tardó lo suyo)
**Lab:** *Blind SQL injection with conditional responses* → **SOLVED** · **sin mirar la solución**
**Resultado:** login como `administrator` / `yg4bjc7q78qc9rz3qmg1`

### El concepto que he interiorizado
En SQLi **ciega** no ves los datos ni los errores: solo tienes un indicador de sí/no. Aquí el "Welcome back!" = la consulta devolvió filas = condición **verdadera**. Así conviertes la BBDD en un **oráculo de 1 bit por petición** y sacas la contraseña carácter a carácter.

### Qué hice
1. Longitud (búsqueda binaria con `>`): `' AND LENGTH((SELECT password FROM users WHERE username='administrator')) = 20 --` → 20 caracteres.
2. Extracción por posición: `' AND SUBSTRING((SELECT password FROM users WHERE username='administrator'), 1, 1) = 'y' --` → 1ª letra `y`.
3. Para las 20: **Burp Intruder, Cluster bomb** con dos posiciones (Payload 1 = índice del `SUBSTRING`, Payload 2 = carácter a probar). Las verdaderas se leen ordenando por índice y leyendo el carácter.

### Errores de principiante (los cacé yo)
- Olvidé el `--` de cierre al principio.
- El sistema me "autocorrigió" las comillas simples por tipográficas (`'`) que la BBDD no interpreta → corregido. Lección: en Repeater/Intruder, petición cruda, nadie te cambia las comillas.

### El verdadero aprendizaje de hoy: depurar el Intruder
Me pasé un buen rato con **0 resultados** y tuve que ir descartando fallos uno a uno:
1. **La petición era la plantilla `/example` de Burp, no la del lab** → todas 404 → ningún "Welcome back" posible. Fix: mandar la petición real (`GET /` con mi cookie) desde Proxy → Send to Intruder.
2. **Me faltaban los dígitos en el charset.** Cargué solo letras `a–z` y la clave tiene números (`yg4bjc7...`) → posiciones numéricas sin acierto. Lo vi, añadí `0–9` y salió completa.
3. **El oráculo: la longitud.** El grep no me marcaba, pero al **ordenar por `Length`** las verdaderas saltan solas: `11623` (con "Welcome back!") vs `11562` (sin él). La longitud fue la señal fiable.
4. Detalles: Intercept en **Off** para Intruder; `SUBSTRING` cuenta desde **1**, no desde 0.

### Lección madre (otra vez, 3ª variación)
Con **0 resultados, no dudes del método: verifica que la herramienta apunta donde crees.** Un vacío total casi nunca es "no hay datos" — es que estás disparando al sitio equivocado (hoy el `/example`; el martes la tabla `users_teorfa` que Burp no renderizaba). Y cuando un indicador no responde (el grep), **contrasta con otro** (la longitud).

### Reflexión AppSec (a mi trabajo)
Dos cosas: (1) la contraseña estaba **en texto plano** otra vez → hash + salt siempre. (2) La "ceguera" NO protege: aunque la app no muestre datos ni errores, se filtran bit a bit. La única defensa real es la **consulta parametrizada**; esconder la salida no sirve de nada.

### Pendiente
- Idea para desarrollador: reescribir esta extracción como **script en Python con `requests`** — sin el estrangulamiento de la Community y mucho más rápido. Gran ejercicio y muy vendible.
- Siguiente: blind SQLi basada en **errores** y en **tiempo** (los labs que vienen).

### 2026-10-16 (viernes)  — PortSwigger · Blind SQLi con errores condicionales (Oracle)

**Tiempo:** sesión nocturna larga, cerrada ~02:15
**Lab:** *Blind SQL injection with conditional errors* → **SOLVED** · login como `administrator`
**Contraseña:** `49b5k9wdyl2vghwcc2mt`
**Honestidad:** miré parte de la solución tras quedarme sin ideas. Dentro de protocolo, PERO → **marcado para rehacer en frío** (test real de si lo he aprendido).

### El mayor acierto (y fue mío)
Diagnostiqué que la BBDD era **Oracle** razonando desde los síntomas: todos mis intentos con sintaxis MySQL devolvían 500 → "si todo lo de MySQL falla, es que NO es MySQL". Esa deducción es el núcleo del lab.

### El concepto: blind basada en ERRORES
Aquí no hay "Welcome back" ni diferencia por filas devueltas. El **único oráculo es si la consulta peta o no**: HTTP **500** (error) = condición verdadera, **200** = falsa. Se fuerza un error condicional:
`(SELECT CASE WHEN <condición> THEN TO_CHAR(1/0) ELSE '' END FROM dual)`
El `1/0` (división por cero) solo se ejecuta cuando la condición es verdadera → error a propósito.

### Lo que me costó entender: `||` vs `AND`
- `||` es **concatenación de cadenas** en Oracle. Mi inyección vive DENTRO de un string (`WHERE trackingId = '...'`), así que concatenar una subconsulta mantiene la sintaxis válida como expresión de texto Y obliga a la BBDD a **ejecutar** esa subconsulta para construir el string.
- El `||'` final **se autocierra** reutilizando la comilla de cierre que ya tenía la consulta → no necesita `--`. (Yo usé `--` para truncar; también vale. Dos estilos: envolver vs truncar.)
- **OJO, no es "AND malo, || bueno".** Mis intentos con `AND` fallaron por usar **sintaxis MySQL sobre Oracle**, no por el `AND`:
  - `SUBSTRING` → en Oracle es `SUBSTR`.
  - Faltaba `FROM dual` / tabla (Oracle no deja SELECT sin FROM).
  - Mezclaba tipos: `THEN 1/0 ELSE 'a'` (número vs string) → error de tipos SIEMPRE, por eso 500 a todo.

### Sintaxis Oracle a fichar
`SUBSTR` · `TO_CHAR` · `ROWNUM` / `WHERE rownum=1` · `FROM dual`. Cada motor su dialecto — tener la cheat sheet delante es lo normal.

### Intruder
Marcador de payload sobre el carácter a probar, charset `a-z` + `0-9`, y el oráculo es la columna **Status = 500**.

### Lecciones extra (depurar la clave)
- **Oracle `SUBSTR(x,0,1)` devuelve el PRIMER carácter, no vacío** → el índice 0 duplica la posición 1. Empezar siempre en 1, o te sobra una letra al principio (clave de 21 en vez de 20). Versión Oracle del `From 0 → 1`.
- **Caracteres ambiguos de fuente** (`l`/`1`/`I`, `0`/`O`): un solo carácter mal leído tira el login entero. Al transcribir una contraseña, revisa estos con lupa. Mi fallo fue leer la `l` (ele) de la posición 10 como un `1`.

### Reflexión AppSec (a mi trabajo)
Lo más inquietante: aun con una app **totalmente muda** (sin salida, sin diferencia por filas), los datos se filtran por el canal error/no-error. Defensas en mi código .NET: (1) **consultas parametrizadas** siempre; (2) **páginas de error genéricas** — nunca devolver el mensaje de error de la BBDD ni dejar que el 500 vs 200 sea observable con esta granularidad.

### Pendiente
- **Rehacer este lab en frío** (instancia nueva, sin solución).
- Siguiente: blind basada en **tiempo** (cuando no hay ni error visible — `dbms_pipe.receive_message` en Oracle, etc.).

### 2026-10-17/18 (sábado/domingo) — PortSwigger · SQLi: error visible + time-based (+ primer script propio)

**Resumen del día:** dos labs de SQLi resueltos. Con ellos cierro el **mapa completo** de canales de exfiltración. Y monté mi primera herramienta de explotación en Python.

---

### LAB 1 — Visible error-based SQL injection (PostgreSQL) → SOLVED
**Tiempo:** sesión de tarde (a hora decente 👌) · **Resuelto solo** (busqué en la red el significado de los errores = recon, no la solución).
**Credenciales:** `administrator` / `rr0lh59ziehn6rnvipi3`

**Técnica — fuga por el mensaje de error:**
`' AND 1 = CAST((SELECT password FROM users LIMIT 1) AS int) --`
`CAST(texto AS int)` falla y PostgreSQL **incluye el valor en el error**:
`ERROR: invalid input syntax for type integer: "rr0lh59ziehn6rnvipi3"` → ahí está el dato.
- `LIMIT 1` + una sola columna: el `CAST` necesita **un valor escalar** (una fila, una columna).

**Salto conceptual (visible vs ciego):** en el blind el error era 1 bit (petó/no petó) → 567 peticiones con Intruder para 20 chars. Aquí el error **escupe el valor entero** → **1 petición = contraseña completa**. La misma SQLi es mucho más grave cuando los errores son verbosos.

**Mi mejor hallazgo — la truncación:** `Unterminated string literal started at position 95` NO era fallo mío de sintaxis → la **cookie se cortaba a ~95 caracteres**, partiendo la query. Fix: **acortar el payload** (quitar el valor del trackingId y los espacios). Lección: los payloads tienen límites de longitud; a veces el problema es que no caben, no el SQL.

---

### LAB 2 — Blind SQLi basada en TIEMPO + primer script propio (PostgreSQL) → SOLVED
**Credenciales:** `administrator` / `ex2ztpz2s1dcpuc7p528`
**Hito de la semana:** mi primera herramienta de explotación en Python.

**Técnica — el reloj como único oráculo:** la app no devuelve datos, ni errores, ni cambia de contenido. Lo ÚNICO observable es el TIEMPO. Se fuerza un `pg_sleep` condicional: si la condición es verdadera, la respuesta tarda.
`'%3BSELECT CASE WHEN (<condición>) THEN pg_sleep(N) ELSE pg_sleep(0) END FROM users--`
- `IF...THEN` NO existe en PostgreSQL → **`CASE WHEN`**.
- Entrega **apilada** con `;` → en la cookie va como **`%3B`** (porque `;` separa cookies).
- **Confirmar primero** con una condición segura-verdadera (`username='administrator'`): si eso tarda, el canal funciona. Si pruebas directamente `SUBSTRING...='a'` y el 1er char no es 'a', verás pg_sleep(0) instantáneo y creerás que "no va" cuando en realidad te dice "no es la a".

**Lección — el tiempo es un oráculo RUIDOSO:** en Intruder aparecieron fantasmas (7455ms ambiguo; 16182ms con Length 108 = anómalo, un error, no un sleep limpio). Reglas:
1. **Delay generoso** (muy por encima del ruido de red).
2. **Reconfirmar los aciertos**: un hit real vuelve a tardar; el ruido no se repite.

**El salto de verdad — dejé de USAR herramientas y empecé a CONSTRUIRLAS:** Intruder Community + sleeps = **+1 hora** con el estrangulamiento. Mi script en Python = **3 minutos**.
- **venv**: macOS/Homebrew da `externally-managed-environment` → `python3 -m venv venv` + `source venv/bin/activate` + `pip install requests`. (Entorno aislado = buena costumbre de dev.)
- Mido el tiempo con `time.perf_counter()` alrededor de la petición.
- `es_hit()` **reconfirma** el positivo → antídoto directo al ruido.
- `break` al primer acierto por posición → no malgasto peticiones.
- Mi perfil de dev deja de ser secundario: es la ventaja. Donde un pentester sin base se estanca, yo escribo el exploit.

---

### Mapa de exfiltración SQLi — COMPLETO
✅ UNION · ✅ booleana ciega · ✅ error ciego (500/200) · ✅ error visible · ✅ time-based

### Reflexión AppSec (a mi trabajo .NET) — las dos lecciones del día
1. **No exponer errores de BBDD al cliente.** Nunca mensajes de error de base de datos, stack traces ni detalles internos en producción → página de error genérica, detalle solo en logs (`UseExceptionHandler` en prod, `UseDeveloperExceptionPage` SOLO en dev). El error verboso es media vulnerabilidad (Lab 1 lo demuestra).
2. **La defensa de raíz es la consulta parametrizada.** El time-based (Lab 2) prueba que tapar canales de salida no basta: aunque cierres UNION, errores y diferencias de contenido, el reloj siempre queda. Una vez que la inyección existe, ningún "ocultar la salida" te salva. Nunca concatenar input en el SQL.

### Pendiente
- Terminar lo que queda de SQLi: **OAST / out-of-band** (⚠️ usa Burp Collaborator, que es solo de la Pro → con Community hace falta alternativa tipo interactsh), *SQLi in different contexts*, *second-order*, *How to prevent SQL injection*.
- Retos del script: **búsqueda binaria** (O(log n) en vez de O(n)) y **paralelismo** con `ThreadPoolExecutor`.
- **Redo en frío** del lab de Oracle (conditional errors) — miércoles/jueves.

### 2026-10-18 (domingo) —  PortSwigger · SQLi con bypass de WAF vía codificación XML

**Lab:** *SQL injection with filter bypass via XML encoding* → **SOLVED**
**Credenciales:** `administrator` / `ttnk6t1b1cdakj80gd9o`
**Honestidad:** revisé la solución tras atascarme, pero para **descubrir una herramienta** (`<@hex_entities>` de Hackvertor) que no se puede deducir, no para que me dieran la respuesta. El SQL, el UNION y la concatenación los razoné yo.

### El concepto: explotar el desacuerdo entre capas
La inyección va en un cuerpo XML (stock check) y hay un WAF que bloquea palabras clave SQL. El bypass:
1. Codifico las keywords como **entidades hex HTML** (`&#x53;` = 'S'…).
2. El **WAF** inspecciona el cable, ve solo entidades → no reconoce `SELECT`/`UNION` → pasa.
3. El **parser XML** decodifica las entidades de vuelta a SQL *después* del WAF.
4. La **BBDD** ejecuta el SQL ya limpio.
→ WAF y BBDD leen cosas distintas del mismo mensaje. Esa es la idea de "SQLi en distintos contextos".

### SQL
`1 UNION SELECT username || '~' || password FROM users`
- El stock se muestra como **un solo valor** → concateno usuario y contraseña con `||` y un separador `~` para sacar ambos por esa única columna.

### Herramienta: Hackvertor
- `<@hex_entities>SQL plano<@/hex_entities>` codifica el contenido al enviar.
- **Cierre `<@/hex_entities>`** (la barra va DESPUÉS del `@`) — mi fallo inicial era `</@...>`.
- Dentro va texto plano; Hackvertor codifica todo. No pre-codificar a mano.
- Descubierto de paso: el flujo de **Repeater** (enviar, modificar, reenviar).

### Reflexión AppSec (a mi trabajo .NET)
Un WAF que filtra por lista de palabras clave **es esquivable** cambiando de codificación o de contexto. No es una defensa de verdad, es un parche. La defensa real sigue siendo: **consulta parametrizada** en la capa de datos, y **normalizar/canonicalizar** la entrada ANTES de validarla (si validas antes de decodificar, validas algo que no es lo que ejecutará la BBDD).

### Pendiente
- 2 labs de OAST (requieren trial de Pro — sesión dedicada).
- Second-order SQLi y "How to prevent SQL injection" (cierre del módulo).
- Redo en frío del Oracle (conditional errors) — mié/jue.

### 2026-10-19 (lunes) — PortSwigger · Access Control (4 labs) — arranque del módulo

**Tema:** Access control / Broken Access Control (OWASP A01). 4 labs resueltos, el último sin mirar solución.
**Hilo conductor del día:** todos son el servidor **confiando en algo que controla el cliente**. El control de acceso se decide en el servidor, sobre datos de confianza, no sobre lo que manda el navegador.

### Lab 1 — Unprotected admin functionality
Panel de admin sin protección, en `/administrator-panel`. Lo descubrí en **`/robots.txt`**.
- Lección: **oscuridad ≠ control de acceso.** Y `robots.txt` delata justo las rutas que quieres "ocultar".

### Lab 2 — Unprotected admin (URL impredecible)
La URL (`/admin-kqpdte`) estaba expuesta en el **JavaScript del cliente** (un `if (admin)` que renderiza el enlace).
- Lección: **todo lo que llega al navegador es público.** La lógica del cliente es UX, no seguridad. Renderizar solo-para-admin no oculta el endpoint.

### Lab 3 — Admin identificado por cookie forjable
Logueado como `wiener:peter`, con el inspector cambié la cookie `Admin` de `false` a `true` → panel habilitado → borré a carlos.
- Lección: el servidor **confiaba en una cookie editable** para decidir el rol. Decisión de autorización sobre dato controlado por el cliente = roto.
- Defensa: el rol se determina en servidor desde la identidad autenticada (sesión firmada/cifrada), nunca desde una cookie que el usuario puede tocar.

### Lab 4 — Rol modificable en el perfil (mass assignment) — *sin solución*
El panel pedía `roleid` 2. En el endpoint de cambiar email, mandé la petición a Repeater y añadí un campo que no estaba:
`{"email":"wiener@normal-user.net", "roleid": 2}`
→ respuesta con `"roleid": 2` → panel de admin → borré a carlos.
- Lección: **mass assignment / over-posting.** El endpoint vincula TODO el cuerpo al objeto usuario, incluido `roleid`, que el usuario no debería poder tocar. Escalada de privilegios metiendo un campo de más.

### Reflexión AppSec (a mi trabajo .NET) — la del día
- **Autorización SIEMPRE en el servidor, por rol, sobre la identidad autenticada** (`[Authorize(Roles="Admin")]` en el endpoint), nunca sobre cookies, JS ni URLs "raras".
- **Mass assignment:** usar **DTOs** que expongan solo los campos editables (email), NUNCA enlazar la entidad directamente desde el body. Es exactamente el "DTOs frente a mass assignment" de mi proyecto — hoy lo he visto explotado en vivo.
- Nunca poner rutas sensibles en `robots.txt`.

### 2026-10-21 (miércoles) — PortSwigger · Access Control: bypass por X-Original-URL
*(arrastrado del 2026-10-20: 1h30 el martes + cierre hoy)*

**Lab:** URL-based access control circumvented (front-end bloquea `/admin`) → **SOLVED**, sin solución.
**Técnica:** el back-end soporta la cabecera `X-Original-URL`. Pido una ruta permitida y sobrescribo la ruta real:
`GET /?username=carlos` + `X-Original-Url: /admin/delete` → borra a carlos.
- El `username` va en la **query de la petición real** (X-Original-URL solo cambia la ruta, no los parámetros). De ahí el "missing parameter username" hasta que lo moví.
- Erratas que me costaron: faltaba la `/` y se duplicó `HTTP/2` en la línea de petición. Lección: leer el request entero, carácter a carácter.

**Concepto:** control de acceso en la capa equivocada = **desincronización front-end/back-end**. El proxy filtra por ruta; el back-end reconstruye la URL desde una cabecera que controla el atacante. Dos capas que leen cosas distintas del mismo mensaje (mismo patrón que el bypass XML con Hackvertor).

**Defensa (.NET):** autorizar SIEMPRE en el back-end, por endpoint y por rol; no delegar el control en el proxy/front-end; no confiar en cabeceras cliente (`X-Original-URL`/`X-Rewrite-URL`) para enrutar o autorizar.

### 2026-10-22 (jueves) — PortSwigger · Access Control (3 labs)

**Resumen:** 1 bypass de control por método HTTP + 2 sabores de IDOR (horizontal).
**Patrón del día:** *oscuridad ≠ control de acceso*, en todas sus formas — y el control tiene que vivir en el **back-end**, no en el borde ni en "ids difíciles".

### 1 · Method-based access control can be circumvented  *(2 días: ~1h45 el miércoles + cierre hoy)*
- **Técnica:** el front-end restringe `POST /admin-roles` a admins, pero no otros métodos. Con cookie de wiener: `POST`→"Unauthorized"; `POSTX`→"missing parameter" (diagnóstico: ya pasé el front-end, el back-end no lee el body); convertir a `GET` (Burp → "Change request method") mueve los params a la query → `GET /admin-roles?username=wiener&action=upgrade` → wiener a admin.
- **Lecciones:** la sesión también es una variable a manipular (cambiar la cookie al usuario de bajo privilegio). Dónde viven los params depende del método (POST→body, GET→query). Honestidad: revisé solución + vídeo tras 1h45.
- **Defensa (.NET):** autorizar en el back-end por rol, **independiente del método HTTP**; tratar métodos no estándar como no permitidos.

### 2 · IDOR — escalada HORIZONTAL (id entero)
- **Técnica:** en `GET /my-account?id=wiener` cambié `id` a `carlos` → cuenta de carlos con su API key, sin comprobar propiedad.
- **Concepto:** referencia directa a objeto controlada por el cliente sin autorización a nivel de objeto. **OWASP API #1 — BOLA.**
- **Defensa (.NET):** verificar dueño antes de devolver (`if (recurso.OwnerId != usuarioActual.Id) Forbid()`).

### 3 · IDOR con GUIDs (horizontal)
- **Técnica:** el `id` es un GUID no adivinable. Encontré el GUID de carlos **filtrado en un comentario**, lo puse en mi `my-account` → API key.
- **Lección:** un identificador impredecible NO es control de acceso — es oscuridad: el GUID se filtra donde se referencia al usuario, y aunque no se filtrara, el servidor sigue sin comprobar propiedad. GUID = defensa en profundidad, no la defensa.
- **Defensa (.NET):** autorización a nivel de objeto; no exponer GUIDs ajenos sin necesidad.
