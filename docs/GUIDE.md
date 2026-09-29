# Learning Guide

## How to use this lab

Do not treat the repository as an application that you deploy once. For each phase:

1. **Understand** the component and the problem it solves.
2. **Deploy** the smallest useful version.
3. **Run** a real data path through it.
4. **Modify** configuration or behavior yourself.
5. **Break** it in a controlled way.
6. **Recover** without resetting everything.
7. **Explain** the design, failure mode, and production trade-offs.

## Phase 1 — Architecture and Docker

Learn containers, images, volumes, networks, ports, health checks, service discovery, and persistent state.

**Build:** start PostgreSQL and MinIO first. Add Trino only after you can explain their state and network paths.

**Break:** stop containers, remove a container without removing its volume, then compare that with deleting a volume.

**Explain:** what state survives and why?

## Phase 2 — MinIO and object storage

Learn buckets, objects, prefixes, S3-compatible APIs, credentials, persistence, and why object storage is different from a filesystem.

**Build:** create the warehouse bucket and inspect objects directly.

**Break:** use bad credentials and an incorrect endpoint.

**Recover:** identify whether the failure is DNS/networking, authentication, authorization, or missing data.

## Phase 3 — Trino

Learn coordinator/workers, catalogs, connectors, schemas, query planning, splits, and predicate/projection pushdown.

Begin with a single-node Trino deployment. A cluster is a later exercise.

**Build:** query PostgreSQL through Trino before adding Iceberg.

**Break:** misconfigure a catalog and diagnose Trino logs.

## Phase 4 — Iceberg fundamentals

Before creating a table, be able to distinguish:

- catalog
- table metadata JSON
- manifest list
- manifest
- data file
- delete file
- snapshot

Then connect Trino to an Iceberg REST catalog backed by the MinIO warehouse.

**Build:** create an Iceberg table and insert a small dataset.

**Inspect:** query the table through Trino and inspect the objects created in MinIO.

**Explain:** why is a directory full of Parquet files not automatically an Iceberg table?

## Phase 5 — Snapshots and time travel

Insert, update, and delete rows. Inspect Iceberg metadata tables and snapshots.

**Break:** make a logically bad change, then use snapshot history to investigate it.

Do not confuse time travel with backup and disaster recovery.

## Phase 6 — Schema and partition evolution

Add/rename columns and evolve partition strategy without rewriting the lab from scratch.

Compare Iceberg hidden partitioning with directory-partition conventions.

## Phase 7 — Incremental ingestion

Use PostgreSQL as the source and Python as the ingestion/control layer.

Implement a watermark-based load yourself. Requirements:

- fixed lower and upper bounds per run;
- audit record per run;
- retry-safe writes;
- watermark advances only after successful completion.

**Failure lab:** fail after writing data but before advancing the watermark. Determine whether retrying can duplicate or corrupt data.

## Phase 8 — MERGE and idempotency

Implement upsert behavior with Trino/Iceberg.

Create duplicate source records deliberately. Define the source-of-truth key and deterministic deduplication rule before writing the MERGE.

## Phase 9 — Data quality and quarantine

Introduce invalid values and malformed source records.

Separate validation outcomes from transport failures. Preserve enough context to replay quarantined records.

## Phase 10 — Maintenance

Generate small files intentionally. Measure the effect before optimizing.

Learn file compaction, metadata growth, snapshot expiration, and orphan-file cleanup. Treat destructive maintenance as an operational procedure.

## Phase 11 — Observability

Add structured run logging and collect:

- run ID
- source bounds
- rows read/written/rejected
- duration
- final state
- error category

Trace one ingestion run from source to Iceberg snapshot.

## Phase 12 — Security

Remove hard-coded credentials. Apply least privilege and separate service identities where practical.

Discuss TLS, secret management, network boundaries, encryption, and multi-user authorization as production extensions.

## Phase 13 — CI/CD

Validate configuration and Python first. Later add integration tests that boot an ephemeral stack.

A pull request should prove the proposed change before deployment rather than merely lint files.

## Phase 14 — Failure engineering capstone

Inject these failures one at a time:

- MinIO unavailable
- catalog unavailable
- Trino unavailable
- PostgreSQL unavailable
- invalid credentials
- duplicate source records
- failed MERGE
- ingestion crash before watermark update
- ingestion crash after data commit
- invalid schema evolution
- orphaned files

For each answer:

1. What failed?
2. Why?
3. What state changed?
4. Is retry safe?
5. Can duplicates occur?
6. How do we recover?
7. How do we prevent recurrence?

## Production gap

The home lab favors visibility and low resource usage. Production would require decisions around high availability, distributed Trino workers, durable catalog deployment, TLS, centralized identity/secrets, authorization, monitoring, backup/DR, object-store durability, capacity planning, upgrades, and tested operational runbooks.
