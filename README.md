![](http://pidway.com/wp-content/uploads/2020/05/logosinfondo.png)


## DevOps Avanzado 
## CaaS Despliegue y a automatizacion.
 
Aqui encontraras un Servidor de Rancher con un cluster ya creado de Kubernetes k3s, ademas de actividades para utilizar correctamente la plataforma.

## Requirementos
 
 Software necesario:


- [Git](https://gitforwindows.org/)  Solo Windows para linux utiliza tu cliente de descarga habitual.
- [VirtualBox](https://www.virtualbox.org)
- [Ansible](https://www.ansible.com/) Solo Linux 
- [Vagrant](https://www.vagrantup.com)


## Hardware recomendado :

- Sistema operativo windows o Linux
- 4 Threads o mas
- 8GB RAM
- 100GB espacio en disco.

Puedes cambiar el tamaño y cantidad de nodos editando el archivo config.yaml


## Deploy

0. Ejecuta en tu cliente git  `git clone https://github.com/mvaldivia90/devops.git` 
0. Ejecuta   `vagrant plugin install vagrant-disksize` (esto sirve para modificar el tamaño de los box)
0. Ejecuta `vagrant up`
	-Si ejecutas de Windows y tienes problemas en el despliege indicando bad interpreter debes instalar lo siguiente:
	- [dos2unix](https://sourceforge.net/projects/dos2unix/)
0. Espera a que los ambientes esten preparados aprox 5 a 10 min.
0. Ejecuta un vagrant status para saber el estado de las VM, Resultado esperado Running.

0. Cuando provisionamiento termine debes entrar a la siguiente URL  [Rancher UI](http://172.22.101.101).

La contraseña por defecto es admin, pero esta puede ser actualizada en el archivo config.yaml

Una vez que accedas a la URL de Rancher debes esperar a que el cluster llamado quiskstart este Active.



##  CaaS RancherOS + k3s + OpenEBS + Grafana + prometheus + wordpress + mayadata

0. Administracion de cluster kubernetes

0. Catalogos de aplicaciones en Helm

0. Modificar cluster via yaml 
-  Agrega las siguietnes lineas al servicio kubelet:

  ` extra_binds:`
  
    ` - '/var/openebs/local:/var/openebs/local'`

0. Espera que se actualize el cluster 

0. provisionar app storage en rancher 
- Debes selecionar el namespace system para instalar esta app base en nuestro cluster
- Elegiremos  la apps openEBS de nuestro catalogo 
- Ejecutaremos el boton `Launch` y esperemos a que todos los POD y servicio esten OK
- Configurar default storage class como hostpath

0.  Activaremos live metric monitoring  en nuestro cluster  
- Activaremos la persistencia de data solo con grafana 
- Asignar 5GB al default storage class
- Esperar a que el despligue termine.
- Identificar el Endpoint en el namespace y Acceder a grafana cluster
- Revisa los cambios en el dashboard de cluster


0. Crear proyecto proyecto `pidway-dev`

0. Seleccionar el proyecto pidway-dev 

Nota: Para activar el load balanced Layer 7 que se generan de forma automatica en rancher debes seguir las siguientes    instrucciones:

   - Clona el siguiente GIT [PoweDNS](https://github.com/basecamp/xip-pdns) 
   - Modifica los valores por defecto en el archivo xip-pdns.conf 
     - XIP_DOMAIN="xip.io"
     - XIP_ROOT_ADDRESSES=( "172.22.101.111" )  IP del nodo
     - XIP_NS_ADDRESSES=( "rancher" "wordpress" )  Pueden ser distintos.
   - Activar el servicio de PowerDNS ( debe ser ejecutado en la maquina host)
   
     ` /path/to/xip-pdns/bin/xip-pdns /path/to/xip-pdns/etc/xip-pdns.conf`
     
     Debes cambiar "/path/to/" por el path donde realizaste el clon del GIT.
     Espera algunos minutos para que el servidor DSN este activo.
     
0. Desplegar Wordpress con PVC 
   -  Asignar 8GB a wordpress
   -  Asignar 5GB a mariadb
   
   Sin Servicio de Balanceo
   -  Labalancing layer 7 `False`
   -  Service type Nodeport
   -  Pincha el boton `Launch`
   
   Con servicio de balanceo Layer 7 xip.io
   -  Expose app using Layer 7 Load Balancer `True`
   -  Hostname `Automatically generate a .xip.io hostname`
   -  Pincha el boton `Launch`
  
     
0. Identificacion de servicio
   - Identificar los workload de wordpress
   - Identicar Volumenes creados estos deben estar en estado bound
   - Acceder al endpoint generado.

0. Administracion de datos para nubes hybridas openEBS
   - Acceder al sitio https://mayadata.io/
   - Crear cuenta con Google
   - Enlazar su cluster local  con director online a traves de kubectl.


## comandos vagrant
Para suspender todas o una VM `vagrant suspend` `vagrant suspend "nombrevm"`


Para eliminar todas las VM dejes ejecutar. `vagrant destroy -f`


Para recargar todas o una VM  `vagrant reload` `vagrant reload "nombrevm"

