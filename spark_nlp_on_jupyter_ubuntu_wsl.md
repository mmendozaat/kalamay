### Setup Jupyter with Spark and Spark NLP on Windows (Ubuntu WSL)

1. Install JDK 8
  ```
  sudo apt-get install openjdk-8-jdk
  echo "export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64" >> ~/.bashrc
  ```
2. Install Spark 2.7
  ```
  wget https://ftp.wayne.edu/apache/spark/spark-2.4.7/spark-2.4.7-bin-hadoop2.7.tgz
  tar -xzvf spark-2.4.7-bin-hadoop2.7.tgz
  export "export SPARK_HOME=/home/dir/spark-2.4.7-bin-hadoop2.7.tgz" >> ~/.bashrc
  ```
3. Create virtual environment for different python version
  ```
  pip install virtualenv
  virtualenv -p python3.7 py37
  source py37/bin/activate
  ```
  Spark NLP requires python version 2.7 up to 3.7.
  There is an issue with python 3.8.
  
4. Create jupyter kernel for new virtual environment
  ```
  pip install ipykernel
  python -m ipykernel install --user --name=py37
  ```
5. Setup Spark NLP
  ```
  pip install spark-nlp==2.4.7
  echo "export PYSPARK_PYTHON=python3.7" >> ~/.bashrc
  echo "export PYSPARK_DRIVER_PYTHON=jupyter" >> ~/.bashrc
  echo "export PYSPARK_DRIVER_PYTHON_OPTS=notebook" >> ~/.bashrc
  echo 'alias pyspark="pyspark --packages com.johnsnowlabs.nlp:spark-nlp_2.11:2.7.4"' >> ~/.bashrc
  ```
6. Invoke `pyspark`. Open new notebook using kernel `py37`
