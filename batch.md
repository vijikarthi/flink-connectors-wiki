<!--
Copyright (c) 2017 Dell Inc., or its subsidiaries. All Rights Reserved.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0
-->
# Batch Connector
The Flink connector library for Pravega makes it possible to use a Pravega stream as a data source and data sink in a batch program.  See the below sections for details.

## Table of Contents
- [FlinkPravegaInputFormat](#flinkpravegainputformat)
  - [Pravega Batch Client](#pravega-batch-client)
  - [Input Stream(s)](#input-streams)
  - [StreamCuts](#streamcuts)
  - [Deserializer](#serialization)
  - [Parallelism](#parallelism)
  - [Builder API usage](#builder-api-usage)

- [FlinkPravegaOutputFormat](#flinkpravegaoutputformat)
  - [Parameters](#parameters)
  - [Output Stream](#output-stream)
  - [Parallelism](#parallelism)
  - [Event Routing](#event-routing)
  - [Serialization](#serialization)

## FlinkPravegaInputFormat
A Pravega stream may be used as a data source within a Flink batch program using an instance of 
```
io.pravega.connectors.flink.FlinkPravegaInputFormat
```
The input format, reads a given Pravega stream as a [`DataSet`](https://ci.apache.org/projects/flink/flink-docs-master/api/java/org/apache/flink/api/java/DataSet.html) (the basic abstraction of the Flink Batch API). Note that the stream elements are considered to be **unordered** in the batch programming model.

Use the following method to open a Pravega stream as a DataSet:

```
[ExecutionEnvironment::createInput](https://ci.apache.org/projects/flink/flink-docs-master/api/java/org/apache/flink/api/java/ExecutionEnvironment.html#createInput-org.apache.flink.api.common.io.InputFormat-)
Here's an example:
```
### Pravega batch Client

Pravega configuration cna be defiend as follows:
```
PravegaConfig config = PravegaConfig.fromParams(params);
```

Event deserializer can be configured as follows:
```
DeserializationSchema<EventType> deserializer = ...
```
FlinkPravegaInputFormat based on a Pravega stream can be defined as follows:
```java
FlinkPravegaInputFormat<EventType> inputFormat = FlinkPravegaInputFormat.<EventType>builder()
    .forStream(...)
    .withPravegaConfig(config)
    .withDeserializationSchema(deserializer)
    .build();

DataSource<EventType> dataSet = env.createInput(inputFormat, TypeInformation.of(EventType.class)
                                   .setParallelism(2);
...
```
### Deserialization

Please, see the [Deserialization](serialization.md) page for more information on how to use the deserializer.

### Input Stream(s)
Each stream in Pravega is contained by a scope.  A scope acts as a namespace for one or more streams. The [BatchClient](https://github.com/pravega/pravega/blob/master/client/src/main/java/io/pravega/client/batch/BatchClient.java) is able to read from numerous streams in parallel, even across scopes.  The builder API accepts both **qualified** and **unqualified** stream names.  

  - In aualified stream names, the scope is explicitly specified, e.g. `my-scope/my-stream`. 
  - In unqualified stream names are assumed to refer to the default scope as set in the `PravegaConfig`.

A stream may be specified in one of three ways:
1. As a string containing a qualified name, in the form `scope/stream`.
2. As a string containing an unqualified name, in the form `stream`.  Such streams are resolved to the default scope.
3. As an instance of `io.pravega.client.stream.Stream`, e.g. `Stream.of("my-scope", "my-stream")`.

### StreamCuts

A `StreamCut` represents a specific position in a Pravega stream, which may be obtained from various API interactions with the Pravega client. The [BatchClient](https://github.com/pravega/pravega/blob/master/client/src/main/java/io/pravega/client/batch/BatchClient.java) accepts a `StreamCut` as the start and/or end position of a given stream.  For further reading on StreamCuts, please refer to documentation on [StreamCut](https://github.com/pravega/pravega/blob/master/documentation/src/docs/streamcuts.md) and [sample code](https://github.com/pravega/pravega-samples/tree/v0.3.2/pravega-client-examples/src/main/java/io/pravega/example/streamcuts).

If unspecified, the default start position is the earliest available data. The default end position is all available data as of when job execution begins.
(can u rephrase??)
### Parallelism
`FlinkPravegaInputFormat` supports parallelization. Use the `setParallelism` method of `DataSet` to configure the number of parallel instances to execute.  The parallel instances consume the stream in a coordinated manner, each consuming one or more stream segments.

```java
final ExecutionEnvironment env = ExecutionEnvironment.getExecutionEnvironment();
         env.setParallelism(3);

```

**Note**: Coordination is achieved with the use of a Pravega reader group, which is based on a [State Synchronizer](http://pravega.io/docs/pravega-concepts/#state-synchronizers). The synchronizer creates a backing stream that may be manually deleted after the job finishes.

### Builder API Usage
A builder API is provided to construct an instance of `FlinkPravegaInputFormat`.  See the table below for a summary of builder properties.  Note that the builder accepts an instance of `PravegaConfig` for common configuration properties.  See the [configurations](configurations) page for more information.

|Method                |Description|
|----------------------|-----------------------------------------------------------------------|
|`withPravegaConfig`|The Pravega client configuration, which includes connection info, security info, and a default scope.|
|`forStream`|The stream to be read from, with optional start and/or end position.  May be called repeatedly to read numerous streams in parallel.|
|`withDeserializationSchema`|The deserialization schema which describes how to turn byte messages into events.|



## FlinkPravegaOutputFormat
A Pravega stream may be used as a data sink within a Flink batch program using an instance of 
```
io.pravega.connectors.flink.FlinkPravegaOutputFormat.

```
The `FlinkPravegaOutputFormat` can be supplied as a sink to the [`DataSet`](https://ci.apache.org/projects/flink/flink-docs-master/api/java/org/apache/flink/api/java/DataSet.html#output-org.apache.flink.api.common.io.OutputFormat-) (the basic abstraction of the Flink Batch API).

### Example

Pravega configuration can be defined using:
```
PravegaConfig config = PravegaConfig.fromParams(params);
```

Event serializer can be configured using:
```
SerializationSchema<EventType> serializer = ...
```

Event router for selecting the Routing Key canbe configured as follows:
```
PravegaEventRouter<EventType> router = ...
```
FlinkPravegaOutputFormat can be designed using:

```java
FlinkPravegaOutputFormat<EventType> outputFormat = FlinkPravegaOutputFormat.<EventType>builder()
    .forStream(...)
    .withPravegaConfig(config)
    .withSerializationSchema(serializer)
    .withEventRouter(router)
    .build();

ExecutionEnvironment env = ExecutionEnvironment.getExecutionEnvironment();
Collection<EventType> inputData = Arrays.asList(...);
env.fromCollection(inputData)
   .output(outputFormat);
env.execute("...");
```

### Builder API Usage
A builder API is provided to construct an instance of `FlinkPravegaOutputFormat`. See the table below for a summary of builder properties.  Note that the builder accepts an instance of `PravegaConfig` for common configuration properties.  See the [configurations](configurations.md) page for more information.

|Method                |Description|
|----------------------|-----------------------------------------------------------------------|
|`withPravegaConfig`|The Pravega client configuration, which includes connection info, security info, and a default scope.|
|`forStream`|The stream to be written to.|
|`withSerializationSchema`|The serialization schema which describes how to turn events into byte messages.|
|`withEventRouter`|The router function which determines the routing key for a given event.|

### Output Stream

Each stream in Pravega is contained by a scope.  A scope acts as a namespace for one or more streams.  The `FlinkPravegaReader` is able to read from numerous streams in parallel, even across scopes.  The builder API accepts both **qualified** and **unqualified** stream names.  
    - In qualified, the scope is explicitly specified, e.g. `my-scope/my-stream`.  
    - In Unqualified stream names are assumed to refer to the default scope as set in the `PravegaConfig`.

A stream may be specified in one of three ways:

 1. As a string containing a qualified name, in the form `scope/stream`.
 2. As a string containing an unqualified name, in the form `stream`.  Such streams are resolved to the default scope.
 3. As an instance of `io.pravega.client.stream.Stream`, e.g. `Stream.of("my-scope", "my-stream")`.


## Serialization
Please, see the [serialization](serialization) page for more information on how to use the serializer.

### Parallelism
FlinkPravegaWriter supports parallelization. For more information please revist the concept [Parallelism](#parallelism) discussed in the above section.

### Event Routing
Every event written to a Pravega stream has an associated Routing Key.  The Routing Key is the basis for event ordering.  See the [Pravega documentation](http://pravega.io/docs/pravega-concepts/#events) for details.

When constructing the FlinkPravegaWriter, to establish the Routing Key for each event, provide the below mentioned implementation:
```
io.pravega.connectors.flink.PravegaEventRouter` 
```

