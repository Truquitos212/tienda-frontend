# Tienda de Perritos — Frontend

Aplicación frontend de la **Tienda de Perritos Innovatech Chile**, desplegada en AWS EKS con pipeline CI/CD automatizado mediante GitHub Actions.

## Arquitectura

```
GitHub (push) → GitHub Actions → ECR (imagen Docker) → EKS (kubectl rollout)
```

- **Tecnología:** Nginx + HTML/JS estático
- **Puerto:** 80
- **Namespace K8s:** tienda
- **Deployment K8s:** tienda-frontend (2 réplicas + HPA 60% CPU)

## URL pública

```
http://af22958d6f4a74207925b8494d34a95a-2001339819.us-east-1.elb.amazonaws.com
```

## Pipeline CI/CD

El pipeline se activa automáticamente con cada `push` a la rama `main` y realiza los siguientes pasos:

| Paso | Descripción |
|------|-------------|
| 1. Checkout | Descarga el código del repositorio |
| 2. Configure AWS credentials | Autentica con AWS usando los secrets configurados |
| 3. Login to ECR | Accede al registro privado de imágenes en AWS |
| 4. Build & Push | Construye la imagen Docker y la sube a ECR |
| 5. Update kubeconfig | Conecta con el clúster EKS devopseks |
| 6. Deploy to EKS | Actualiza el container con la nueva imagen |
| 7. Verify deployment | Espera que el rollout termine correctamente |

## Secrets requeridos

Configurar en **Settings → Secrets and variables → Actions**:

| Secret | Descripción |
|--------|-------------|
| `AWS_ACCESS_KEY_ID` | Credencial AWS (renovar cada sesión) |
| `AWS_SECRET_ACCESS_KEY` | Credencial AWS (renovar cada sesión) |
| `AWS_SESSION_TOKEN` | Token de sesión AWS (renovar cada sesión) |
| `AWS_ACCOUNT_ID` | ID de cuenta AWS: `178542789655` |
| `AWS_REGION` | Región: `us-east-1` |

## Infraestructura AWS

- **Clúster EKS:** devopseks (us-east-1)
- **ECR Repository:** tienda-frontend
- **Stack CloudFormation:** devops-infra
- **VPC:** devopsvpc
- **Autoscaling:** HPA configurado (60% CPU, mín 2 — máx 5 réplicas)

## Despliegue manual (desde CloudShell)

```bash
aws eks update-kubeconfig --region us-east-1 --name devopseks
kubectl get pods -n tienda
kubectl get svc -n tienda
```
