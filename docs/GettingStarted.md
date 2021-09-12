## Downloading Apache Spark

Go to https://spark.apache.org/downloads.html and download latest Spark file .

![](/docs/GettingStarted/1.png)

This will download all the binaries which will be required to run spark in local mode . In case of an existing Hadoop installation we would need to select Hadoop version from the dropdown .


## Spark Directories and Files .

On extracting the spark tar file , below would be the directories which would be created .

![](/docs/GettingStarted/2.png)

	• Readme.md: contains instructions on how to use spark shell , build spark from source & Contains links to documentation
	• bin : contains scripts to interact with spark , contains shells and executables
	• sbin: contains all the scripts required for administrative purposes such as starting / stopping spark .
	• kubernetes : contains docker files for creating docker images for spark distribution on a kubernetes cluster
	• data : contains .txt files that serve as input for spark components such as MLib , structured streaming , GraphX etc .
	• examples : contains example Java , R , python files .

## Using the Spark or Pyspark Shell

Check the Java installation as it is a pre-requisite run java-version to check
![](/docs/GettingStarted/3.png)


Run update & then install Java
```bash
sudo apt-get update

sudo apt-get install openjdk-8-jdk
```

![](/docs/GettingStarted/4.png)

cd into the bin directory and type pyspark to stark the shell .

```bash
./pyspark
```
![](/docs/GettingStarted/5.png)


Similarly to start spark-shell run below command

```bash
./spark-shell
```
![](/docs/GettingStarted/6.png)



Spark UI
Graphical interface used to inspect or monitor spark applications . Runs on http://localhost:4040/

![](/docs/GettingStarted/7.png)
