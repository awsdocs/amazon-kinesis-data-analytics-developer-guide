# Modifiable Flink configuration properties<a name="reference-modifiable-settings"></a>

Following are Flink configuration settings you can modify using a [support case](https://console.aws.amazon.com/support/home#/)\. You can modify more than one property at a time, and for multiple applications at the same time by specifying the application prefix\. If there are other Flink configuration properties outside this list you want to modify, please specify the exact property in your case\. 

## Fault Tolerance<a name="reference-modifiable-settings-fault-tolerance"></a>

`restart-strategy:`

`restart-strategy.fixed-delay.delay:`

## Checkpoints and State Backends<a name="reference-modifiable-settings-checkpoints-state-backends"></a>

`state.backend:`

`state.backend.fs.memory-threshold:`

`state.backend.incremental:`

## RocksDB Native Metrics<a name="reference-modifiable-settings-rocksdb"></a>

`state.backend.rocksdb.compaction.style:`

`state.backend.rocksdb.memory.partitioned-index-filters:`

`state.backend.rocksdb.metrics.actual-delayed-write-rate:`

`state.backend.rocksdb.metrics.background-errors:`

`state.backend.rocksdb.metrics.block-cache-capacity:`

`state.backend.rocksdb.metrics.block-cache-pinned-usage:`

`state.backend.rocksdb.metrics.block-cache-usage:`

`state.backend.rocksdb.metrics.column-family-as-variable:`

`state.backend.rocksdb.metrics.compaction-pending:`

`state.backend.rocksdb.metrics.cur-size-active-mem-table:`

`state.backend.rocksdb.metrics.cur-size-all-mem-tables:`

`state.backend.rocksdb.metrics.estimate-live-data-size:`

`state.backend.rocksdb.metrics.estimate-num-keys:`

`state.backend.rocksdb.metrics.estimate-pending-compaction-bytes:`

`state.backend.rocksdb.metrics.estimate-table-readers-mem:`

`state.backend.rocksdb.metrics.is-write-stopped:`

`state.backend.rocksdb.metrics.mem-table-flush-pending:`

`state.backend.rocksdb.metrics.num-deletes-active-mem-table:`

`state.backend.rocksdb.metrics.num-deletes-imm-mem-tables:`

`state.backend.rocksdb.metrics.num-entries-active-mem-table:`

`state.backend.rocksdb.metrics.num-entries-imm-mem-tables:`

`state.backend.rocksdb.metrics.num-immutable-mem-table:`

`state.backend.rocksdb.metrics.num-live-versions:`

`state.backend.rocksdb.metrics.num-running-compactions:`

`state.backend.rocksdb.metrics.num-running-flushes:`

`state.backend.rocksdb.metrics.num-snapshots:`

`state.backend.rocksdb.metrics.size-all-mem-tables:`

`state.backend.rocksdb.thread.num:`

## Advanced State Backends Options<a name="reference-modifiable-settings-advanced-state-backends-options"></a>

`state.storage.fs.memory-threshold:`

## Full TaskManager Options<a name="reference-modifiable-settings-full-task-manager-options"></a>

`task.cancellation.timeout:`

`taskmanager.jvm-exit-on-oom:`

`taskmanager.numberOfTaskSlots:`

`taskmanager.slot.timeout:`

`taskmanager.network.memory.fraction:`

`taskmanager.network.memory.max:`

`taskmanager.network.request-backoff.initial:`

`taskmanager.network.request-backoff.max:`

## Memory Configuration<a name="reference-modifiable-settings-memory-configuration"></a>

`taskmanager.heap.size:`

`taskmanager.memory.jvm-metaspace.size:`

`taskmanager.memory.jvm-overhead.fraction:`

`taskmanager.memory.jvm-overhead.max:`

`taskmanager.memory.managed.consumer-weights:`

`taskmanager.memory.managed.fraction:`

`taskmanager.memory.network.fraction:`

`taskmanager.memory.network.max:`

`taskmanager.memory.segment-size:`

`taskmanager.memory.task.off-heap.size:`

## RPC / Akka<a name="reference-modifiable-settings-RPC-Akka"></a>

`akka.ask.timeout:`

`akka.client.timeout:`

`akka.framesize:`

`akka.lookup.timeout:`

`akka.tcp.timeout:`

## Client<a name="reference-modifiable-settings-client"></a>

`client.timeout:`

## Advanced Cluster Options<a name="reference-modifiable-settings-advanced-cluster-options"></a>

`cluster.intercept-user-system-exit:`

`cluster.processes.halt-on-fatal-error:`

## Filesystem Configurations<a name="reference-modifiable-settings-advanced-filesystem-configurations"></a>

`fs.s3.connection.maximum:`

`fs.s3a.connection.maximum:`

`fs.s3a.threads.max:`

`s3.upload.max.concurrent.uploads:`

## Advanced Fault Tolerance Options<a name="reference-modifiable-settings-advanced-fault-tolerance-options"></a>

`heartbeat.timeout:`

`jobmanager.execution.failover-strategy:`

## Memory configuration<a name="reference-modifiable-settings-memory-configuration"></a>

`jobmanager.memory.heap.size:`

## Metrics<a name="reference-modifiable-settings-metrics"></a>

`metrics.latency.interval:`

## Advanced Options for the REST endpoint and Client<a name="reference-modifiable-settings-rest"></a>

`rest.flamegraph.enabled:`

`rest.server.numThreads:`

## Advanced SSL Security Options<a name="reference-modifiable-settings-ssl"></a>

`security.ssl.internal.handshake-timeout:`

## Advanced Scheduling Options<a name="reference-modifiable-settings-scheduling"></a>

`slot.request.timeout:`

## Advanced Options for Flink Web UI<a name="reference-modifiable-settings-webui"></a>

`web.timeout:`