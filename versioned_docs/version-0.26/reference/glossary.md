---
id: glossary
title: "Glossary"
---

This section defines commonly used terminology referenced within the documentation.

### Broker

A broker is an instance of a Zeebe installation which executes workflows and manages workflow state. A single broker will be installed on a single machine.

- [Architecture](/components/zeebe/technical-concepts/architecture.md#broker)

### Client

A client interacts with the Zeebe broker on behalf of the business application. Clients poll for work from the broker.

- [Architecture](/components/zeebe/technical-concepts/architecture.md#client)

### Cluster

A cluster represents a configuration of one or more brokers collaborating to execute workflows. Each broker in a cluster acts as a leader or a follower.

- [Clustering](/components/zeebe/technical-concepts/clustering.md)

### Command

A command represents an action to be taken or executed. Example commands include: deploy a workflow, execute a workflow, etc.

- [Internal processing](/components/zeebe/technical-concepts/internal-processing.md#events-and-commands)

### Correlation

Correlation refers to the act of matching a message with an inflight workflow instance.

- [Message correlation](/components/concepts/messages.md)

### Correlation Key

A correlation is an attribute within a message which is used to match this message against a certain variable within an inflight workflow instance. If the value of the correlation key matches the value of the variable within the workflow instance, the message is matched to this workflow instance.

- [Message correlation](/components/concepts/messages.md)

### Deployment

A workflow cannot execute unless it is known by the broker. Deployment is the process of pushing or deploying worklows to the broker.

- [Getting started tutorial: Deploy a workflow](/components/zeebe/deployment-guide/getting-started/deploy-a-workflow.md)

### Event

An event represents a state change associated with an aspect of an executing workflow instance. Events capture variable changes, state transition in workflow elements, etc. An event will be represented by a timestamp, the variable name and variable value. Events are stored in an append-only log.

- [Internal processing](/components/zeebe/technical-concepts/internal-processing.md#events-and-commands)

### Exporter

An exporter represents a sink to which Zeebe will submitted all records within the log. This gives users of Zeebe an opportunity to persist records with the log for future use as this data will not be available after log compaction.

- [Exporter](/components/zeebe/open-source/exporters.md)

### Follower

In a clustered environment, a broker which is not a leader is a follower of a given partition. A follower can become the new leader when the old leader is no longer reachable.

- [Clustering](/components/zeebe/technical-concepts/clustering.md#raft-consensus-and-replication-protocol)

### Gateway

Clients communicate with the Zeebe cluster through a gateway. The gateway provides a gRPC API and forwards client commands to the cluster. Depending on the setup, a gateway can be embedded in the broker or can be configured to be standalone.

- [Architecture](/components/zeebe/technical-concepts/architecture.md#gateway)

### Incident

An incident represents an error condition which prevents Zeebe from advancing an executing workflow instance. Zeebe will create an incident if there was an uncaught exception thrown in your code and the number of retries of the given step has been exceeded.

- [Incident](/components/concepts/incidents.md)

### Job

A job represents a distinct unit of work within a business process. Service tasks represent such
jobs in your workflow and are identified by a unique id. A job has a type to allow specific job
workers to find jobs that they can work on.

- [Job workers](/components/concepts/job-workers.md)

### Job activation timeout

This is the amount of time the broker will wait for a complete or fail response from the job worker after a job has been submitted to the job worker for processing before it marks the job as available again for other job workers.

- [Job workers](/components/concepts/job-workers.md#requesting-jobs)

### Job worker

A special type of client that polls for and executes available jobs. An uncompleted job prevents Zeebe from advancing workflow execution to the next step.

- [Job workers](/components/concepts/job-workers.md)

### Leader

In a clustered environment, one broker, the leader, is responsible for workflow execution and housekeeping of data within a partition. Housekeeping includes, taking snapshots, replication and running exports.

- [Clustering](/components/zeebe/technical-concepts/clustering.md#raft-consensus-and-replication-protocol)

### Log

The log comprises of an ordered sequence of records written to persistent storage. The log is appended-only and is stored on disk within the broker.

- [Partitions](/components/zeebe/technical-concepts/partitions.md#partition-data-layout)

### Message

A message contains information to be delivered to interested parties during execution of a workflow instance. Messages can be published via Kafka or Zeebe’s internal messaging system. Messages are associated with timestamp and other constraints such as time-to-live (TTL).

- [Messages](/components/concepts/messages.md)

### Partition

A partition represents a logical grouping of data in a Zeebe broker. This data includes workflow instance variables stored in RocksDB, commands and events generated by Zeebe stored in the log. The number of partitions is defined by configuration.

- [Partitions](/components/zeebe/technical-concepts/partitions.md)

### Record

A record represents a command or an event. For example, a command to create a new workflow instance, or a state transition of an executing workflow instance representing an event at a given point in time would result to generation of a record. During the execution lifecycle of a workflow instance, numerous records will be generated to capture various commands and events generated. Records are stored in the log.

- [Internal processing](/components/zeebe/technical-concepts/internal-processing.md#events-and-commands)

### Replication

Replication is the act of copying data in a partition from a leader to its followers within a clustered Zeebe installation. After _replication_, the leader and followers of a partition will have the exact same data. Replication allows the system to be resilient to brokers going down.

- [Clustering](/components/zeebe/technical-concepts/clustering.md#raft-consensus-and-replication-protocol)

### Replication factor

This is the number of times data in a partition will be copied and this depends on the number of brokers in a cluster. A cluster with one leader and two followers will have a replication factor of three, as data in each partition needs to have three copies.

- [Clustering](/components/zeebe/technical-concepts/clustering.md#raft-consensus-and-replication-protocol)

### Request timeout

This is how long a client will wait for a response from the broker after the client has submitted a request. If a response is not received within the client request timeout, the client can consider the broker unreachable.

- [Zeebe API (gRPC)](grpc.md)

### Snapshot

The state of all active workflows instances, (these are also known as inflight workflow instances) are stored as records in an in-memory database called RocksDB. A snapshot represents a copy of all data within the in-memory database at any given point in time. Snapshots are binary images stored on disk and can be used to restore execution state of a workflow. The size of a snapshot is affected by the size of the data. Size of the data depends on several factors including complexity of the model or business process, the size and quantity of variables in each workflow instance as well as the total number of executing workflow instances in a broker.

- [Resource planning](/components/zeebe/deployment-guide/operations/resource-planning.md#snapshots)

### Segment

The log consists of one or more _segments_. Each _segment_ is a file that contains an ordered sequence records. _Segments_ are deleted when the log is compacted.

- [Resource planning](/components/zeebe/deployment-guide/operations/resource-planning.md#event-log)

### Worker

A worker executes a job. In the Zeebe nomenclature, these are also referred to as job workers.

- [Job workers](/components/concepts/job-workers.md)

### Workflow

A workflow is a defined sequence of distinct steps representing your business logic. Examples of a
workflow could be an e-commerce shopping experience, onboarding a new employee, etc. In Zeebe,
workflows are identified by a unique process id. The workflow is usually also referred to as the
BPMN model.

- [Workflows](/components/concepts/workflows.md)

### Workflow instance

While a workflow represents a defined sequence of distinct steps representing your business logic, a workflow instance represents a currently executing or completed workflow. For a single workflow, there could be many associated _workflow instances_ in various stages of their executing lifecycle. Workflow instances are identitied by workflow instance id. Executing workflows instances are also sometimes referred to as inflight workflows.

- [Workflows](/components/concepts/workflows.md)

### Workflow instance variable

A workflow instance variable represents the execution state (i.e data) of a workflow instance. These variables capture business process parameters which are input and output of various stages of the workflow instance and which also influence process flow execution.

- [Variables](/components/concepts/variables.md)
- [Data flow](/reference/bpmn-workflows/data-flow.md)
