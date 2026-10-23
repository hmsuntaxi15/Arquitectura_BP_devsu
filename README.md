# Arquitectura de banca por internet

Propuesta de arquitectura de solución para la plataforma de banca por internet (web y app móvil) de una entidad financiera en Ecuador, desplegada sobre Microsoft Azure.

**Autor:** Henry Suntaxi · **Fecha:** octubre de 2026

## Entregables

| Archivo | Contenido |
|---|---|
| [`Arquitectura_Banca_Internet_HSuntaxi.pdf`](Arquitectura_Banca_Internet_HSuntaxi.pdf) | Documento de arquitectura completo (lectura recomendada) |
| [`Diagrama C4/C4_Banca_Internet.drawio`](Diagrama%20C4/C4_Banca_Internet.drawio) | Fuente editable de los 16 diagramas. Se abre en [app.diagrams.net](https://app.diagrams.net) o en draw.io Desktop. |
| [`Diagrama C4/*.png`](Diagrama%20C4/) | Los 16 diagramas exportados como imagen |

> Para revisar rápido: el **Resumen ejecutivo (sección 1)**, la **Matriz de cobertura de requisitos (sección 12)** y el **Cuadro de decisiones (sección 13)** del PDF.

## Vista general

**Contexto (C4 nivel 1)**

![Diagrama de contexto](Diagrama%20C4/C4_Banca_Internet-C1%20Contexto.drawio.png)

**Contenedores (C4 nivel 2)**

![Diagrama de contenedores](Diagrama%20C4/C4_Banca_Internet-C2%20Contenedores.drawio.png)

## Diagramas incluidos

| Nivel | Diagramas |
|---|---|
| C4 nivel 1, contexto | [C1 Contexto](Diagrama%20C4/C4_Banca_Internet-C1%20Contexto.drawio.png) |
| C4 nivel 2, contenedores | [C2 Contenedores](Diagrama%20C4/C4_Banca_Internet-C2%20Contenedores.drawio.png) |
| C4 nivel 3, componentes | [C3 Componentes Transferencias](Diagrama%20C4/C4_Banca_Internet-C3%20Componentes%20Transferencias.drawio.png) · [C3 BFF y autenticación](Diagrama%20C4/C4_Banca_Internet-C3%20BFF%20y%20autenticaci%C3%B3n.drawio.png) · [C3 Históricos y Movimientos](Diagrama%20C4/C4_Banca_Internet-C3%20Hist%C3%B3ricos%20y%20Movimientos.drawio.png) · [C3 Clientes frecuentes y caché](Diagrama%20C4/C4_Banca_Internet-C3%20Clientes%20frecuentes%20y%20cach%C3%A9.drawio.png) · [C3 Onboarding](Diagrama%20C4/C4_Banca_Internet-C3%20Onboarding.drawio.png) · [C3 Auditoría y Notificaciones](Diagrama%20C4/C4_Banca_Internet-C3%20Auditor%C3%ADa%20y%20Notificaciones.drawio.png) · [C3 Frontend SPA](Diagrama%20C4/C4_Banca_Internet-C3%20Frontend%20SPA.drawio.png) · [C3 App móvil](Diagrama%20C4/C4_Banca_Internet-C3%20App%20m%C3%B3vil.drawio.png) |
| Secuencia | [Secuencia Histórico](Diagrama%20C4/C4_Banca_Internet-Secuencia%20Hist%C3%B3rico.drawio.png) · [Secuencia Transferencia interna](Diagrama%20C4/C4_Banca_Internet-Secuencia%20Transferencia%20interna.drawio.png) · [Secuencia Transferencia interbancaria](Diagrama%20C4/C4_Banca_Internet-Secuencia%20Transferencia%20interbancaria.drawio.png) · [Secuencia Resultado incierto](Diagrama%20C4/C4_Banca_Internet-Secuencia%20Resultado%20incierto.drawio.png) · [Secuencia Onboarding y login](Diagrama%20C4/C4_Banca_Internet-Secuencia%20Onboarding%20y%20login.drawio.png) |
| Infraestructura | [Infra HA y DR](Diagrama%20C4/C4_Banca_Internet-Infra%20HA%20y%20DR.drawio.png) |

## Resumen de la solución

- **Nube:** Microsoft Azure, con Brazil South como región primaria y South Central US como región secundaria en warm standby.
- **Cómputo:** microservicios por dominio en AKS y un BFF por canal (web y móvil), detrás de Azure Front Door con WAF y API Management.
- **Identidad:** Microsoft Entra External ID con OAuth 2.0 Authorization Code + PKCE y OpenID Connect. La app vincula el dispositivo con una llave desbloqueada por biometría, y la web exige un segundo factor en cada transferencia.
- **Transferencias:**
  - Las internas son una operación atómica en el Core.
  - Las interbancarias usan una Saga orquestada con Transactional Outbox, idempotencia y conciliación con el Core y el SPI.
- **Consultas:** CQRS con una base de lectura sincronizada desde el Core y caché Redis para los clientes frecuentes.
- **Auditoría y notificaciones:** eventos en Service Bus Premium, evidencia inmutable (WORM) en Azure Storage y consultas de auditoría en Azure Data Explorer.
- **Operación:** objetivos de RTO de 1 hora y RPO de 5 minutos, con indicadores y objetivos de nivel de servicio, alertas, auto-healing y runbooks.
- **Costos:** estimación de referencia de unos 19 942 USD al mes con precios de lista de Azure, más las medidas de gobierno del gasto (FinOps).
- **Decisiones:** 39 decisiones de arquitectura (21 estructurales y 18 complementarias), cada una con dos justificaciones y las alternativas descartadas.

## Estructura del documento

1. Resumen ejecutivo
2. Alcance
3. Visión general y principios
4. C4, nivel 1: Contexto
5. C4, nivel 2: Contenedores
6. C4, nivel 3: Componentes
7. Diagramas de secuencia
8. Alta disponibilidad, recuperación ante desastres y rendimiento
9. Monitoreo y auto-healing
10. Costos
11. Normativa
12. Matriz de cobertura de requisitos
13. Decisiones de arquitectura

Anexos: A. Glosario · B. Referencias · C. Registro de decisiones (ADR) · D. Decisiones complementarias · E. Estándares técnicos y evidencias de cumplimiento

## Estructura del repositorio

```
.
├── README.md
├── Arquitectura_Banca_Internet_HSuntaxi.pdf
└── Diagrama C4/
    ├── C4_Banca_Internet.drawio        # fuente de los 16 diagramas
    └── C4_Banca_Internet-*.drawio.png  # 16 diagramas exportados
```
