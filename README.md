# DBMarlin

Setup DBMarlin server

  Prerequisitos
  
  1. Instalar net-tools para validar disponibilidad de puertos 9070, 9080 y 9090.
  
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




  


