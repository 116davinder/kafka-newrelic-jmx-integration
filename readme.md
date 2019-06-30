# Apache Kafka NewRelic Integration
```
This is a ansible role which will install newrelic infra jmx plugin and add kafka jmx configs to specified hosts. 
```
## Cautions
1. It can generate atleast half million events per 1min if cluster size is 3.

## Assumptions
```
1. Production Ready Kafka Installation (https://github.com/116davinder/kafka-cluster-ansible )
2. NewRelic Infra Agent should be already installed and version should be higher than 1.3.x.
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

    - domain: kafka.controller
      event_type: kafkaMonitoring
      beans:
          - query: type=KafkaController,name=OfflinePartitionsCount
          - query: type=KafkaController,name=ActiveControllerCount
          - query: type=ControllerStats,name=LeaderElectionRateAndTimeMs
          - query: type=ControllerStats,name=UncleanLeaderElectionsPerSec

    - domain: kafka.log
      event_type: kafkaMonitoring
      beans:
          - query: type=LogFlushStats,name=LogFlushRateAndTimeMs

    - domain: kafka.network
      event_type: kafkaMonitoring
      beans:
          - query: type=SocketServer,name=NetworkProcessorAvgIdlePercent
          - query: type=RequestChannel,name=RequestQueueSize
          - query: type=RequestMetrics,name=TotalTimeMs,request=*
          - query: type=RequestMetrics,name=RequestQueueTimeMs,request=*
          - query: type=RequestMetrics,name=LocalTimeMs,request=*
          - query: type=RequestMetrics,name=RemoteTimeMs,request=*
          - query: type=RequestMetrics,name=ResponseQueueTimeMs,request=*
          - query: type=RequestMetrics,name=ResponseSendTimeMs,request=*

    - domain: kafka.server
      event_type: kafkaMonitoring
      beans:
          - query: type=*,name=*
          - query: type=ReplicaFetcherManager,name=MaxLag,clientId=Replica
          - query: type=FetcherLagMetrics,name=ConsumerLag,clientId=*,topic=*,partition=*
          - query: type=DelayedOperationPurgatory,name=PurgatorySize,delayedOperation=*

    - domain: kafka.utils
      event_type: kafkaMonitoring
      beans:
          - query: type=*,name=*
```

## Ref:
*  https://docs.confluent.io/current/kafka/monitoring.html