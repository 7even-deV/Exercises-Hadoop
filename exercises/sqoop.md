# Ejercicios Sqoop

## Importar una tabla en __MySQL__ a __Hive__

### Creación de la tabla en __MySQL__

1. Abrir un terminal en la __VM__ y probar la conexión con __MySQL__. (Recuerda que al escribir el __Password__ aunque no lo veas en pantalla si estás escribiendo)
    - `mysql -u root -p`
    - Password: `cloudera`

2. Ver las __BBDD__ que contiene.
- `SHOW databases;`

3. Crear en __MySQL__ la __BBDD__ que queremos importar en __Hive__.
- `CREATE database pruebadb;`

4. Crear una __table__ con datos que luego importaremos a __Hive__ mediante __Sqoop__.
    - `USE pruebadb;`
    - `CREATE table tabla_prueba (nombre varchar(30), edad int);`

5. Comprobar que se ha creado.
- `SHOW tables;`

6. Importar algunas filas.
    - `INSERT INTO tabla_prueba VALUES ("Alberto", 22);`
    - `INSERT INTO tabla_prueba VALUES ("Luis", 23);`
    - `INSERT INTO tabla_prueba VALUES ("Pablo", 24);`
    - `INSERT INTO tabla_prueba VALUES ("Carlos", 25);`
    - `INSERT INTO tabla_prueba VALUES ("Santiago", 26);`

7. Comprobar que los datos se han insertado en la __table__.
    - `SELECT * FROM tabla_prueba;`
    - `DESCRIBE tabla_prueba;`

### Creación de la tabla en __Hive__

Crear la __table__ en __Hive__ donde se importarán los datos que acabamos de crear.

1. Abrir otro terminal en la __VM__ y acceder a __Hive__.
    - `hive`

2. Crear una base de datos para esta prueba y acceder a ella.
    - `CREATE database prueba_sqoop_hive;`
    - `USE prueba_sqoop_hive;`

3. Abrir otro terminal en la __VM__ y comprobar que está en el __warehouse__ de __Hive__.
- `hadoop fs -ls /user/hive/warehouse;`

4. Abrir otro terminal en la __VM__ y acceder a __Hive__ para crear la estructura de la __table__ que contendrá los datos importados desde __MySQL__ con __Sqoop__.
    - `hive`
    - `DROP table tabla_prueba_hive;`
    - `CREATE TABLE tabla_prueba_hive(nombre string, edad int)ROW FORMAT DELIMITED STORED AS TEXTFILE;`

5. Comprobar que se ha creado con éxito.
- `SHOW tables;`

### Importar la tabla con __Sqoop__

1. Dado que la __BBDD__ __Accumulo__ no está configurada, abrir un __Shell__ y ejecutar los siguientes comandos para evitar __warnings__ molestos.
    - `sudo mkdir /var/lib/accumulo |`
    - `ACCUMULO_HOME='/var/lib/accumulo' |`
    - `export ACCUMULO_HOME`

2. Verificar que __Sqoop__ está conectado con el __MySQL__.
- `sqoop list-databases --connect jdbc:mysql://localhost --username root --password cloudera`

3. Listar la tabla __table_prueba__ de la __BBDD__ __pruebadb__ que creaste en __MySQL__.
- `sqoop list-tables --connect jdbc:mysql://localhost/pruebadb --username root --password cloudera`

4. Usar los argumentos de importación __Hive__ mostrados en las __slides__ del curso, importar la tabla creada en __MySQL__ en la estructura creada en __Hive__. Usar como conector __(jdbc:mysql://localhost/bbddMysql)__ y un solo __mapper__.
- `sqoop import --connect jdbc:mysql://localhost/pruebadb --table tabla_prueba --username root --password cloudera -m 1 --hive-import --hive-overwrite --hive-table prueba_sqoop_hive.tabla_prueba_hive`
