- [jenkis](#jenkis)
-

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