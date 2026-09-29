# Architecture

## Target data path

```text
                    Transactional source
                       PostgreSQL
                           |
                 incremental extraction
                           |
                         Python
                 control + validation
                           |
                           v
                    +-------------+
 SQL / CLI -------->|    Trino    |
                    +------+------+ 
                           |
              Iceberg connector / REST
                    +------+------+
                    | REST Catalog|
                    +------+------+
                           |
                    catalog state
                           |
                           v
                       PostgreSQL
                  (catalog persistence)

Trino ---------------- S3 API ----------------> MinIO
REST Catalog ---------- S3 API --------------> MinIO

MinIO warehouse:
  metadata/*.metadata.json
  metadata/*manifest*
  data/*.parquet
```

## Responsibilities

**MinIO** stores bytes. It does not provide Iceberg table semantics.

**Iceberg** defines table metadata, snapshots, manifests, schema evolution, partition evolution, and atomic table commits.

**The REST catalog** maps logical table names to Iceberg metadata and coordinates catalog-level table operations.

**Trino** executes SQL and reads/writes Iceberg tables through its Iceberg connector.

**PostgreSQL source** gives the lab a realistic mutable OLTP source for incremental ingestion exercises.

**Python** is used for orchestration and ingestion exercises where writing the control logic is itself part of the lesson.

## Why no Spark?

Spark is not required to learn Iceberg. Trino provides the SQL compute layer for this lab, keeping the platform small enough to understand deeply.

## Learning simplification vs production

The first implementation will run on one machine with Docker Compose and a single Trino node. That is intentional.

A production design would separately evaluate object-store durability, catalog HA, Trino coordinator/worker topology, authentication and authorization, TLS, secrets, monitoring, backups, resource isolation, and upgrade strategy.
