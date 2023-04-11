{
"title": "Upgrade Apache Cassandra to 4.0.7",
  "linkTitle": "Upgrade Apache Cassandra to 4.0.7",
  "weight": 20,
  "date": "2019-10-07",
  "description": "Upgrade your Cassandra environment from version 3.11.11 to version 4.0.7."
}

You can only upgrade to Cassandra version `4.0.7` from version `3.11.11`. If your version is earlier than `3.11.11`, see [Upgrade Apache Cassandra to 3.11.11](/docs/apim_installation/apigw_upgrade/upgrade_cassanda/upgrade_cassandra_v3/).

## Before you start

* You must upgrade your API Gateway to the [February 2023](/docs/apim_relnotes/20230228_apimgr_relnotes/index.html) release, or later, prior to upgrading your Cassandra environment to `4.0.7`.
* When upgrading either a cluster on a single-datacenter or a multi-datacenter setup, you must avoid any schema changes until the entire cluster has been upgraded to the same version.
* In multi-datacenter clusters:

    * Upgrade and restart each node, one at a time, in one datacenter before upgrading another datacenter.
    * Nodes in the cluster continue to operate at the earlier version until all nodes are upgraded.
* It is recommended that you run `nodetool repair -pr` on each node in your existing Cassandra environment prior to the upgrade.
* Running `nodetool repair` on a Cassandra node affects performance on a system running live traffic. We recommend that you perform the Cassandra upgrade in the evening or during a maintenance window when the load is minimal.

## Upgrade Cassandra in a multi-node single datacenter

You must upgrade each node in your Cassandra cluster individually before moving to the next node in the cluster.

### Download your Cassandra 4.0.7 installation

* Download [Cassandra 4.0.7](https://archive.apache.org/dist/cassandra/4.0.7/).
* Unzip the downloaded package, and copy the installation directory to the target Cassandra server node in an appropriate directory, for example, `/home/cassandra-407/`.

### Backup your old Cassandra data

Perform the backup of your 3.11.11 data.

1. Use the `cqlsh` command to find your Cassandra keyspaces to back up.

   ```
   SELECT * from system_schema.keyspaces;
   ```

   In the following example, `xxx_group_2` is an API Management keyspace:

   ```
   keyspace_name                                  | durable_writes | replication
   -----------------------------------------------+----------------+-------------------------------------------------------------------------------------------
   system_auth                                    |           True | {'class': 'org.apache.cassandra.locator.NetworkTopologyStrategy', 'dc1': '3', 'dc2': '3'}
   xb9076241_7cf8_4963_a3ac_375c53f21b7d_group_2  |           True | {'class': 'org.apache.cassandra.locator.NetworkTopologyStrategy', 'dc1': '3', 'dc2': '3'}
   system_schema                                  |           True | {'class': 'org.apache.cassandra.locator.LocalStrategy'}
   system_distributed                             |           True | {'class': 'org.apache.cassandra.locator.SimpleStrategy', 'replication_factor': '3'}
   system                                         |           True | {'class': 'org.apache.cassandra.locator.LocalStrategy'}
   system_traces                                  |           True | {'class': 'org.apache.cassandra.locator.SimpleStrategy', 'replication_factor': '2'}
   ```
2. Copy the `apigw-backup-tool` folder, located at `install_dir/apigateway/tools/`, to your Cassandra node.
3. Update the `/conf/apigw-backup-tool.ini` file to configure your backup. For more information, see [Apache Cassandra backup and restore](/docs/cass_admin/cassandra_bur#update-your-configuration-file).
4. After the configuration is set and Cassandra is running, validate the configuration:

   ```
   apigw-backup-tool validateConfig
   ```
5. While Cassandra is running, create a backup in the `backup_root_dir` folder. The folder is configured in the `/conf/apigw-backup-tool.ini` file:

   ```
   apigw-backup-tool backup -k <keyspace name> -s <snapshot name>
   ```
6. Back up your Cassandra configuration.

   In addition to backing up your data in Apache Cassandra keyspaces, you must also back up your Apache Cassandra configuration and API Gateway configuration. You must back up the `CASSANDRA_HOME/conf` directory on the Cassandra nodes.

### Update Cassandra configuration files

Compare the configuration in `CASSANDRA_HOME/conf` of your current Cassandra installation to the default configuration of Cassandra 3.11.11, and apply any custom changes to the relevant configuration file in your new Cassandra installation.

Also, ensure that the `num_tokens` value in `conf/cassandra.yaml` in your new Cassandra installation matches the value set in your current Cassandra installation.

### Copy SSL certificates

If you have SSL certificates in your old Cassandra installation, you must copy them to your new Cassandra installation.

Copy the following files to the `CASSANDRA_HOME/conf/` folder in your new installation:

* `CASSANDRA_HOME/conf/.truststore`
* `CASSANDRA_HOME/conf/.keystore`

### Enable legacy SSL storage port

If you have Cassandra server encryption enabled, set `enable_legacy_ssl_storage_port` to `true` in `CASSANDRA_HOME/conf/cassandra.yaml` in your new installation to enable the legacy SSL storage port:

```
enable_legacy_ssl_storage_port: true
```

You can disabled this setting after the upgrade process has been completed.

### Shutdown your old Cassandra installation

To shutdown your old Cassandra installation, follow these steps:

1. Run Cassandra's `nodetool drain` command in `CASSANDRA_HOME/bin` to flush any data in memory and write it to disk:

   ```
   $./nodetool drain
   ```
2. Kill the Cassandra process:

   ```
   $ps auwx | grep cassandra
   $sudo kill pid #Stop Cassandra
   ```

### Copy data between Cassandra installations

Copy the `CASSANDRA_HOME/data` directory from your old Cassandra installation to the corresponding directory in your new installation (for example, `/home/cassandra-407/cassandra/`).

The Cassandra 4.0.7 `data` directory should then have the following subdirectories:

```
commitlog
data
saved_caches
```

### Start Cassandra 4.0.7

Run the following command from the `bin` directory of the Cassandra 4.0.7 installation to start the Cassandra instance:

```
$cd /home/cassandra-407/cassandra/bin
$./cassandra
```

### Repair and upgrade your tables

After you upgrade Cassandra in all nodes, you must repair and upgrade your tables:

1. Repair tables on your new installation:

   ```
   $cd /home/cassandra-407/cassandra/bin
   $./nodetool repair -pr
   ```
2. Rewrite SSTables that are not on the current version and upgrade them to the 4.0.7 version:

   ```
   $cd /home/cassandra-407/cassandra/bin
   $./nodetool upgradesstables
   ```

## Upgrade Cassandra in a multi-node multi-datacenter

To upgrade Apache Cassandra in a multi-node multi-datacenter environment, follow the same steps as for the [Multi-node single-datacenter](#upgrade-cassandra-in-a-multi-node-single-datacenter) procedure.

Attention to the following:

* Repeat the steps on each node in the cluster.
* Upgrade all of the nodes in one datacenter before moving to the next one.

## Troubleshooting

For more information on problems you might encounter when running Cassandra with API Gateway, see [Cassandra troubleshooting](/docs/cass_admin/cassandra_troubleshooting).
