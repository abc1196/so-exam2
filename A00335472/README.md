### Examen 2 Solución
**Universidad ICESI**  
**Curso:** Sistemas Operativos  
**Docente:** Daniel Barragán C.  
**Tema:** Namespaces, CGroups, LXC
**Correo:** daniel.barragan at correo.icesi.edu.co

**Nombre:** Alejandro Bueno Cardona  
**Código:** A00335472  
**Correo:** alejandro.bueno at correo.icesi.edu.co  
**URL Git:** https://github.com/abc1196/so-exam2.git  

### Punto 3

 Realice una prueba de concepto empleando systemd y el recurso de control CPUQuota teniendo en cuenta los requerimientos que se describen a cotinuación. Incluya evidencias del funcionamiento de lo solicitado (30%):
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
 
 De cada archivo, destacan dos líneas: la primera, asocia el servicio con su script correspondiente; La segunda, asigna la CPUQuota a 50%, es decir, el máximo uso del núcleo de la CPU, es del 50%. 
 ```
 # ExecStart = /home/operativos/parcialDos/procesoUno.service
 # CPUQuota=50%
 ```
Los dos servicios se habilitan utilizando los siguientes comandos:
```
# systemctl enable procesoUno.service
# systemctl enable procesoDos.service
# systemctl daemon-realod
# systemctl start procesoUno.service
# systemctl start procesoUno.service
```
 
| Ejecución como Scripts |Ejecución como Servicios |
| --- | --- |
| ![][6] | ![][7] |

 En la tabla, se puede observar que los servicios si llegan a consumir como máximo el 50% de la CPU. En cambio, la ejecución como scripts le permite consumir a cada proceso, aproximadamente, el 40-50% de la CPU.
 
 Para confirmar que un solo proceso consume no más del 50%, se mata el proceso con PID=7796, que esta asociado a procesoDos.service.
 
 ![][8]
 
 Como se puede observar, el procesoUno se encuentra dentro del límite establecido.

 ### Punto 4
 
 Para esta prueba, se utiliza máquina del punto anterior, con un solo núcleo de la CPU. También, se tienen los dos procesos: el procesoUno.sh, con 75% de la CPU; el procesoDos.sh, con el 25% de la CPU. Esta situación se logra utilizando CPUShares, que se agrega en el archivo .service de cada proceso. 
 ```
CPUShares=750 //Para procesoUno.service
CPUShares=250 //Para procesoDos.service
```
Ambos archivos quedan de la siguiente manera:

![][9]

Los dos servicios se habilitan utilizando los siguientes comandos:
```
# systemctl enable procesoUno.service
# systemctl enable procesoDos.service
# systemctl daemon-realod
# systemctl start procesoUno.service
# systemctl start procesoUno.service
```
![][10]
 
 En efecto, el procesoUno consume no más del 75% y el procesoDos no excede del 25% de la CPU. Para continuar con la prueba, se mata el procesoDos, cuyo PID es 9310. Después, el procesoUno consume como máximo el 100% de la CPU.
 
 ![][11]
 
### Punto 5

### CPUQuota

Grupo de control que asigna el específicado **CPU time quota** a un proceso determinado. Este valor se representa en porcentaje (%), que establece el tiempo máximo que puede tener de CPU. Si hay más tiempo para consumir, el proceso no puede acceder a este, debido a su valor establecido en el CPUQuota. Se usan valores superiores a 100% cuando se le estipula más de una CPU al proceso.

Como se pudo ver en la práctica, para procesos con CPUQuota=50%, su tiempo de CPU no excedió el 50%.

### CPUShares

Grupo de control que le asigna al proceso específicado un peso determinado de CPU que puede consumir. Este valor es un entero y controla el atributo del grupo de control, que por defecto es 1024. La cantidad disponible de CPU se divide entre los diferentes servicios según su peso determinado de CPU. Inicialmente, un proceso único puede consumir el 100% de la CPU. Cuando se ejecutan nuevos servicios, el porcentaje de CPU para cada uno se determinado según su peso, como ya se mencionó.

En la práctica, se confirmó que al tener un proceso solo, este tiene toda la CPU a disposición, pero al ejecutar el segundo proceso, cada uno termina con su porcentaje establecido, 75% y 25% respectivamente.

En resumen, CPUQuota permite establecer límites de consumo a los procesos, con el objetivo de evitar la falta de recursos en la CPU. En cambio, CPUShares permite hacer un uso más eficiente de los recursos, porque determina el consumo de CPU de cada servicio según un peso determinado. 

Por lo anterior, es preferible utilizar CPUQuota cuando se tienen procesos que consumen en alto grado la CPU. Con este grupo de control, se puede establecer un límite y evitar que los recursos se agoten. Por otro lado, es preferible usar CPUShares cuando se quiere compartir los recursos de manera eficiente, es decir, si se tiene un solo proceso en ejecución, CPUShares le permite consumir el 100% de la CPU. Si otros procesos inician, este grupo de control permite que a cada uno se le asigne un porcentaje determinado, que puede ser establecido según su necesidad de consumir recursos.

### Referencias
https://github.com/ICESI/so-containers  
https://www.systutorials.com/docs/linux/man/5-systemd.resource-control/  

[1]: images/p21.PNG
[2]: images/p22.PNG
[3]: images/p23.PNG
[4]: images/p24.PNG
[5]: images/p25.PNG
[6]: images/p26.PNG
[7]: images/p27.PNG
[8]: images/p28.PNG
[9]: images/p29.PNG
[10]: images/p30.PNG
[11]: images/p31.PNG
