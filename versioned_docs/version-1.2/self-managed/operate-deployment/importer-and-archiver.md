---
id: importer-and-archiver
title: Importer and archiver
description: "Let's analyze how Operate is organized by modules to import and archive data."
---

Operate consists of three modules:

 * **Webapp**: Contains the UI and operation executor functionality.
 * **Importer**: Responsible for importing data from Zeebe.
 * **Archiver**: Responsible for archiving "old" data (finished process instances and user operations.) See [Data retention](data-retention.md).
 
Modules can be run together or separately in any combination and can be scaled. When you run an Operate instance, by default, all modules are enabled. To disable them, use the following configuration parameters:

Configuration parameter | Description | Default value
-----|-------------|--------------
camunda.operate.importerEnabled | When true, Importer module is enabled. | True
camunda.operate.archiverEnabled | When true, Archiver module is enabled. | True
camunda.operate.webappEnabled |  When true, Webapp module is enabled. | True

Additionally, you can have several importer and archiver nodes to increase throughput. Internally, they will spread their work based on Zeebe partitions.

For example, if your Zeebe runs 10 partitions and you configure two importer nodes, they will import data from five partitions each.

Each single importer/archiver node must be configured using the following configuration parameters:

Configuration parameter | Description | Default value
-----|-------------|--------------
camunda.operate.clusterNode.partitionIds | Array of Zeebe partition ids this Importer (or Archiver) node must be responsible for. | Empty array, meaning all partitions data is loaded.
camunda.operate.clusterNode.nodeCount |  Total amount of Importer (or Archiver) nodes in the cluster. | 1
camunda.operate.clusterNode.currentNodeId |  Id of current Importer (or Archiver) node, starting from 0. | 0

It's enough to configure either `partitionIds` or a pair of `nodeCount` and `currentNodeId`. If you provide `nodeCount` and `currentNodeId`, each node will automatically guess the Zeebe partitions they're responsible for.

:::note
`nodeCount` always represents the number of nodes of one specific type.
:::

For example, the configuration of a cluster with one Webapp node, two Importer nodes, and one Archiver node could look like the following:

```
Webapp node

camunda.operate:
  archiverEnabled: false
  importerEnabled: false
  #other configuration...

Importer node #1

camunda.operate:
  archiverEnabled: false
  webappEnabled: false
  clusterNode:
    nodeCount: 2
    currentNodeId: 0
  #other configuration...
  
Importer node #2

camunda.operate:
  archiverEnabled: false
  webappEnabled: false
  clusterNode:
    nodeCount: 2
    currentNodeId: 1
  #other configuration...
  
Archiver node

camunda.operate:
  webappEnabled: false
  importerEnabled: false
  
```

You can further parallelize archiver and/or importer within one node using the following configuration parameters:

Configuration parameter | Description | Default value
-----|-------------|--------------
camunda.operate.archiver.threadsCount | Number of threads in which data will be archived. | 1
camunda.operate.importer.threadsCount | Number of threads in which data will be imported. | 3

:::note
Parallelization of import and archiving within one node will also happen based on Zeebe partitions, meaning only configurations with (number of nodes) * (threadsCount) <= (total number of Zeebe partitions) will make sense. Too many threads and nodes will still work, but some of them will be idle.
:::

