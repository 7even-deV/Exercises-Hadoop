# Ejercicios Spark

### Los ejercicios están preparados para ser resueltos en __Scala__.

## Usando el __Shell__ de __Spark__ __(módulo 1)__

Trabajar con el __Shell__ de __Spark__ en __Scala__ para leer un fichero en un __RDD__.

1. Arrancar el __Shell__ de __Spark__ para __Scala__ y familiarizarse con la información que aparece por pantalla (Infos, Warnings, versión de __Scala__ y __Spark__, etc…). Tarda un poco en arrancar.
- `spark-shell`

2. Comprobar que se ha creado un contexto __sc__.
- `sc;`

3. Presionar el tabulador después de escribir el objeto __SparkContext__ seguido de un punto.
- `saprkcontext.`

4. Salir del __Shell__.
- `exit;` o `Cntrl+C`

## Comenzando con los __RDDs__ __(módulo 2)__

Practicar con los __RDDs__ a través del __Shell__ de __Spark__, para lo que usaremos ficheros externos.

### A - Exploración de fichero plano 1

1. Iniciar el __Shell__ de __Spark__ si te saliste en el ejercicio anterior.
- `spark-shell`

2. Trabajar con datos en local. Para acceder al fichero en local, se coloca delante de la ruta la palabra `file:`.

3. Crear una carpeta llamada __BIT__ en __/home__ de forma que se cree la ruta __/home/BIT__ y copiar dentro de ella todos los ficheros de datos necesarios para el curso. Copiar la carpeta __data_spark__ a la __VM__ como en otras ocasiones y familiarizaos con su contenido. (Necesitarás habilitar los permisos)
    - `sudo -s`
    - `mkdir /home/BIT`
    - `cp /mnt/hgfs/data_spark/* /home/BIT`
    - `chmod 777 /home/BIT/*`

4. Dentro de __data_spark__ se encuentra el fichero __relato.txt__. Copiar este fichero en la __VM__ en la ruta __/home/BIT/data/relato.txt__.
    - `mkdir /home/BIT/data`
    - `cp /home/BIT/relato.txt /home/BIT/data`

5. Visualizar el fichero con un editor de texto como __gedit__ o __vi__ o a través de la __Shell__ con el comando __cat__.
- `cat /home/BIT/data/relato.txt`

6. Dentro del __Shell__ de __Spark__. Crear un __RDD__ llamado __relato__ que contenga el contenido del fichero utilizando el método __textFile__.
    - `spark-shell`
    - `val relato=sc.textFile("file:/home/BIT/data/relato.txt")`

7. Una vez hecho esto, observa que aún no se ha creado el __RDD__. Esto ocurrirá cuando ejecutemos una acción sobre el __RDD__.

8. Cuenta el número de líneas del __RDD__ y observa el resultado. Si el resultado es __23__ es correcto.
- `relato.count()`

9. Ejecutar el método __collect()__ sobre el __RDD__ y observa el resultado.
- `relato.collect()`

10. Observar el resto de métodos aplicables sobre el __RDD__ como vimos en el ejercicio anterior.

11. Investigar cómo usar la función __foreach__ para visualizar el contenido del __RDD__ de una forma más cómoda de entender.
- `relato.foreach(println)`

### B - Exploración de fichero plano 2

1. Copiar la carpeta __weblogs__ contenida en la carpeta de ejercicios de __Spark__ a __/home/BIT/data/weblogs/__ y revisa su contenido.
    - `cp /home/BIT/weblogs /home/BIT/data`
    - `ls -l /home/BIT/data/weblogs`

2. Escoger uno de los ficheros, acceder, y estudiar cómo está estructurada cada una de sus líneas (datos que contiene, separadores (espacio), etc).
- `cat /home/BIT/data/weblogs/2013-09-15.log`

3. _116.180.70.237_ es la __IP__, _128_ el número de usuario y _GET /KBDOC-00031.html HTTP/1.0_ el artículo sobre el que recae la acción.

4. Dentro del __Shell__ de __Spark__. Crear una variable que contenga la ruta del fichero, por ejemplo _file:/home/BIT/data/weblogs/2013-09-15.log_.
- `val ruta=("file:/home/BIT/data/2013-09-15.log")`

5. Crear un __RDD__ con el contenido del fichero llamada logs.
- `val logs=sc.textFile(ruta)`

6. Crear un nuevo __RDD__, __jpglogs__, que contenga solo las líneas del __RDD__ que contienen la cadena de caracteres _.jpg_. Puedes usar el método __contains()__.
- `val jpglogs=logs.filter(x=>x.contains(".jpg")`

7. Imprimir en pantalla las 5 primeras líneas de __jpglogs__.
- `jpglogs.take(5)`

8. Es posible anidar varios métodos en la misma línea. Crea una variable __jpglogs2__ que devuelva el número de líneas que contienen la cadena de caracteres _.jpg_.
- `val jpglogs=logs.filter(x=>x.contains(".jpg")).count()`

9. Ahora usar una de las funciones más importantes de __Spark__, la función _map()_. Para ello, coge el __RDD logs__ y calcula la longitud de las _5_ primeras líneas. Puedes usar la función _size()_ o _length()_ Recordad que la función _map_ ejecuta una función sobre cada línea del __RDD__, no sobre el conjunto total del __RDD__.
- `logs.map(x=>x.length).take(5)`

10. Imprimir por pantalla cada una de las palabras que contiene cada una de las 5 primeras líneas del __RDD logs__. Puedes usar la función _split()_.
- `logs.map(x=>x.split(" ")).take(5)`

11. Mapear el contenido de __logs__ a un __RDD__ _logwords_ de arrays de palabras de cada línea.
- `val logwords=log.map(line=>line.split(" ")`

12. Crear un nuevo __RDD__ llamado _ips_ a partir del __RDD logs__ que contenga solamente las __ips__ de cada línea (primer elemento de cada fila).
- `val ips=logs.map(line=>line.split(" ")(0))`

13. Imprimir por pantalla las 5 primeras líneas de __ips__.
- `ips.take(5)`

14. Visualizar el contenido de __ips__ con la función _collect()_. Verás que no es demasiado intuitivo. Prueba a usar el comando _foreach_.
- `ips.collect()`

15. Crear un bucle __for__ para visualizar el contenido de las 10 primeras líenas de __ips__. Ayuda: un bucle __for__ tiene la siguiente sintaxis.
- `for (x <- ips.take(10)){ print(x) }`

16. Guardar el contenido de __ips__ entero en un fichero de texto usando el método __saveAsTextFile__ en la ruta _/home/cloudera/iplist_ y observa su contenido.
- `ips.saveAsTextFile("file:/home/cloudera/iplist")`

### C - Exploración de un conjunto de ficheros planos en una carpeta

1. Crear un __RDD__ que contenga solo las __ips__ de todos los documentos contenidos en la ruta _/home/BIT/data/weblogs_. Guardar su contenido en la ruta _/home/cloudera/iplistw_ y observa su contenido.
- `val ips=sc.textFile("file:/home/BIT/data/weblogs/*").map(line=> line.split(' ')(0))saveAsTextFile("file:/home/cloudera/iplistw")`

2. A partir del __RDD logs__, crea un __RDD__ llamado _htmllogs_ que contenga solo la __ip__ seguida de cada __ID__ de usuario de cada fichero _html_. El __ID__ de usuario es el tercer campo de cada línea de cada __log__. Después imprimir las 5 primeras líneas.
    - `var htmllogs=logs.filter(_.contains(".html")).map(line => line.split(' ')(0))line.split(' ')(2)))`
    - `htmllogs.take(5).foreach(t => println(t._1 + "/" + t._2))`

## Trabajando con __PairRDDs__ __(módulo 4)__

Familiarizarnos con el trabajo con pares __RDD__.

### A - Trabajo con todos los datos de la carpeta de _logs: "/home/BIT/data/weblogs/*"_

1. Usando __MapReduce__, cuenta el número de peticiones de cada usuario, es decir, las veces que cada usuario aparece en una línea de un __log__. Para ello:

    __a.__ Usar un __Map__ para crear un __RDD__ que contenga el par __(ID, 1)__, siendo la clave el __ID__ y el __Value__ el número 1. Recordad que el campo __ID__ es el tercer elemento de cada línea. Los datos obtenidos tendrían que quedar de la siguiente manera.
    - `var logs=sc.textFile("file:/home/BIT/data/weblogs/*")`
    - `var rdd = logs.map(line => line.split(' ')).map(words => (words(2), 1))`

    __b.__ Usar un __Reduce__ para sumar los valores correspondientes a cada _userid_. Los datos tendría que mostrarse de la siguiente manera:
    - `var logs=sc.textFile("file:/home/BIT/data/weblogs/*")`
    - `var userreqs = logs.map(line => line.split(' ')).map(words => (words(2), 1)).reduceByKey((v1,v2) => v1 + v2)`

2. Mostrar los __ID__ de usuario y el número de accesos para los 10 usuarios con mayor número de accesos. Para ello:

    __a.__ Utilizar un _map()_ para intercambiar la __Clave__ por el __Valor__. (Si no se te ocurre cómo hacerlo, investiga la función _swap()_).
    - `val swapped=userreqs.map(field => field.swap)`

    __b.__ Utilizar la función vista en teoría para ordenar un __RDD__. Ten en cuenta que queremos mostrar los datos en orden descendiente (De mayor a menor número de peticiones). Recuerda que el __RDD__ debe estar en la misma forma que al inicio, es decir, con __Clave__: _userid_ y __Valor__: _nº de peticiones_.
    - `swapped.sortByKey(false).map(field => field.swap).take(10).foreach(println)`

3. Crear un __RDD__ donde la __Clave__ sea el _userid_ y el __Valor__ sea una lista de _ips_ a las que el __userid__ se ha conectado (es decir, agrupar las __IPs__ por __userID__). Ayúdate de la función _groupByKey()_ para conseguirlo.
- `var userips = logs.map(line => line.split(' ')).map(words => (words(2), words(0))).groupByKey().take(10)`

### B - Trabajo con todos los datos de la carpeta de _logs: "/home/BIT/data/accounts.cvs"_

1. Abrir el fichero _accounts.cvs_ con el editor de texto que prefieras y estudiar su contenido. Verás que el primer campo es el __id__ del usuario, que corresponde con el __id__ del usuario de los __logs__ del servidor web. El resto de campos corresponden con _fecha, nombre, apellido, dirección, etc_.
- `cat /home/BIT/accounts.cvs`

2. Haz un _JOIN_ entre los datos de __logs__ del ejercicio pasado y los datos de __accounts.csv__, de manera que se obtenga un conjunto de datos en el que la __Clave__ sea el _userid_ y como __Valor__ tenga la información del usuario seguido del número de visitas de cada usuario.

    __a.__ Haz un _map()_ de los datos de _accounts.cvs_ de forma que la __Clave__ sea el _userid_ y el __Valor__ sea toda la línea, incluido el _userid_.
    - `var accounts = sc.textFile("file:/home/BIT/data/accounts.csv").map(line => line.split(',')).map(account => (account(0), account))`

    __b.__ Haz un _JOIN_ del __RDD__ que acabas de crear con el que creaste en el paso anterior que contenía _(userid, nº visitas)_.
    - `var accounthits = accounts.join(userreqs)`

    __c.__ Crea un __RDD__ a partir del __RDD__ anterior, que contenga el _userid, número de visitas, nombre y apellido_ de las 5 primeras líneas.
    - `for (pair <- accounthits.take(10)) {println(pair._1,pair._2._2, pair._2._1(3), pair._2._1(4))}`

### C - Trabajo con más métodos sobre pares __RDD__

1. Usar _keyBy_ para crear un __RDD__ con los datos de las cuentas, pero con el código postal como __Clave__ _(noveno campo del fichero accounts.CSV)_. Puedes buscar información sobre este método en la __API__ online de __Spark__.
- `var accountsByPCode = sc.textFile("file:/home/BIT/data/accounts.csv").map(_.split(',')).keyBy(_(8))`

2. Crear un __RDD__ de pares con el código postal como la __Clave__ y una lista de nombres _(Apellido, Nombre)_ de ese código postal como el __Valor__. Sus lugares son el _5º_ y el _4º_ respectivamente. Estudia la función _mapValues()_ e intenta utilizarla para cumplir con el propósito de este ejercicio.
- `var namesByPCode = accountsByPCode.mapValues(values => values(4) + ',' + values(3)).groupByKey()`

3. Ordenar los datos por _código postal_ y luego, para los primeros 5 __códigos postales__, muestra el código y la lista de nombres cuyas cuentas están en ese __código postal__.
    - `namesByPCode.sortByKey().take(10).foreach{case(x,y) => println ("---" + x) y.foreach(println)};`
    - `namesByPCode.sortByKey().take(10).foreach{x => println ("---" + x._1) x._2.foreach(println)};`

## __SparkSQL__ __(JSON)__ __(módulo 5.1)__

Familiarizarnos con el uso de la herramienta __SQL__ de __Spark__.

1. Crear un nuevo contexto _SQLContext_.
- `var ssc = new org.apache.spark.sql.SQLContext(sc);`

2. Importar los _implicits_ que permiten convertir __RDDs__ en __DataFrames__.
- `import sqlContext.implicits;`

3. Cargar el __dataset__ _zips.json_ que se encuentra en la carpeta de ejercicios de __Spark__ y que contiene datos de _códigos postales_ de _Estados Unidos_. Puedes usar el comando _ssc.load("ruta_fichero", "formato")_.
- `var zips = ssc.load("file:/home/BIT/data/zips.json", "json");`

4. Visualizar los datos con el comando _show()_. Tienes que ver una __tabla__ con 5 columnas con un __subconjunto__ de los datos del fichero. Puedes ver que el __código postal__ es _id_, la __ciudad__ es _city_, la __ubicación__ _loc_, la __población__ _pop_ y el __estado__ _state_.
- `zips.show();`

5. Obtener las __filas__ cuyos __códigos postales__ cuya __población__ es superior a _10000_ usando el _api_ de __DataFrames__.
- `zips.filter(zips("pop") > 10000).collect();`

6. Guardar esta __tabla__ en un fichero temporal para poder ejecutar __SQL__ en ella.
- `zips.registerTempTable("zips");`

7. Realizar la misma consulta que en el punto 5, pero esta vez usando __SQL__.
- `ssc.sql("select * from zips where pop > 10000").collect();`

8. Usando __SQL__, obtén la __ciudad__ con más de _100 códigos postales_.
- `ssc.sql("select city from zips group by city having count(*) > 100 ").show();`

9. Usando __SQL__, obtén la __población__ del _estado de Wisconsin (WI)_.
- `ssc.sql("select SUM(pop) as POPULATION from zips where state='WI'").show();`

10. Usando __SQL__, obtén los 5 _estado más poblados_.
- `ssc.sql("select state, SUM(pop) as POPULATION from zips group by state order by SUM(pop) DESC").show(5);`

## __SparkSQL__ __(Hive)__ __(módulo 5.2)__

Familiarizarnos con el uso de __SparkSQL__ para acceder a __tablas__ en __Hive__, dado que es una herramienta ampliamente extendida en entornos analíticos.

1. Abrir un terminal y logaros como root, con pass cloudera. Para más información podéis consultar esta web _https://community.cloudera.com/t5/Advanced-Analytics-Apache-Spark/how-to-access-the-hive-tables-from-spark-shell/td-p/36609_.
- `sudo cp /usr/lib/hive/conf/hive-site.xml /usr/lib/spark/conf/`

2. En un terminal, arrancar el __Shell__ de __Hive__ y echar un vistazo a las bases de datos y tablas que hay en cada una de ellas.
    - `hive`
    - `show tables;`

3. En otro terminal aparte, arrancar el __Shell__ de __Spark__, y a través de __SparkSQL__ crear una base de datos y una __tabla__ con 2 o 3 columnas. Si no creamo con Spark a través de __Hive__.
- bbdd: hivespark
- tabla: empleados
- columnas: id INT, name STRING, age INT
- config table: FIELDS TERMINATED BY ',' LINES TERMINATED BY '\n'

    - `spark-shell`
    - `val sqlContext = new org.apache.spark.sql.hive.HiveContext(sc);`
    - `sqlContext.sql("CREATE DATABASE IF NOT EXISTS hivespark");`
    - `sqlContext.sql("CREATE TABLE IF hivespark.empleados(id INT, name STRING, age INT) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '\n'");`

4. Crear un fichero _/home/cloudera/empleado.txt_ que contenga los siguientes datos de esta manera dispuestos.
- `cat > /home/cloudera/empleado.txt`
    - 1201, nombre1, 25
    - 1202, nombre2, 28
    - 1203, nombre3, 39
    - 1204, nombre4, 23
    - 1205, nombre5, 23

5. Aprovechando la estructura de la __tabla__ que hemos creado antes, usando __SparkSQL__, subir los datos del fichero _/home/cloudera/empleado.txt_ a la __tabla__ __Hive__, usando como la sintaxis de __HiveQL__ (LOAD DATA LOCAL INPATH).
- `sqlContext.sql("LOAD DATA LOCAL INPATH '/home/cloudera/empleado.txt' INTO TABLE hivespark.empleados");`

6. Ejecutar cualquier consulta en los terminales de __Hive__ y __Spark__ para comprobar que todo funciona y se devuelven los mismos datos. En el terminal de __Spark__ usar el comando _show()_ para mostrar los datos.
- Hive: `select * from empleados;`
- Spark: `var query1=sqlContext.sql("SELECT * FROM hivespark.empleados");`
- Spark: `query1.show();`

## __SparkSQL__ __(DataFrames)__ __(módulo 5.3)__

Familiarizarnos un poco más con la __API__ de __DataFrames__.

1. En un terminal, arrancar el __Shell__ de __Spark__. Crear un contexto __SQL__.
- `spark-shell`
- `var ssc = new org.apache.spark.sql.SQLContext(sc);`

2. Importar los _implicits_ que permiten convertir __RDDs__ en __DataFrames__ y __Row__.
    - `import sqlContext.implicits;`
    - `import org.apache.spark.sql.Row;`
    - `import org.apache.spark.sql.types.{StructType,StructField,StringType};`

3. Crear una variable con la ruta al fichero _/home/cloudera/Desktop/DataSetPartidos.txt_. Será necesario copiar el __dataset__ _DataSetPartidos.txt_ al escritorio de la __VM__. Las líneas tienen el siguiente formato:
- idPartido::temporada::jornada::EquipoLocal::EquipoVisitante::golesLocal::golesVisitante::fecha::timestamp
    - `var ruta_datos="file:/home/cloudera/Desktop/DataSetPartidos.txt";`

4. Leer el contenido del archivo en una variable.
- `var datos =sc.textFile(ruta_datos);`

5. Crear una variable que contenga el esquema de los datos.
- `val schemaString = "idPartido::temporada::jornada::EquipoLocal::EquipoVisitante::golesLocal::golesVisitante::fecha::timestamp";`

6. Gener el esquema basado en la variable que contiene el esquema de los datos que acabamos de crear.
- `val schema = StructType(schemaString.split("::").map(fieldName => StructField(fieldName, StringType, true)));`

7. Convertir las filas de nuestro __RDD__ a __Rows__.
- `val rowRDD = datos.map(_.split("::")).map(p => Row(p(0), p(1) , p(2) , p(3) , p(4) , p(5) , p(6) , p(7) , p(8).trim));`

8. Aplicar el __Schema__ al __RDD__.
- `val partidosDataFrame = sqlContext.createDataFrame(rowRDD, schema);`

9. Registrar el __DataFrame__ como una __Tabla__.
- `partidosDataFrame.registerTempTable("partidos");`

10. Ya estamos listos para hacer consultas sobre el __DF__ con el siguiente formato.
    - `val results = sqlContext.sql("SELECT temporada, jornada FROM partidos");`
    - `results.show();`

11. Los resulados de las __queries__ son __DF__ y soportan las operaciones como los __RDDs__ normales. Las columnas en el __Row__ de resultados son accesibles por índice o nombre de campo.
- `results.map(t => "Name: " + t(0)).collect().foreach(println);`

12. ¿Cuál es el record de goles como visitante en una temporada del Oviedo?.
    - `val recordOviedo = sqlContext.sql("select sum(golesVisitante) as goles, temporada from partidos where equipoVisitante='Real Oviedo' group by temporada order by goles desc");`
    - `recordOviedo.take(1);`

13. ¿Quién ha estado más temporadas en 1 Division Sporting u Oviedo?.
    - `val temporadasOviedo = sqlContext.sql("select count(distinct(temporada)) from partidos where equipoLocal='Real Oviedo' or equipoVisitante='Real Oviedo' ");`
    - `val temporadasSporting = sqlContext.sql("select count(distinct(temporada)) from partidos where equipoLocal='Sporting de Gijon' or equipoVisitante='Sporting de Gijon' ");`

## __Spark__ __Streaming I__ __(módulo 6.1)__

Iniciarnos en el uso de __Spark Streaming__ y observar sus cualidades. Para ello generaremos un script en un terminal que contará las palabras que introduzcamos en otra terminal a modo de streaming simulado.

1. Visita en la web la documentación de __Spark__ _https://spark.apache.org/docs/1.5.2/streaming-programming-guide.html_ y familiarízate con el ejercicio.

2. Tomate un tiempo para navegar por la web y explorar todo lo que puede ofrecerte. Cuando lo consideres, comienza el ejercicio.

3. Abre un terminal y escribe el siguiente comando, que hace que todo lo que escribas se envíe al _puerto 4444_.
-  `nc -lkv 4444`

4. Inicia un nuevo terminal y arranca el __Shell__ de __Spark__ en modo local con al menos 2 _threads_.
-  `spark-shell --master local[2]`

5. Por otro lado, accede al fichero _/usr/lib/spark/conf/log4j.properties_, y edítalo para poner el nivel de __log__ a _ERROR_, de modo que en tu __Shell__ puedas ver con claridad el __streaming__ de palabras contadas devuelto por tu script.

6. Importar las clases necesarias para trabajar con __Spark Streaming__.
    -  `import org.apache.spark.streaming.StreamingContext;`
    -  `import org.apache.spark.streaming.StreamingContext;`
    -  `import org.apache.spark.streaming.Seconds;`

7. Crear un __SparkContext__ con una duración de 5 segundos.
-  `var ssc = new StreamingContext(sc, Seconds(5));`

8. Crear un __DStream__ para leer texto del puerto que pusiste en el comando _nc_, especificando el _hostname_ de nuestra máquina, que es _quickstart.cloudera_.
-  `var mystream = ssc.socketTextStream("localhost", 4444);`

9. Crear un __MapReduce__, para contar el número de palabras que aparecen en cada _Stream_.
    -  `var words = mystream.flatMap(line => line.split("\\W"));`
    -  `var wordCounts = words.map(x => (x, 1)).reduceByKey((x,y) => x+y);`

10. Imprimir por pantalla los resultados de cada __batch__.
-  `wordCounts.print();`

11. Arrancar el __Streaming Context__ y llama a _awaitTermination_ para esperar a que la tarea termine.
    -  `ssc.start();`
    -  `ssc.awaitTermination();`

12. Deberías ver algo de tipo _(listado)_.

13. Una vez hayas acabado, sal del __Shell__ y del terminal donde ejecutaste el comando _nc_ haciendo un _CNTRL+C_.

14. Ejecutarlo desde un script.
-  `spark-shell --master local[2] -i prueba.scala`
