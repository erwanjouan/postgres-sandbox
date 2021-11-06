# Postgresql Sandbox

### Pre-requesite
A postgresql distribution installed locally through package manager

### Basic Scenario

Create a primary instance and a replica
```sh
make create_instance
```

Activate wal_level=logical on primary and bind replica to primary
```sh
make create_replication
```

See data replication
```sh
make insert_table
make dump_replica_table
```

Clean
```sh
make drop_instance
```