Passo a Passo para instalar e configurar do 0 o WSL / Python / Spark / AirFlow


exemplo simples de codigo que pode ser executado e orquestrado:
---------------------------------------------------------------
from airflow import DAG
from airflow.operators.python_operator import PythonOperator
from pyspark.sql import SparkSession
from datetime import datetime

def run_spark_job():
    spark = SparkSession.builder \
        .appName("Exemplo Airflow com PySpark") \
        .getOrCreate()

    data = [("João", 29), ("Maria", 32), ("Pedro", 34)]
    columns = ["Nome", "Idade"]
    df = spark.createDataFrame(data, schema=columns)

    df.show()
    row_count = df.count()
    print(f"Número de linhas no DataFrame: {row_count}")

    spark.stop()

default_args = {
    'owner': 'gustavo',
    'depends_on_past': False,
    'start_date': datetime(2024, 8, 26),
    'email_on_failure': False,
    'email_on_retry': False,
    'retries': 1,
}

with DAG(
    'dag_pyspark_exemplo',
    default_args=default_args,
    description='Exemplo de DAG utilizando PySpark',
    schedule_interval='@daily',
    catchup=False
) as dag:

    tarefa_spark = PythonOperator(
        task_id='executa_spark_job',
        python_callable=run_spark_job
    )

tarefa_spark
---------------------------------------------------------------

