This example shows how to run using CircleCI with a mysql server instance in the stack. 
There are only two files needed to make this work. You need the `.circleci/config.yml` 
file that control how the docker image is built and run on CircleCI, and the GLM model file 
`test_mysql_collector.glm`that defines the how the simulation is constructed and data from 
it is collected.

# .circleci/config.yml

This file describes a fairly standard docker stack configuration. But there are a few aspects
worthy of discussion.

## MYSQL_ROOT_PASSWORD

The MySQL database support an administrative user called `root` that requires a password.  
In this example the password is simply `gridlabd`, which is obviously not very secure. If
you are not too concerned with security while your simulation is running, this is probably ok. 
The details of how run a more secure system is beyond the scope of this example.

## Install dockerize and wait for database to initialize

The simulation should not be started until the MySQL server is fully up and running.  The
`dockerize` command provides you the ability to wait until it is ready before starting your
simulation.

## Install mysql client and dump the database

The easiest way to save the database results is to use the `mysqldump`, which is part of the
`mysql` package, which must be installed. When the simulation is complete, the database will
be dumped into the `test_mysql_collector.sql` file, which you can download and restore into a
MySQL server that you use to do your analysis queries.

## Run the simulation

The `--debug` and `--verbose` options are included in this example to help debug database
access issues.  Normally, these generate excessive output and should only be used to 
troubleshoot simulation problems.

# test_mysql_collector.glm

The example model is taken from the `mysql` module autotests.  The only significant changes
are the addition of the database access parameters:
~~~
 object database {
+    hostname "127.0.0.1";
+    schema "gridlabd";
+    username "root";
+    password "gridlabd";
+    port 3306;
    options NEWDB;
}
~~~
The `hostname` is the IP address of the localhost, which is running the MySQL server.
The `schema` refers to the database name, which will be dumped by `mysqldump`. If you want
to change the `schema`, you will have change the `mysqldump` command option `gridlabd` to match.
The `username` is `root` to simplify things, even though this is generally regarded as insecure.
Changing the `username` is not in scope for this example. The `password` parameter has to match
the password set by `MYSQL_ROOT_PASSWORD`, and used in the `mysqldump` command in the `-p` option.
