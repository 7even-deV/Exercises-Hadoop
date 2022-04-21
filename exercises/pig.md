# Ejercicios Pig

1. Abrir un terminal en la __VM__ para copiar en __local file system__ el fichero __datos_pig.txt__ desde la carpeta compartida a la ruta __/home/cloudera/exercises/pig__ y abrir el fichero para revisar su contenido. Crear antes su carpeta correspondiente.
    - `mkdir /home/cloudera/exercises/pig |`
    - `cp /mnt/hgfs/datos_pig.txt /home/cloudera/exercises/pig |`
    - `cat /home/cloudera/exercises/pig/datos_pig.txt`

2. Arrancar el __Shell__ de __Pig__ en modo local.
- `pig -x local`

3. Cargar los datos en __pig__ en una variable llamada __data__. Los nombres de las __columnas__ deben ser __(key, campana, fecha, tiempo, display, accion, cpc, pais, lugar)__. Los tipos de las __columnas__ deben ser __chararray__ excepto __acction__ y __cpc__ que son __int__.
- `data = LOAD '/home/cloudera/exercises/pig/datos_pig.txt' AS (key:chararray, campana:chararray, fecha:chararray, tiempo:chararray, display:chararray, accion:int, cpc:int, pais:chararray, lugar:chararray);`

4. Usar el comando __DESCRIBE__ para ver el esquema de la variable __data__.
- `DESCRIBE data;`

5. Seleccionar las filas de __data__ que provengan de __USA__.
- `result = FILTER data BY pais == 'USA';`

6. Listar los datos que contengan en su key el sufijo __surf__.
- `result = FILTER data BY key MATCHES '^surf.*';`

7. Crear una variable llamada __ordenado__ que contenga las __columnas__ de __data__ en el siguiente orden: __(campaña, fecha, tiempo, key, display, lugar, accion, cpc)__.
- `ordenado = FOREACH result GENERATE campana, fecha, tiempo, key, display, lugar, accion, cpc;`

8. Guardar el contenido de la variable __ordenado__ en una carpeta en el __local file system__ de tu __VM__ llamada __resultado__ en la ruta __/home/cloudera/exercises/pig__.
- `STORE ordenado INTO '/home/cloudera/exercises/pig/result';`

9. Comprobar el contenido de la carpeta.
- `cat /home/cloudera/exercises/pig/result`
