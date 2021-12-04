# Linux
#PG_HOME:=/usr/lib/postgresql/10/bin
#PG_USERNAME:=lena
#PG_GROUPNAME:=lena
#sudo chown -R $(PG_USERNAME):$(PG_GROUPNAME) /var/run/postgresql

# MAC OS X
#PG_HOME:=/usr/local/bin

# Fedora
PG_HOME=/usr/bin

PG_PORT:=5433
PG_DB_NAME:=mydb
PG_TABLE_NAME:=mytable
PG_UNLOGGED_TABLE_NAME:=myunloggedtable
PG_DATA_FOLDER:=primary_data

PSQL_POSTGRES_PRIMARY:=psql -d postgres -p $(PG_PORT)
PSQL_PRIMARY:=psql -d $(PG_DB_NAME) -p $(PG_PORT)

fix_directory_permission:
	sudo chown -R postgres:postgres /var/run/postgresql && \
	sudo chmod a+w /var/run/postgresql

create_primary_instance:
	$(PG_HOME)/pg_ctl init -D $(PG_DATA_FOLDER) -o "--auth-local=trust" -l pg.log && \
	$(PG_HOME)/pg_ctl -w -D $(PG_DATA_FOLDER) -l pg.log -o "-p $(PG_PORT)" start

drop_primary_instance:
	$(PG_HOME)/pg_ctl -D $(PG_DATA_FOLDER) -l pg.log stop && \
	rm -rf $(PG_DATA_FOLDER)

connect_primary:
	@psql -d $(PG_DB_NAME) -p $(PG_PORT)

create_db_table_primary:
	@echo "create database $(PG_DB_NAME)" | $(PSQL_POSTGRES_PRIMARY) && \
	echo "create table if not exists $(PG_TABLE_NAME) (id integer)" | $(PSQL_PRIMARY)

dump_table:
	@echo "select * from $(PG_TABLE_NAME)" | $(PSQL_REPLICA) | more

list_table:
	@psql -d $(PG_DB_NAME) -p $(PG_PORT) -c '\dt+'
 
list_db:
	@psql -d postgres -p $(PG_PORT) -l

truncate_table:
	@echo "truncate table $(PG_TABLE_NAME)" | $(PSQL_PRIMARY)

