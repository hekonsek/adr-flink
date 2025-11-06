# Use Java as a language for Apache Flink jobs

## Context

We are standardizing the implementation language for Flink stream/batch jobs across the platform. We require:

* First-class API coverage across Flink releases (including newer 2.x APIs).
* Predictable performance and memory behavior in long-running jobs.
* Rich connector and serialization ecosystems (Kafka, Debezium, Avro/Protobuf, Iceberg).
* Straightforward operability (debugging, profiling, CI/CD, dependency hygiene).
* Simplified dependency management.

## Decision

Adopt **Java** as the primary language for all production Apache Flink jobs. Non-Java usage (e.g., SQL-only pipelines) may exist but Java is the default for custom logic.

## Consequences

**Pros:**

* **API maturity & compatibility:** Flink’s core APIs, connectors, and runtime features land first and most completely in Java; minimal gaps across versions.
* **Performance & observability:** Mature JIT/GC, flamegraphs, JFR, async profiling, and battle-tested memory tuning for long-running jobs.
* **Ecosystem depth:** Broad library support (Avro/Protobuf, Debezium, Iceberg), Testcontainers, flink-test-utils, and reliable shaded builds.
* **Tooling & DX:** Excellent IDE support, refactoring, static analysis, reproducible builds, and widespread team familiarity.
* **Type safety:** Strong compile-time guarantees for complex event schemas.

**Cons:**

* **Verbosity:** More boilerplate than Scala/Kotlin; mitigated with records, Lombok (selectively), and helper DSLs.
* **Skill focus:** Hiring and training centered on Java; Python-only analysts may prefer SQL/PyFlink for ad-hoc tasks.

## Alternatives considered

1. **Scala**

   * **Pros:** Concise syntax, powerful FP patterns common in streaming.
   * **Cons:** Added complexity with Scala/Flink binary version alignment, smaller hiring pool, slower upgrade cadence for some libs; mixed IDE experience at scale.

2. **Python (PyFlink)**

   * **Pros:** Lower barrier for data scientists; quick UDF prototyping.
   * **Cons:** Higher latency/overhead for heavy UDFs, fewer connectors/features at parity with Java, packaging/runtime quirks in production clusters.

3. **Kotlin**

   * **Pros:** Concise, null-safety, good Java interop.
   * **Cons:** Thinner community and samples around Flink; occasional reflection/serialization surprises; limited advantages over modern Java for our use cases.

4. **Flink SQL as primary**

   * **Pros:** Fast delivery for declarative transformations; good for simple ETL.
   * **Cons:** Complex stateful logic, custom sinks, and non-trivial orchestration still require host language; testing and versioning of large SQL sets is harder.

5. **Go/others**

   * **Pros:** N/A in Flink runtime (no first-class support).
   * **Cons:** Not supported for operators; would require JVM bridges, undermining benefits.
