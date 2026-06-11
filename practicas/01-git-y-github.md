# Práctica 01 - Configuracióin inicial de Git y Github en Debian

## Objetivo: Instalar Git, configurar la identidad del usuario y conectar el laboratorio local con Github utiliando autenticación SSH.

## Entorno
- Sistema operativo: Debian
- Repositorio: linux-lab
- Fecha: Junio 2026

## Problema inicial
Al ejecutar git --version el sistema respondió que Git no estaba instalao.

## Instalación de Git}
Se actualizó la lista de paquetes e instaló Git:

sudo apt update
sudo apt install git -y

Verificación git --version

## Configuración de identidad
Se configuró el nombre y correo para los commits:

git config --global user.name "mi nombre"
git config --global user.email "mi mail"

Verificación git config --global --list

## inicialización del repositorio
Se creó el repositorio local y se realizó el primer commit:

git add .
git commit -m "Inicio del laboratorio Linux"

## Creación e clave SSH
Se generó una clave SSH para autenticación con Github:

ssh-keygen -t 00000 -C "mi mail" 

Posteriormente se inició el agente SSh y se agregó la clave:
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_00000

## Conexión con Github
Verificación de remotos:

git remote -v

## Cambio de URL remota a SSH:

git remote set-url original git@github.com:anyemon/linux-lab.git

## Prueba de autenticación:

ssh -T git@github.com

## Publicación
Se envió la rama principal al repositorio remoto:

git push -u origin main


## Resultado:
-Git instalado correctamente.
-Github conectado mediante SSH.
-Primer repositorio publicado.
-Flujo de trabajo git Funcional.

## Aprendizajes
-Diferencia entre HTTPS y SSH.
-Configuración global de Git
-Uso básico de Github desde Linux.
-Gestión de claves SSH


