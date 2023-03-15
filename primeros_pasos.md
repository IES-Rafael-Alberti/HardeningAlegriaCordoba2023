# Ansible

![Fotito Shula Ansible.png](img/Fotito_Shula_Ansible.png)

# Proceso de Instalación Ansible

Para la instalación de Ansible, ejecutaremos los siguientes comandos:

![Untitled](img/Untitled.png)

Antes de comenzar a configurar Ansible, vamos a generar un fichero “ansible.cfg” con las opciones totalmente comentadas, para que a la hora de que lo vayamos a configurar, nos sea más fácil dar con la opción que queramos:

$ ansible-config init --disabled > ansible.cfg

El siguiente comando ofrece una configuración más completa:

$ ansible-config init --disabled -t all > ansible.cfg

# Configuración Ansible

Para configurar Ansible, tendremos que tener en cuenta las siguientes cosas:

## **Archivo de inventario (Hosts)**

En dicho fichero introduciremos los clientes a los que Ansible se va a conectar. Estos estarán agrupados por tipo.

![Untitled](img/Untitled%201.png)

Como se observa en la imagen anterior, el primer grupo que se ha creado, es el de los servidores web. Y dentro de él, se han introducido dos parámetros que ayudarán a Ansible a comunicarse con, en este caso, el servidor web:

usuario@192.168.1.83: Comando básico de conexión por SSH

ansible_password=usuario: Parámetro que le indicará a Ansible cuál es la contraseña de acceso a la máquina a la que se va a conectar.

La última línea, le indicará a Ansible a qué ruta deberá de acceder en caso de que necesite interpretar cualquier línea de comando que este escrita en Python3.

Nota: En nuestro caso, hemos renombrado al fichero “Hosts” como “Hosts.ini”. Entonces, para no crear ningún tipo de conflicto a la hora de implementar, por ejemplo, un playbook, tendremos que acceder al fichero “ansible.cfg” y modificar la siguiente línea:

![Untitled](img/Untitled%202.png)

## **Configurar las claves SSH**

Para que Ansible pueda conectarse a los servidores clientes, deberemos de configurar las claves SSH entre el servidor Ansible y los hosts especificados en el archivo “Hosts”. Esto solo funcionará si los clientes de Ansible no tienen una clave pública habilitada y tienen una cuenta de “root” habilitada con una contraseña.

Para ello, utilizaremos los siguientes comandos:

![Untitled](img/Untitled%203.png)

![Untitled](img/Untitled%204.png)

**Nota: Dependiendo de con qué usuario del cliente nos conectemos a través de SSH, podremos ejecutar o no comandos que requieran de elevación de privilegios.**

## Comprobación de conexión

Una vez hayamos compartido la clave pública, intentaremos comprobar que estamos conectados al cliente utilizando el siguiente comando:

sudo ansible (ComandoConexiónSSH) -m ping

![Untitled](img/Untitled%205.png)

Nota: Si queremos realizar esta comprobación con todos los clientes, tendremos que sustituir el comando de conexión por SSH por “all”.

## Ejecución de comandos básicos

Por último, vamos a ejecutar un comando básico de manera remota para comprobar que todo está bien:

sudo ansible (ComandoConexiónSSH) -a “ifconfig”

![Untitled](img/Untitled%206.png)

# Creación de un PlayBook

Antes de crear nuestro primer “PlayBook”, crearemos una carpeta en la ruta “/etc/ansible/” para poder alojarlo.

Una vez creada dicha carpeta, accederemos a ella y crearemos el playbook con el siguiente comando:

sudo nano apache2.yml

A continuación, vamos a ver apartado por apartado de qué está compuesto un playbook:

![Untitled](img/Untitled%207.png)

En la imagen anterior se indica que el playbook va a realizar una instalación de Apache en los clientes que estén dentro del grupo “webserver” del inventario.

Una vez definido lo anterior, tendremos que establecer una serie de tareas que se irán ejecutando de manera secuencial:

Como buena práctica, antes de instalar cualquier servicio, es conveniente actualizar los repositorios del sistema. Por ello, la primera tarea que va a realizar este playbook, va a ser la de actualizar los paquetes del sistema:

![Untitled](img/Untitled%208.png)

Una vez instalados los paquetes, el playbook procederá a instalar la versión más reciente del servicio Apache:

![Untitled](img/Untitled%209.png)

Ahora el playbook va a configurar una regla en el firewall del cliente Linux que abrirá el puerto 80 TCP para que podamos visualizar el contenido de la página o páginas web que establezcamos en nuestro Apache desde un navegador web:

![Untitled](img/Untitled%2010.png)

Como Apache crea una página de muestra para comprobar que todo funciona correctamente y nosotros queremos tener una página personalizada, vamos a eliminar dicha página con la opción “absent”:

![Untitled](img/Untitled%2011.png)

A continuación, le indicamos al playbook que cree de nuevo el fichero “Index.html” pero vacío. Para ello, le diremos que utilice el comando “touch”:

![Untitled](img/Untitled%2012.png)

Ahora con esta tarea que vamos a ver a continuación, le vamos a indicar al playbook que meta lo que venga tras la opción “line” en el “Index.html” que creamos anteriormente:

![Untitled](img/Untitled%2013.png)

Por último, le vamos a indicar al playbook que lance el servicio Apache2:

![Untitled](img/Untitled%2014.png)

# Comprobaciones

Una vez terminado el fichero, guardamos y lo lanzamos:

![Untitled](img/Untitled%2015.png)

El comando anterior explicado, sería:

**ansible-playbook:** Comando base para el lanzamiento de playbooks.

**apache2.yml:** Fichero de configuración del playbook

**-i ../hosts.ini:** Opción para seleccionar el inventario

**-l webserver:** Opción para seleccionar el grupo al que le queremos aplicar el playbook.

**-kK:** Opción que te puede salvar de un disgusto, que te pregunta por la contraseña de acceso por SSH del cliente y te permite realizar todo lo anterior con privilegios elevados.

![Untitled](img/Untitled%2016.png)

Como se observa en la imagen anterior, el playbook ha realizado 5 cambios en el cliente. Si volviésemos a ejecutar el mismo playbook sobre el mismo cliente, Ansible no realizaría ningún cambio a menos de que el servicio estuviese parado o desinstalado.

Para comprobar que todo ha salido bien, nos vamos al navegador y colocamos la dirección IP del cliente en la zona de URLs:

![Untitled](img/Untitled%2017.png)
