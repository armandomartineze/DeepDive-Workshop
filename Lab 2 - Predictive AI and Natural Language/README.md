# Lab 2 - Predictive AI and Natural Language Data Access



En el laboratorio realizaras la ingesta de los datos en Oracle IA Database y en AI Data Platform, asi como la creación de un modelo de Machine Learning, necesitas haber ejecutado en laboratorio 1.

## Contenido
[Ingesta de datos](#sec-2)  
- [Ingesta de datos vía Autonomous](#sec-2.1)  
- [Ingesta de datos vía AIDP](#sec-2.2)
- [Creación de un catálogo dentro de AIDP](#sec-2.3)  

[Ingenieria de Datos](#sec-3)
- [Importación del notebook del laboratorio en el workspace](#sec-3.1)  
- [Importación del notebook para la sesión 2](#sec-3.2)
- [Creación de Modelo de Machine Learning](#sec-3.3) 


---
<a id="sec-2"></a>
## Paso 2 - Ingesta de datos  


<a id="sec-2.1"></a>
#### Ingesta de datos vía Autonomous

En este laboratorio haremos dos tipos de ingesta: una directamente en Autonomous y otra en AIDP (sección 2.2). Accede a tu instancia activa en Autonomous.

<p align="center"><img width="80%" height="80%"  alt="image" src="/images/15e75f59-ab1a-4395-afc0-f8c75fcd5b44" /></p><br>


Haz clic en el nombre de la base de datos.

<p align="center"><img width="90%" height="90%"  alt="image-18" src="/images/8295a07b-00cf-475a-9d05-5162b971997e" /></p><br>


Dentro de la pantalla de la base de datos, abre el menú de `database actions` y haz clic en `SQL`. Esta acción abrirá el workspace de la base para ejecutar SQL.

<p align="center"><img width="80%" height="80%" alt="image-20" src="/images/6f50ecf6-b81b-4e36-a868-63a40fd25081" /></p><br>


En la pantalla de SQL, ejecuta el siguiente comando:

````sql
CREATE TABLE BRONZE_WC_MATCHES (
    key_id NUMBER,
    tournament_id VARCHAR2(50),
    tournament_name VARCHAR2(200),
    match_id VARCHAR2(100),
    match_name VARCHAR2(200),
    stage_name VARCHAR2(100),
    group_name VARCHAR2(100),
    group_stage NUMBER,
    knockout_stage NUMBER,
    replayed NUMBER,
    replay NUMBER,
    match_date VARCHAR2(50),
    match_time VARCHAR2(50),
    stadium_id VARCHAR2(50),
    stadium_name VARCHAR2(200),
    city_name VARCHAR2(100),
    country_name VARCHAR2(100),
    home_team_id VARCHAR2(50),
    home_team_name VARCHAR2(100),
    home_team_code VARCHAR2(10),
    away_team_id VARCHAR2(50),
    away_team_name VARCHAR2(100),
    away_team_code VARCHAR2(10),
    score VARCHAR2(20),
    home_team_score NUMBER,
    away_team_score NUMBER,
    home_team_score_margin NUMBER,
    away_team_score_margin NUMBER,
    extra_time NUMBER,
    penalty_shootout NUMBER,
    score_penalties VARCHAR2(20),
    home_team_score_penalties NUMBER,
    away_team_score_penalties NUMBER,
    result VARCHAR2(50),
    home_team_win NUMBER,
    away_team_win NUMBER,
    draw NUMBER
);
````
<br>

Ejecuta el comando con el botón verde `Run Statement`.

<p align="center"><img width="90%" height="90%"  alt="image-21" src="/images/acccea84-7850-450b-925f-a1edeb35a516" /></p><br>


Este comando crea una tabla con la estructura requerida, con todas las columnas y tipos listados. Luego copiaremos los datos a la tabla ejecutando el siguiente comando:

````BEGIN
  DBMS_CLOUD.COPY_DATA(
    table_name => 'BRONZE_WC_MATCHES',
    credential_name => NULL,
    file_uri_list => 'https://objectstorage.us-chicago-1.oraclecloud.com/n/idajmumkp9ca/b/DeepDiveWorkshopData/o/worldcup_matches.csv',
    format => json_object(
        'type' value 'csv',
        'skipheaders' value '1'
    )
  );
END;
````
<br>

Este comando busca el CSV en un repositorio público y copia sus datos a la tabla creada anteriormente. Después de ejecutarlo, puedes visualizar los datos con un SELECT, agregue este comando a continuación y haga clic en ejecutar nuevamente.

````
SELECT * FROM BRONZE_WC_MATCHES
````
<br>

<p align="center"><img width="80%" height="80%"  alt="image-22" src="/images/6915ad7d-d8c7-4c55-8caa-1482bd686712" /></p><br>


También puedes verlo en la propia consola, buscando el nombre de la tabla en el panel lateral, haciendo clic derecho y seleccionando `Open`.

<p align="center"><img width="50%" height="50%"  alt="image-23" src="/images/02ed2fe2-b542-47a3-b849-77c009706b5e" /></p><br>


Se abrirá automáticamente una pestaña lateral con los datos de la tabla, columnas, datos, triggers y otras propiedades.

<p align="center"><img width="80%" height="80%"  alt="image-24" src="/images/e4a22a20-e804-43ea-a52e-52ad7c777d1e" /></p><br>

<a id="sec-2.2"></a>
### Ingesta de datos vía AIDP


A continuación, haremos la ingesta del segundo dataset de otra forma, usando AIDP. Para eso, accede al AIDP creado anteriormente.

<p align="center"><img width="80%" height="80%"  alt="image-10" src="/images/4a8a4382-9635-441b-b0f8-95ca3b210718" /></p><br>


Haz clic en el nombre de tu plataforma de datos para entrar e iniciar sesión.

<p align="center"><img width="80%" height="80%"  alt="image-25" src="/images/104dd9c1-8a43-43c9-9ef3-8ebd5229fea8" /></p><br>


Esta es la página principal de AIDP. En el menú lateral ya puedes ver tu catálogo de datos, workspace, workflows, agentes y demás información.

<p align="center"><img width="80%" height="80%"  alt="image-27" src="/images/580b399f-bd3a-4ef3-9233-5f8f95c59be4" /></p><br>


<a id="sec-2.3"></a>
### Creación de un catálogo dentro de AIDP


Primero crearemos un catálogo apuntando al Autonomous que creamos antes. Para ello, haz clic en `create` en el menú lateral.

<p align="center"><img width="80%" height="80%"  alt="image-28" src="/images/1f78b5f4-13cc-434e-a379-fc297cdc8ade" /></p><br>


El nombre de nuestro catálogo será `DeepDiveCatalog_Bronze` y usaremos conexión externa, seleccionando el Autonomous previamente creado. Completa el resto de la información como en la imagen.

<p align="center"><img width="80%" height="80%"  alt="image-30" src="/images/03d7af96-a415-4f3a-80e7-184ae9704bd9" /></p><br>


Después de elegir el servicio, debes ingresar la misma contraseña de Autonomous en los campos de wallet y contraseña, y dejar el usuario como `admin`. Prueba la conexión antes de continuar con el botón de crear. Si es correcta, procede con la creación.

<p align="center"><img width="80%" height="80%"  alt="image-32" src="/images/bf8b6c9a-dd04-41e3-8972-bf173f9f2f06" /></p><br>


Tu catálogo iniciará el proceso de creación.

<p align="center"><img width="90%" height="90%"  alt="image-33" src="/images/40831924-3c9a-449d-a14f-913977ccba9e" /></p><br>


Cuando finalice, ya podrás visualizar las tablas existentes en Autonomous con su respectivo esquema.

<p align="center"><img width="40%" height="40%"  alt="image-34" src="/images/7e69ac71-f59a-49a9-a180-7dacf528a33a" /></p><br>

<a id="sec-3"></a>
## Paso 3 - Ingeniería de datos  

<a id="sec-3.1"></a>
### Importación del notebook del laboratorio en el workspace


Para importar el notebook, primero accede al workspace desde el menú lateral.

<p align="center"><img width="80%" height="80%"  alt="image-35" src="/images/7d79eb5d-b225-4e3b-ab52-1d16164bc6c8" /></p><br>


El workspace ya trae una carpeta llamada `Shared` con ejemplos. Para importar el notebook del laboratorio, primero debes descargarlo en tu máquina. Luego haz clic en el botón de upload (icono indicado en la imagen).

<p align="center"><img width="80%" height="80%"  alt="image-37" src="/images/37850300-084e-432b-84d9-7fd5cc83948a" /></p><br>


Busca el archivo en tu repositorio y haz clic en upload para subirlo.

<p align="center"><img width="80%" height="80%"  src="/images/c48bb726-67b4-4d90-b247-7292d708466a" /></p><br>


El archivo se agregará inmediatamente al workspace. Ya puedes abrirlo haciendo clic en el nombre del notebook.

<p align="center"><img width="80%" height="80%"  src="/images/0a94086a-bf69-4213-ad3f-1a511f3e2702" /></p><br>


Al abrir el notebook, en la parte superior central verás `no cluster attached`. Llegamos así al último paso de configuración para poder realizar todos los laboratorios de las sesiones 1 y 2: crear el cluster. Haz clic en el botón de cluster (arriba a la derecha) y luego en `Create Cluster`.

<p align="center"><img width="80%" height="80%"  src="/images/624bb611-e2c6-45b4-96e7-070b9f42e091" /></p><br>


Se abrirá una pestaña; agrega el nombre y la configuración deseada. En nuestro caso usamos `DeepDiveCluster`, dejando la configuración por defecto y haciendo clic en `Create`.

<p align="center"><img width="80%" height="80%"  src="/images/5c162ec8-ef44-47b9-b82e-1b834e1f079a" /></p><br>


Espera a que el cluster se cree. Si no se adjunta automáticamente al notebook, vuelve al botón de cluster y busca `attach a cluster`, seleccionando el que acabas de crear.

<p align="center"><img width="80%" height="80%"  src="/images/09977d3e-af1c-49bf-9a81-9ad2ba7431f6" /></p><br>


Hasta que el cluster quede activo en el notebook.

<p align="center"><img width="50%" height="50%"  src="/images/ac673755-9172-4751-b9db-e974a39baa82" /> </p><br>
<p align="center"><img width="50%" height="50%" alt="image-45" src="/images/447118ef-acff-4b1c-aa8b-c32c9a213cd4" /></p><br>

<a id="sec-3.2"></a>
### Importación del notebook para la sesión 2


Repite el mismo proceso de upload para el archivo Jupyter de la segunda sesión.

<p align="center"><img width="80%" height="80%"  src="/images/23809cbb-1f90-45de-8fa2-7d45708e4cf1" /></p><br>


Con eso tendrás todos los notebooks necesarios para realizar las sesiones prácticas directamente en tu workspace.

<p align="center"><img width="80%" height="80%"  src="/images/c2e2771f-911f-41aa-bf37-1db0d766338a" /></p><br>


Ahora tienes un entorno completamente configurado y puedes seguir las instrucciones del propio Jupyter junto con el instructor para ejecutar los laboratorios.


<a id="sec-3.3"></a>
### Creación de Modelo de Machine Learning

Vamos a comenzar con al ejecución de los notebooks, esto nos permitira ingestar trabajar con los datos, enriquecer la informaci{on y entrenar nuestro modelo.

Selecciona el notebook "session1-AIDP.ipynb", haciendo clic en el nombre del archivo:

<p align="center"><img width="80%" height="80%"  src="/images/image-lab2-3.3-1.png" /></p>

Como puedes observar en la misma plataforma podemos ejecutar los notebooks, primero en la seccion izquierda selecciona la tabla de contenidos, aqui muestra todas las secciones de nuestro notebooks y podemos navegar de forma mas rapida. Tambien podemos observar que tener el menu para guardr, editar y ejecutar nuestro notebook. Y en el panel central podemos iniciar nuestro trabajo:

<p align="center"><img width="80%" height="80%"  src="/images/image-lab2-3.3-2.png" /></p>

El primer paso es ejecutar el parrafo para leer un archivo desde alamcenamiento por objetos de OCI y crear un dataset, haz clic en el icono para ejecutar:

````import requests
import pandas as pd

url = "https://objectstorage.us-chicago-1.oraclecloud.com/n/idajmumkp9ca/b/DeepDiveWorkshopData/o/fifa_players.csv"

response = requests.get(url)

with open("/tmp/fifa_players.csv", "wb") as f:
    f.write(response.content)

csv = pd.read_csv("/tmp/fifa_players.csv")
````

Los siguientes dos parrafos crean funciones que mapea tipos de Spark a tipos SQL (STRING, INT, DOUBLE, etc.), y otra función que genera automáticamente el CREATE TABLE (DDL) usando el esquema del DataFrame y el nombre de la tabla como entrada:

````
def spark_to_spark_sql_type(spark_type):
    t = str(spark_type)
    
    if "StringType" in t:
        return "STRING"
    elif "IntegerType" in t:
        return "INT"
    elif "LongType" in t:
        return "BIGINT"
    elif "DoubleType" in t:
        return "DOUBLE"
    elif "FloatType" in t:
        return "FLOAT"
    elif "BooleanType" in t:
        return "BOOLEAN"
    elif "DateType" in t:
        return "DATE"
    elif "TimestampType" in t:
        return "TIMESTAMP"
    else:
        return "STRING"


def generate_create_table(df, table_name):
    columns = []
    
    for field in df.schema.fields:
        col_name = field.name
        col_type = spark_to_spark_sql_type(field.dataType)
        columns.append(f"{col_name} {col_type}")
    
    cols_sql = ",\n  ".join(columns)
    
    ddl = f"""
    CREATE TABLE {table_name} (
      {cols_sql}
    )
    """
    
    return ddl
````

Nuestro siguiente paso es limpiar nombres de columnas, convierte pandas a Spark, crea/reemplaza la tabla bronze y carga los datos:

````
import re

def clean_col(c):
    return re.sub(r'[^a-zA-Z0-9_]', '', c.lower())

# limpa nomes
csv.columns = [clean_col(c) for c in csv.columns]

# converte pra Spark DF
spark_df = spark.createDataFrame(csv)

# nome da tabela
table_name = "bronze_fifa_players"

# gera DDL
ddl = generate_create_table(spark_df, table_name)

spark.sql("USE deepdivecatalog_bronze.admin")
spark.sql(f"""
DROP TABLE IF EXISTS {table_name}
""")
# cria tabela
spark.sql(ddl)

# escreve dados
spark_df.write \
    .mode("append") \
    .saveAsTable(table_name)
````

Vamos a mostrar la tabla bronze_fifa_players creada y muestra una vista previa de 5 filas:

````df = spark.table("bronze_fifa_players")
display(df.limit(5))

Calcula cuántos valores NULL hay por cada columna del DataFrame:

from pyspark.sql.functions import col, sum

null_counts = df.select([
    sum(col(c).isNull().cast("int")).alias(c)
    for c in df.columns
])

display(null_counts)
````

Calcula cuántos valores inválidos (NULL o NaN) hay por columna:

````from pyspark.sql.functions import isnan, col, sum

display(df.select([
    sum((col(c).isNull() | isnan(col(c))).cast("int")).alias(c)
    for c in df.columns
]))
````

Carga la capa bronze y aplica limpieza: fecha, deduplicación, reemplazo de NaN y filtro de nombres nulos:

````from pyspark.sql.functions import col, to_date

bronze_df = spark.table("deepdivecatalog_bronze.admin.bronze_fifa_players")

silver_df = (bronze_df 
    .withColumn("birth_date", to_date("birth_date", "M/d/yyyy")) 
    .dropDuplicates(["full_name"])  # Remover jogadores duplicados
	.na.replace(float("nan"), None)  # Valores inválidos substituídos por None
	.filter(col("name").isNotNull()) # Remover colunas com nome vazio
)
````

Crea la edad del jugador, columna calculated_age, usando año actual menos año de nacimiento:

````from pyspark.sql.functions import current_date, year

silver_df = silver_df.withColumn(
    "calculated_age",
    year(current_date()) - year(col("birth_date"))
)
````

Compara age vs calculated_age y muestra una muestra con la diferencia (age_diff):

````from pyspark.sql.functions import col, current_date, months_between, abs

silver_df.select(
    "name",
    "age",
    "calculated_age",
    abs(col("age") - col("calculated_age")).alias("age_diff")
).show(5, truncate=False)
````

Guarda el DataFrame limpio en la capa plata sobrescribiendo datos y esquema:

````silver_df.write \
    .mode("overwrite") \
    .option("overwriteSchema", "true") \
    .saveAsTable("deepdivecatalog_prata.prata_fifa_players")
````

Convierte overall_rating a entero y crea una categoría de rating (elite, bom, comum):

````from pyspark.sql.functions import when
from pyspark.sql.functions import col

silver_df = silver_df.withColumn(
    "overall_rating",
    col("overall_rating").cast("int")
)

gold_df = silver_df.withColumn(
    "rating_category",
    when(col("overall_rating") >= 85, "elite")
    .when(col("overall_rating") >= 75, "bom")
    .otherwise("comum")
)
````

Selecciona las columnas finales relevantes para construir la capa oro:

````gold_df = gold_df.select(
    "name",
    "full_name",
    "birth_date",
    "calculated_age",
    "nationality",
    "overall_rating",
    "potential",
    "value_euro",
    "wage_euro",
    "preferred_foot",
    "rating_category"
)
````

Guarda gold_df como tabla ouro_fifa_players en la capa oro:

````gold_df.write.mode("overwrite").saveAsTable(
    "deepdivecatalog_ouro.ouro_fifa_players"
)
````

Genera ranking de jugadores por overall_rating y muestra el top 10:

````gold_top_players = gold_df \
    .select("name", "nationality", "overall_rating", "value_euro") \
    .orderBy(col("overall_rating").desc()) 
	

display(gold_top_players.limit(10))
````

Calcula el valor promedio (value_euro) por nacionalidad y lo muestra:

````from pyspark.sql.functions import avg

gold_value_by_country = gold_df \
    .groupBy("nationality") \
    .agg(avg("value_euro").alias("avg_value_euro"))

display(gold_value_by_country)
````

Filtra jugadores con alto potencial (>85) y toma campos clave para análisis:

````gold_high_potential = gold_df \
    .filter(col("potential") > 85) \
    .select("name", "age", "potential", "value_euro")  

gold_high_potential.head()
````

Calcula eficiencia económica (value_euro / wage_euro) por jugador:

````gold_efficiency = gold_df \
    .withColumn("value_per_wage", col("value_euro") / col("wage_euro")) \
    .select("name", "value_per_wage", "overall_rating")
````

Guarda las tablas analíticas finales en la capa oro (top_players, value_by_country, high_potential, efficiency):

````gold_top_players.write.mode("overwrite").saveAsTable(
    "deepdivecatalog_ouro.ouro_top_players"
)

gold_value_by_country.write.mode("overwrite").saveAsTable(
    "deepdivecatalog_ouro.ouro_value_by_country"
)

gold_high_potential.write.mode("overwrite").saveAsTable(
    "deepdivecatalog_ouro.ouro_high_potential"
)

gold_efficiency.write.mode("overwrite").saveAsTable(
    "deepdivecatalog_ouro.ouro_efficiency"
)
````

