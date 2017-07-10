AppDynamics Agents for Kubernetes
======
# Introduction
This Kubernetes YAML file deploys AppDynamics Agents as a Daemon Set within your Kubernetes Cluster and lets' you share the Java Agents across your PODs.
# Deploy
The DaemonSet relies on the Docker Image found in the [Docker AppDynamics Agents] Repository.
* Clone the Repository
* Change the Docker Image Name to fit your needs
* ```kubectl create -f appd-agents.yaml```

# Monitor Applications
To monitor an Application you have to tell it where to find the Agent and how it should be named in AppDynamics. A simple example is shown here:

```
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: liferay
  labels:
    app: liferay
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: liferay
    spec:
      containers:
      - name: liferay-container
        image: snasello/liferay-6.2
        volumeMounts:
          - mountPath: /app-agent
            name: app-agent
        env:
          - name: APPDYNAMICS_AGENT_APPLICATION_NAME
            value: Application
          - name: APPDYNAMICS_AGENT_TIER_NAME
            value: Tier
          - name: CATALINA_OPTS
            value: -Dappdynamics.agent.reuse.nodeName.prefix=$APPDYNAMICS_AGENT_TIER -Dappdynamics.agent.reuse.nodeName=true -Dappdynamics.force.default.ssl.certificate.validation=false -javaagent:/app-agent/javaagent.jar
      volumes:
        - name: app-agent
          hostPath:
            path: /opt/app-agent
```
You can change the different settings to fit your needs.

Notes:
* When you scale up and down Nodes will be named based on ```-Dappdynamics.agent.reuse.nodeName.prefix``` and will be appended by a number
* Nodes will be automatically marked as historical once the JVM shuts down gracefully

[Docker AppDynamics Agents]: https://github.com/michaelenglert/docker.appd_agents
