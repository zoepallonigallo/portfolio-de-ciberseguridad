**Bonus: Troubleshooting de VirtualizaciónDurante la práctica del CTF INBICE 2019/2020**

nos enfrentamos a una fuga de memoria masiva (memory leak) provocada por VirtualBox,
donde los procesos en segundo plano consumían más de 14 GB de RAM, congelando el sistema Windows anfitrión y requiriendo reinicios forzados.  
Para poder finalizar el CTF, aplicamos metodologías de resolución de problemas en diferentes capas: 

Capa de Hardware Virtual: Limitamos estrictamente la RAM (Kali a 4GB, objetivo a 512MB) y deshabilitamos la aceleración 3D, audio y puertos USB para reducir la superficie de interacción.  

Capa de Red: Cambiamos de "Adaptador Puente" a "Red NAT" para crear un entorno seguro aislado y evitar la inyección de controladores en la tarjeta física del host.  

Capa de Sistema Operativo: Deshabilitamos la "Integridad de Memoria" (Aislamiento del núcleo) en Windows, ya que los VBS chocaban con el hipervisor. Modificamos el registro de arranque (bcdedit /set hypervisorlaunchtype off).

Ejecución Headless: Finalmente, ejecutamos la máquina vulnerable "Sin pantalla" (en segundo plano) para mitigar los consumos de memoria por renderizado gráfico. 


La solución definitiva al problema fue reinstalar la máquina virtual de Linux desde cero, asegurándonos de asignarle un espacio en disco significativamente mayor.
La falta de almacenamiento en la partición original estaba generando un cuello de botella y saturación en el sistema de archivos, lo que desencadenaba el colapso del hipervisor y la posterior fuga masiva de RAM en el equipo anfitrión.
Al aprovisionar correctamente el disco, el entorno funcionó con total estabilidad.


Este troubleshooting exhaustivo apuntó a un fallo profundo de compatibilidad entre la versión del kernel de VirtualBox y los parches de seguridad de Windows. 


