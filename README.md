# docker-compose_grafana-mysql

```
docker-compose up

Creating network "grafana-ha_metrics_net" with the default driver
Creating volume "grafana-ha_db_data" with default driver
Creating grafana-ha_db_1 ... done
Creating grafana             ... done
Attaching to grafana-ha_db_1, grafanadb_1       | Initializing database

...
grafana    | t=2018-08-12T11:58:40+0000 lvl=info msg="Starting Grafana" logger=server version=5.2.2 commit=aeaf7b2 compiled=2018-07-25T11:17:28+0000

...
grafana    | t=2018-08-12T11:58:44+0000 lvl=info msg="HTTP Server Listen" logger=http.server address=0.0.0.0:3000 protocol=http subUrl= socket=
```

### docker-compose up
Grafana container starts only after the MySQL container becomes Ready, and Heathy. At the end Grafana is ready at [http://localhost:3000](http://localhost:3000)
