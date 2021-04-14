- [jenkis](#jenkis)
- [Instalación y Configuración Básica de Jenkins](#instalación-y-configuración-básica-de-jenkins)
  - [password](#password)
  - [RESUMEN](#resumen)
- [Cadenas de Jobs](#cadenas-de-jobs)

# jenkis

Jenkins es open source y probablemente el software de automatización más usado de todos, escrito en Java y corre en JVM. Es muy conveniente al ser una herramienta extensible al tener un ecosistema de plugins que te permiten extenderlo, puedes escribir tus propios plugins con Java, pero ya la comunidad ha desarrollado un sinfín de ellos.

También nos permite escalar de manera horizontal y verticalmente, puede correr un sin número de trabajos concurrentemente en una sola máquina y si esa máquina no da abasto se le puede dar más recursos a Jenkins. O una máquina no es suficiente entonces Jenkins nos permite escalar horizontalmente con ““slaves”” y controlar varios nodos para que trabajen por él.

Jenkins siempre esta siendo innovado y teniendo actualizaciones de seguridad, esto es importante porque es el target más grande de seguridad de una empresa porque lo tiene todo.

Algo que Jenkins ha trabajado mucho en los últimos años es que puedes escribir tus ““jobs”” o unidades de trabajo en código. Nosotros queremos que nuestra automatización también sea programática, no solo los comando a ejecutar sino poder migrar nuestro trabajo a un nuevo Jenkins de manera reproducible. Han creado Pipelines as code

# Instalación y Configuración Básica de Jenkins

(recuerda habilitar el puerto 8080 en tu máquina)

```bash
sudo apt-get update
sudo apt-get upgrade
sudo apt-get install openjdk-8-jdk wget gnupg
sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 9B7D32F2D50582E6
sudo apt-get update
sudo apt-get install git jenkins
ssh-keygen
service jenkins start
service jenkins status
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

or <https://github.com/jenkinsci/docker/blob/master/README.md>

```bash
docker run -p 8080:8080 -p 50000:50000 jenkins/jenkins:lts
```

La contraseña para entrar a Jenkins lo encuentran en la consola que crearon la imagen en docker, después del siguiente mensaje: “Please use de following password to proceed to installation:”

## password

```bash
docker exec -it <conteiner-name> /bin/bash
cat /var/jenkins_home/secrets/initialAdminPassword
```

General

`Discard old build`
Como las cosas de los jobs se quedan en el disco duro en algun momento se va a llenar, ésta opcion es una manera de impedir que eso pase. Si se marca tiene varios opciones:
Dias: se puede decir que quiero tener este build por ‘X’ dias, por ejemplo 365 dias (un año).
Numero de builds a guardar: Tambien puedo decir 'quiero guardar los ultimos 2 bulilds"

`This project is parameterized`
Se le puede pasar parametros al build. Por ejemplo un string parameter

`Disable this project`
Si algo sale mal en un job y no quieres que nadie lo corra, se marca la cajita y el job no va a correr.

`Source Code Management`
Se puede elegir entre diferentes versionadores de codigo como subervsion o git

`Build Triggers`

- Remotely
- A travez de una API
- After other projects are built
- Si termino de ejecutar job A quiero ejecutar job B unicamente si job A fue estable

`Periodicamente`

- Recibe la sintaxis de un cron, se puede ejecutar cronologicamente
- Github hook trigger for GITScm polling
- Cuando se haga un pus en githu el job se va a ejecutar

`Bulid Environment`

- Delete workspace before build starts
- Si tu corres el job y modificas el workspace, por ejemplo creas un archivo, la proxima vez que se ejecute, a a estar ahí, a menos que especifiques ésta opcion para eliminar el workspace

`Use secret text(s) or file(s)`

- Añadir secretos, algo que no deberia estar expuesto a otros usuarios, esto te permite guardarlo y accesarlo a travez del script pero no va a estar expuesto a los ojos de otras personas, solo jenkins lo va a ver.
- Abort the build if it’s stuck
- Puede llegar el caso en el que el job nunca termino, por cualquier motivo que sea, es ideal que no se quede atorado, que falle. Entonces podemos deir que si no ha terminado en 3 minutos que se cancele el build y falle.
- Add timestamp to the console output

## RESUMEN

Descripcion: ayuda a resolver cuando tienes un monton de jobs para describir.

Discard old builds: ayuda a resolver cuando muchas cosas se llenan en tu disco duro
Days to keep builds: 365 dias --> quiero tener este build por un año
Max # of builds to keep: 2 —> guardar los ultimos dos builds

This project is parameterized: Le puedes pasar parametros al build
Add Parameter -> String Parameter
Name: NAME
Default Value: Boris Vargas
Description: Descripcion

Disable this project: sumamente importante, si algo sale mal en un job y quieres que nadie lo corra (La mayor parte de jobs corren automaticos)

`Source Code Management`
Git: Añadir un repositorio
Credentials: Credenciales
(Usaremos un script para ejecutar esta parte)

`Build Triggers`
(Estuvimos ejecutando a mano)
Trigger builds remotely (e.g., from scripts): Tienes para correrlo por una API
Build after other projects are built: Si termino de ejecutar job A quiero correr job B, unicamente si job A fue estable.
Build periodically: Acepta la sintaxis de un CRON jobs (Corre cada minuto cada X dia, ‘si queremos que algo se ejecute sabado en la noche me corres este JOB’)
GitHub hook trigger for GITScm polling: Vamos usar futuramente, cuando tengamos un push en Github el job se va ejecutar

`Build Environment`
Delete workspace before build starts: (Importante que lo marquen) si tu corres tu job y modificas tu job y dejas files (algo) en la proxima ejecucion va estar. Queremos que el subfolder este limpio.
Use secret text(s) or file(s): Para añadir secretos

`Bindings`
Llaves o variables de entorno o algo que no deberia estar expuesto a otros usuarios te permite guardarlo y accesarlo atraves de script.

Abort the build if it’s stuck: Si el job que va a correr toda su vida, porque paso algo. (Si el job fallo o el S.O. fallo)
Timeout minutes: 3 --> Si paso 3 minutos que cancele el build y falle (Poner como una variable global por comando)
Add timestamps to the Console Output: Marcar para ver el tiempo de ejecucion en consola
Build
Command: echo “Hello platzi $NAME”

Run with timeout: Si un comando demora mas, si un comando tarda demasiado le permites una ventaja mas de tiempo
Archive the artifacts: Vamos a usar en el futuro, watch others jobs y que se ejecute.

# Cadenas de Jobs

Primero instalamos el plugin Parameterized Trigger, igual cómo instalamos anteriormente y reiniciamos.

Luego vamos a crear 2 jobs nuevos:
watchers: En este job, vamos a configure y vamos a “Build after other projects are built” y escribimos y escribimos hello-platzi, sí hello-platzi es successful, quiero que se ejecute watchers.
Y en la parte de executed shell, escribimos : echo “Running after hello-platzi success” y guardamos.
parameterized: Acepta parámetros cuando lo llamo. Marcamos la opción “ This project is parameterized” y en el name escribimos ROOT_ID.
Y en el execute shell: echo “calle with $ROOT_ID” y guardamos.

Y en hello-platzi, en Downstream project, y estos se añaden cuando jenkins se da cuenta que su job tiene una dependencia con otro.
Vamos al configure de hello-platzi y en el execute shell escribimos:
echo “Hello Platzi from $NAME”
Y añadir un build step que se llama : “Trigger/call build on other projects”, y en projects to build escribimos parameterized y le damos en añadir parámetros, luego parámetros predefinidos y escribimos:
ROOT_ID=$BUILD_NUMBER
BUILD_NUMBER es una variable de entorno, que es el valor de esta ejecución y guardamos.

Le damos en “build with parameters” y entramos al console output de parameterized y vemos que la ejecución número tal, fue la que ejecutó a parameterized.
Corre hello-platzi, él llama declarativamente a parameterized e indirectamente a watchers.

Corre los test para esta versión, cuando acabes, mandame esta versión a producción le pasó el id del commit, y se lo pasó a mí job que hace deployment y cuando lo resuelvas me lo despliegas.
El sabe la cadena de ejecuciones que tuvo, y cuál fue el que inició este proceso.
El profe recomienda usar parameterized jobs en vez de watchers, porque cuando uso watchers solo tengo tres opciones mientras que con parameterized jobs tengo más opciones.


