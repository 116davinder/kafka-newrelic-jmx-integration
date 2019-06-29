# Apache Kafka NewRelic Integration
```
This is a ansible role which will install newrelic infra jmx plugin and add kafka jmx configs to specified hosts. 
```
## Cautions
1. It will generate atleast half million events per 1min if cluster size is 3.

## Assumptions
```
NewRelic Infra Agent should be already installed and version should be higher than 1.3.x.
```

## Requirements
* NewRelic Infra Plugin RPMs.
* JMX Enabled on Kafka Brokers.
* Ansible Management Kafka Cluster.

## How to use
1. Include `ansible-nri-kafka-jmx` role to your playbook.
2. Added following vars
    * `kafkaJmxPort`
    * `kafkaClusterName`

## Following Stats for Kafka JVM
```
collect:
# Standard JVM Metrics
    - domain: java.lang
      event_type: kafkaMonitoring
      beans:
          - query: type=GarbageCollector,name=*
            attributes:
                - CollectionCount
                - CollectionTime
                - LastGcInfo
          - query: type=Memory
            attributes:
                - HeapMemoryUsage.Committed
                - HeapMemoryUsage.Init
                - HeapMemoryUsage.Max
                - HeapMemoryUsage.Used
                - NonHeapMemoryUsage.Committed
                - NonHeapMemoryUsage.Init
                - NonHeapMemoryUsage.Max
                - NonHeapMemoryUsage.Used
          - query: type=Threading
            attributes:
                - ThreadCount
                - TotalStartedThreadCount
                - PeakThreadCount
          - query: type=ClassLoading
            attributes:
                - LoadedClassCount
          - query: type=Compilation
            attributes:
                - TotalCompilationTime
```

## Following Stats for Kafka
```
collect:
    - domain: kafka.cluster
      event_type: kafkaMonitoring
      beans:
          - query: type=Partition,name=UnderReplicated,partition=*,topic=*

    - domain: kafka.controller
      event_type: kafkaMonitoring
      beans:
          - query: type=*,name=*

    - domain: kafka.log
      event_type: kafkaMonitoring
      beans:
          - query: type=Log,name=*
          - query: type=LogFlushStats,name=LogFlushRateAndTimeMs

    - domain: kafka.network
      event_type: kafkaMonitoring
      beans:
          - query: type=RequestMetrics,name=*
          - query: type=RequestChannel,name=*
          - query: type=SocketServer,name=NetworkProcessorAvgIdlePercent

    - domain: kafka.server
      event_type: kafkaMonitoring
      beans:
          - query: type=ReplicaManager,name=*
          - query: type=DelayedOperationPurgatory,name=*
          - query: type=BrokerTopicMetrics,name=*
          - query: type=KafkaRequestHandlerPool,name=RequestHandlerAvgIdlePercent

    - domain: kafka.utils
      event_type: kafkaMonitoring
      beans:
          - query: type=*,name=*
```
