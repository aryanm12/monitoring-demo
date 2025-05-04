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


----

EKS:
----

Control Plane (Master) / API Server Endpoint - 
    - AWS itself manages this
    - Prometheus gets 403 error when trying to query the control plane
    - Public
    - Private

Worker Nodes (Nodes)
    - Where your pods actually runs
    - Here you specify the type instance type, t3, t4, c3 etc.
    - Should always be in Private Subnets


From lower env, which is from Dev / QA / Nod Prod:
    - Control Plane API Server Endpoint remain Public - meaning that we can interact with the cluster using kubectl commands from our local laptops
    - Worker Nodes resides in Private Subnets


In the Production Env:
    - Control Plane API Server Endpoint remain Private - meaning the admin should be in the same network where the cluster resides and then only they can interact with the cluster using kubectl commands
    - Worker Nodes resides in Private Subnets

------

Multi-tenant cluster:
---------------------

EKS Cluster Cost: 72 $ / Month for running the cluster
Components of EKS Cluster:
    Ingress Controllers + Load Balancer
    prometheus & grafana

= ~ 100$ - 120$ / Month 

Scenario: I am a part of a central DevOps team, and we have been told to optimize the Kubernetes cost in at-least in the Non-Production Env

My approach could be:

- Let me create a common EKS cluster and share the cluster with different teams
- 1 Cluster in each non-prod env and which is shared with 8 teams
    - We will create 1 Namespace for each team and share the namespace details with them
    - Create RBAC in such a way that each team only can view / read / write things in their own namespaces
    - Give each team access to leverage the central controllers which are deployed e.g. ingress controller
    - give them the access to leverage central monitoring stack

-----------------------------

Multiple micro-services within an application like ecommerce website 

How to work with on Kubernetes

1.) How to scale micro-services independently?
- I have 4 login based micro-services which needs to be scaled based on load
- The frontend should also scale based on load
- Some APIs should also auto-scale based on load

- How do you deploy these services on Kubernetes
    - You will create a deployment object type
        - image
        - replicas - 1 to n but static
            - e.g. my frontend for sure needs 4 replicas, 4 pods of fronend is running
            - login based microsrvice, I need 2 replicase, 2 pods will run

- How to auto-scale them?
    - HPA - Horizontal Pod Auto-scaling
    - It is also written w.r.t each micro-service
        - For Frontend MS - I will have a HPA object where-in I will define following
            - min replica - 4
            - max replica - 10
            - condition on which it should scale,
                - CPU of the MS, goes beyond 80% - do upscale
                - CPU goes less 40% - downscale

I have a cluster with 4 worker nodes - combining all the worker nodes cpu and memory
    - 16 core cpu
    - 128 GB Memory

- There should be a mechanism to auto-scale the worker nodes as well
    - Cluster Auto-scalar / Karpenter

