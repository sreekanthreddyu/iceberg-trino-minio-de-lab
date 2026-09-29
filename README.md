# Iceberg + Trino + MinIO Data Engineering Lab

A hands-on home-lab for learning open lakehouse engineering without Apache Spark.

## Goal

Build, operate, break, recover, and explain a small data platform based on:

- **Apache Iceberg** — open table format: snapshots, manifests, schema/partition evolution, time travel.
- **Trino** — distributed SQL query and compute engine.
- **MinIO** — S3-compatible object storage for Iceberg data and metadata files.
- **Iceberg REST Catalog** — catalog service used by Trino to discover and commit Iceberg tables.
- **PostgreSQL** — transactional source system and catalog persistence where required by the selected catalog implementation.
- **Python** — source-data generation, ingestion helpers, validation, and automation.
- **Docker Compose** — reproducible local orchestration.
- **GitHub Actions** — validation and later CI exercises.

> Apache Spark is intentionally not part of this project.

## Learning method

Every major feature follows:

**Understand → Deploy → Run → Modify → Break → Recover → Explain**

The repository is a learning lab, not a prebuilt application to run once.

## Architecture

See [docs/architecture.md](docs/architecture.md).

```text
                     +--------------------+
                     |  PostgreSQL source |
                     +----------+---------+
                                |
                       Python ingestion
                                |
                                v
+----------------+      +-------+--------+       +----------------+
| Iceberg REST   |<---->|     Trino      |<----->| SQL / CLI / BI |
| Catalog        |      | query + writes |       | learning client|
+-------+--------+      +-------+--------+       +----------------+
        |                       |
        | metadata/catalog      | S3 API
        v                       v
+------------------------------------------------+
|                    MinIO                       |
| Iceberg metadata + manifests + Parquet files  |
+------------------------------------------------+
```

## Learning sequence

1. Architecture and repository
2. Docker and service networking
3. MinIO and object storage
4. Trino fundamentals
5. Iceberg architecture and catalogs
6. Create and query Iceberg tables
7. Parquet vs Iceberg
8. Snapshots and time travel
9. Schema evolution
10. Partition evolution and hidden partitioning
11. Inserts, updates, deletes and MERGE
12. Incremental ingestion and idempotency
13. Metadata tables and troubleshooting
14. Small files, compaction and optimization
15. Expiring snapshots and orphan-file cleanup
16. Data quality and quarantine
17. Observability
18. Security and secrets
19. Failure engineering
20. CI/CD and production trade-offs

## Repository map

```text
.
├── README.md
├── docs/
│   ├── GUIDE.md
│   └── architecture.md
├── docker/
│   └── README.md
├── trino/
│   └── catalog/
├── sql/
│   ├── source/
│   └── iceberg/
├── python/
│   └── README.md
├── labs/
│   └── README.md
├── tests/
│   └── README.md
└── .github/workflows/
    └── validate.yml
```

## Important design rule

We will add infrastructure incrementally during the labs rather than hide the learning behind a finished stack. The guide distinguishes:

1. what the repository currently implements;
2. what is intentionally simplified for learning;
3. what a production platform would require.

Start with [docs/GUIDE.md](docs/GUIDE.md).
