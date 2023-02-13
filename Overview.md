# Migrate Azure Database for Postgresql Single Server to Flexible Server using Flexible Migration Service (FMS)


## Introduction

The Single to Flexible server migration tool is designed to move databases from Azure Database for PostgreSQL Single server to Flexible server. We launched the preview version of the tool also known as the **Migration (Preview)** feature in Flexible server, back in August 2022 for customers to try out migrations. The preview version used an infrastructure based on Azure Database Migration Service (DMS). We have aggregated feedback from customers and have come up with an improved version of the migration tool which will use a new infrastructure to perform migrations in a more easy, robust and resilient way. This document will help you understand the functioning of the new version of the tool along with its limitations and the pre-requisites that need to be taken care of before using the tool.

Currently, the new version of the tool only supports **Offline** mode of migration. Offline mode presents a simple and hassle-free way to migrate your databases but might incur downtime to your server depending on the size of the databases and the way in which data is distributed across tables in your database.

## How does it work?

The new version of the migration tool is a hosted solution where we spin up a purpose-built docker container in the target Flexible server VM and drive the incoming migrations. This docker container will be spun up on-demand when a migration is initiated from a single server and will be decommissioned as soon as the migration is completed. The migration container will use a new binary called [**pgcopydb**](https://github.com/dimitri/pgcopydb) which provides a fast and efficient way of copying databases from one server to another. Though pgcopydb uses the traditional pg_dump and pg_restore for schema migration, it implements its own data migration mechanism which involves multi-process streaming parts from source to target. Also, pgcopydb bypasses pg_restore way of index building and drives that internally in a way that all indexes can be built concurrently. So, the data migration process is much quicker with pgcopydb. Following is the process diagram of the new version of the migration tool.

![Process diagram](./images/ProcessDiagram.png "Process Diagram")

The following table shows the approximate time for performing offline migrations for databases of various sizes using the new version of the tool. Note that the migration was performed on the **General-Purpose Flexible server with Standard_D4s_v3 SKU (4VCore), 4TB Storage and 6400 IOPs.**

| Database size | Approximate time taken (HH:MM:SS) |
|:---------------|:-------------|
| 1 GB | 00:01:00 |
| 5 GB | 00:03:00 |
| 10 GB | 00:08:00 |
| 50 GB | 01:45:00 |
| 100 GB | 06:00:00 |
| 500 GB | 08:00:00 |
| 1,000 GB | 09:30:00 |

The below table has data on the time taken to perform offline migration with **General-Purpose flexible server with Standard_D8s_v3 SKU (8VCore), 4TB storage and 7500 IOPs.**

| Database size | Approximate time taken (HH:MM:SS) |
|:---------------|:-------------|
| 1 GB | 00:00:30 |
| 5 GB | 00:02:00 |
| 10 GB | 00:04:00 |
| 50 GB | 01:15:00 |
| 100 GB | 05:00:00 |
| 500 GB | 06:00:00 |
| 1,000 GB | 07:30:00 |

Please note that these numbers are the average time taken to complete offline migration for a database with a given size with multiple different schemas and with varying data distribution.

From the above data, it is very clear that with a higher compute on Flexible server, the migration will complete faster. It is a good practice to create a flexible server with a higher SKU and complete the migrations from single server in a quick way. Post successful migrations, you can tune the SKU of your flexible server to meet the application requirements.
