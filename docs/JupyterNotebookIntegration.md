## Anaconda Installation

Download anaconda from below url
https://www.anaconda.com/products/individual


Install Anaconda

```bash
bash Anaconda3-2021.05-Linux-x86_64.sh
```


![](/docs/JupyterNotebookIntegration/1.png)

## Downloading Apache Spark

Go to https://spark.apache.org/downloads.html and download latest Spark file .

![](/docs/JupyterNotebookIntegration/2.png)

Unzip it and Update Bash profile:

```bash
tar -xzf spark-3.1.1-bin-hadoop2.7.tgz`
```

Configure Path

```bash
nano ~/.bashrc`
```

Add below to bash profile
```bash
export SPARK_HOME=/home/diwakar/Downloads/spark-3.1.1

export PATH=$SPARK_HOME/bin:$PATH
```

![](/docs/JupyterNotebookIntegration/3.png)


## Configure PySpark driver

```bash
nano ~/.bashrc`
```

Add below to the environment variables

```bash
export PYSPARK_DRIVER_PYTHON=jupyter

export PYSPARK_DRIVER_PYTHON_OPTS='notebook
```

![](/docs/JupyterNotebookIntegration/4.png)

Run "pyspark" in the window to start the jupyter notebook

Run below code to test

```python
from pyspark.sql.types import StructType, StructField, FloatType, BooleanType

from pyspark.sql.types import DoubleType, IntegerType, StringType

import pyspark

from pyspark import SQLContext

conf = pyspark.SparkConf()

sc = pyspark.SparkContext.getOrCreate(conf=conf)
sqlcontext = SQLContext(sc)

schema = StructType([
    StructField("sales", IntegerType(),True),
    StructField("sales person", StringType(),True)
])

data = ([(10, 'Walker'),
        ( 20, 'Stepher')
        ])

df=sqlcontext.createDataFrame(data,schema=schema)

df.show()
```

![](/docs/JupyterNotebookIntegration/5.png)
