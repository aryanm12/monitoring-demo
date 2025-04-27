Monitoring / Observability - Checking parameters related to system or application, proactive monitoring

health checks

Alerting - Breach in threshold like 80%

-------

Monitoring: (Metrices)
-----------

-> System monitoring - servers (cpu, memory/RAM, storage)
-> Database monitoring - query performance, slow query, performance of the DB
-> Network monitoring - throughput of the network, hit per second, open ports, incoming traffic etc.
-> Application monitoring - api request served / second, health check, resource utilization by the application, failed requests

Tools: Nagios, Zabbix


Central Logging:
----------------

Gather logs from different streams, put inside a central database and show them at a central place

Traditional deployment:
    1 App: Nginx -> NodeJs
    is running on 10 servers

    Nginx: /var/log/nginx folder access.log and error.log and this folder will reside in all those 10 servers

    tail -100f /var/log/nginx/access.log; tail -100f /var/log/nginx/error.log;

    NodeJs: The application will write its own log, /opt/app/app.log and this file will reside in all those 10 servers

    tail -100f /opt/app/app.log;

Kubernetes deployment:
    4 microservices: all of them are NodeJs based

    They are deployed in K8S using deployment.yaml file with 2 replicas each

    In total now we have 8 application pods running

    they write their logs on console output

    kubectl get svc -n <ns>
    kubectl get pods -n <ns>

    kubectl logs <pod-name> -n <ns> -f // run this commands 8 time with different pod names

Enterprise Tool: Splunk
Open Source: ELK Stack / Elastic Stack


Alerting:
---------

configure breaches - if crossing 70% of CPU usage is a breach for you or crossing 90% for you

breaches are happenning then an alert via email / teams channel/ slack channel / opening tickets in Jira or ServiceNow

-----

Tools:

Open Source:
Nagios
Zabbix
Elastic Stack (ElasticSearch - is nosql database, File Beat - logs shipper, Kibana - Dashboard)
Prometheus - 2nd project in CNCF (Cloud Native Computing Foundation)
Grafana 

Enterprise:
DataDog
Dynatrace
Splunk
NewRelic
App Dynamics
Instana


-----------------------

Prometheus:

Functions as Time Series Database

Nagios: Configure to query CPU crossing above 80% and it should check that in every 5 mins

    - Nagios in every 5 mins, will run a query to get the current CPU usage.

    top / htop: it will print the current cpu usage

    - if above 80% send an alarm, if it is below 80% then simply ignore

Prometheus: Get the CPU usage in every 1 min

    - It will keep on fetching the CPU details from the server every 1 min and keep on storing the data
    - it stores the data w.r.t the timestamp of that instance