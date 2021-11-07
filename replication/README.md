# Logical replication

### Pre-requesite
A postgresql distribution installed locally through package manager

### Hands on

Create a primary instance and a replica
```sh
make create_instances
```

Activate wal_level=logical on primary and bind replica to primary
```sh
make create_replication
```

Insert data in replicated table
```sh
make insert_table
```

See data replication
```sh
make dump_table
make dump_replica_table
```

Clean
```sh
make drop_instances
```