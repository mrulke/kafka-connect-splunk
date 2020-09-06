# Getting Started with the Turbonomic Splunk Exporter

Turbonomic 8 continuously monitors applications and infrastructure components communicating with different APIs
(Dynatrace, Openshift, VMware, VMM, HyperV, etc..) The topology and utilization data from these various sources are
continuously stitched together into a full stack topology containing all the current/most recent monitored values
for each entity as it is collected from the sources within the last few minutes.
This full stack topology and metrics is internally broadcasted using an kafka message bus within Turbonomic 8
to various components, for example to the market to run the analytics or for the history to store historical metrics.

We have created a container image that subscribes to the same live topology broadcast using the internal
kafka messaging bus and sends the same information with all the details to a Splunk server using a Splunk connector.
This leverages the opensource Splunk connector, which is available to any free or paying Splunk customer
https://github.com/splunk/kafka-connect-splunk

The Turbonomic Exporter makes it easy for Turbonomic Administrators to export time-series metrics
with topology context to Splunk. Packaged as a container it can be easily deployed either using helm or a simple yaml.

## Installing the Turbonomic Exporter from a yaml file
````
kubectl create -f https://raw.githubusercontent.com/turbonomic/kafka-connect-splunk/turbonomic/deploy/splunk-kafka-connect_yamls/deployment.yaml -n turbonomic
````
## Installing the Turbonomic Exporter using helm
````
helm install splunk-kafka-connect splunk-kafka-connect -n turbonomic
````

This component expects that you have a Splunk server (hec uri) available and accessible from where Turbo 8 runs
and also that you have created a hec token to authenticate this kafka-splunk-connector instance.
This information can be sent to the kafka-splunk-connector using a single http post api (as documented on the github page):
````
$ curl splunk-kafka-connect:8083/connectors -X POST -H "Content-Type: application/json" -d '{
  "name":"kafka-connect-splunk",
  "config":{
     "connector.class":"com.splunk.kafka.connect.SplunkSinkConnector",
     "tasks.max":"3",
     "splunk.indexes":"main",
     "topics":"turbonomic.exporter",
     "splunk.hec.uri":"https://splunk.splunk.svc.cluster.local:8088",
     "splunk.hec.token":"XXX",
     "splunk.hec.ssl.validate.certs": "false"
  }
}
````

At which point the data will start flowing into the specified Splunk index, from the exporter topic from Turbonomic 8


### Delete the Turbonomic Exporter from a yaml file

````
kubectl delete -f https://raw.githubusercontent.com/turbonomic/kafka-connect-splunk/turbonomic/deploy/splunk-kafka-connect_yamls/deployment.yaml -n turbonomic
````

### Delete the Turbonomic Exporter using helm

````
helm delete splunk-kafka-connect -n turbonomic
````

