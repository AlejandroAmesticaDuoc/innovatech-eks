# Innovatech - Orquestacion en AWS EKS (EP3)

Migracion de la aplicacion Innovatech a un cluster de Kubernetes administrado (Amazon EKS), con autoescalado, alta disponibilidad, observabilidad y un pipeline CI/CD que despliega automaticamente desde GitHub.

ISY1101 - Introduccion a Herramientas DevOps - DUOC UC - 2026

## Arquitectura

Cuatro componentes desplegados en el namespace `innovatech`:

| Componente | Imagen (ECR) | Puerto | Service |
|---|---|---|---|
| Frontend (React + Nginx) | innovatech-frontend | 80 | LoadBalancer (publico) |
| Backend Ventas (Spring Boot) | innovatech-back-ventas | 8082 | ClusterIP |
| Backend Despachos (Spring Boot) | innovatech-back-despachos | 8081 | ClusterIP |
| Base de datos (MySQL 8) | innovatech-db | 3306 | ClusterIP |

Flujo: Navegador -> ELB publico -> Nginx -> ventas:8082 / despachos:8081 (DNS interno) -> mysql:3306.
El Nginx hace de reverse proxy hacia los backend; los backend crean las tablas con Hibernate (ddl-auto=update).

## Estructura del repositorio

```
frontend/         React + Vite + Nginx (Dockerfile)
back-ventas/      Spring Boot 8082 (Dockerfile)
back-despachos/   Spring Boot 8081 (Dockerfile)
db/               MySQL 8 (Dockerfile + init.sql)
k8s/              Manifiestos: namespace, secret, deployments, services, HPA
.github/workflows/deploy-eks.yml   Pipeline CI/CD
```

## Requisitos

- AWS CLI, kubectl, Docker y Git instalados.
- Laboratorio de AWS Academy iniciado (credenciales temporales).
- Cluster EKS `devops-eks` en la region us-east-1 (creacion paso a paso en GUIA_PASO_A_PASO).

## Despliegue manual (resumen)

```bash
# 1. Conectar kubectl al cluster
aws eks update-kubeconfig --region us-east-1 --name devops-eks

# 2. En los manifiestos de k8s/, reemplazar ACCOUNT_ID por tu numero de cuenta AWS

# 3. Construir y publicar las 4 imagenes en ECR (tag eks-v1) con docker build / docker push

# 4. Desplegar en el cluster
kubectl apply -f k8s/namespace.yaml
kubectl apply -f k8s/mysql-secret.yaml
kubectl apply -f k8s/mysql-deployment.yaml -f k8s/mysql-service.yaml
kubectl apply -f k8s/ventas-deployment.yaml -f k8s/ventas-service.yaml
kubectl apply -f k8s/despachos-deployment.yaml -f k8s/despachos-service.yaml
kubectl apply -f k8s/frontend-deployment.yaml -f k8s/frontend-service.yaml
kubectl apply -f k8s/ventas-hpa.yaml -f k8s/despachos-hpa.yaml -f k8s/frontend-hpa.yaml

# 5. Obtener la URL publica del frontend
kubectl get svc frontend -n innovatech
```

## CI/CD

Cada push a la rama `main` ejecuta `.github/workflows/deploy-eks.yml`, que construye las 4 imagenes, las publica en Amazon ECR y redespliega en EKS (kubectl set image + rollout status).

Secrets requeridos en GitHub (Settings -> Secrets and variables -> Actions): `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`, `AWS_SESSION_TOKEN`, `AWS_REGION`, `EKS_CLUSTER_NAME`.
