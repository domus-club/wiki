**Servicios `domus-backend`**

| Services                                  | Port |
| ----------------------------------------- | -------------- |
| domus-backend-load-balancer-4000          | 4000:4000      |
| domus-backend-kafka-proxy-4001            | 4001:4001      |
| domus-backend-user-onboarding-4002        | 4002:4002      |
| domus-backend-chats-4005                  | 4005:4005      |
| domus-backend-residential-onboarding-4006 | 4006:4006      |
| domus-backend-houses-4009                 | 4008:4008      |
| domus-backend-residentials-4009           | 4009:4009      |
| domus-backend-config-server-4400          | 4400:4400      |

---


| Services                          | Port               |
| --------------------------------- | ------------------------------- |
| domus-docker-jenkins-1            | 50000:50000, 8080:8080          |
| mailcowdockerized-mysql-mailcow   | 13306:3306                      |
| domus-docker-prometheus-1         | 9090:9090                       |
| eureka-server                     | 8761:8761                       |
| mailcowdockerized-nginx-mailcow   | 8443:8443, 880:880              |
| domus-docker-kafka-ui-1           | 8082:8080                       |
| mailcowdockerized-redis-mailcow   | 7654:6379                       |
| domus-docker-redis-1              | 6379:6379                       |
| domus-docker-n8n-1                | 5678:5678                       |
| domus-docker-helpy-postgres-1     | 5433:5432                       |
| domus-docker-postgres-1           | 5432:5432                       |
| domus-docker-nexus-1              | 5123:5123, 8081:8081            |
| pgadmin                           | 5050:80                         |
| domus-docker-portainer-1          | 5000:9000, 5001:9443            |
| domus-docker-helpy-app-1          | 3400:8080                       |
| domus-docker-grafana-1            | 3300:3000                       |
| domus-docker-tempo-1              | 3200:3200, 4318:4318, 9411:9411 |
| domus-docker-loki-1               | 3100:3100                       |
| domus-docker-uptime-kuma-1        | 3031:3001                       |
| libretranslate                    | 2081:5000                       |
| mailcowdockerized-dovecot-mailcow | 110:110, 995:995, 19991:12345   |
| domus-docker-nginx-proxy-manager  | 80:80, 81:81, 443:443           |
| mailcowdockerized-postfix-mailcow | 25:25, 465:465, 587:587         |
