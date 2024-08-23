# DB Marlin

Setup DBMarlin server

  Prerequisitos
  
  1. Revisar plataformas soportadas Linux y Windows (https://docs.dbmarlin.com/docs/Getting-Started/supported-platforms)
  2. Para este laboratorio utilizaremos un servidor Ubuntu 22.04.3 LTS.
  3. Instalar net-tools para validar disponibilidad de puertos 9070, 9080 y 9090.
  
    # Install if needed RHEL or Centos
    sudo yum install net-tools
    
    # Install if needed Ubuntu
    sudo apt-get install net-tools
    
    netstat -na | grep LISTEN | grep 9080
    netstat -na | grep LISTEN | grep 9090
    netstat -na | grep LISTEN | grep 9070
  
  2. Crear usuario y grupo dbmarlin
  
    useradd dbmarlin
    mkdir /opt/dbmarlin
    chown -R dbmarlin:dbmarlin /opt/dbmarlin
  
  3. Descargar instalador desde la web https://download.dbmarlin.com


  Instalación
  
  1. Loguearse con el usurio dbmarlin para descomprimir e inciar la instalacion. 

  2. Descomprimir en la ruta /opt
     
    dbmarlin-Linux-x64-[VERSION].tar.gz 
    cd /opt
    tar -xzvf dbmarlin-Linux-x64-[VERSION].tar.gz

  2. Ingresar a la ruta donde se descomprimio el instalador e instalar de acuerdo a como se muestra.

    cd /opt/dbmarlin
    ./configure.sh
    Press Enter then space bar to scroll
    ...
    Do you accept the agreement Y/N ? y
    Done.
    Set port for Nginx (current 9090): 9090
    Set port for Tomcat (current 9080): 9080
    Set port for PostgreSQL (current 9070): 9070
    See https://docs.dbmarlin.com/docs/Getting-Started/server-installation/hardware-requirements for Profile sizes
    1) XSmall
    2) Small
    3) Medium
    4) Large
    5) Xlarge
    Choose the profile [1-5]: 2
    Small profile selected
    Recommended RAM is 4GB. You have 4G
    Continue Y/N ? y
    
    
    Configuring DBmarlin now...
    Changing Nginx port from 9090 to 9090
    Changing Tomcat port from 9080 to 9080
    Using profile Small
    All done!
    Use ./start.sh to startup the DBmarlin services


  3. Iniciar servicios del DBMarlin server.

    cd /opt/dbmarlin
    ./start.sh

  4. Verificar estado de servicios.

    cd /opt/dbmarlin
    ./status.sh
    
    nginx       (Running)
    tomcat      (Running)
    postgres    (Running)

# Configuracion de ambiente de pruebas en AKS

Componentes:

Azure Kubernetes service:
- Base de Datos Mysql contactos(tabla emails)
- Aplicacion Frontend en nodesjs para consulta y registro de contactos.
- DB Marlin agent desplegado en kubernetes.

Virtual Server:
- DB Marlin server implemementado en los pasos anteriores

![image](https://github.com/juan-conde-21/DBMarlin/assets/13276404/b22f88bc-00f8-4430-82f2-f247cf3c02ca)

Procedimiento:

1. Los recursos seran desplegados dentro del namespace default.
  
2. Aplicar la configuracion de base de datos.

        kubectl apply -f database.yaml

3. Aplicar la configuracion del fontend.
 
        kubectl apply -f frontend.yaml

4. Aplicar la configuracion del agente DB Marlin ( Reemplazar la ip publica del servidor DB Marlin y aplicar la configuracion, para este ejemplo es la ip 163.107.83.92 )

        kubectl apply -f marlin-agent.yaml

5. Verificar recursos desplegados:
 
        kubectl get all

    ![image](https://github.com/juan-conde-21/DBMarlin/assets/13276404/19608ed5-2d5a-4b05-bc01-0c3937300047)

6. Ejecutar la validacion para la ip publica del frontend, en el ejemplo 20.171.241.74

   Operación GET para listar los contactos registrados

   ![image](https://github.com/juan-conde-21/DBMarlin/assets/13276404/9af7d201-6ef1-4efa-bd03-c3aed7cd570d)

   Operación POST para registrar nuevos contactos
 
   ![image](https://github.com/juan-conde-21/DBMarlin/assets/13276404/6eb4760a-68c1-4cef-b512-d29c47bbc9ac)

7. Para realizar el monitoreo mediante el agente DB Marlin en la BD Mysql se requiere de la creacion de un usuario con los permisos de lectura sobre las bases de datos. Para ello ingresar al pod de la BD y ejecutar las siguientes sentencias. [documentacion](https://docs.dbmarlin.com/docs/Monitored-Technologies/Databases/mysql)

       *Colocar el nombre del pod mysql correspondiente en su ambiente.
       kubectl exec -it mysql-5b8854c844-74vrv -- bash

        mysql -u root -pP4ssw0rd
        
        CREATE USER dbmarlin IDENTIFIED BY 'securepassword';
        GRANT SELECT,PROCESS,SHOW DATABASES on *.* to 'dbmarlin'@'%';
        GRANT REPLICATION CLIENT ON *.* to 'dbmarlin'@'%';
        FLUSH privileges;


8. Registrar la BD Mysql en la consola de DB Marlin y configurar el monitoreo mediante el agente DB Marlin previamente desplegado en el cluster AKS.


![image](https://github.com/juan-conde-21/DBMarlin/assets/13276404/4a817c36-fb79-44ed-8197-dc33f9351e9a)
![image](https://github.com/juan-conde-21/DBMarlin/assets/13276404/4a615e7b-6b81-47ff-8baa-5407f12230c5)



9. Configurar la extension de DB Marlin en su navegador web Edge o Chrome. Colocar la url del servidor DB Marlin como se muestra en la imagen.


![image](https://github.com/juan-conde-21/DBMarlin/assets/13276404/299f9595-e1dd-45c0-94d3-3f6688605350)

10. Verificar las trazas capturadas en instana y ubicar las sentencias SQL donde se mostrara el icono que enlaza instana con DB Marlin para obtener mayor detalle a nivel de base de datos.

![image](https://github.com/juan-conde-21/DBMarlin/assets/13276404/802191de-08c2-49b2-bc71-bc0b83712916)
![image](https://github.com/juan-conde-21/DBMarlin/assets/13276404/a997a03d-dfe1-41f1-8359-76845f15d986)
![image](https://github.com/juan-conde-21/DBMarlin/assets/13276404/6b9662df-7375-480d-8a33-b05ac710590e)

![image](https://github.com/juan-conde-21/DBMarlin/assets/13276404/a504b674-1f9d-444a-a286-cd1cf797cf54)
![image](https://github.com/juan-conde-21/DBMarlin/assets/13276404/d488cc1f-2b08-427c-b39e-84dee7e6387e)
![image](https://github.com/juan-conde-21/DBMarlin/assets/13276404/ad9f0346-ad59-4a5e-a3e3-b9879429fbca)









  


