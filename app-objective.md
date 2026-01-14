# CRM + Automation Platform — Especificação Completa

## Objetivo do SaaS

Plataforma B2B para gestão de leads, pipelines e automações, totalmente **API-first**, escalável, multi-tenant, em tempo real, com dashboards web e integração mobile.

### Pontos-chave:

* Backend complexo (Symfony, PHP 8)
* Regras de negócio robustas (Automation Engine)
* Filas e eventos assíncronos (RabbitMQ)
* Dashboard em tempo real (WebSockets)
* Analytics e relatórios (ElasticSearch)
* Deploy em cloud (AWS) com Docker/Kubernetes
* Multi-tenant, seguro, auditável
* Integração futura com mobile apps (Flutter)

---

## Principais Módulos / Domínios (Backend)

### Users & Authentication

* Usuários (Admins, Clientes, Vendedores)
* Roles & Permissions (RBAC)
* JWT Authentication
* Audit logs
* Reset password, MFA (opcional)

### Tenants & Organizations

* Multi-tenant
* Configurações por cliente
* Planos (Free, Pro, Enterprise)
* Limits (ex: automations por plano)

### Leads & Contacts

* CRUD leads e contatos
* Tags e categorias
* Status (novo, qualificado, perdido)
* Assigned owners
* History log

### Pipelines & Deals

* Funis customizáveis
* Etapas do pipeline
* Probabilidade / valor estimado
* Ações de transição (trigger eventos)

### Automation Engine (Core)

* Triggers (evento → delay → ação)
* Conditions (if / else)
* Actions (email, webhook, tag, move pipeline)
* Scheduling (delays, recurring)
* Event-driven (RabbitMQ)
* Idempotência
* Logging / history

### Messaging / Notifications

* Emails (SMTP / SES AWS)
* Webhooks (integração externa)
* Push / WebSocket notifications
* SMS (opcional)

### Analytics / Reporting

* ElasticSearch para:

  * Leads por estágio
  * Conversão por pipeline
  * Automations executadas
  * Tempo médio de lead
* Dashboards para admins e clientes

### Real-time Updates

* WebSocket server
* Atualizações instantâneas no dashboard
* Status de leads, automations, mensagens

---

## APIs (REST + WebSockets)

### Auth

* POST /auth/login
* POST /auth/logout
* POST /auth/refresh

### Users & Tenants

* GET /users
* GET /users/{id}
* POST /users
* PATCH /users/{id}
* GET /tenants/{id}/settings

### Leads & Contacts

* GET /leads
* POST /leads
* GET /leads/{id}
* PATCH /leads/{id}
* DELETE /leads/{id}
* GET /contacts
* POST /contacts

### Pipelines & Deals

* GET /pipelines
* POST /pipelines
* PATCH /pipelines/{id}
* GET /deals
* POST /deals
* PATCH /deals/{id}

### Automation Engine

* POST /automations → cria workflow
* PATCH /automations/{id} → edita workflow
* GET /automations/{id}/logs → histórico
* POST /triggers → simula trigger
* POST /actions → executa ação manual

### Messaging / Notifications

* POST /emails
* POST /webhooks
* GET /notifications

### Analytics

* GET /reports/leads
* GET /reports/pipelines
* GET /reports/automations

### WebSocket Endpoints

* ws://crm.platform/updates → atualizações em tempo real

---

## Frontend (Web Dashboard)

* Login / Auth
* Tenant Overview (dashboard)
* Leads & Contacts (CRUD + search + tags)
* Pipelines / Deals (visual pipeline board, drag & drop)
* Automation Workflows (visual editor / tree / triggers & actions)
* Messaging / Notifications
* Analytics & Reports (charts + tables)
* Settings / Users / Roles
* Activity Log / Audit

> Pode usar Vue.js, React ou Alpine.js, foco API-first.

---

## Workers e Filas

* **RabbitMQ**:

  * Executa triggers e workflows
  * Processa emails, webhooks, push
  * Retry + Dead Letter Queue

* **Workers Dockerizados**:

  * AutomationWorker
  * EmailWorker
  * WebhookWorker
  * AnalyticsWorker

* **Escaláveis na AWS ECS/EKS**

---

## Deploy / Infraestrutura (AWS)

* Docker Compose para dev
* Kubernetes / ECS para produção
* RDS PostgreSQL (multi-tenant)
* ElasticSearch (analytics)
* S3 (attachments, logs)
* CloudWatch / Metrics / Alarms
* WebSocket server (ECS + ALB)
* CI/CD (GitHub Actions → Docker build → deploy)

---

## Segurança / Compliance

* JWT auth + roles
* TLS / HTTPS obrigatório
* Audit logs para todas ações
* Data isolation (multi-tenant)
* Input validation + rate limits
* GDPR ready (opcional)

---

## Arquitetura Visual (Esboço)

```
        ┌─────────────┐
        │  Frontend   │
        │  Dashboard  │
        └─────┬───────┘
              │ REST API / WebSocket
┌─────────────▼─────────────┐
│       Symfony API         │
│  (Leads, Pipelines,       │
│   Automation, Messaging)  │
└─────┬─────┬─────┬────────┘
      │     │     │
      │     │     │
      ▼     ▼     ▼
 ┌─────────┐ ┌─────────┐ ┌─────────┐
 │ RabbitMQ│ │ Postgre │ │Elastic  │
 │  Queues │ │SQL DB   │ │Search   │
 └─────────┘ └─────────┘ └─────────┘
      │
      ▼
 ┌─────────────┐
 │ Workers     │
 │ (Docker)    │
 │ Automation  │
 │ Emails/Webhooks│
 └─────────────┘
```

---

## MVP mínimo para produção

* CRUD Leads / Contacts
* Pipelines básicos
* Automation Engine simples (triggers + actions)
* WebSocket dashboard (status em tempo real)
* Email worker
* Analytics básica (ElasticSearch)
* Multi-tenant básico
* Docker + deploy AWS

> Cobre toda a stack Celesta: Symfony, PHP8, REST, RabbitMQ, Docker, ElasticSearch, WebSockets, microservices mindset, AWS.
