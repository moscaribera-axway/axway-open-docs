---
title: Apache Cassandra backup and restore
linkTitle: Backup and restore
weight: 5
date: 2019-06-05T00:00:00.000Z
description: |
  Learn how to back up and restore Cassandra.
---
In an Apache Cassandra database cluster, data is replicated between multiple nodes and potentially between multiple datacenters. Cassandra is highly fault-tolerant, and depending on the size of the cluster, it can survive the failure of one or more nodes without any interruption in service. However, backups are still needed to recover from the following scenarios:

* Any errors made in data by client applications.
* Accidental deletions.
* Catastrophic failure that requires a rebuild of the entire cluster.
* Rollback of the cluster to a known good state in the event of data corruption.

This topic describes which Apache Cassandra data keyspaces to back up, and explains how to use scripts to create and restore the data in those keyspaces. It also describes which Apache Cassandra configuration and API Gateway configuration to back up.

## Before you start

You must read the following items carefully before you perform any of the instructions describe on this page:

* The instructions on this page:
    * Are intended to back up API Management keyspaces only.
    * Apply only to an Apache Cassandra cluster where the replication factor is the same as the cluster size, which means that each node contains 100% of the data. This is the standard configuration for Axway API Management processes.
    * Use the Cassandra snapshot functionality. A *snapshot* is a set of hard links for the current data files in a keyspace. While the snapshot does not take up noticeable diskspace, it will cause stale data files not to be cleaned up. This is because a snapshot directory is created under each table directory and will contain hard links to all table data files. When Cassandra cleans up the stale table data files, the snapshot files will remain. Therefore, it is critical to remove them promptly. The example backup script takes care of this by deleting the snapshot files at the end of the backup.
* The instructions and scripts are intended as a starting point and must be customized and automated as needed to match your backup polices and environment.
* Because 100% of the data is stored on each node, you must run the backup procedure on a single node only, preferably on the seed node.
* For safety reasons, the backup location should *not* be on the same disk as the Cassandra data directory, and it must have enough free space to contain the keyspace.
* If using [Apache Cassandra 4.0.7](/docs/apim_installation/apigw_upgrade/upgrade_cassanda/upgrade_cassandra_v4/), you must upgrade your API Gateway to the [February 2023](/docs/apim_relnotes/20230228_apimgr_relnotes/) release, or later, before backing up the data. This is because the `apigw-backup-tool` has been upgraded to support Apache Cassandra 4.0.7 in this release.

## Which data keyspaces to back up?

These procedures apply to data in API Management and KPS keyspaces only.

You must first obtain a list of the keyspace names to back up. API Management keyspaces may have a custom name defined, but they are named in the format of `<UUID>_group_[n]` by default. For example:

```none
x9fa003e2_d975_4a4a_a27e_280ab7fd8a5_group_2p_2
```

All Cassandra internal keyspaces begin with `system`, and should not be backed up using this process. You can do this using `cqlsh` or `kpsadmin` commands:

### Find keyspaces using cqlsh

Using `cqlsh`, execute the following command:

```none
SELECT * from system.schema_keyspaces;
```

In the following example, `xxx_group_2` and `xxx_group_3` are API Management keyspaces:

![API Management keyspaces](/Images/CassandraAdminGuide/cqlsh_keyspace.png)

### Find keyspaces using kpsadmin

Using `kpsadmin`, choose: `option 30) Show Configuration`, and enter the API Gateway group and any instance to back up, or use the command line, as shown in the following example:

![Find keyspaces using kpsadmin](/Images/CassandraAdminGuide/kpsadmin_keyspace.png)

## Back up or restore Cassandra using the backup tool

The API Gateway backup tool is located in `install_dir/apigateway/tools/apigw-backup-tool`. To get started, you must copy the `apigw-backup-tool` folder to your Cassandra seed node.

### Update your configuration file

Update the `/conf/apigw-backup-tool.ini` file to configure your backup.

The following table describes the parameters in the `apigw-backup-tool.ini` file:

| Configuration Name         | Description                                                                 |
| -------------------------- | --------------------------------------------------------------------------- |
| cassandra_data_dir         | Directory which stores the Cassandra data, usually `cassandra/data/data`                 |
| cassandra_commitlog_dir    | Directory which stores Cassandra commit logs, usually `cassandra/data/commitlog`       |
| cassandra_saved_caches_dir | Directory which stores Cassandra saved cache, usually `cassandra/data/saved_caches` |
| cqlsh_bin                  | Path to the `cqlsh` utility, usually `cassandra/bin/cqlsh`                |
| nodetool_bin               | Path to the `nodetool` utility, usually `cassandra/bin/nodetool`          |
| cqlsh_ip                   | IP address used by the `cqlsh`                                          |
| cqlsh_request_timeout      | CQL request timeout in seconds                                          |
| cqlsh_ssl_enabled          | Set to `true` if the `cqlsh` utility is configured for SSL           |
| cql_username               | Username used by the `cqlsh` (optional)                 |
| cql_password               | Password used by the `cqlsh` (optional)                 |
| nodetool_username          | Username used by the `nodetool` (optional)              |
| nodetool_password          | Password used by the `nodetool` (optional)              |
| nodetool_ssl_enabled       | Set to `true` if the `nodetool` utility is configured for SSL        |
| backup_root_dir            | Path to the backup directory                                            |
| printcmd                   | Set to `true` to see the commands ran by the tool during the process          |
| debug                      | Set to `true` to see debug logs                                               |

{{< alert title="Note" color="primary" >}}`cql_username`, `cql_password`, `nodetool_username`, and `nodetool_password` are optional parameters. If they are not provided in the `apigw-backup-tool.ini` file, then you will be prompted for them during command execution. {{< /alert >}}

After the configuration is set and Cassandra is running, run the following command to validate the configuration:

```none
apigw-backup-tool validateConfig
```

### Back up your Cassandra keyspace

While Cassandra is running, run the following command to create a backup in the `backup_root_dir` folder:

```none
apigw-backup-tool backup -k <keyspace name> -s <snapshot name>
```

After the backup is complete, it can be found in the following structure:

```none
  <BACKUP_ROOT_DIR>
  ├── <BACKUP_SNAPSHOT_NAME>
  │ ├── <SNAPSHOT_NAME>
  │ │ ├── <TABLE_NAME>
  │ │ │  ├── <SNAPSHOT_FILES>
  │ │ ├── <TABLE_NAME>
  │ │ │  ├── <SNAPSHOT FILES>
  │ │ │...
```

Use your company's archive method to archive the `BACKUP_SNAPSHOT_NAME` directory, so you can restore it later if needed.

### Restore the keyspace backup

If you are restoring the keyspace in a *new* Cassandra cluster, you must ensure the following before you start this task:

* The backup snapshot must only contain data from the tables and columns in the keyspace schema.
* The Cassandra cluster must be created in [a highly available Cassandra cluster](/docs/cass_admin/cassandra_config/).
* All API Gateway groups must have their schema created in the new cluster, and the replication factor must be the same as the cluster size (normally, `3`).

To restore a keyspace:

1. Shut down all API Gateway instances and any other clients in the Cassandra cluster.
2. Run the following command:

    ```none
    apigw-backup-tool restore -k <keyspace name> -s <snapshot name>
    ```

    * The tool will perform several tasks to restore the cluster. The process can take time, depending on the amount of data in the backup.
    * The tool will prompt the user to perform manual actions on other nodes in the cluster

{{< alert title="Note" color="primary" >}}You must read the manual actions carefully. Some actions need to be performed on every node of the cluster, others only on the other nodes (not on the one where the script is running).{{< /alert >}}

### Back up and restore a cluster with multiple nodes

To back up and restore a 3-node cluster into a *new* 3-node cluster.

1. To create the backup, copy the `apigw-backup-tool` folder to the seed node of both the old and the new cluster.
2. Configure the `apigw-backup-tool` in both the old and the new clusters, then verify that the configuration is correct:

    ```none
    apigw-backup-tool validateConfig
    ```

3. Run the backup script on the old cluster:

    ```none
    apigw-backup-tool backup -k <keyspace name> -s <snapshot name>
    ```

4. Copy the backup folder to the new cluster. The backup must be copied to the `backup_root_dir` directory that is configured in new cluster.
5. In the new cluster, run the restore script:

    ```none
    apigw-backup-tool restore -k <keyspace name> -s <snapshot name>
    ```

{{< alert title="Note" color="primary" >}}The actions of starting and stopping Cassandra are not handled by the script, which mean you will need to perform them manually when the script ask you to.{{< /alert >}}

To restore a backup of multiple nodes taken in the same cluster, ensure the backup is in `backup_root_dir`, and run the restore command.

```none
apigw-backup-tool restore -k <keyspace name> -s <snapshot name>
```

If data exists in the cluster, the script will prompt you to remove it and start fresh. If you decide not to remove the keyspace, the script will abort.

## Which configuration to back up?

In addition to backing up your data in Apache Cassandra keyspaces, you must also back up your Apache Cassandra configuration and API Gateway configuration.

### Apache Cassandra configuration

You must back up the `CASSANDRA_HOME/conf` directory on all nodes.

### API Gateway group configuration

You must back up the API Gateway group configuration in the following directory:

```none
API_GW_INSTALL_DIR/apigateway/groups/<group-name>/conf
```

This directory contains the API Gateway, API Manager, and KPS configuration data.

## Migrating data between environments

Encrypted KPS data cannot be transferred directly between environments, so to migrate data between environments you must [re-encrypt the KPS data](/docs/apim_policydev/apigw_kps/how_to_use_kpsadmin_command#re-encrypt-the-kps-data).
