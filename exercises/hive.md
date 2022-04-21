# Ejercicios Hive

1. Abrir un terminal en la __VM__ y entrar en __Hive__.
- `hive`

2. Modificar la propiedad correspondiente para mostrar por pantalla las cabeceras de las tablas.
- `set hive.cli.print.header=true;`

3. Crear una nueva base de datos llamada __cursohivedb__. Eliminar antes la base de datos si existe y asegurarnos que no está antes de crearla.
    - `drop database if exists userdb cascade;`
    - `show databases;`
    - `CREATE DATABASE cursohivedb;`

4. Situarnos en la base de datos recién creada para trabajar con ella.
- `USE cursohivedb;`

5. Comprobar que la base de datos está vacía.
- `SHOW TABLES;`

6. Crear una tabla llamada __iris__ en la base de datos, que contenga 5 columnas __(s_length float, s_width float, p_length float, p_width float, clase string)__, cuyos campos estén separados por comas __(ROW FORMAT DELIMITED FIELDS TERMINATED BY ',')__. Eliminar antes la tabla si existe.
    - `DROP TABLE iris;`
    - `CREATE TABLE iris(s_length float, s_width float, p_length float, p_width float, clase string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',';`

7. Comprobar que la tabla se ha creado y el tipado de sus columnas.
    - `SHOW TABLES;`
    - `DESC iris;`

8. Abrir otro terminal en la __VM__ para importar el fichero __iris_completo.txt__ de la carpeta compartida al local file system del cluster en una nueva carpeta llamada __hive__ con la ruta __/home/cloudera/exercises/hive__.
    - `mkdir /home/cloudera/exercises/hive |`
    - `cp /mnt/hgfs/iris_completo.txt /home/cloudera/exercises/hive`

9. Copiar el fichero a __HDFS__ en la ruta __/user/cloudera/hive__. Realizar las acciones necesarias.
    - `hadoop fs -mkdir /user/cloudera/hive |`
    - `hadoop fs -put /home/cloudera/exercises/hive/iris_completo.txt /user/cloudera/hive`

10. Comprobar que el fichero está en la ruta en __HDFS__ indicada.
- `hadoop fs -ls /user/cloudera/hive`

11. Dentro de __Hive__, situarnos en la base de datos __cursohivedb__ e importar el fichero en la tabla __iris__ que acabamos de crear desde __HDFS__.
    - `hive`
    - `USE cursohivedb;`
    - `LOAD DATA INPATH '/user/cloudera/hive/iris_completo.txt' INTO TABLE iris;`

12. Comprobar que la table tiene datos.
- `SHOW * FROM iris;`

13. Mostrar las 5 primeras filas de la tabla __iris__.
- `SELECT * FROM iris LIMIT 5;`

14. Mostrar solo aquellas filas cuyo __s_length__ sea mayor que 5. Observar que se ejecuta un __MapReduce__ y que el tiempo de ejecución es un poco mayor.
- `SELECT * FROM iris WHERE s_length > 5;`

15. Seleccionar la media de __s_width__ agrupados por clase. Observar que ahora el tiempo de ejecución aumenta considerablemente.
- `SELECT avg(s_width) FROM iris GROUP BY clase;`

16. Pregunta: Vemos que aparece un valor __NULL__ como resultado en la query anterior. ¿Por qué? ¿cómo los eliminarías?
    - `Por que había algún dato erróneo, no numérico o nulo en el campo de alguna clase.`
    - `SELECT avg(s_width) FROM iris WHERE s_width IS NOT NULL GROUP BY clase;`

17. Insertar en la tabla la siguiente fila __(1.0,3.2,4.3,5.7, "Iris-virginica")__.
- `INSERT INTO TABLE iris VALUES (1.0,3.2,4.3,5.7, "Iris-virginica");`

18. Contar el número de ocurrencias de cada __clase__.
- `SELECT clase, COUNT(*) FROM iris GROUP BY clase;`

19. Seleccionar las __clases__ que tengan más de 45 ocurrencias.
- `SELECT clase, COUNT(*) FROM iris GROUP BY clase HAVING COUNT(*) > 45;`

20. Utilizando la función __LEAD__, ejecutar una query que devuelva la __clase__, __p_length__ y el __LEAD__ de __p_length__ con __Offset=1__ y __Default_Value=0__, particionado por __clase__ y ordenado por __p_length__.
- `SELECT clase, p_length, LEAD(p_length,1,0) OVER (PARTITION BY clase ORDER BY p_length) AS LEAD FROM iris;`

21. Utilizando funciones de ventanas, seleccionar la __clase__, __p_length__, __s_length__, __p_width__, el número de valores distintos de __p_length__ en todo el dataset, el valor máximo de __s_length__ por __clase__ y la media de __p_width__ por __clase__, ordenado por __clase__ y __s_lengthde__ manera descendente.
- `SELECT clase, p_length, s_length, p_width, COUNT(p_length) OVER (partition by p_length) AS pl_ct, MAX(s_length) OVER (partition by clase) AS sl_ct,avg(p_width) OVER (partition by clase) AS sl_av FROM iris ORDER BY clase, s_length DESC;`
