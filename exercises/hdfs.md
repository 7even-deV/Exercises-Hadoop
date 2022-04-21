# Ejercicios HDFS

1. Habilitar la transferencia de ficheros entre la __VM__ y el __host__. En las __settings__ del __VMWare__ asociadas a la __VMCloudera__, habilitar la opción __sharedfolders__ y añadir una carpeta en el __host__ Windows desde donde copiar los archivos necesarios para trabajar.

2. Una vez creada, arrancar la __VMCloudera__. La carpeta en Windows compartida estará en la ruta `/mnt/hgfs`.

3. Abrir un terminal en la __VM__ y habilitar los permisos para acceder a carpetas compartidas. Después de ejecutar el __cd de guest additions__, reiniciar la __VM__ para habilitar los cambios.
- `sudo usermod -a -G vboxsf cloudera`

4. Abrir un terminal en la __VM__ y mostrar todos los comandos posibles que contiene la __FsShell__.
- `hadoop fs` o `hdfs dfs`

5. Mostrar el contenido del directorio root en __HDFS__.
- `hadoop fs -ls /` o `hdfs dfs -ls /`

6. Crear un nuevo directorio local de trabajo en la __VM__ con la ruta __/home/cloudera/exercises__.
- `mkdir /home/cloudera/exercises`

    1. Copiar el archivo compartido __shakespeare.tar.gz__ en ese directorio __(cp origen destino)__.
    - `cp /mnt/hgfs/shakespeare.tar.gz /home/cloudera/exercises`

    2. Entrar en el directorio __exercises__.
    - `cd /home/cloudera/exercises`

    3. Descomprimir el archivo __shakespeare.tar.gz__.
    - `tar zxvf shakespeare.tar.gz`

    4. Copiar la carpeta que acabas de descomprimir en el __HDFS__ en la ruta __/user/cloudera/shakespeare__. Tu home a partir de ahora será __/user/cloudera__.
    - `hadoop fs -put shakespeare /user/cloudera/shakespeare`

7. Listar el contenido de tu home en __HDFS__ para comprobar que se ha copiado la carpeta __shakespeare__.
- `hadoop fs -ls /user/cloudera`

8. Observar el contenido de la carpeta __shakespeare__ en el __HDFS__.
- `hadoop fs -ls /user/cloudera/shakespeare`

9. Borrar la subcarpeta __glossary__ de la carpeta __shakespeare__ en __HDFS__.
- `hadoop fs -rm /user/cloudera/shakespeare/glossary`

10. Comprobar que se ha borrado.
- `hadoop fs -ls /user/cloudera/shakespeare`

11. Listar las primeras 50 últimas líneas de la subcarpeta __histories__. Puedes usar los comandos __cat__ y __tail__ (head -> primeras).
- `hadoop fs -cat /user/cloudera/shakespeare/histories | tail -n 50`

12. Copiar al sistema de ficheros local de tu __VM__ el fichero __poems__ en la ruta __/home/cloudera/exercises/shakespeare/shakepoems.txt__.
- `hadoop fs -get /user/cloudera/shakespeare/poems /home/cloudera/exercises/shakespeare/shakepoems.txt`

13. Mostar las últimas líneas de __shakepoems.txt__ copiado en tu local por pantalla.
- `tail 5 /home/cloudera/exercises/shakespeare/shakepoems.txt`

14. Probar algunos de los comandos disponibles en la __Shell__ de __hadoop fs__.
- `hadoop fs`

## Ejecutar un __MapReduce__: __wordcount__

Ejecutar un __Job__ consistente en la ejecución del __wordcount__ en __MapReduce__ sobre el __dataset__ __shakespeare__. Por simplicidad, los ficheros __.class__ y el __.jar__ ya están creados.

El __wordcount__ cuenta el número de palabras distintas que hay en un texto dado.

### Pasos a ejecutar

1. Abrir un terminal en la __VM__ y copiar la carpeta __wordcount__ con su contenido del __HDFS__ en la ruta __/home/cloudera/exercises__.
    - `mkdir /home/cloudera/exercises/wordcount`
    - `cp /mnt/hgfs/wordcount/* /home/cloudera/exercises/wordcount`

2. Comprobar que se han copiado correctamente.
- `ls -ls /home/cloudera/exercises/wordcount`

3. Examinar el contenido de los tres ficheros __java__ para verificar que están correctos.

4. Ejecutar el __submit__ del __Job__ __Hadoop__ usando el fichero __jar__ para contar las ocurrencias de palabras contenidas de la carpeta __shakespeare__. El __jar__ contiene las clases __java__ compiladas dentro de un paquete llamado __solutions__.
- `hadoop jar wc.jar solution.WordCount shakespeare /user/cloudera/wordcount`

5. Una vez ejecutado, probar a ejecutarlo nuevamente y responder que ocurre.
- `Ya existe`

6. Comprobar el resultado del __MapReduce__.
- `hadoop fs -ls /user/cloudera/wordcount`

7. Comprobar que solo hay un archivo de salida.
- `hadoop fs -ls /user/cloudera/wordcount/part-r-00000`

8. Observar el contenido del fichero. (Escribir la letra `q` para salir del comando __less__)
- `hadoop fs -cat /user/cloudera/wordcount/part-r-00000 | less`

9. Volver a ejecutar el __Job__ de nuevo.
- `hadoop jar wc.jar solution.WordCount shakespeare/poems /user/cloudera/pwords`

10. Borrar la salida producida por los __Jobs__.
- `hadoop fs -rm -r /user/cloudera/wordcount /user/cloudera/pwords`

11. Ejecutar nuevamente el __Job__.
- `hadoop jar wc.jar solution.WordCount shakespeare /user/cloudera/count2`

12. Mientras se ejecuta, en otro terminal ejecutar lo siguiente, para ver la lista de __Jobs__ que se están ejecutando.
- `mapred job -list`

13. Si conoces la __id__ de un __Job__ podrás matarlo. Recordar que cerrando un terminal no se mata el job. Ejecutar en otra terminal lo siguiente.
- `mapred job -kill jobid`

14. Si no te ha dado tiempo, prueba a ejecutar el __Job__ otra vez cambiándolo de nombre y prueba nuevamente.
