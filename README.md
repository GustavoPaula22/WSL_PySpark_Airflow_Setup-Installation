# WSL_PySpark_Airflow_Setup-Installation


# Updating the instructions in the .txt file with the new 'airflow db migrate' command
final_instructions = """
1. Habilitar o WSL no Windows

1.1. Abrir o PowerShell como Administrador:
- Clique com o botão direito no menu Iniciar e selecione "Windows PowerShell (Admin)".

1.2. Habilitar o WSL:
- No PowerShell, execute:
  wsl --install
- Isso instalará o WSL e o Ubuntu como distribuição padrão.

1.3. Reiniciar o computador:
- Após a instalação, reinicie o sistema.

1.4. Configurar o Ubuntu:
- Após reiniciar, abra o Ubuntu no menu iniciar. Será solicitado que você crie um nome de usuário e senha para o Ubuntu.

2. Atualizar e instalar pacotes básicos no Ubuntu

2.1. Atualizar o sistema:
  sudo apt update && sudo apt upgrade -y

2.2. Instalar ferramentas essenciais:
  sudo apt install build-essential curl file git -y

3. Instalar Python e pip

3.1. Instalar Python:
  sudo apt install python3 python3-pip python3-venv -y

3.2. Verificar a instalação:
  python3 --version
  pip3 --version

4. Instalar e configurar PySpark

4.1. Instalar Java:
  sudo apt install openjdk-11-jdk -y
  java -version

4.2. Instalar PySpark:
  pip3 install pyspark

4.3. Configurar variáveis de ambiente (opcional):
  export SPARK_HOME=/usr/local/spark
  export PATH=$SPARK_HOME/bin:$PATH
  source ~/.bashrc

5. Instalar e configurar o Apache Airflow

5.1. Criar e ativar um ambiente virtual:
  python3 -m venv airflow_venv

5.2. Instalar o Airflow com suporte para PySpark:
  export AIRFLOW_HOME=~/airflow
  export PATH="$HOME/.local/bin:$PATH"
  AIRFLOW_VERSION=2.10.0
  PYTHON_VERSION="$(python3 -c 'import sys; print(f"{sys.version_info.major}.{sys.version_info.minor}")')"
  CONSTRAINT_URL="https://raw.githubusercontent.com/apache/airflow/constraints-${AIRFLOW_VERSION}/constraints-${PYTHON_VERSION}.txt"
  source ~/.bashrc

  source airflow_venv/bin/activate
  pip3 install "apache-airflow==${AIRFLOW_VERSION}" --constraint "${CONSTRAINT_URL}"

5.3. Migrar o banco de dados do Airflow:
  airflow db init

5.4. Criar um usuário administrador:
  airflow users create \
      --username gustavo \
      --firstname Gustavo \
      --lastname Paula \
      --role Admin \
      --email admin@example.com

5.5. Iniciar o scheduler e a web interface:
  airflow webserver --port 8080
  airflow scheduler

6. Criar um Projeto com PySpark e Airflow

6.1. Criar diretório do projeto:
  mkdir ~/meu_projeto_etl
  cd ~/meu_projeto_etl

6.2. Criar e ativar o ambiente virtual do projeto:
  python3 -m venv .venv
  source .venv/bin/activate

6.3. Instalar dependências:
  pip install pyspark apache-airflow

6.4. Configurar um DAG no Airflow:
  mkdir dags
  nano dags/etl_dag.py

6.5. Exemplo de código para o DAG:
  from airflow import DAG
  from airflow.operators.python_operator import PythonOperator
  from datetime import datetime
  from pyspark.sql import SparkSession

  def run_spark_job():
      spark = SparkSession.builder.appName("ETL Job").getOrCreate()
      # Código PySpark aqui
      spark.stop()

  default_args = {
      'owner': 'airflow',
      'start_date': datetime(2023, 1, 1),
  }

  dag = DAG('etl_dag', default_args=default_args, schedule_interval='@daily')

  run_etl = PythonOperator(
      task_id='run_spark_job',
      python_callable=run_spark_job,
      dag=dag,
  )

6.6. Rodar o projeto:
  Adicione o caminho do projeto ao AIRFLOW_HOME e inicie o Airflow.
  O DAG aparecerá na interface web do Airflow para execução.
"""

# Saving the final instructions to a text file
with open('/mnt/data/WSL_PySpark_Airflow_Setup.txt', 'w') as file:
    file.write(final_instructions)



comandos de apoio:
deligar ambiente virtual python:
  deactivate

deletar um diretorio:
  rm -rf airflow_venv

abrir o VScode:
  code .

criar projeto:
  mkdir -p /home/gustavo/Python_ContratoLicitacoesETL
  cd ~/Python_PortalTransparenciaETL
  python3 -m venv .venv

  touch /home/gustavo/Python_ContratoLicitacoesETL/etl.py

  source .venv/bin/activate

adicionar o projeto a dags do airflow exemplo adicionando 2 projeto aqui:
  export AIRFLOW__CORE__DAGS_FOLDER="~/Python_ContratoLicitacoesETL:~/Python_PortalTransparenciaETL"

liberar dags dos projetos:
  export AIRFLOW__CORE__DAGS_FOLDER="/home/gustavo"

  mkdir -p /home/gustavo/airflow/dags
  ln -s /home/gustavo/Python_ContratoLicitacoesETL /home/gustavo/airflow/dags/Python_ContratoLicitacoesETL
  ln -s /home/gustavo/Python_PortalTransparenciaETL /home/gustavo/airflow/dags/Python_PortalTransparenciaETL
