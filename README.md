# docker-compose_grafana-mysql

### How docker solves dependency?
Docker's dependencies can be determined by the following elements without specifying the order:
* *links*
* *volumes_from*
* *network_mode: "service:<service_name>"*

For a simple scenario it will suffice, how about when the dependent container is ready but the dependent service is not? Particularly, database, or any other services need prolonged startup time.

### Solutions

You can:
1. use [wait-for-it](https://github.com/vishnubob/wait-for-it), or [dockerzie](https://github.com/jwilder/dockerize), etc. Since Grafana container uses *ENTRYPOINT* of *[run.sh](https://github.com/grafana/grafana-docker/blob/master/run.sh)* it's not easy to **append** *./wait-for-it.sh*. There are other ways to solve it , such as using customized Grafana container image or using *docker-compose run*.
1. use *depends_on* from Docker, **simple and sweet!**.

```
docker-compose up

Creating network "grafana-mysql_docker-compose_metrics_net" with the default driver
Creating volume "grafana-mysql_docker-compose_db_data" with default driver
Creating grafana-mysql_docker-compose_db_1 ... done
Creating grafana                           ... done
Attaching to grafana-mysql_docker-compose_db_1, grafana

...
db_1       | 2018-08-12 11:58:38 1 [Note] mysqld: ready for connections.
db_1       | Version: '5.6.41'  socket: '/var/run/mysqld/mysqld.sock'  port: 3306  MySQL Community Server (GPL)
grafana    | t=2018-08-12T11:58:40+0000 lvl=info msg="Starting Grafana" logger=server version=5.2.2 commit=aeaf7b2 compiled=2018-07-25T11:17:28+0000

...
grafana    | t=2018-08-12T11:58:44+0000 lvl=info msg="HTTP Server Listen" logger=http.server address=0.0.0.0:3000 protocol=http subUrl= socket=
```
### Result
```
docker-compose ps
              Name                            Command                State               Ports         
-------------------------------------------------------------------------------------------------------
grafana                             /run.sh                       Up (healthy)   0.0.0.0:3000->3000/tcp
grafana-mysql_docker-compose_db_1   docker-entrypoint.sh mysqld   Up (healthy)   3306/tcp
```

```
mysql> USE grafana;SHOW tables;
Database changed
+------------------------+
| Tables_in_grafana      |
+------------------------+
| alert                  |
| alert_notification     |
| annotation             |
| annotation_tag         |
| api_key                |
| dashboard              |
| dashboard_acl          |
| dashboard_provisioning |
| dashboard_snapshot     |
| dashboard_tag          |
| dashboard_version      |
| data_source            |
| login_attempt          |
| migration_log          |
| org                    |
| org_user               |
| playlist               |
| playlist_item          |
| plugin_setting         |
| preferences            |
| quota                  |
| session                |
| star                   |
| tag                    |
| team                   |
| team_member            |
| temp_user              |
| test_data              |
| user                   |
| user_auth              |
+------------------------+
30 rows in set (0.00 sec)
```

If MySQL turns out to be _unhealthy_ Grafana container will not start.

Grafana container starts only after the MySQL container becomes Ready, and Healthy. Now, Grafana is ready at [http://localhost:3000](http://localhost:3000) :+1:
