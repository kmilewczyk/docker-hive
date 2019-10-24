# DISCLAIMER

DO NOT USE THIS IN ANY PRODUCTION ENVIRONMENT. Whole setup is put together on stitches and is not a great example of secure environment. This is student work for student project. Please keep it in mind.

Also this is a fork of https://github.com/big-data-europe/docker-hive.


# Containers

* namenode - essentially master for hdfs. [Documentation](https://cwiki.apache.org/confluence/display/HADOOP2/NameNode).
* datanode - essentially worker for hdfs. Stores data. [Documentation](https://cwiki.apache.org/confluence/display/HADOOP2/DataNode)
* hive-server - server interface for hive. [Documentation](https://cwiki.apache.org/confluence/display/Hive/Setting+Up+HiveServer2#SettingUpHiveServer2-HiveServer2)
* hive-metastore - separated metastore service for hive
* hive-metastore-postgresql - postgres configured for hive metastore


# Exercises notes

Use Hue GUI on localhost:8888 for any interaction with hive and hdfs.
Information below is hands-on presentation of the running parts of a docker-compose.
Also select whole SQL (With Ctrl-A) before clicking run. It will run whole script. You can also run parts by selecting relevant lines.

You can copy files with `docker cp` command.
There are mounted volume, `docker-hive_datanode` and `docker-hive_namenode`. You can find them with `docker volume ls`. Information like host path is found by executing `docker volume inspect <volume>`.

You can access any container with `docker-compose exec <containers docker compose name> bash`.

You can access beeline with `sudo docker-compose exec hive-server beeline -u jdbc:hive2://localhost:10000`

MySQL is replaced with PostgreSQL for hive metastore.
Client is available via `sudo docker-compose exec hive-metastore-postgresql psql -U postgres` command.
PostgreSQL client is psql and is a bit finicky at times.
Type `\?` to get help.
Access metastore with `\c metastore`.
There's only one schema: `public`.
Remember to use quotes, because postgres does not like capital letters. Example: `select * from "TBLS";`

I would recommend getting some text editor in `hive-server`. It's jessie based image so type `apt update` and `apt install` your applications. `tmux` and `vim` are what I would use.

HDFS is accessible from any container. Type `sudo docker-compose exec hive-server hadoop fs <rest of commands>` to get access.


# GUI

I opted in to Hue GUI, which I integrated to have access to Hive, HDFS and metastore. Note that Hue can throw 500s at the login screen. If this happens restart the hue container. There is a health check done to prevent this in docker compose file.

I did not use Ambari. From what I understand Ambari works as master-slave architecture. Agents actually ping local running daemons to check their state.
This sort of management is not really suited for docker contained services and would require some hoop jumping like giving privileged access.
I would be against such measures, so I opt out of implementing this.
