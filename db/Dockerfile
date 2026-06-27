# Imagen base MySQL 8 oficial para Innovatech
FROM mysql:8

# Script opcional de inicializacion (se ejecuta solo en el PRIMER arranque).
# Las tablas (ventas, despachos) las crea automaticamente Hibernate (ddl-auto=update),
# por eso aqui solo aseguramos que la base exista.
COPY init.sql /docker-entrypoint-initdb.d/

EXPOSE 3306
