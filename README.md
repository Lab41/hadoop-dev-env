This is a couple driver scripts to set up a CDH 4.4 developer environment that
uses YARN. It has been tested on OS X 10.8.5 with Java 10.7. It is designed to
work with both running CDH on the local machine as well as a VM running on the
local machine.

Initializing CDH
================

Before we can run CDH, we first need to download it:

```
% ./bin/download cdh4.4.0
```

Next, initialize HDFS:

```
% ./bin/run local hdfs namenode -format
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

Starting CDH
============

There is a simple helper script to start CDH:

```
% ./bin/start local
```

To confirm CDH is working correctly, run:

```
% ./bin/run local hadoop fs -mkdir -p /user/$USER
% echo "hey\nthere" > test
% ./bin/run local hadoop fs -put test
% rm test
% cd 
% ./bin/hadoop jar \
    cdh/cdh4.4.0/hadoop-2.0.0-cdh4.4.0/share/hadoop/mapreduce2/hadoop-mapreduce-examples-2.0.0-cdh4.4.0.jar \
    grep test output hey
```

Stopping CDH
============

There is also a helper script to shut down CDH:

```
% ./bin/stop local
```
