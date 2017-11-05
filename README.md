### Examen 2
**Universidad ICESI**  
**Curso:** Sistemas Operativos  
**Docente:** Daniel Barragán C.  
**Tema:** Namespaces, CGroups, LXC
**Correo:** daniel.barragan at correo.icesi.edu.co

**Nombre:** Alejandro Bueno Cardona  
**Código:** A00335472  
**Correo:** alejandro.bueno at correo.icesi.edu.co  

### Objetivos
* Comprender los fundamentos que dan origen a las tecnologías de contenedores virtuales
* Conocer y emplear funcionalidades del sistema operativo para asignar recursos a procesos
* Conocer y emplear capacidades de CentOS7 para la virtualización

### Prerrequisitos
* Virtualbox o WMWare
* Máquina virtual con sistema operativo CentOS7

### Descripción
El segundo parcial del curso sistemas operativos trata sobre el manejo de namespaces, cgroups y virtualización por medio de LXC/LXD

### Actividades
1. Incluir nombre, código (5%)
2. Ortografía y redacción cuando sea necesario (5%)
3. Realice una prueba de concepto empleando systemd y el recurso de control CPUQuota teniendo en cuenta los requerimientos que se describen a cotinuación. Incluya evidencias del funcionamiento de lo solicitado (30%):
 * Las pruebas se realizaran sobre un solo núcleo de la CPU
 * Se deben ejecutar dos procesos
 * Cada proceso debe poder acceder solo al 50% de la CPU
 * Cuando uno de los procesos se cancela, el que continua ejecutándose no debe acceder a mas del 50% de la CPU
 
 Para empezar, se configura la máquina virtual **so-centos7-intro** con un solo núcleo de la CPU:
 
 ![][1]
 
 Ahora, se deben ejecutar dos procesos. Para esto, se crea el directio **parcialDos** en el usuario operativos. Luego, se crean los siguientes procesos:
 
 ![][2]
 
 Al ejecutar cada proceso individualmente, se observa que consumen más del 80% del núcleo de CPU:
 
| procesoUno.sh | procesoDos.sh |
| --- | --- |
| ![][3] | ![][4] |

Para convertir los procesos en servicios, se ejecutan los siguientes comandos, como se ven en la guía de so-processes:
```
# cd /etc/systemd/system
# touch procesoUno.service
# chmod 777 procesoUno.service
# touch procesoDos.service
# chmod 777 procesoDos.service
```
 Con el editor vi, se editan los dos servicios. Cada archivo queda de la siguiente manera:
 
 ![][5]
 
 De cada archivo, destacan dos líneas:
 ```
 # ExecStart = /home/operativos/parcialDos/procesoUno.service
 # CPUQuota=50%
 ```
 La primera, asocia el servicio con su script correspondiente. La segunda, asigna la CPUQuota a 50%, es decir, el máximo uso del núcleo de la CPU, es del 50%.
 
 
 
4.  Realice una prueba de concepto empleando systemd y el recurso de control CPUShares teniendo en cuenta los requerimientos que se describen a continuación. Incluya evidencias del funcionamiento de lo solicitado (30%):
 * Las pruebas se realizaran sobre un solo núcleo de la CPU
 * Se deben ejecutar dos procesos
 * Uno de los procesos tendrá el 25% de la CPU mientras que el otro tendrá el 75% de la CPU
 * Cuando uno de los procesos se cancela, el que continua ejecutándose debe poder llegar al 100% de la CPU
5. Por medio de las evidencias obtenidas en los puntos anteriores y de fuentes de consulta en Internet, elabore las definiciones para los grupos de control CPUQuota y CPUShares, además concluya acerca de cuando es preferible usar un recurso de control sobre otro (20%)
6. El informe debe ser entregado en formato pdf a través del moodle y el informe en formato README.md debe ser subido a un repositorio de github. El repositorio de github debe ser un fork de https://github.com/ICESI-Training/so-exam2 y para la entrega deberá hacer un Pull Request (PR) respetando la estructura definida. El código fuente y la url de github deben incluirse en el informe (10%)  

### Referencias
https://github.com/ICESI/so-containers

[1]: images/p21.PNG
[2]: images/p22.PNG
[3]: images/p23.PNG
[4]: images/p24.PNG
[5]: images/p25.PNG
