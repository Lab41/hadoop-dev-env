This is a couple driver scripts to control a Hadoop developer environment. It
has been tested on OS X 10.8.5, Java 10.7, and Cloudera CDH4 and CDH5. It is
designed to work with both running Hadoop on the local machine as well as a
remote cluster.

Initializing Hadoop
===================

Before we can run Hadoop, we need to download and format an HDFS filesystem.
This can be automatically done by running:

```
% ./bin/hadoop-dev-env run local-cdh5.0.0 hadoop namenode -format
```

There's one last thing we have to do before we start the services. First we
need to be able to ssh into the machine. Here's an example on how to set it up:

```
% ssh-keygen -t rsa
...
% cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
```

Next, the `JAVA_HOME` environment variable must be set such that if you run
this command, you will get a result:

```
% ssh localhost env | grep JAVA_HOME
JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.7.0_25.jdk/Contents/Home
```

How you do this depends on which shell you use. You need to add:

```
export JAVA_HOME=$(/usr/libexec/java_home)
```

To either `~/.bash_profile` if you `bash`, or `~/.zshenv` if you use `zsh`.

Starting Hadoop
===============

All the Hadoop services can be started by running:

```
% ./bin/hadoop-dev-env start local-cdh5.0.0
```

To confirm Hadoop is working correctly, run:

```
% ./bin/hadoop-dev-env run local-cdh5.0.0 hadoop fs -mkdir -p /user/$USER
% echo "hey\nthere" > test
% ./bin/hadoop-dev-env run local-cdh5.0.0 hadoop fs -put test
% rm test
% cd 
% ./bin/hadoop-dev-env run local-cdh5.0.0./bin/hadoop jar \
    ~/versions/hadoop-2.3.0-cdh5.0.0/share/hadoop/mapreduce2/hadoop-mapreduce-examples-2.3.0-cdh5.0.0.jar \
    grep test output hey
```

Stopping Hadoop
===============

All the Hadoop services can be stopped by running:

```
% ./bin/hadoop-dev-env stop local-cdh5.0.0
```

Configuration
=============

Additional clusters can be setup by modifying the `~/.hadoop-dev-env/config` config file. Here is an example on how to connect to remote clusters:

```
{
    "local-cdh5.0.0": {
        "local": true,
        "version": "cdh5.0.0",
        "config_dir": "~/.hadoop-dev-env/conf/local-cdh5.0.0",
        "libs": ["hadoop", "hbase", "zookeeper"]
    },
    "cluster1": {
        "version": "cdh5.0.0",
        "config_dir": "~/.hadoop-dev-env/conf/cluster1",
        "libs": ["hadoop", "hbase", "zookeeper"]
    },
    "cluster2": {
        "version": "cdh4.4.0",
        "config_dir": "~/.hadoop-dev-env/conf/cluster2",
        "libs": ["hadoop"]
    }
}

```
