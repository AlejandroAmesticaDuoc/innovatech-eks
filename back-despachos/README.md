# innovatech-back-despachos

Microservicio de despachos para Innovatech EP2 (ISY1101). Spring Boot 3.4 sobre puerto 8081, persiste en MySQL.

Variables de entorno que usa: DB_ENDPOINT, DB_PORT, DB_NAME, DB_USERNAME, DB_PASSWORD.

Para levantar todo el stack en local conviene usar el docker-compose.yml que esta en el directorio padre (innovatech-ep2). Ese compose tira la BD, los dos backends y el front juntos.

Si quiero correr solo este servicio:

    docker build -t despachos .
    docker run -p 8081:8081 \
      -e DB_ENDPOINT=host.docker.internal -e DB_PORT=3306 \
      -e DB_NAME=innovatech -e DB_USERNAME=appuser -e DB_PASSWORD=AppPass2025 \
      despachos

Despliegue: push a la rama deploy y el workflow de GitHub Actions hace el build, push de la imagen a Amazon ECR y deploy a la EC2 backend con AWS SSM.
