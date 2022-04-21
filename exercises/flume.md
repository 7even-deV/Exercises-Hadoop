# Ejercicios Flume

## Configuración básica de __Flume__

1. La configuración de un agente __Flume__ está almacenada en un fichero. A continuación, se detalla el contenido que ha de tener dicho fichero. Para este ejercicio vamos a definir:
    - Un agente: `a1`
    - Que escucha por un puerto: `44444`
    - Un channel que almacena los datos en memoria
    - Un sink que muestra datos por consola

2. En caso de no tener instalado el __Telnet__ en la __VM__ ejecutar los siguientes pasos:
    - `yum install telnet telnet-server –y`
    - `sudo chmod 777 /etc/xinetd.d/telnet`

3. Editar el archivo anterior y actualizar la variable __disable=no__ en caso que no estuviera.
- `vi /etc/xinetd.d/telnet disable=no`

4. Abrir otra __Shell__ para comprobar que está correcto.
- `cat /etc/xinetd.d/telnet`

5. Reiniciar el servicio.
- `sudo service xinetd start`

6. Hacer que el servicio arranque automáticamente.
    - `sudo chkconfig telnet on`
    - `sudo chkconfig xinetd on`

7. En el directorio __/etc/flume-ng__ listar las carpetas que contienen las plantillas que hay que configurar para realizar una importación de datos con __Flume__.
- `ls /etc/flume-ng`

8. Escribir por consola lo que se escribirá a través de __Telnet__ en un __Shell__. Para ello crear (manualmente) un fichero llamado __example.conf__ y guardarlo en la ruta __/home/cloudera__. A continuación meter en el fichero el contenido siguiente:

| # Name the components on this agent |
| - |
`a1.sources = r1`
`a1.sinks = k1`
`a1.channels = c1`

| # Describe/configure the source |
| - |
`a1.sources.r1.type = netcat`
`a1.sources.r1.bind = localhost`
`a1.sources.r1.port = 44444`

| # Describe the sink |
| - |
`a1.sinks.k1.type = logger`

| # Use a channel which buffers events in memory |
| - |
`a1.channels.c1.type = memory`
`a1.channels.c1.capacity = 1000`
`a1.channels.c1.transactionCapacity = 100`

| # Bind the source and sink to the channel |
| - |
`a1.sources.r1.channels = c1`
`a1.sinks.k1.channel = c1`

9. Abrir un primer __Shell__ donde arrancar el __agente__ __Flume__.
- `flume-ng agent --conf conf --conf-file /home/cloudera/example.conf --name a1 -Dflume.root.logger=INFO, console`

10. Abrir un segundo __Shell__ donde ejecutar el __Telnet__.
- `telnet localhost 44444`

11. Escribir algo en este segundo __Shell__ para comprobar que se envía del __Telnet__ al primer __Shell__ donde está el __agente__ __Flume__.
- `Hello World!`

## Importar datos de un __spool-dir__

La idea de este ejercicio es ir dejando ficheros en un directorio __(spool-dir)__ y ver como __Flume__ los va consumiendo.

1. Abrir un __Shell__ para crear el directorio __spool__ y darle permisos.
    - `sudo mkdir -p /var/log/apache/flumeSpool`
    - `sudo chmod 777 /var/log/apache/flumeSpool`

2. Crear también los directorios __checkpoint__ y __datadir__ y darles permisos. (Si no se hiciera, __Flume__ los crea por default pero para poder utilizarlos es necesario darles permisos.
    - `sudo mkdir -p /mnt/flume/checkpoint`
    - `sudo mkdir -p /mnt/flume/data`
    - `sudo chmod 777 /mnt/flume/checkpoint`
    - `sudo chmod 777 /mnt/flume/data`

3. Crear un nuevo fichero (manualmente) de configuración en la misma ruta que en el ejemplo anterior llamado __example2.conf__, y modificar la configuración del __source__ cambiándola por la siguiente:
    - `a1.sources.r1.type = spooldir`
    - `a1.sources.r1.spoolDir = /var/log/apache/flumeSpool`
    - `a1.sources.r1.fileHeader = true`

4. Arrancar __Flume__ en un __Shell__.
- `flume-ng agent --conf conf --conf-file /home/cloudera/example2.conf --name a1 -Dflume.root.logger=DEBUG,console -Dorg.apache.flume.log.printconfig=true -Dorg.apache.flume.log.rawdata=true`

5. Abrir un nuevo __Shell__ para comprobar que funciona, posicionandose en la ruta donde está definido el __spool-dir__ y crear un fichero con el editor __vi__ (recomendable) o con el explorador de archivos de __Linux__.
    - `cd /var/log/apache/flumeSpool`
    - `touch example`
    - `computer/ FilesSytem/var/log/apache/flumeSpool`

6. Prestar atención al __Shell__ donde está __Flume__ corriendo y ver cómo se envían y muestran los ficheros por consola.

## Importar datos desde un __spool-dir__ a __HDFS__

1. Abrir un __Shell__ para crear el directorio en __HDFS__ donde dejar los datos importados desde el __spool-dir__ a través del __channel__ de __Flume__.
    - `hadoop fs -mkdir /flume`
    - `hadoop fs -mkdir /flume/events`

2. Crear un nuevo fichero (manualmente) de configuración llamado __example3.conf__, igual que el del ejemplo anterior, pero sustituyendo la descripción del __sink__ por lo siguiente:
    - `a1.sinks.k1.type = hdfs`
    - `a1.sinks.k1.hdfs.path=/flume/events`

3. Correr el __agente__ __Flume__.
- `flume-ng agent --conf conf --conf-file /home/cloudera/example3.conf --name a1 -Dflume.root.logger=DEBUG, console -Dorg.apache.flume.log.printconfig=true -Dorg.apache.flume.log.rawdata=true`

4. Posicionar en el directorio __spool__ y crear un fichero con algo escrito. Después acceder a la carpeta __HDFS__ donde debe estar para comprobarlo. (Tarda un poco)
- `sqoop import --connect jdbc:mysql://localhost/pruebadb --table tabla_prueba --username root --password cloudera -m 1 --hive-import --hive-overwrite --hive-table prueba_sqoop_hive.tabla_prueba_hive`

5. Prestar atención al nombre de la carpeta.

6. Para mejorar un poco la info que nos devuelve __Flume__, añadir la siguiente configuración del __HDFS__:
    - `a1.sinks.k1.hdfs.path = /flume/events/%y-%m-%d/%H%M/%S`
    - `a1.sinks.k1.hdfs.filePrefix = events-`
    - `a1.sinks.k1.hdfs.round = true`
    - `a1.sinks.k1.hdfs.roundValue = 10`
    - `a1.sinks.k1.hdfs.roundUnit = minute`

7. Observar cómo cambia la estructura de carpetas donde se almacenan los datos en el __sink__. Crear un nuevo fichero en el __spool__ e ir al __HDFS__ para ver cómo se ha importado:
    - `(hadoop fs -cat /flume/events/*`
    - `hadoop fs -rm /flume/events/*)`

8. Si abres uno de los ficheros de datos importados, verás que el contenido del fichero que envias tiene caracteres extraños. Esto es porque por defecto __Flume__ escribe datos serializados __(....BytesWritable)__. Recuerda que una de las propiedades de __Hadoop__ es que serializa los datos con los que trabaja __(interfaz Writable)__.
Mostrar los datos en formato Texto.
    - `a1.sinks.k1.hdfs.writeFormat = Text`
    - `a1.sinks.k1.hdfs.fileType = DataStream`
