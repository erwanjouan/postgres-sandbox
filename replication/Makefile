# Linux
#PG_HOME:=/usr/lib/postgresql/10/bin
#PG_USERNAME:=lena
#PG_GROUPNAME:=lena
#sudo chown -R $(PG_USERNAME):$(PG_GROUPNAME) /var/run/postgresql

# MAC OS X
PG_HOME:=/usr/local/bin

PG_PORT:=5433
PG_DB_NAME:=mydb
PG_TABLE_NAME:=mytable
PG_UNLOGGED_TABLE_NAME:=myunloggedtable
PG_DATA_FOLDER:=primary_data

PG_REPLICA_PORT:=5434
PG_REPLICA_DB_NAME:=mydb2
PG_REPLICA_TABLE_NAME:=mytable
PG_REPLICA_DATA_FOLDER:=replica_data

PSQL_POSTGRES_PRIMARY:=psql -d postgres -p $(PG_PORT)
PSQL_PRIMARY:=psql -d $(PG_DB_NAME) -p $(PG_PORT)

PSQL_POSTGRES_REPLICA:=psql -d postgres -p $(PG_REPLICA_PORT)
PSQL_REPLICA:=psql -d $(PG_REPLICA_DB_NAME) -p $(PG_REPLICA_PORT)

create_replicated_instance:
	@make create_primary_instance && \
	make create_db_table_primary && \
	make create_replica_instance && \
	make create_db_table_replica
		
create_primary_instance:
	@echo $(PG_HOME)/pg_ctl && $(PG_HOME)/pg_ctl init -s -D $(PG_DATA_FOLDER) -l pg.log && \
	$(PG_HOME)/pg_ctl -w -s -D $(PG_DATA_FOLDER) -l pg.log -o "-p $(PG_PORT)" start

create_replica_instance:
	@$(PG_HOME)/pg_ctl init -s -D $(PG_REPLICA_DATA_FOLDER) -l pg.log && \
	$(PG_HOME)/pg_ctl -w -s -D $(PG_REPLICA_DATA_FOLDER) -l pg.log -o "-p $(PG_REPLICA_PORT)" start

drop_primary_instance:
	@$(PG_HOME)/pg_ctl -s -D $(PG_DATA_FOLDER) -l pg.log stop && \
	rm -rf $(PG_DATA_FOLDER)

drop_replica_instance:
	@$(PG_HOME)/pg_ctl -s -D $(PG_REPLICA_DATA_FOLDER) -l pg.log stop && \
	rm -rf $(PG_REPLICA_DATA_FOLDER)

drop_instance:
	@make drop_primary_instance && make drop_replica_instance && rm pg.log

connect_primary:
	@psql -d $(PG_DB_NAME) -p $(PG_PORT)

connect_replica:
	@psql -d $(PG_REPLICA_DB_NAME) -p $(PG_REPLICA_PORT)

create_db_table:
	@make create_db_table_primary && \
		make create_db_table_replica

create_db_table_primary:
	@echo "create database $(PG_DB_NAME)" | $(PSQL_POSTGRES_PRIMARY) && \
	echo "create table if not exists $(PG_TABLE_NAME) (id integer)" | $(PSQL_PRIMARY)

create_db_table_replica:
	@echo "create database $(PG_REPLICA_DB_NAME)" | $(PSQL_POSTGRES_REPLICA) && \
	echo "create table if not exists $(PG_REPLICA_TABLE_NAME) (id integer)" | $(PSQL_REPLICA)

create_unlogged_table:
	@echo "create unlogged table if not exists $(PG_UNLOGGED_TABLE_NAME) (id integer)" | $(PSQL_PRIMARY)

dump_table:
	@echo "select * from $(PG_TABLE_NAME)" | $(PSQL_REPLICA) | more

dump_replica_table:
	@echo "select * from $(PG_REPLICA_TABLE_NAME)" | $(PSQL_REPLICA) | more
	
insert_table:
	@echo "explain (analyze, buffers) insert into $(PG_TABLE_NAME) select generate_series(1,100000)"  | $(PSQL_PRIMARY)

list_table:
	@psql -d $(PG_DB_NAME) -p $(PG_PORT) -c '\dt+'

list_table_replica:
	@echo "\dt+" | $(PSQL_REPLICA)

list_db:
	@psql -d postgres -p $(PG_PORT) -l

truncate_table:
	@echo "truncate table $(PG_TABLE_NAME)" | $(PSQL_PRIMARY)

drop_db_table:
	@make drop_db_table_primary && make drop_db_table_replica

drop_db_table_primary:
	@echo "drop table $(PG_TABLE_NAME)" | $(PSQL_PRIMARY) && \
	echo "drop database $(PG_DB_NAME)" | $(PSQL_PRIMARY)

drop_db_table_replica:
	@echo "drop table $(PG_TABLE_NAME)" | $(PSQL_REPLICA) && \
	eho "drop database $(PG_REPLICA_DB_NAME)" | $(PSQL_REPLICA)

create_replication:
	@echo "alter system set wal_level=logical" | $(PSQL_PRIMARY) && \
	$(PG_HOME)/pg_ctl restart -s -D $(PG_DATA_FOLDER) && \
	echo "create publication percpub for table $(PG_TABLE_NAME)" | $(PSQL_PRIMARY) && \
	echo "create subscription percpub connection 'port=$(PG_PORT) dbname=$(PG_DB_NAME)' publication percpub" | $(PSQL_REPLICA)
	
drop_replication:
	@echo "drop publication percpub" | $(PSQL_PRIMARY) && \
	echo "drop subscription percpub" | $(PSQL_REPLICA)

show_wal_replica:
	@echo "select * from pg_stat_subscription" | $(PSQL_REPLICA) && \
	echo "select * from pg_stat_bgwriter" | $(PSQL_REPLICA)
	
