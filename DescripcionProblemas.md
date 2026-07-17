Taller 1: Telecomunicaciones (Análisis CDR)
Negocio:
Una empresa de telecomunicaciones necesita analizar el comportamiento de llamadas de su base de 800,000 suscriptores a partir de 
un extracto masivo de 12 millones de registros CDR (Call Detail Records).

Problema:
El equipo comercial desea lanzar una promoción de "Números Favoritos". Para ello, necesitan identificar, para cada suscriptor, cuáles 
son los 3 números de destino a los que más tiempo les ha tomado hablar en llamadas individuales (las 3 llamadas más largas realizadas 
por cada persona).

Que se espera como respuesta:
Usted debe generar un DataFrame de PySpark llamado obligatoriamente df_top3_contactos. Este DataFrame debe ser el resultado de un 
cruce entre las tablas y la aplicación de una función de ventana que asigne un ranking. El resultado final deberá contener 
únicamente las 3 filas con mayor duración por cada id_suscriptor, mostrando las columnas: id_suscriptor, numero_destino y duracion_llamada_seg.


=================================

Taller 2: Logística (Tiempos de Ociosidad)
Negocio:
Una empresa de logística nacional monitorea las entregas de su flota compuesta por 500,000 vehículos. El sistema ha registrado 15 
millones de eventos de entrega, cada uno con una marca de tiempo exacta.

Problema:
El gerente de operaciones sospecha que hay ineficiencias en las rutas, ya que los conductores pasan demasiado tiempo detenidos entre 
una entrega y la siguiente. Se requiere calcular el "tiempo ocioso" exacto para cada entrega, definido como la diferencia de tiempo 
transcurrido desde que el camión terminó su entrega anterior hasta que inició la entrega actual.

Que se espera como respuesta:
Usted debe generar un DataFrame de PySpark llamado obligatoriamente df_tiempo. Este DataFrame debe contener todas las columnas 
originales más una nueva columna llamada minutos_ociosos. Esta columna debe calcularse utilizando una función de ventana de 
desplazamiento (lag) que traiga la marca de tiempo de la entrega inmediatamente anterior para el mismo camion_id, reste ambos 
tiempos convertidos a epoch, y divida el resultado entre 60. Las filas correspondientes a la primera entrega de cada camión 
tendrán un valor nulo (NaN) en esta nueva columna, lo cual es correcto.

=================================

Retail (Acumulado y Optimización)
Negocio:
Una cadena de retail está procesando 10 millones de transacciones históricas pertenecientes a 300,000 clientes en distintas ciudades del país.

Problema:
El área de fidelización necesita calcular el gasto acumulado (Running Total) de cada cliente a lo largo de su vida comercial para asignarles 
un nivel de lealtad. Dado que este proceso de cálculo involucra ordenar y procesar los 10 millones de registros, y que posteriormente 
los analistas de negocio necesitarán hacer múltiples consultas filtradas por ciudad (ej. "¿Cuál es el acumulado total solo en Bogotá?" y 
luego "¿Y solo en Medellín?"), se requiere optimizar el proceso para no recalcular todo el conjunto de datos desde cero en cada consulta.

Que se espera como respuesta:
Usted debe generar un DataFrame de PySpark llamado obligatoriamente df_lealtad. Este DataFrame debe incluir una nueva columna llamada 
gasto_acumulado, calculada mediante una función de agregación sobre una ventana (sum con rowsBetween).
Requisito crítico de arquitectura: Antes de ejecutar los filtros por ciudad, el DataFrame df_lealtad debe ser almacenado explícitamente 
en la memoria caché del clúster y-forzar su materialización. La validación automática medirá los tiempos de las dos consultas por ciudad; 
la segunda consulta debe ejecutarse en una fracción del tiempo de la primera para demostrar que la optimización fue exitosa.
