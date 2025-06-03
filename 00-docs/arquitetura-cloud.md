# Arquitetura - Plataforma de Reviews de Filmes e Séries

## Visão Geral do Projeto

**Tipo:** Aplicação web pública para reviews de filmes e séries  
**Tecnologia:** ASP.NET Core + PostgreSQL  
**Escala:** Média (1.000 a 10.000 usuários simultâneos)  
**Disponibilidade:** Alta (99.9%)  
**Cloud Provider:** AWS

## Funcionalidades Principais

- Visualização de filmes e séries (dados via migration)
- Criação de reviews (sem autenticação - apenas nome + review)
- Leitura de reviews por outros usuários
- Interface web responsiva

## Arquitetura Proposta

### 1. Camada de Aplicação

**Amazon EKS (Elastic Kubernetes Service)**
- Cluster gerenciado com 2-3 nós em múltiplas AZs
- Auto-scaling baseado em CPU/memória
- Deployment da aplicação ASP.NET Core em containers
- Load balancer interno para distribuição de carga entre pods

### 2. Camada de Dados

**Amazon RDS PostgreSQL**
- Instância Multi-AZ para alta disponibilidade
- Backup automático semanal configurado
- Read replicas para queries de leitura (otimização de performance)
- Tipo de instância: db.t3.medium (inicial, escalável conforme necessidade)

### 3. Camada de Rede e Segurança

**Application Load Balancer (ALB)**
- Distribuição de tráfego entre múltiplas AZs
- Será criado pelos services do Kubernetes

**Amazon VPC**
- Subnets privadas para EKS e RDS
- Subnets públicas para ALB
- NAT Gateway para acesso à internet dos recursos privados
- Security Groups restritivos

## Diagrama de Arquitetura

```
Internet
    │
    ▼
[Amazon EKS Cluster]
├── Pod 1: ASP.NET Core App
├── Pod 2: ASP.NET Core App
└── Pod N: ASP.NET Core App
    │
    ▼
[Amazon RDS PostgreSQL]
├── Primary Instance (AZ-a)
├── Standby Instance (AZ-b)
└── Read Replica (AZ-c)
```

## Estratégia de Deployment

### CI/CD Pipeline

**Amazon ECR (Elastic Container Registry)**
- Repositório para imagens Docker da aplicação
- Build e push manual inicial
- Evolução futura para CI/CD automatizado

### Blue-Green Deployment
- Deploy sem downtime usando estratégias de rolling update
- Rollback rápido em caso de problemas

## Segurança

### Rede
- Tráfego criptografado (HTTPS/TLS)
- Isolamento de rede com VPC e Security Groups
- RDS em subnet privada sem acesso direto da internet

### Aplicação
- Validação de input no ASP.NET Core
- Logs de auditoria para monitoramento de atividades
- Rate limiting futuro via Kubernetes Ingress

### Dados
- Criptografia em repouso no RDS
- Backup criptografado
- Acesso ao banco via IAM roles

## Escalabilidade

### Horizontal
- Auto-scaling do EKS baseado em métricas
- Read replicas do RDS para distribuir carga de leitura

### Vertical
- Upgrade de instâncias RDS conforme necessário
- Ajuste de recursos dos pods Kubernetes

## Backup e Recuperação

### Banco de Dados
- Backup automático semanal do RDS
- Point-in-time recovery até 35 dias
- Snapshot manual antes de grandes mudanças

### Aplicação
- Imagens Docker versionadas no ECR
- Manifests do Kubernetes versionados

## Considerações Futuras

### Autenticação
- Migração para AWS Cognito quando necessário
- Implementação de perfis de usuário

### Performance
- Implementação de cache (Redis/ElastiCache)
- CDN (CloudFront) para assets estáticos
- CI/CD automatizado

### Operacional
- Implementação de GitOps
- Monitoramento e observabilidade (CloudWatch, alertas)

### Analytics
- Integração com Amazon Analytics para métricas de uso
- Data lake para análises avançadas de reviews