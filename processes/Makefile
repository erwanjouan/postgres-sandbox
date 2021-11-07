include ../common/Makefile

create_instance:
	@make create_primary_instance && \
	make create_db_table_primary

drop_instance:
	@make drop_primary_instance && rm pg.log