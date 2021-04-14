- [jenkis](#jenkis)
- [Instalación y Configuración Básica de Jenkins](#instalación-y-configuración-básica-de-jenkins)
  - [password](#password)

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
