# GPDB 5 Demo

**January, 2018**

#### Initial items to cover

* Jupyter Notebook and connecting to GPDB
* Apache MADlib
* PL/Python
* PL/Containers
* GPDB-Spark Connector


#### Build Docker Images

1. Download the following files to "docker-gpdb/pivotal" directory from https://network.pivotal.io/products/pivotal-gpdb
  * Greenplum Database 5.3.0 Binary Installer for RHEL 6 (greenplum-db-5.3.0-rhel6-x86_64.zip)
  * MADlib 1.13 for RHEL 6 (madlib-1.13-gp5-rhel6-x86_64.tar.gz)
  * Python Data Science Package for RHEL 6 (DataSciencePython-1.1.1-gp5-rhel6-x86_64.gppkg)
  * PL/Container 1.0.0_beta1 for RHEL 6 (plcontainer-1.0.0_beta1-rhel6-x86_64.gppkg)
  * Pl/Container Image for Python 1.0.0_beta1 (plcontainer-python-images-1.0.0_beta1.tar.gz)

2. Download the following files to "docker-jupyter/pivotal" directory from https://network.pivotal.io/products/pivotal-gpdb
  * Greenplum-Spark Connector 1.1.0 (greenplum-spark_2.11-1.1.0.jar)

3. Build containers
```bash
# build gpdb container
cd docker-gpdb
docker build -t jvawdrey/gpdb .
cd ..

# build jupyter container
cd docker-jupyter
docker build -t jvawdrey/jupyter .
cd ..

```

4. Run containers
```bash

# create network for inter container communication
docker network create -d bridge contbridge

# run gpdb image
docker run -it --rm --network=contbridge -p 5432:5432 --name=gpdb jvawdrey/gpdb

# build spark container
docker run -it --rm --network=contbridge -p 4040:4040 -p 8080:8080 -p 8081:8081 -h spark --name=spark p7hb/docker-spark
# from within container
start-master.sh && start-slave.sh spark://spark:7077

# run jupyter-notebook container
docker run -it --rm --network=contbridge -p 8888:8888 --name=jupyter --mount type=bind,source=$(pwd)/notebooks,destination=/jupyter/notebooks jvawdrey/jupyter

```

5. Docker IP
```bash
# Grab IP of 'default' image
docker-machine ip default
# 192.168.99.101

```

* Jupyter Notebook: http://< IP ADDRESS >:8888/
* Spark Master WebUI console: http://< IP ADDRESS >:8080/
* Spark Worker WebUI console: http://< IP ADDRESS >:8081/
* Spark WebUI console: http://< IP ADDRESS >:4040/


#### Issues

Configuring shell session
```bash

# check docker is available
docker --version
# Docker version 1.9.1, build a34a1d5
# Docker version 1.8.0, build 0d03096

docker run -it --rm -p 8888:8888 jupyter/pyspark-notebook
# Cannot connect to the Docker daemon. Is the docker daemon running on this host?

# Run this command to configure your shell:
eval "$(docker-machine env default)"

# Error checking TLS connection: default is not running. Please start it in order to use the connection settings
docker-machine rm default
docker-machine create default --driver virtualbox
# Running pre-create checks...
# Creating machine...
# (default) Creating VirtualBox VM...
# (default) Creating SSH key...
# Error attempting heartbeat call to plugin server: connection is shut down
# Error creating machine: Error in driver during machine creation: unexpected EOF

```


#### Contact

* Jarrod Vawdrey (jvawdrey@pivotal.io)
