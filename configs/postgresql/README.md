You can choose between some options:

Option 1: Use one database with different schemas for all services
Option 2: Use one docker container with different databases for each service
Option 3: Use different docker container for each database

Option 1:
Use the 'init_schemas.sql' file if you want to host all services in one database - each one as schema.
Mount the init_schemas.sql to the volume of the postgres container:
    volumes:
        - ./configs/postgresql/init.sql:/docker-entrypoint-initdb.d/init_schemas.sql
Change the DSN of each container (keto, kratos and hydra) to search for the schema. Just add '&search_path=kratos' to the DNS
    DSN: postgres://dbuser:dbpassword@database:5432/orydb?sslmode=disable&search_path=keto
    DSN: postgres://dbuser:dbpassword@database:5432/orydb?sslmode=disable&search_path=kratos&max_conns=20&max_idle_conns=4
    DSN: postgres://dbuser:dbpassword@database:5432/orydb?sslmode=disable&search_path=hydra&max_conns=20&max_idle_conns=4

Option 2:
Use the 'create-multiple-postgresql-databases.sh' file to host all services in one docker container but different databases:
Mount the folder / file to the volume of the postgres container:
    volumes:
        - ./configs/postgresql/create-multiple-postgresql-databases.sh:/docker-entrypoint-initdb.d/create-multiple-postgresql-databases.sh
Change the environment variables - remove the 'POSTGRES_DB' variable and add 'POSTGRES_MULTIPLE_DATABASES':
    environment:
        # POSTGRES_DB: orydb
        POSTGRES_MULTIPLE_DATABASES: kratos,keto,hydra

Non-standard database names
If you need to use non-standard database names (hyphens, uppercase letters etc), quote them in POSTGRES_MULTIPLE_DATABASES:
    environment:
        - POSTGRES_MULTIPLE_DATABASES: "test-db-1","test-db-2"

Option 3:
Just create a new container in the docker-compose.yml file for each database.