include ../common/Makefile

create_instance:
	@make create_primary_instance && \
	make create_json_db_table_primary

create_json_db_table_primary:
	echo "create database $(PG_DB_NAME) WITH ENCODING='UTF8' TEMPLATE template0" | $(PSQL_POSTGRES_PRIMARY) && \
	echo "CREATE TABLE books (id SERIAL PRIMARY KEY, client TEXT NOT NULL, data JSONb NOT NULL)" | $(PSQL_PRIMARY) && \
	cat insert_rows.sql | $(PSQL_PRIMARY)

dump_json:
	echo "SELECT * FROM books;" | $(PSQL_PRIMARY)

query_json:
	echo "SELECT client, data->'title' AS title, data->'author'->'last_name' as author_last_name FROM books;" | $(PSQL_PRIMARY)

drop_instance:
	@make drop_primary_instance && rm pg.log
