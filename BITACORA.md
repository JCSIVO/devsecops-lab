### Bitácora de Aprendizaje - DevSecOps Lab

### 2026-09-19 · ~4 h

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

### 2026-09-21 · ~50 min

-  **Hice:** Primer apartado "Fundamentos de Docker" del curso de Platzi. 
-  **Se rompió:** Nada.
-  **Aprendí:** Los comandos básicos de Docker (`--version`, `images`, `run`,
  `info`, `ps`) y sobre todo el reflejo del `--help`, que sirve igual para
  trivy, semgrep, terraform y kubectl.  
-  **Pendiente:** `docker info` tiene un apartado de Security Options.

### 2026-09-22 · ~50 min

-  **Hice:** Descargar el dotnet en brew, crear la API, y el archivo "Dockerfile" 
-  **Se rompió:** El build falló (NETSDK1045): mi SDK local es .NET 10 y el
           contenedor traía el SDK 8. Deriva de versiones entre mi máquina
           y el entorno de build. Arreglado alineando la imagen base.
-  **Aprendí:** Los comandos básicos de Docker para compilar la imagen(`docker build .`, `images`, `docker build -t nombreImagen:latest .`)  y para eliminar la imagen `docker rmi -f` nombre de la imagen   
-  **Pendiente:** Nada.

  ### 2026-09-23 · ~40 min

-  **Hice:** Ejecutar trivy image mi-api:latest, para detectar los CVE críticos y altos. 
-  **Se rompió:** Trivy cortó al descargar su BD de vulnerabilidades (GOAWAY del  servidor). Reintenté y fue. Lección: el escáner depende de una BD externa; en un pipeline real eso hay que cachearlo.
-  **Aprendí:** Imagen base: 40 CVE (5 low, 35 medium, 0 high, 0 critical)
           y 1,36 GB. De ese peso, mi código son unos pocos KB; el resto
           es SDK que nunca se ejecuta en producción.
           Cero críticas NO es seguro: Trivy no ve mi código, ni que el
           contenedor corre como root.  
-  **Pendiente:** Nada.

  ### 2026-09-24 · ~40 min

-  **Hice:** Multi-stage build con imagen de runtime y usuario no root. 
-  **Se rompió:** Nada. 
-  **Aprendí:** 40 → 13 CVE y 1,36 GB → 369 MB. Las 5 bajas no se movieron:
           son del sistema base, común a ambas. Lo que quité fue el
           toolchain. Reducir superficie > parchear hallazgos.
           Lo de no correr como root no lo cuenta Trivy y es lo que más
           importa de hoy.  
-  **Pendiente:** Nada.

  ### 2026-09-25 · ~30 min

-  **Hice:** Comparar los CVE de los dos días anteriores y realizar una tabla de comparación en el README del proyecto. 
-  **Se rompió:** Nada. 
-  **Aprendí:** Crear tablas en el archivo README del proyecto. 
-  **Pendiente:** Nada.

### 2026-09-26 · ~3 h

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

  ## 2026-09-28 · ~20 min

- **Hice:** Ver los vídeo de Platzi, convertir imagen en servicio web y gestión de imagenes (videos: 8-9)
- **Se rompió:** Nada.
- **Aprendí:** aprendí el comando -> docker run -it --rm -d -p 8080:80 --name mi-api mi-api
- **Pendiente:** PAUSA de 5 días por viaje. Al volver: clases 8-11 de Docker
  (gestión de imágenes y contenedores, capas y caché).

## 2026-09-29 a 10-03 · PAUSA PLANIFICADA
Viaje. Sin sesiones. No es ruptura de cadena: pausa anunciada y retomada.

## 2026-10-05 · ~45 min · vuelta de la pausa

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

    ## 2026-10-06 · ~2h15

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
