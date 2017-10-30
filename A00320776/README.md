# Solución

**Nombre:** Diego Lamus  

**Código:** A00320776

# Respuestas

**1. Prueba systemd y CPUQuota**

Nota: Las pruebas se realizaron sobre un solo nucleo de la CPU. Lo anterior se puede evidenciar al observar los datos producidos por el aplicativo TOP en las siguientes imágenes; la tercera linea lista las CPUs de la máquina, y como se observa casi todo el procesamiento recae sobre la CPU "us" (la primera en la lista):

Primero se crearon dos units, count.service y count2.service, que systemd puede procesar y manejar. Las units inician cada una un proceso, count.sh y count2.sh respectivamente. En cada una de las units, como se observa en la imagen a continuacion, en la sección de [Service] se estableció CPUQuota=50%. Al establecer una CPUQuota del 50% lo que se está haciendo es decirle a systemd que ese servicio tiene acceso máximo al 50% del procesamiento de la CPU.

  ![](https://github.com/diegolamus/so-exam2/blob/A00320776/Respuestas/A00320776/imagenes/Servicios.PNG)  

Luego, se ejecutaron los dos servicios haciendo uso de los comandos "systemctl start count.service" y "systemctl start count2.service", los cuales incician los dos procesos que tienen asigandos. Al observar el consumo de recursos con el aplicativo TOP es posible observar que cada proceso utiliza aproximadamente el 50% del la CPU.

![](https://github.com/diegolamus/so-exam2/blob/A00320776/Respuestas/A00320776/imagenes/dos%20procesos.PNG)  

Finalmente, al detener uno de los procesos es posible observar que el proceso que queda en ejecución sigue utilizando aproximandamente el 50% de la CPU, a pesar de que ya hay mas capacidad disponible (se limitó el uso de la CPU para ese proceso).

![](https://github.com/diegolamus/so-exam2/blob/A00320776/Respuestas/A00320776/imagenes/1%20proceso.PNG)  


**2. Prueba systemd y CPUShares**

A continuación se realizan pruebas con las mismas caracteristicas que las del punto anterior, con la diferencia de que ahora no se utilizará CPUQuota para limitar el uso de la CPU sino que se utilizará CPUShares para darle una participación de la CPU a cada uno de los procesos en ejecución. Las shares se pueden ver como acciones, indican que tanto le pertenece a un proceso de la CPU. Como se observa en la imagen a continuación, a un proceso se le asignaron 500 shares y al otro 1500 shares.

![](https://github.com/diegolamus/so-exam2/blob/A00320776/Respuestas/A00320776/imagenes/shares%20servicio.PNG)  

Posteriormente se ejecutaron ambos procesos. Al ejecutar los dos procesos, entre los dos tienen 2000 shares. A un proceso le pertenecen 500 shares, lo que representa el 25% del total ((500/2000)*100); al otro 1500 shares, lo que representa el 75% del total ((1500/2000)*100). Los porcentajes indican cuanto puede usar cada proceso de la CPU, y como se observa con el aplicativo TOP los procesos utilizan aproximadamente lo que tienen que utilizar. 

![](https://github.com/diegolamus/so-exam2/blob/A00320776/Respuestas/A00320776/imagenes/2%20cpu%20shares.PNG)  

Al detener uno de los procesos, en este caso el que le pertenecen 1500 shares, quedan un total de 500 shares. Al proceso que esta corriendo le corresponden 500 shares, lo que representa el 100% del total. Como se observa con el aplicativo TOP el proceso que queda utiliza cerca del 100% de la CPU.

![](https://github.com/diegolamus/so-exam2/blob/A00320776/Respuestas/A00320776/imagenes/1%20cpu%20shares.PNG)  


**3. definición CPUQUota y CPUShares**  

  **CPUQuota=X%**  
  
  Indica el maximo que podra ser usado de la CPU por un proceso. En este caso el maximo está definido entre  0%-100% de la CPU. 
  
      Ejemplo:
      [Service]
      CPUQuota=20%  
      
   
      Lo anterior indica que el servicio que tenga asociado ese segmento de código podrá utilizar como máximo el 20% de la CPU.

  **CPUShares=X**  
  
  Indica cuanta participación tiene un proceso de la CPU. Un proceso, al tener definido CPUShares, le perteneceran un porcentaje del uso de la CPU, el cual estara definido de la siguiente forma: "numero_shares_asignadas/total_shares_asignadas". numero_shares_asignadas es el numero de shares que se definieron para ese proceso en particular; total_shares_asiganadas será el total de shares de los procesos activos.
  
      Ejemplo:
      [Service]
      CPUShares=1500
    
      Lo anterior indica que a este proceso en particular le corresponden 1500 shares. Ahora supongamos que se está ejecutando otro proceso al que le pertenecen 500 shares. Por lo tanto, al proceso actual cuando se ejecute le corresponderá  el 75% (1500/(1500+500)) del uso de la CPU. Ahora si el proceso al cual le corresponden 500 shares es detenido, al proceso actual le corresponderá el 100% (1500/1500) del uso de la CPU.
  
**Cuando usar CPUQuota y cuando usar CPUShares**

La respuesta a esto depende de lo que se quiera lograr. Si lo que se quiere es limitar un proceso para que tenga acceso solo a cierto porcentaje de la CPU es preferible usar CPUQuota. Si lo que se requiere  es dividir el uso de la CPU entre ciertos procesos, dandole prioridad a los mas importantes, será preferible usar CPUShares.

Ambos controles pueden ser utilizados en conjunto. Por ejemplo, si se da el caso en el que se requiere ejecutar un conjunto de procesos en una misma CPU y que cada uno pueda usar un porcentaje de la CPU específico, pero que cuando solo se este ejecutando un subconjunto del conjunto original, dicho subconjunto no pueda sobrepasar cierto límite del uso de la CPU. En este caso CPUShares se utilizará para asignar el procentaje que le corresponde a cada uno y CPUQUota para limitar el uso maximo en caso de que no se ejecuté todo el conjunto de procesos. 
  
  
