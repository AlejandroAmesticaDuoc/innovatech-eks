# innovatech-frontend

Frontend del caso Innovatech EP2 (ISY1101). React + Vite construido como bundle estatico y servido por Nginx, que ademas hace de reverse proxy hacia los dos backends (las llamadas del front van por rutas relativas /api/v1/despachos y /api/v1/ventas).

Variables de entorno en runtime (las lee envsubst de nginx en el contenedor):
DESPACHOS_HOST, DESPACHOS_PORT, VENTAS_HOST, VENTAS_PORT.

Para levantar todo el stack en local conviene usar el docker-compose.yml que esta en el directorio padre (innovatech-ep2).

Si quiero correr solo este contenedor (con backends ya arriba):

    docker build -t frontend .
    docker run -p 80:80 \
      -e DESPACHOS_HOST=host.docker.internal -e DESPACHOS_PORT=8081 \
      -e VENTAS_HOST=host.docker.internal -e VENTAS_PORT=8082 \
      frontend

Despliegue: push a la rama deploy y el workflow de GitHub Actions hace el build, push de la imagen a Amazon ECR y deploy a la EC2 frontend con AWS SSM.
