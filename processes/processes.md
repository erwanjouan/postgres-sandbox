## Processus

[PostgreSQL - 5. Fonctionnement : les processus | tutos fr](https://www.youtube.com/watch?v=xZ_RmnMSGYQ&list=PLn6POgpklwWonHjoGXXSIXJWYzPSy2FeJ&index=6)


### Principle

Parent Process is ```postgres```. Each connection will make a fork of main process.

#### __checkpointer__

- Orchestrates disk writes from memory
    - Writes dirty pages in memory on data files on disk
    - Configures write on disk frequency (duration or wal size) : checkpoint_segments or checkpoint_timeout
    - Checkpoints can be logged
    - Configured by postgres.conf
    - Is a key element for recovery

#### __background writer__
    
- Writes data to disk based on checkpointer order

#### __wal writer__
    
- Writes WAL on disk (transactions) from wal buffer (in memory) to wal segments files 16MB

#### __auto vacuum launcher__

- Defragmentation of tables, maintenance

#### __stats collector__

- Collects statistics to pg_stats
- Allows explain

#### __logical replication launcher__

- logical replication

#### __postgres fork__

- each connection triggers a fork of postgres main process
- allocates necessary resources
- See work memory in postgres.conf (memory needed for each connection)


### Hands on

Create a primary instance
```sh
make create_primary_instance
```

Dump process tree
```sh
sh> pstree
 |-+= 08876 erwanjouan /usr/local/Cellar/postgresql/14.0/bin/postgres -D primary_data -p 5433
 | |--= 08878 erwanjouan postgres: checkpointer
 | |--= 08879 erwanjouan postgres: background writer
 | |--= 08880 erwanjouan postgres: walwriter
 | |--= 08881 erwanjouan postgres: autovacuum launcher
 | |--= 08882 erwanjouan postgres: stats collector
 | \--= 08883 erwanjouan postgres: logical replication launcher
```

Clean
```sh
make drop_instance
```
