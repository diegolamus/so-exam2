# Solución

**Nombre:** Diego Lamus
**Código:** A00320776

# Respuestas

**1. Prueba systemd y CPUQuota**

Nota: Las pruebas se realizaron sobre un solo nucleo de la CPU. Lo anterior se puede evidenciar al observar los datos producidos por el aplicativo TOP en las siguientes imagenes; la tercera linea lista las CPUs de la maquina, y como se observa casi todo el procesamiento recae sobre la CPU us (la primera en la lista):

Primero se crearon dos units, count.service y count2.service, que systemd puede procesar y manejar. Los servicios inician cada uno un proceso, count.sh y count2.sh respectivamente. En cada una de las units, como se observa en la imagen a continuacion, en la sección de [Service] se estableció CPUQuota=50%. Al establecer una CPUQuota del 50% lo que se está haciendo es decirle a systemd que ese servicio tiene acceso máximo al 50% del procesamiento de la CPU.

  ![](https://github.com/diegolamus/so-exam2/blob/A00320776/Respuestas/A00320776/imagenes/Servicios.PNG)  

Luego, se ejecutaron los dos servicios haciendo uso del comando "systemctl start count.service" y al observar el consumo de recursos con el aplicativo TOP es posible observar que cada servicio utiliza aproximadamente el 50% del la CPU.

![](https://github.com/diegolamus/so-exam2/blob/A00320776/Respuestas/A00320776/imagenes/dos%20procesos.PNG)  

Finalmente, al detener uno de los procesos es posible observar que el proceso que queda en ejecución sigue utilizando aproximandamente el 50% de la CPU, a pesar de que ya hay mas capacidad disponible (se limitó el uso de la CPU para ese proceso).

![](https://github.com/diegolamus/so-exam2/blob/A00320776/Respuestas/A00320776/imagenes/1%20proceso.PNG)  


**2. Prueba systemd y CPUShares**


![](https://github.com/diegolamus/so-exam2/blob/A00320776/Respuestas/A00320776/imagenes/shares%20servicio.PNG)  

![](https://github.com/diegolamus/so-exam2/blob/A00320776/Respuestas/A00320776/imagenes/2%20cpu%20shares.PNG)  

![](https://github.com/diegolamus/so-exam2/blob/A00320776/Respuestas/A00320776/imagenes/1%20cpu%20shares.PNG)  


**3.** Por medio de las evidencias obtenidas en los puntos anteriores y de fuentes de consulta en Internet, elabore las definiciones para los grupos de control CPUQuota y CPUShares, además concluya acerca de cuando es preferible usar un recurso de control sobre otro (20%
