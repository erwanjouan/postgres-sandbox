include ../common/Makefile

PG_REPLICA_PORT:=5434
PG_REPLICA_DB_NAME:=mydb2
PG_REPLICA_TABLE_NAME:=mytable
PG_REPLICA_DATA_FOLDER:=replica_data

PSQL_POSTGRES_REPLICA:=psql -d postgres -p $(PG_REPLICA_PORT)
PSQL_REPLICA:=psql -d $(PG_REPLICA_DB_NAME) -p $(PG_REPLICA_PORT)

create_instances:
	@make create_primary_instance && \
	make create_db_table_primary && \
	make create_replica_instance && \
	make create_db_table_replica

create_replica_instance:
	@$(PG_HOME)/pg_ctl init -s -D $(PG_REPLICA_DATA_FOLDER) -l pg.log && \
	$(PG_HOME)/pg_ctl -w -s -D $(PG_REPLICA_DATA_FOLDER) -l pg.log -o "-p $(PG_REPLICA_PORT)" start

drop_replica_instance:
	@$(PG_HOME)/pg_ctl -s -D $(PG_REPLICA_DATA_FOLDER) -l pg.log stop && \
	rm -rf $(PG_REPLICA_DATA_FOLDER)

drop_instances:
	@make drop_primary_instance && make drop_replica_instance && rm pg.log

connect_replica:
	@psql -d $(PG_REPLICA_DB_NAME) -p $(PG_REPLICA_PORT)

create_db_tables:
	@make create_db_table_primary && \
		make create_db_table_replica

create_db_table_replica:
	@echo "create database $(PG_REPLICA_DB_NAME)" | $(PSQL_POSTGRES_REPLICA) && \
	echo "create table if not exists $(PG_REPLICA_TABLE_NAME) (id integer)" | $(PSQL_REPLICA)

create_unlogged_table:
	@echo "create unlogged table if not exists $(PG_UNLOGGED_TABLE_NAME) (id integer)" | $(PSQL_PRIMARY)

dump_replica_table:
	@echo "select * from $(PG_REPLICA_TABLE_NAME)" | $(PSQL_REPLICA) | more
	
insert_table:
	@echo "explain (analyze, buffers) insert into $(PG_TABLE_NAME) select generate_series(1,100000)"  | $(PSQL_PRIMARY)

list_table_replica:
	@echo "\dt+" | $(PSQL_REPLICA)

drop_db_tables:
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
	
