---
title: "Bookslot"
description: "API para la gestión de reservas en espacios con aforo limitado como gimnasios o salas de coworking."
pubDate: 2026-07-02
cover: "https://images.unsplash.com/photo-1549576490-b0b4831ef60a?q=80&w=1170&auto=format"
tags: ["AWS", "DynamoDB", "Terraform"]
featured: true
links:
  - label: "GitHub"
    url: "https://github.com/thaishdz/bookslot"
    icon: "simple-icons:github"
toc: "center"
---

## Overview

BookSlot es una API que permite reservar espacios que cuenten con aforo limitado como gimnasios o salas de coworking.

![Flujo de reserva](https://raw.githubusercontent.com/thaishdz/bookslot/main/docs/assets/flujo-reserva.png)

En vez de montar un servidor que esté encendido 24/7 esperando tráfico esporádico, planteé todo el sistema como arquitectura serverless en AWS (se paga por uso, cero coste en reposo y escala solo si el tráfico crece). El resultado es una API de reservas con slots (huecos con fecha, hora y capacidad) donde cada reserva se procesa de forma segura incluso bajo concurrencia real:

- **Cómputo**: AWS Lambda (funciones bajo demanda)
- **API**: API Gateway (HTTP API)
- **Base de datos**: DynamoDB (NoSQL, single-table)
- **Autenticación**: Amazon Cognito (JWT)
- **IaC**: Terraform
- **CI/CD**: GitHub Actions
- **Observabilidad**: CloudWatch (logs + alarmas) + SNS

> [!NOTE]
> Bookslot es mi proyecto final para obtener el certificado del curso de AWS & Cloud de [Commit Academy](https://www.commitacademy.io/).

## Objetivos

- **Evitar overbooking bajo concurrencia real**, para que nunca se confirme una reserva sobre un espacio que ya está agotado.
- **Coste operativo cercano a cero**, suficiente para un MVP con tráfico casi inexistente.
- **Infraestructura reproducible**, gestionada íntegramente con Terraform en vez de clicks manuales en la consola de AWS..
- **Observabilidad**, si algo falla en producción, me entero por las alarmas antes que por una queja de usuario.
- **Control de gasto activo**, AWS Budgets me alerta antes de que el coste se salga de lo esperado.

## Decisiones arquitectónicas (ADRs)

- [ADR-0001: State locking nativo en S3](https://github.com/thaishdz/bookslot/blob/main/docs/adr/0001-state-locking-s3-native.md)
- [ADR-0002: Modelo de datos single-table](https://github.com/thaishdz/bookslot/blob/main/docs/adr/0002-single-table-design.md)
- [ADR-0003: Rol IAM de ejecución de las Lambdas](https://github.com/thaishdz/bookslot/blob/main/docs/adr/0003-lambda-iam-execution-role.md)
- [ADR-0004: Control de concurrencia](https://github.com/thaishdz/bookslot/blob/main/docs/adr/0004-concurrency-control.md)
- [ADR-0005: Autenticación del pipeline CI/CD con AWS](https://github.com/thaishdz/bookslot/blob/main/docs/adr/0005-cicd-authentication.md)
- [ADR-0006: Bootstrap manual del backend de Terraform](https://github.com/thaishdz/bookslot/blob/main/docs/adr/0006-terraform-backend-bootstrap.md)
- [ADR-0007: Autenticación y autorización con AWS Cognito](https://github.com/thaishdz/bookslot/blob/main/docs/adr/0007-cognito-authentication-authorization.md)
- [ADR-0008: Observabilidad para reservas](https://github.com/thaishdz/bookslot/blob/main/docs/adr/0008-observability-booking-alarm.md)

## FinOps

### Tags
Todos los recursos llevan etiquetas (`Project`, `Environment`) aplicados vía `default_tags` en el provider, lo que permite filtrar el gasto por proyecto en **Cost Explorer**.

### Estimación de coste
Con el tráfico de un MVP, BookSlot opera **dentro del free tier** de AWS:

| Servicio       | Uso estimado (MVP)  | Free tier              | Coste |
|----------------|---------------------|------------------------|-------|
| Lambda         | ~100 req/mes        | 1M req + 400k GB-s/mes | 0 €   |
| DynamoDB       | ~200 ops/mes        | 200M req + 25 GB/mes   | 0 €   |
| API Gateway    | ~100 req/mes        | 1M req/mes             | 0 €   |
| CloudWatch/SNS | volumen mínimo      | dentro de free tier    | ~0 €  |
| S3 (tfstate)   | pocos KB            | —                      | ~0 €  |

**Coste total estimado: ~0 €/mes** — el sistema completo opera dentro del free 
tier con el tráfico real de un MVP académico (~100 req/mes). 

El forecast de **AWS Budgets confirma 0.12$/mes previstos y 0$ gastados**.

>NOTE: Precios verificados en julio de 2026

### Control de gasto
Un AWS Budget (`bookslot-dev-monthly`, límite 10 $/mes) con umbrales escalonados alerta por email ante desviaciones:
- **50%** (5 $) → aviso temprano (gasto real)
- **80%** (8 $) → alerta (gasto real)
- **100%** (10 $) → aviso predictivo (forecast)