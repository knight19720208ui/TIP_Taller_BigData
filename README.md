Apache Spark es un motor de computación unificada de código abierto diseñado para el procesamiento de datos a gran escala. A diferencia de los sistemas anteriores que escribían y leían de disco constantemente, Spark está optimizado para la computación en memoria, lo que lo hace órdenes de magnitud más rápido para ciertas cargas de trabajo, especialmente en algoritmos iterativos como los de Inteligencia Artificial y Machine Learning.

PySpark es la interfaz de Python para Spark. Permite a los desarrolladores y científicos de datos interactuar con el motor distribuido de Spark utilizando la sintaxis y las librerías propias de Python (como Pandas), pero escalando a petabytes de datos. En PySpark, la estructura de datos principal es el DataFrame, que es una colección de datos distribuida organizada en columnas nombradas, optimizada internamente por el motor Catalyst y el gestor de memoria Tungsten.

Alternativas en el Ecosistema Hadoop para el Mismo Trabajo
Para entender el valor de Spark, es necesario contrastarlo con las herramientas que originalmente resolvían estos problemas en el ecosistema Hadoop:

Hadoop MapReduce: Es el motor de procesamiento por lotes original de Hadoop. Para realizar una operación como una Window Function (por ejemplo, calcular un ranking o un acumulado), MapReduce requeriría diseñar múltiples fases de Map y Reduce interconectadas. Cada fase obliga a escribir los resultados intermedios en el disco duro físico, generar nuevos archivos y leerlos de nuevo. Lo que en PySpark se resuelve en unas pocas líneas de código y segundos, en MapReduce requiere cientos de líneas de código Java, complejas gestionas de diccionarios distribuidos y tardaría varios minutos u horas.
Apache Hive: Es un sistema de almacén de datos construido sobre Hadoop. Traduce consultas SQL a trabajos de MapReduce (o Tez). Hive incorporó el soporte para Window Functions a partir de su versión 0.11. Sin embargo, dado que su motor de ejecución subyacente históricamente dependía del disco, el rendimiento es inferior al de Spark. Además, Hive carece de un mecanismo de Caché interactivo nativo en memoria; cada consulta SQL ejecuta el plan de ejecución desde cero.
Apache Pig: Es una plataforma para analizar grandes conjuntos de datos mediante un lenguaje de scripting de alto nivel llamado Pig Latin. En el pasado, realizar operaciones analíticas complejas sobre ventanas de datos en Pig era extremadamente tedioso, requiriendo la creación de User Defined Functions (UDFs) personalizadas en Java, lo cual elevaba la curva de aprendizaje y mantenía el problema de la lectura/escritura constante a disco. Hoy en día, Pig ha quedado prácticamente obsoleto en favor de Spark.
Conceptos Clave para los Ejercicios
1. El concepto de "Window" (Ventana)
A diferencia de un groupBy estándar que colapsa todas las filas en un único resultado numérico por grupo (por ejemplo, el promedio total de ventas), una Window Function calcula un resultado para cada fila individual, pero basándose en un grupo de filas relacionadas (la "ventana").

Analogía: Un groupBy responde a la pregunta "¿Cuál fue la nota promedio del curso?". Una Window Function responde a "¿Cuál fue la nota promedio del curso comparada con la nota de este estudiante específico?".

2. Los pilares: partitionBy y orderBy
Para definir una ventana en PySpark se utiliza la sintaxis Window.partitionBy(...).orderBy(...).

partitionBy(col): Funciona como un divisor lógico. Si se particiona por "usuario", Spark calculará las funciones matemáticas de manera aislada para los datos pertenecientes a cada usuario, sin mezclar la información entre distintos usuarios.
orderBy(col): Define el orden estricto de las filas dentro de la partición establecida. Es un requisito obligatorio para funciones de rango o de tiempo.
3. Tipos de Funciones de Ventana comunes
Ranking: rank(), dense_rank(), row_number(). Asignan una posición secuencial (1ro, 2do, 3ro) según el orden indicado.
Desplazamiento (Offset): lag(col, n) y lead(col, n). Permiten observar el valor de una columna en filas anteriores o posteriores dentro de la misma partición. Es fundamental para calcular diferencias temporales.
Agregación en ventana: sum(col).over(ventana). Permite calcular sumas acumuladas (Running Totals) manteniendo el detalle de cada fila.
4. El uso de Cache (Optimización de Memoria)
Las transformaciones en Spark tienen un comportamiento "perezoso" (Lazy Evaluation). Si se realiza una unión (Join) compleja sobre 15 millones de filas y posteriormente se ejecutan tres consultas filtradas diferentes sobre ese resultado, Spark procesará el Join pesado tres veces desde el principio.

Para mitigar esto, se utiliza la acción df.cache():

Se ejecutan las transformaciones pesadas (Joins, Windows).
Se aplica .cache() al DataFrame resultante.
Se fuerza una acción rápida (como .count()) para que Spark ejecute todo el plan de ejecución y almacene el resultado en la memoria RAM del clúster.
Las siguientes consultas sobre ese DataFrame leerán directamente desde la memoria, reduciendo el tiempo de ejecución a milisegundos.
