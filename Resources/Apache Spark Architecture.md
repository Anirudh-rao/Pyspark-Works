# Spark Architecture:

Apaches sparks performance advantages comes form its unique distributed architecture. Unlike traditional single-node architecture,sparh uses **Driver-exector architecture** where one central coordinator (the driver) manages many distributed workers(executors).

Apache Spark’s performance advantage comes from its distributed execution model. But the same architecture that makes Spark fast is also the root cause of common production issues like OutOfMemory errors, skewed jobs, and long shuffles.


## The Driver Executor Model:

At a high level every spark applciation follows a distributed architecture consisting of two main pieces that communicate with each other.

1. **Driver Program** (The Coordinator/Controller)
2. **Worker Nodes** (Compute resources)

They are connected by **Cluster Manager** which allocates resources.

|Component| Role.                    |Key responsibilities|
|---------|--------------------------|--------------------|
|Driver   | Central Coordinator.     |Converts your code to execution plan, scheules tasks and collects results|
|Cluster Manager|Resource manager|Allocates CPU/Memory , launches executors , monitors cluster health|
|Executors| Distributed Workers| Runs Tasks in Parallet , cache data partitions , report status to driver|

Lets go over these components in detail:

1. **Driver(Master Node)**:
  The driver is the process where you `main()` method runs. It is the control center of your application.

  - Reposibilities
    1. Converts your user code into tasks
    2. Creates the **SparkSession** , the entry point to the cluster.
    3. Constructs a **DAG(Directed Acyclic Graph)** of the Job execution.
    4. Schedules tasks on Executors and monitors there progress.

2. **Cluster Manager**
  The Cluster Manager is an external service that allocates resources across the cluster and manages the lifecyle of Executors. It acts as the intermediary between the Driver and the worker nodes, ensuring efficient resource distribution across applications.

  Spark is agnostic to the underlying cluster manager. It can run on:

  1. Standalone: Spark's simple built-in manager.
  2. YARN: Hadoop's resource manager (common in big data).
  3. Kubernetes: For containerized deployments.
  4. Mesos: An older general-purpose cluster manager.

3. **The Executors(Worker nodes)**:
Executors are distrbuted agents responsible for two things:
**Executing Code** and **Storing data**.

-  Responsibilites:
    1. Run the tasks assigned by the Driver.
    2. Return results to the Driver.
    3. Provide in-memory storage for cached RDDs/DataFrames.
    4. Each application gets its own set of executor processes.



## Driver & Excutor Configuration

Configuring the driver and executor correctly is critical first step in Spark performance tuning. These Properties establish the baseling resources (memory and CPU) available for your job to execute efficiently. Setting them correctly is essential to prevent **OutOfMemory** errors and ensure your cluster is utilized effectively.

### Driver Properties
The Driver needs enough memory to store the DAG, task metadata, and any results collected back to it (e.g., via `.collect()`).

|Property|	Default|	Description|
|-------|---------|-------------|
|`spark.driver.memory`|	1g|Amount of memory to use for the driver process. If your job collects large results, increase this.|
|`spark.driver.cores`|1	|Number of cores to use for the driver process (only in cluster mode). For production jobs, set it to 2-4 (default is 1). This is cheap insurance against stability issues.|

### Executor Properties
Executors do the heavy lifting. Their configuration balances parallelism (cores) against memory capacity.


