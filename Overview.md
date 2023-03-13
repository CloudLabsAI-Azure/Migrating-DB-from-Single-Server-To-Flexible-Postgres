# Migrate Azure Database for Postgresql Single Server to Flexible Server using Flexible Migration Service (FMS)


## Introduction

The Single to Flexible server migration tool is designed to move databases from Azure Database for PostgreSQL Single server to Flexible server. We launched the preview version of the tool also known as the **Migration (Preview)** feature in Flexible server, back in August 2022 for customers to try out migrations. The preview version used an infrastructure based on Azure Database Migration Service (DMS). We have aggregated feedback from customers and have come up with an improved version of the migration tool which will use a new infrastructure to perform migrations in a more easy, robust and resilient way. This document will help you understand the functioning of the new version of the tool along with its limitations and the pre-requisites that need to be taken care of before using the tool.

Currently, the new version of the tool only supports **Offline** mode of migration. Offline mode presents a simple and hassle-free way to migrate your databases but might incur downtime to your server depending on the size of the databases and the way in which data is distributed across tables in your database.

## How does it work?

The new version of the migration tool is a hosted solution where we spin up a purpose-built docker container in the target Flexible server VM and drive the incoming migrations. This docker container will be spun up on-demand when a migration is initiated from a single server and will be decommissioned as soon as the migration is completed. The migration container will use a new binary called [**pgcopydb**](https://github.com/dimitri/pgcopydb) which provides a fast and efficient way of copying databases from one server to another. Though pgcopydb uses the traditional pg_dump and pg_restore for schema migration, it implements its own data migration mechanism which involves multi-process streaming parts from source to target. Also, pgcopydb bypasses pg_restore way of index building and drives that internally in a way that all indexes can be built concurrently. So, the data migration process is much quicker with pgcopydb. Following is the process diagram of the new version of the migration tool.

![Process diagram](https://raw.githubusercontent.com/CloudLabsAI-Azure/Migrating-DB-from-Single-Server-To-Flexible-Postgres/main/Images/ProcessDiagram.png "Process Diagram")

The following table shows the time for performing offline migrations for databases of various sizes using the single to flex migration tool. The migration was performed using a flexible server with the SKU – **Standard_D4ds_v4(4 cores, 16GB Memory, 128GB disk and 500 iops)**

| Database size | Approximate time taken (HH:MM:SS) |
|:---------------|:-------------|
| 1 GB | 00:01 |
| 5 GB | 00:03 |
| 10 GB | 00:08 |
| 50 GB | 00:35 |
| 100 GB | 01:00 |
| 500 GB | 04:00 |
| 1,000 GB | 07:00 |


Please note that these numbers are the average time taken to complete offline migration for a database with a given size with multiple different schemas and with varying data distribution.

From the above data, it is very clear that with a higher compute on Flexible server, the migration will complete faster. It is a good practice to create a flexible server with a higher SKU and complete the migrations from single server in a quick way. Post successful migrations, you can tune the SKU of your flexible server to meet the application requirements.

#### Migration prerequisites
The following pre-requisites need to be taken care of before using the Single to Flex Migration tool for migration

##### Network connectivity between Single and Flexible Server
The following table summarizes the possible network configuration on single and flexible server.

|Server Type| Public Access | Private Access |
|:---------------|:-------------|:-----------------|
| Single Server | Public access turned **ON** with firewall rules or VNet rules (service endpoints). | Public access turned OFF with private end points configured.|
| Flexible Server | Public access with firewall rules | Server deployed inside a VNet and delegated subnet.|

>[!NOTE]
> Currently private end points are not supported in Flexible server. It will be supported at a later point time.

The following table summarizes the list of networking scenarios supported by the Single to Flex migration tool.
  
|Single Server Config| Flexible Server Config | Supported by Migration tool |
|:---------------|:-------------|:-----------------|
| Public Access | Public Access | Yes|
| Public Access | Private Access | Yes|
| Private Access | Public Access | No|
| Private Access | Private Access | Yes|

**Steps needed to establish connectivity between your Single and Flexible Server**
* If your single server is public access (case #1 and case #2 in the above table), there's nothing needed from your end. The single to flex migration tool automatically establishes connection between single and flexible server and the migration will go through.

* If your single server is in private access, then the only supported scenario is when your Flexible server is inside a VNet. If your flexible server is deployed in the same VNet as the private end point of your Single server, connections between single server and flexible server should automatically work provided there is no network security group(NSGs) blocking the connectivity between subnets. If flexible server is deployed in another VNet, [peering should be established between the VNets](../../virtual-network/tutorial-connect-virtual-networks-portal.md) for the connection to work between Single and Flexible server.  
