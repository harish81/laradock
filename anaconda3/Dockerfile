# Use the Anaconda3 base image
FROM continuumio/miniconda3

ENV SPARK_VERSION=3.4.1
ARG PYTHON_CODE_PATH_CONTAINER

RUN apt-get update

# Install Jupyter
RUN conda install -y jupyter

# Install Spark and PySpark
RUN conda install -y -c conda-forge pyspark=$SPARK_VERSION openjdk=8 findspark

## Add Non Root user
ARG PUID=1000
ENV PUID ${PUID}
ARG PGID=1000
ENV PGID ${PGID}
RUN groupadd -g ${PGID} laradock && \
  useradd -l -u ${PUID} -g laradock -m laradock && \
  usermod -p "*" laradock -s /bin/bash

USER laradock

# Set the working directory
WORKDIR ${PYTHON_CODE_PATH_CONTAINER}
RUN chown -R laradock:laradock ${PYTHON_CODE_PATH_CONTAINER}

# Install Azure ADLS Gen2 support
# RUN pip install azure-storage-blob
# RUN pip install azure-datalake-store
# #RUN pip install azure-storage-file-datalake
# RUN pip install adlfs

# Install additional Python packages
# RUN pip install pg8000 psycopg2-binary

USER root

# Set JAVA_HOME environment variable
ENV JAVA_HOME=/opt/conda
ENV PATH=$JAVA_HOME/bin:$PATH

# Set environment variables for Spark
ENV SPARK_HOME=/opt/spark
ENV PATH=$SPARK_HOME/bin:$PATH

# Copy Spark binaries to the image
RUN wget -qO- https://archive.apache.org/dist/spark/spark-$SPARK_VERSION/spark-$SPARK_VERSION-bin-hadoop3.tgz | tar xvz -C /opt/
RUN mv /opt/spark-$SPARK_VERSION-bin-hadoop3 /opt/spark

## Install additional jars for spark
# Postgres JDBC
RUN wget -qO- https://jdbc.postgresql.org/download/postgresql-42.7.5.jar -P /opt/spark/jars/

USER laradock

# Expose Jupyter port
EXPOSE 8888

# Start Jupyter Notebook
CMD ["jupyter", "notebook", "--ip='*'", "--port=8888", "--no-browser", "--notebook-dir=$PYTHON_CODE_PATH_CONTAINER", "--NotebookApp.token=''", "--NotebookApp.password=''"]
