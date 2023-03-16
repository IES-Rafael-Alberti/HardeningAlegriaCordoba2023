# Ansible

![Fotito_Shula_Ansible](https://user-images.githubusercontent.com/114920834/225441956-9b21e975-d524-4fa5-8d83-bfd736231179.png)
https://1drv.ms/u/s!Ap4tp2IfWUyGhvMKxnihhLjh6Rs1BA?e=oQHeBT

# Proceso de Instalación Ansible

Para la instalación de Ansible, ejecutaremos los siguientes comandos:



```bash
sudo apt update
sudo apt install software-properties-common
sudo add-apt-repository --yes --update ppa:ansible/ansible
sudo apt install ansible
```

Antes de comenzar a configurar Ansible, vamos a generar un fichero “ansible.cfg” con las opciones totalmente comentadas, para que a la hora de que lo vayamos a configurar, nos sea más fácil dar con la opción que queramos:

```bash
$ ansible-config init --disabled > ansible.cfg
```


El siguiente comando ofrece una configuración más completa:

```bash
$ ansible-config init --disabled -t all > ansible.cfg
```


# Configuración Ansible

Para configurar Ansible, tendremos que tener en cuenta las siguientes cosas:

## **Archivo de inventario (Hosts)**

En dicho fichero introduciremos los clientes a los que Ansible se va a conectar. Estos estarán agrupados por tipo.

![Untitled 1](https://user-images.githubusercontent.com/114920834/225444262-248818e5-a1cf-427a-bb23-30b67b515055.png)


Como se observa en la imagen anterior, el primer grupo que se ha creado, es el de los servidores web. Y dentro de él, se han introducido dos parámetros que ayudarán a Ansible a comunicarse con, en este caso, el servidor web:

usuario@192.168.1.83: Comando básico de conexión por SSH

ansible_password=usuario: Parámetro que le indicará a Ansible cuál es la contraseña de acceso a la máquina a la que se va a conectar.

La última línea, le indicará a Ansible a qué ruta deberá de acceder en caso de que necesite interpretar cualquier línea de comando que este escrita en Python3.

Nota: En nuestro caso, hemos renombrado al fichero “Hosts” como “Hosts.ini”. Entonces, para no crear ningún tipo de conflicto a la hora de implementar, por ejemplo, un playbook, tendremos que acceder al fichero “ansible.cfg” y modificar la siguiente línea:

![Untitled 2](https://user-images.githubusercontent.com/114920834/225444474-8ce3239c-39bc-4388-9d8c-a8d7b7992bcf.png)


## **Configurar las claves SSH**

Para que Ansible pueda conectarse a los servidores clientes, deberemos de configurar las claves SSH entre el servidor Ansible y los hosts especificados en el archivo “Hosts”. Esto solo funcionará si los clientes de Ansible no tienen una clave pública habilitada y tienen una cuenta de “root” habilitada con una contraseña.

Para ello, utilizaremos los siguientes comandos:


![Untitled 3](https://user-images.githubusercontent.com/114920834/225444834-18fcf9f8-d0ed-46e2-bf0c-3d52ced2387b.png)
![Untitled 4](https://user-images.githubusercontent.com/114920834/225444864-ce7db78e-4741-4bb3-9591-79b324989c15.png)

**Nota: Dependiendo de con qué usuario del cliente nos conectemos a través de SSH, podremos ejecutar o no comandos que requieran de elevación de privilegios.**

## Comprobación de conexión

Una vez hayamos compartido la clave pública, intentaremos comprobar que estamos conectados al cliente utilizando el siguiente comando:

sudo ansible (ComandoConexiónSSH) -m ping

![Untitled 5](https://user-images.githubusercontent.com/114920834/225445132-77ba9932-ce99-41c7-957b-108bdcbd8c93.png)

Nota: Si queremos realizar esta comprobación con todos los clientes, tendremos que sustituir el comando de conexión por SSH por “all”.

## Ejecución de comandos básicos

Por último, vamos a ejecutar un comando básico de manera remota para comprobar que todo está bien:

sudo ansible (ComandoConexiónSSH) -a “ifconfig”

![Untitled 6](https://user-images.githubusercontent.com/114920834/225445166-cec478cc-f077-4d88-8971-fa7ad59f048a.png)


# Creación de un PlayBook

Antes de crear nuestro primer “PlayBook”, crearemos una carpeta en la ruta “/etc/ansible/” para poder alojarlo.

Una vez creada dicha carpeta, accederemos a ella y crearemos el playbook con el siguiente comando:

sudo nano apache2.yml

A continuación, vamos a ver apartado por apartado de qué está compuesto un playbook:

![Untitled 7](https://user-images.githubusercontent.com/114920834/225445219-46ef21d6-f415-45ef-a26c-f572d7df2aad.png)


En la imagen anterior se indica que el playbook va a realizar una instalación de Apache en los clientes que estén dentro del grupo “webserver” del inventario.

Una vez definido lo anterior, tendremos que establecer una serie de tareas que se irán ejecutando de manera secuencial:

Como buena práctica, antes de instalar cualquier servicio, es conveniente actualizar los repositorios del sistema. Por ello, la primera tarea que va a realizar este playbook, va a ser la de actualizar los paquetes del sistema:

![Untitled 8](https://user-images.githubusercontent.com/114920834/225445249-146c3f9f-385f-469b-86e6-c67898e60f19.png)


Una vez instalados los paquetes, el playbook procederá a instalar la versión más reciente del servicio Apache:

![Untitled 9](https://user-images.githubusercontent.com/114920834/225445291-4a319d74-b24a-49ce-ad5c-f8ed38a7556a.png)


Ahora el playbook va a configurar una regla en el firewall del cliente Linux que abrirá el puerto 80 TCP para que podamos visualizar el contenido de la página o páginas web que establezcamos en nuestro Apache desde un navegador web:

![Untitled 10](https://user-images.githubusercontent.com/114920834/225445328-f16e6fb2-a94d-4c45-8c2d-5e11587bb109.png)

Como Apache crea una página de muestra para comprobar que todo funciona correctamente y nosotros queremos tener una página personalizada, vamos a eliminar dicha página con la opción “absent”:

![Untitled 11](https://user-images.githubusercontent.com/114920834/225445369-d78c7c5e-eea9-460c-ad87-1ef458e3a12f.png)


A continuación, le indicamos al playbook que cree de nuevo el fichero “Index.html” pero vacío. Para ello, le diremos que utilice el comando “touch”:


![Untitled 12](https://user-images.githubusercontent.com/114920834/225445421-d1829bb5-e348-4712-b00e-8e06a8dc727e.png)


Ahora con esta tarea que vamos a ver a continuación, le vamos a indicar al playbook que meta lo que venga tras la opción “line” en el “Index.html” que creamos anteriormente:

![Untitled 13](https://user-images.githubusercontent.com/114920834/225445462-1ff1e438-cdb7-498c-a240-1c23ed80f6bf.png)


Por último, le vamos a indicar al playbook que lance el servicio Apache2:

![Untitled 14](https://user-images.githubusercontent.com/114920834/225445495-467939c6-ec67-47a5-ab39-c240c4438386.png)

# Comprobaciones

Una vez terminado el fichero, guardamos y lo lanzamos:

![Untitled 15](https://user-images.githubusercontent.com/114920834/225445559-10e21f8d-222f-4f6c-a4a0-772cfe2895e1.png)

El comando anterior explicado, sería:

**ansible-playbook:** Comando base para el lanzamiento de playbooks.

**apache2.yml:** Fichero de configuración del playbook

**-i ../hosts.ini:** Opción para seleccionar el inventario

**-l webserver:** Opción para seleccionar el grupo al que le queremos aplicar el playbook.

**-kK:** Opción que te puede salvar de un disgusto, que te pregunta por la contraseña de acceso por SSH del cliente y te permite realizar todo lo anterior con privilegios elevados.

![Untitled 16](https://user-images.githubusercontent.com/114920834/225445620-9765989d-78d6-46f9-828b-73468d9fdc3d.png)


Como se observa en la imagen anterior, el playbook ha realizado 5 cambios en el cliente. Si volviésemos a ejecutar el mismo playbook sobre el mismo cliente, Ansible no realizaría ningún cambio a menos de que el servicio estuviese parado o desinstalado.

Para comprobar que todo ha salido bien, nos vamos al navegador y colocamos la dirección IP del cliente en la zona de URLs:

![Untitled 17](https://user-images.githubusercontent.com/114920834/225445657-e1bd1fc8-7e6c-4d5d-858b-ed1c39b7e976.png)

