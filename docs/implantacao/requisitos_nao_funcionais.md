---
sidebar_position: 1
---

# Requisitos Não Funcionais (RNF)

Versão: 1.0  
Data: Novembro 2025  
Status: Em Desenvolvimento

---

## 1. Introdução

### 1.1 Objetivos e Escopo

Este documento define os requisitos não funcionais da plataforma de gerenciamento de reservas via WhatsApp. Os RNFs estabelecem as características de qualidade, restrições técnicas e atributos operacionais que o sistema deve atender.

Escopo: Todos os componentes da solução (Plataforma Web, WhatsApp Bot, Backend, Infraestrutura).

### 1.2 Pilares Estratégicos

Os requisitos não funcionais são guiados por três pilares fundamentais:

1. Simplicidade Operacional: Interfaces claras, setup rápido, curva de aprendizado mínima
2. Eficiência Técnica: Arquitetura leve, componentes resilientes, performance otimizada
3. Baixo TCO: Uso otimizado de recursos, infraestrutura enxuta, custos previsíveis

### 1.3 Priorização (MoSCoW)

- MUST: Essencial para MVP, sem isso o sistema não funciona
- SHOULD: Importante mas não bloqueante para lançamento
- COULD: Desejável, agrega valor mas pode ser postergado
- WON'T: Fora do escopo atual, roadmap futuro

---

## 2. Performance e Escalabilidade

### RNF001 - Tempo de Resposta da API

Descrição: As requisições à API devem ser processadas dentro de limites de tempo aceitáveis para garantir boa experiência do usuário.

Mensuração:
- Percentil 50 (mediana): < 100ms
- Percentil 95: < 200ms
- Percentil 99: < 500ms

Critérios de Aceite:
- [ ] 95% das requisições GET respondem em < 200ms
- [ ] 95% das requisições POST/PATCH respondem em < 300ms
- [ ] Endpoints críticos (/availability, /bookings) < 150ms (p95)
- [ ] Métricas coletadas via CloudWatch
- [ ] Dashboard de latência configurado

Prioridade: MUST

---

### RNF002 - Tempo de Resposta do WhatsApp Bot

Descrição: O bot deve responder às mensagens do cliente de forma rápida, simulando conversa natural sem delays perceptíveis.

Mensuração:
- Tempo entre recebimento do webhook e envio da resposta: < 3 segundos (p95)
- Tempo médio de resposta: < 1,5 segundos

Critérios de Aceite:
- [ ] 95% das respostas enviadas em < 3s após recebimento
- [ ] Mensagens de "digitando..." enviadas se processamento > 2s
- [ ] Timeout de 10s com mensagem de erro amigável
- [ ] Logs de latência por tipo de mensagem
- [ ] Alertas configurados para latência > 5s

Prioridade: MUST

---

### RNF003 - Tempo de Carregamento da Plataforma Web

Descrição: As páginas da plataforma web devem carregar rapidamente para não prejudicar a produtividade dos usuários.

Mensuração:
- First Contentful Paint (FCP): < 1,5s
- Time to Interactive (TTI): < 3s
- Largest Contentful Paint (LCP): < 2,5s

Critérios de Aceite:
- [ ] Core Web Vitals no verde (Google Lighthouse)
- [ ] Score de Performance > 85 (Lighthouse)
- [ ] Páginas críticas (dashboard, agenda) carregam em < 2s
- [ ] Lazy loading implementado para imagens/componentes
- [ ] Bundle JS otimizado (< 200KB inicial)

Prioridade: SHOULD

---

### RNF004 - Capacidade de Agendamentos (Mês 6)

Descrição: O sistema deve suportar a carga projetada para validação inicial (Mês 6).

Mensuração:
- Agendamentos/mês: 1.400
- Pico simultâneo: 10 agendamentos/minuto
- Tenants ativos: 60

Critérios de Aceite:
- [ ] Sistema processa 1.400+ agendamentos/mês sem degradação
- [ ] Suporta 15 agendamentos/minuto em picos
- [ ] Latência < 200ms mesmo em pico
- [ ] Testes de carga confirmam capacidade
- [ ] Monitoramento de throughput configurado

Prioridade: MUST

---

### RNF005 - Capacidade de Agendamentos (Mês 12)

Descrição: O sistema deve escalar para suportar crescimento projetado (Mês 12).

Mensuração:
- Agendamentos/mês: 14.800
- Pico simultâneo: 100 agendamentos/minuto
- Tenants ativos: 550

Critérios de Aceite:
- [ ] Sistema processa 15.000+ agendamentos/mês
- [ ] Suporta 150 agendamentos/minuto em picos
- [ ] Latência mantém-se < 300ms em carga alta
- [ ] Auto-scaling de Lambdas configurado
- [ ] Cache Redis otimizado para escala

Prioridade: SHOULD

---

### RNF006 - Capacidade de Agendamentos (Mês 24)

Descrição: O sistema deve operar em escala relevante (Mês 24).

Mensuração:
- Agendamentos/mês: 66.000
- Pico simultâneo: 500 agendamentos/minuto
- Tenants ativos: 3.200

Critérios de Aceite:
- [ ] Sistema processa 70.000+ agendamentos/mês
- [ ] Suporta 750 agendamentos/minuto em picos
- [ ] Banco de dados migrado para RDS se necessário
- [ ] Sharding implementado se requisitado pela carga
- [ ] Plano de migração multi-região documentado

Prioridade: COULD

---

### RNF007 - Escalabilidade Horizontal

Descrição: Componentes stateless devem escalar horizontalmente de forma automática.

Mensuração:
- Lambda: auto-scaling até 1000 instâncias concorrentes
- SQS: processamento ilimitado de mensagens
- Redis: suporta 10.000+ ops/segundo

Critérios de Aceite:
- [ ] Lambdas escalam automaticamente com carga
- [ ] SQS configurado com DLQ e retry policies
- [ ] Redis Cluster configurado para HA (roadmap Mês 12+)
- [ ] Testes de carga validam auto-scaling
- [ ] Sem single point of failure em componentes críticos

Prioridade: MUST

---

## 3. Disponibilidade e Resiliência

### RNF008 - SLA de Disponibilidade (MVP)

Descrição: O sistema deve manter alta disponibilidade durante fase de validação.

Mensuração:
- Uptime: 99.5% mensal
- Downtime máximo tolerado: ~3,6 horas/mês
- MTTR (Mean Time to Recovery): < 2 horas

Critérios de Aceite:
- [ ] Uptime medido e reportado mensalmente
- [ ] Incidentes documentados com post-mortem
- [ ] Janelas de manutenção comunicadas com 48h antecedência
- [ ] Status page pública (UptimeRobot)
- [ ] Alertas de downtime configurados

Prioridade: MUST

---

### RNF009 - SLA de Disponibilidade (Escala)

Descrição: O sistema deve evoluir para alta disponibilidade em operação em escala.

Mensuração:
- Uptime: 99.9% mensal
- Downtime máximo tolerado: ~43 minutos/mês
- MTTR: < 30 minutos

Critérios de Aceite:
- [ ] Redundância em componentes críticos (DB, Redis)
- [ ] Failover automático configurado
- [ ] Multi-AZ para RDS (se migrado)
- [ ] Runbooks de incidentes documentados
- [ ] On-call rotation estabelecida

Prioridade: SHOULD

---

### RNF010 - Backup de Banco de Dados

Descrição: Dados críticos devem ser protegidos por backups regulares e testados.

Mensuração:
- Backup completo: diário (retenção 7 dias)
- Backup incremental: a cada 6 horas
- Backup offsite (S3): semanal (retenção 30 dias)

Critérios de Aceite:
- [ ] Backups automáticos configurados
- [ ] Snapshots armazenados em S3
- [ ] Testes de restore mensais executados
- [ ] Restore testado em < 1 hora
- [ ] Documentação de procedimento de restore

Prioridade: MUST

---

### RNF011 - RPO (Recovery Point Objective)

Descrição: Em caso de falha catastrófica, perda máxima de dados aceitável.

Mensuração:
- RPO: < 24 horas (MVP)
- RPO: < 6 horas (escala)

Critérios de Aceite:
- [ ] Backup a cada 6h garante RPO
- [ ] Transações críticas em journal log
- [ ] Eventos na fila não processados são recuperáveis
- [ ] Procedimento de disaster recovery documentado
- [ ] Simulação de DR executada semestralmente

Prioridade: MUST

---

### RNF012 - RTO (Recovery Time Objective)

Descrição: Tempo máximo para restaurar o sistema após incidente crítico.

Mensuração:
- RTO: < 4 horas (MVP)
- RTO: < 1 hora (escala)

Critérios de Aceite:
- [ ] Runbook de recuperação testado
- [ ] Restore de backup em < 1h
- [ ] Infraestrutura como código (Terraform) permite rebuild rápido
- [ ] Equipe treinada em procedimentos de DR
- [ ] Ferramentas de automação de recovery configuradas

Prioridade: MUST

---

### RNF013 - Sistema de Lock Distribuído

Descrição: Prevenir conflitos de agendamento simultâneo usando lock distribuído.

Mensuração:
- Lock timeout: 60 segundos
- TTL automático para evitar deadlocks
- Taxa de colisão: < 1% dos agendamentos

Critérios de Aceite:
- [ ] Redis SETNX implementado para locks
- [ ] TTL configurado em todos os locks
- [ ] Cleanup automático de locks expirados
- [ ] Retry strategy com exponential backoff
- [ ] Métricas de colisão monitoradas

Prioridade: MUST

---

### RNF014 - Circuit Breaker

Descrição: Proteção contra falhas em cascata usando circuit breakers.

Mensuração:
- Threshold de erro: 50% em 10 requisições
- Timeout: 5 segundos
- Half-open: após 30 segundos

Critérios de Aceite:
- [ ] Circuit breaker implementado em chamadas externas
- [ ] WhatsApp API protegida por circuit breaker
- [ ] Métricas de estado (open/closed/half-open) coletadas
- [ ] Degradação graciosa quando aberto
- [ ] Alertas configurados para circuit aberto

Prioridade: SHOULD

---

### RNF015 - Retry com Exponential Backoff

Descrição: Tentativas automáticas de reprocessamento para falhas transitórias.

Mensuração:
- Max retries: 3 tentativas
- Backoff inicial: 1 segundo
- Multiplicador: 2x (1s, 2s, 4s)

Critérios de Aceite:
- [ ] Retry implementado em chamadas HTTP externas
- [ ] SQS configurado com retry policy
- [ ] Dead Letter Queue (DLQ) para falhas permanentes
- [ ] Idempotência garantida em operações críticas
- [ ] Logs de retry para debugging

Prioridade: MUST

---

## 4. Segurança

### RNF016 - Autenticação JWT

Descrição: Autenticação stateless usando JSON Web Tokens.

Mensuração:
- Access token TTL: 30 minutos
- Refresh token TTL: 7 dias
- Algoritmo: RS256 (chaves assimétricas)

Critérios de Aceite:
- [ ] JWT implementado com expiração
- [ ] Refresh token armazenado no Redis
- [ ] Tokens revogáveis (blacklist no Redis)
- [ ] Rotação de chaves semestral
- [ ] Claims incluem tenant_id e role

Prioridade: MUST

---

### RNF017 - Controle de Acesso (RBAC)

Descrição: Controle de acesso baseado em papéis (Role-Based Access Control).

Mensuração:
- Roles: OWNER, STAFF
- Permissões granulares por endpoint
- Validação em 100% dos endpoints protegidos

Critérios de Aceite:
- [ ] OWNER: acesso total (CRUD em tudo)
- [ ] STAFF: leitura de agenda, update de status de atendimentos
- [ ] Middleware de autorização em todas as rotas protegidas
- [ ] Testes de permissão para cada role
- [ ] Audit log de alterações de permissões

Prioridade: MUST

---

### RNF018 - Rate Limiting

Descrição: Proteção contra abuso de API por rate limiting por tenant.

Mensuração:
- API pública: 100 req/min por IP
- API autenticada: 1000 req/min por tenant
- WhatsApp webhook: 500 req/min por tenant

Critérios de Aceite:
- [ ] Rate limiting implementado no API Gateway
- [ ] Resposta HTTP 429 (Too Many Requests)
- [ ] Headers informativos (X-RateLimit-*)
- [ ] Whitelist para IPs confiáveis (testes, monitoring)
- [ ] Métricas de rate limit por tenant

Prioridade: MUST

---

### RNF019 - Criptografia em Trânsito

Descrição: Toda comunicação externa deve usar criptografia TLS.

Mensuração:
- Protocolo: TLS 1.2 ou superior
- Certificados: Let's Encrypt (renovação automática)
- Score SSL Labs: A ou superior

Critérios de Aceite:
- [ ] HTTPS obrigatório (redirect HTTP → HTTPS)
- [ ] TLS 1.2+ configurado
- [ ] Certificado válido e auto-renovável
- [ ] HSTS habilitado
- [ ] SSL Labs score A validado

Prioridade: MUST

---

### RNF020 - Criptografia em Repouso

Descrição: Dados sensíveis devem ser criptografados no banco de dados.

Mensuração:
- Algoritmo: AES-256
- Campos criptografados: senhas (bcrypt), dados pessoais sensíveis
- Key rotation: anual

Critérios de Aceite:
- [ ] Senhas com bcrypt (cost factor 12)
- [ ] Dados pessoais sensíveis criptografados (se aplicável)
- [ ] Chaves armazenadas em AWS Secrets Manager (roadmap)
- [ ] Backup criptografado (S3 encryption)
- [ ] Documentação de campos criptografados

Prioridade: MUST

---

### RNF021 - Validação HMAC (WhatsApp Webhooks)

Descrição: Validar autenticidade de webhooks do WhatsApp usando HMAC.

Mensuração:
- Algoritmo: HMAC-SHA256
- 100% dos webhooks validados
- Reject inválidos: < 10ms

Critérios de Aceite:
- [ ] Signature verificada em todos os webhooks
- [ ] Webhooks inválidos rejeitados (HTTP 401)
- [ ] Secret armazenado de forma segura
- [ ] Logs de tentativas de webhook inválido
- [ ] Alertas para spike de rejeições

Prioridade: MUST

---

### RNF022 - Proteção OWASP Top 10

Descrição: Sistema protegido contra as 10 vulnerabilidades mais críticas (OWASP).

Mensuração:
- 100% dos endpoints validados contra:
  - Injection (SQL, NoSQL, Command)
  - Broken Authentication
  - Sensitive Data Exposure
  - XXE, XSS, CSRF
  - Security Misconfiguration

Critérios de Aceite:
- [ ] Input validation em todos os endpoints
- [ ] Prepared statements (SQL injection)
- [ ] Output encoding (XSS)
- [ ] CORS configurado adequadamente
- [ ] Security headers (CSP, X-Frame-Options)
- [ ] Scan de vulnerabilidades mensal (OWASP ZAP)

Prioridade: MUST

---

### RNF023 - LGPD Compliance

Descrição: Sistema em conformidade com Lei Geral de Proteção de Dados.

Mensuração:
- 100% dos fluxos com consentimento explícito
- Direito ao esquecimento: < 72 horas
- Portabilidade de dados: formato JSON

Critérios de Aceite:
- [ ] Termos de uso e política de privacidade
- [ ] Consentimento registrado no onboarding
- [ ] Funcionalidade de exportação de dados
- [ ] Funcionalidade de exclusão de dados
- [ ] Logs de consentimento e revogação
- [ ] DPO (Data Protection Officer) designado

Prioridade: MUST

---

### RNF024 - Audit Log

Descrição: Registro de auditoria para ações críticas do sistema.

Mensuração:
- Retenção: 7 anos
- Eventos auditados: CRUD de agendamentos, alteração de permissões, acesso a dados sensíveis
- Imutabilidade: append-only

Critérios de Aceite:
- [ ] Tabela audit_log implementada
- [ ] Eventos críticos logados (quem, quando, o quê)
- [ ] Dados anteriores e novos registrados (updates)
- [ ] Interface de consulta para auditoria
- [ ] Backup separado para logs de auditoria

Prioridade: MUST

---

## 5. Observabilidade

### RNF025 - Logging Estruturado

Descrição: Logs estruturados em formato JSON para facilitar análise.

Mensuração:
- Formato: JSON
- Níveis: ERROR, WARN, INFO, DEBUG
- Campos obrigatórios: timestamp, level, trace_id, tenant_id, message

Critérios de Aceite:
- [ ] Logs em JSON parseável
- [ ] Correlation ID (trace_id) em todas as requisições
- [ ] Contexto de tenant em logs relevantes
- [ ] Logs agregados no CloudWatch Logs
- [ ] Queries de exemplo documentadas

Prioridade: MUST

---

### RNF026 - Métricas de Performance

Descrição: Coleta de métricas técnicas para monitoramento de saúde.

Mensuração:
- Latência (p50, p95, p99)
- Taxa de erro (4xx, 5xx)
- Throughput (req/s)
- Atualização: tempo real

Critérios de Aceite:
- [ ] Métricas coletadas no CloudWatch
- [ ] Dashboard de latência por endpoint
- [ ] Dashboard de taxa de erro
- [ ] Alarmes configurados para anomalias
- [ ] Métricas exportadas para Grafana Cloud

Prioridade: MUST

---

### RNF027 - Métricas de Negócio

Descrição: Métricas de produto para acompanhamento de KPIs.

Mensuração:
- Agendamentos criados/confirmados/cancelados
- Taxa de conversão (trial → pago)
- Churn mensal
- MRR (Monthly Recurring Revenue)

Critérios de Aceite:
- [ ] Eventos de negócio publicados na fila
- [ ] Analytics Service processa eventos
- [ ] Dashboard de KPIs no painel admin
- [ ] Métricas atualizadas em near real-time
- [ ] Exportação para análise (CSV/JSON)

Prioridade: SHOULD

---

### RNF028 - Alertas Críticos

Descrição: Sistema de alertas para incidentes críticos.

Mensuração:
- Latência de alerta: < 2 minutos
- Canais: Email, Slack (roadmap)
- Severity: Critical, High, Medium, Low

Critérios de Aceite:
- [ ] Alertas configurados para:
  - Uptime < 99%
  - Taxa de erro > 5%
  - Latência p95 > 1s
  - DB down
  - WhatsApp API down
- [ ] Notificações por email funcionais
- [ ] Runbook linkado em cada alerta
- [ ] On-call rotation estabelecida (roadmap)

Prioridade: MUST

---

### RNF029 - Health Check

Descrição: Endpoint de saúde para verificação de disponibilidade.

Mensuração:
- Endpoint: GET /health
- Resposta: < 100ms
- Verifica: DB, Redis, WhatsApp API

Critérios de Aceite:
- [ ] Endpoint /health implementado
- [ ] Retorna HTTP 200 se saudável
- [ ] Retorna HTTP 503 se dependência crítica falhar
- [ ] Response body com status de cada dependência
- [ ] UptimeRobot monitora /health a cada 1min

Prioridade: MUST

---

### RNF030 - Tracing Distribuído

Descrição: Rastreamento de requisições através de múltiplos serviços.

Mensuração:
- Protocolo: OpenTelemetry
- Sampling: 10% (MVP) → 100% (escala)
- Retenção: 7 dias

Critérios de Aceite:
- [ ] Trace ID propagado em headers
- [ ] Spans criados para operações críticas
- [ ] Integração com AWS X-Ray (roadmap)
- [ ] Visualização de traces em dashboard
- [ ] Correlação trace → logs → métricas

Prioridade: COULD

---

## 6. Usabilidade

### RNF031 - Responsividade da Plataforma Web

Descrição: Interface adaptável para desktop, tablet e mobile.

Mensuração:
- Breakpoints: 320px (mobile), 768px (tablet), 1024px (desktop)
- Touch targets: mínimo 44x44px
- Teste em: Chrome, Firefox, Safari, Edge

Critérios de Aceite:
- [ ] Layout responsivo implementado
- [ ] Testes em dispositivos reais (iOS, Android)
- [ ] Navegação funcional em touch
- [ ] Sem scroll horizontal em mobile
- [ ] Imagens otimizadas para mobile (WebP)

Prioridade: MUST

---

### RNF032 - Tempo de Aprendizado

Descrição: Usuário deve conseguir usar sistema sem treinamento extenso.

Mensuração:
- Setup completo: < 5 minutos
- Primeiro agendamento: < 10 minutos
- Satisfação (NPS): > 50

Critérios de Aceite:
- [ ] Onboarding guiado implementado
- [ ] Tooltips em campos críticos
- [ ] Vídeos tutoriais curtos (< 2min)
- [ ] Documentação de ajuda acessível
- [ ] Teste de usabilidade com usuários reais

Prioridade: MUST

---

### RNF033 - Clareza do Bot (WhatsApp)

Descrição: Conversação natural e fácil de entender.

Mensuração:
- Taxa de abandono: < 10%
- Taxa de confusão (fallback): < 5%
- Satisfação do cliente: > 4/5

Critérios de Aceite:
- [ ] Linguagem clara e objetiva
- [ ] Menus com no máximo 5 opções
- [ ] Botões de resposta rápida quando possível
- [ ] Mensagens de erro amigáveis
- [ ] Opção "falar com atendente" sempre visível

Prioridade: MUST

---

### RNF034 - Acessibilidade (Roadmap)

Descrição: Interface acessível para pessoas com deficiência.

Mensuração:
- Padrão: WCAG 2.1 nível AA
- Contraste: mínimo 4.5:1
- Navegação: 100% por teclado

Critérios de Aceite:
- [ ] Textos alternativos em imagens
- [ ] Navegação por teclado funcional
- [ ] Screen reader compatível
- [ ] Contraste validado (aXe DevTools)
- [ ] Formulários com labels adequados

Prioridade: COULD

---

## 7. Manutenibilidade

### RNF035 - Cobertura de Testes

Descrição: Código coberto por testes automatizados.

Mensuração:
- Cobertura total: > 70%
- Funções críticas: > 90%
- Testes: unitários, integração, e2e

Critérios de Aceite:
- [ ] Testes unitários para lógica de negócio
- [ ] Testes de integração para APIs
- [ ] Testes e2e para fluxos críticos
- [ ] Coverage report em CI/CD
- [ ] Build falha se cobertura < 70%

Prioridade: MUST

---

### RNF036 - Documentação de API

Descrição: API documentada em formato padrão.

Mensuração:
- Formato: OpenAPI 3.0 (Swagger)
- 100% dos endpoints documentados
- Exemplos de request/response

Critérios de Aceite:
- [ ] Spec OpenAPI gerada automaticamente
- [ ] Swagger UI acessível (/docs)
- [ ] Exemplos funcionais para cada endpoint
- [ ] Schemas de validação documentados
- [ ] Códigos de erro documentados

Prioridade: SHOULD

---

### RNF037 - CI/CD Automatizado

Descrição: Pipeline de deploy totalmente automatizado.

Mensuração:
- Tempo de deploy: < 10 minutos
- Taxa de sucesso: > 95%
- Rollback automático em falha

Critérios de Aceite:
- [ ] GitHub Actions configurado
- [ ] Deploy em staging automático (push main)
- [ ] Deploy em produção manual (tag)
- [ ] Testes executam antes de deploy
- [ ] Rollback em < 5 minutos

Prioridade: MUST

---

### RNF038 - Zero Downtime Deployment

Descrição: Deploys não devem causar indisponibilidade.

Mensuração:
- Downtime: 0 segundos
- Estratégia: Blue-Green ou Rolling Update
- Health check antes de switch

Critérios de Aceite:
- [ ] Lambda: versionamento automático
- [ ] Frontend: cache invalidation controlada
- [ ] DB migrations: backward compatible
- [ ] Health check validado antes de promover
- [ ] Conexões ativas drenam gracefully

Prioridade: SHOULD

---

### RNF039 - Versionamento de API

Descrição: APIs versionadas para evitar breaking changes.

Mensuração:
- Formato: /v1/, /v2/ no path
- Suporte: 2 versões simultâneas
- Deprecation notice: 6 meses

Critérios de Aceite:
- [ ] Versão no path da API
- [ ] Múltiplas versões coexistem
- [ ] Changelog documentado
- [ ] Deprecation warnings em responses
- [ ] Comunicação de breaking changes

Prioridade: SHOULD

---

## 8. Eficiência de Custo (TCO)

### RNF040 - Meta de Custo Mês 6

Descrição: Manter custos operacionais dentro do budget para validação.

Mensuração:
- Custo total: < R$ 400/mês
- Breakdown: Infra R$ 40, Comunicação R$ 213, Overhead R$ 99

Critérios de Aceite:
- [ ] Custos rastreados mensalmente
- [ ] AWS Cost Explorer configurado
- [ ] Alertas de budget configurados
- [ ] Otimizações implementadas se > R$ 400
- [ ] Relatório mensal de custos

Prioridade: MUST

---

### RNF041 - Meta de Custo Mês 12

Descrição: Custos escalando de forma controlada com receita.

Mensuração:
- Custo total: < R$ 2.500/mês
- MRR projetado: R$ 37.500
- Margem operacional: > 90%

Critérios de Aceite:
- [ ] Custos não excedem R$ 2.500/mês
- [ ] Custo por tenant < R$ 5
- [ ] Eficiência de WhatsApp API monitorada
- [ ] Otimização de cache (hit rate > 80%)
- [ ] Revisão de custos trimestral

Prioridade: MUST

---

### RNF042 - Meta de Custo Mês 24

Descrição: Operação em escala com TCO otimizado.

Mensuração:
- Custo total: < R$ 10.000/mês
- MRR projetado: R$ 250.000
- Margem operacional: > 95%

Critérios de Aceite:
- [ ] Custos não excedem R$ 10.000/mês
- [ ] Custo por tenant < R$ 3,50
- [ ] Migração para serviços gerenciados avaliada
- [ ] Reserved instances (se aplicável)
- [ ] Negociação de volume com WhatsApp provider

Prioridade: SHOULD

---

### RNF043 - Otimização de Cache

Descrição: Uso eficiente de cache para reduzir carga no DB.

Mensuração:
- Hit rate: > 80%
- TTL médio: 5 minutos
- Invalidação: < 1 segundo

Critérios de Aceite:
- [ ] Cache em endpoints de leitura frequente
- [ ] Cache-aside pattern implementado
- [ ] Invalidação em writes relevantes
- [ ] Métricas de hit/miss rate
- [ ] Warm-up de cache em deploy

Prioridade: MUST

---

## 9. Compatibilidade

### RNF044 - WhatsApp Business API

Descrição: Integração compatível com múltiplos provedores.

Mensuração:
- Providers suportados: Gupshup, Twilio
- Tempo de switch: < 4 horas
- Versionamento: Cloud API (latest)

Critérios de Aceite:
- [ ] Abstração de provider implementada
- [ ] Configuração via environment variables
- [ ] Testes com ambos os providers
- [ ] Documentação de migration
- [ ] Failover entre providers (roadmap)

Prioridade: MUST

---

### RNF045 - Navegadores Suportados

Descrição: Plataforma web compatível com navegadores modernos.

Mensuração:
- Chrome: últimas 2 versões
- Firefox: últimas 2 versões
- Safari: últimas 2 versões
- Edge: últimas 2 versões

Critérios de Aceite:
- [ ] Testes manuais em cada navegador
- [ ] Autoprefixer configurado (CSS)
- [ ] Polyfills para features modernas
- [ ] Detecção de navegador não suportado
- [ ] Mensagem amigável para browsers antigos

Prioridade: MUST

---

### RNF046 - Localização (PT-BR)

Descrição: Sistema totalmente localizado para português do Brasil.

Mensuração:
- Idioma: PT-BR
- Fuso: America/Sao_Paulo (BRT)
- Moeda: BRL (R$)
- Formato data: DD/MM/YYYY
- Formato hora: 24h

Critérios de Aceite:
- [ ] Todas as strings em PT-BR
- [ ] Timestamps em BRT
- [ ] Valores monetários com R$
- [ ] Datas formatadas corretamente
- [ ] Números com separador brasileiro (1.000,00)

Prioridade: MUST

---

## 10. Regulatório

### RNF047 - Retenção de Logs

Descrição: Logs operacionais retidos por período adequado.

Mensuração:
- Retenção: 90 dias
- Formato: JSON comprimido
- Storage: S3 Glacier (custo otimizado)

Critérios de Aceite:
- [ ] Logs rotacionados automaticamente
- [ ] Compressão antes de archive
- [ ] Lifecycle policy configurada (S3)
- [ ] Acesso via query (CloudWatch Insights)
- [ ] Política de retenção documentada

Prioridade: MUST

---

### RNF048 - Retenção de Dados de Agendamentos

Descrição: Dados de agendamentos retidos para fins fiscais/legais.

Mensuração:
- Retenção: 5 anos
- Backup: anual em archive
- Acesso: read-only após 1 ano

Critérios de Aceite:
- [ ] Dados históricos não deletados
- [ ] Particionamento por ano (performance)
- [ ] Archive após 1 ano (cold storage)
- [ ] Interface de consulta histórico
- [ ] Conformidade com legislação tributária

Prioridade: MUST

---

### RNF049 - Retenção de Audit Log

Descrição: Logs de auditoria retidos por período estendido.

Mensuração:
- Retenção: 7 anos
- Imutabilidade: append-only table
- Backup: trimestral em archive

Critérios de Aceite:
- [ ] Audit log separado de logs operacionais
- [ ] Write-only access para auditores
- [ ] Backup trimestral automático
- [ ] Criptografia em repouso
- [ ] Conformidade com requisitos legais

Prioridade: MUST

---

### RNF050 - Direito ao Esquecimento (LGPD)

Descrição: Exclusão de dados pessoais sob demanda do titular.

Mensuração:
- Prazo: < 72 horas
- Escopo: dados pessoais identificáveis
- Confirmação: email ao titular

Critérios de Aceite:
- [ ] Funcionalidade de exclusão de conta
- [ ] Anonimização de dados em agendamentos históricos
- [ ] Logs de exclusão registrados
- [ ] Email de confirmação enviado
- [ ] Processo documentado

Prioridade: MUST

---

## Anexos

### A. Glossário

| Termo | Definição |
|-------|-----------|
| p50/p95/p99 | Percentis de latência (50%, 95%, 99% das requisições) |
| TTL | Time To Live - tempo de vida de cache/token |
| MTTR | Mean Time To Recovery - tempo médio para recuperação |
| RPO | Recovery Point Objective - perda máxima de dados aceitável |
| RTO | Recovery Time Objective - tempo máximo para restore |
| SLA | Service Level Agreement - acordo de nível de serviço |
| TCO | Total Cost of Ownership - custo total de propriedade |
| DLQ | Dead Letter Queue - fila de mensagens que falham |

### B. Matriz de Rastreabilidade

| RNF | Fluxo Relacionado | Prioridade |
|-----|-------------------|------------|
| RNF001-RNF003 | Todos | MUST |
| RNF013 | Fluxo 5 Cliente - Conflito de horário | MUST |
| RNF016-RNF024 | Fluxo 1 Config - Login | MUST |
| RNF025-RNF030 | Operações (todos) | MUST |
| RNF033 | Todos fluxos Cliente | MUST |

### C. Plano de Evolução

Fase 1 (Mês 0-6) - MVP:
- Foco: RNFs prioritários (MUST)
- Infraestrutura: VPS + Free Tier AWS
- Observabilidade: Básica (CloudWatch)

Fase 2 (Mês 6-12) - Validação:
- Adicionar: RNFs SHOULD
- Migração: VPS → RDS (se necessário)
- Observabilidade: Avançada (Grafana)

Fase 3 (Mês 12-24) - Escala:
- Adicionar: RNFs COULD
- Multi-região: Avaliação
- HA: Redundância completa