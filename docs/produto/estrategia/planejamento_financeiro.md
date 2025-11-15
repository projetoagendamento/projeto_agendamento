---
sidebar_position: 3
---

# Planejamento financeiro

## 1. Contexto e Premissas

Este planejamento financeiro projeta a viabilidade econômica da plataforma em três horizontes temporais: curto prazo (6 meses), médio prazo (12 meses) e longo prazo (24 meses), permitindo avaliar a evolução do negócio desde o lançamento do MVP até a consolidação no mercado. 

As premissas macroeconômicas consideram inflação anual de 4,5% (meta IPCA 2025), variação cambial moderada (R$ 5,00-5,50/USD para custos de infraestrutura) e crescimento do setor de serviços de 2,5% a.a., refletindo um cenário conservador para o mercado brasileiro. 

O perfil do cliente-alvo permanece focado nas três personas documentadas: Dono do Estabelecimento (decisor de compra, busca ROI em redução de no-shows e automação), Funcionário (usuário final, valoriza simplicidade operacional) e Cliente Final (beneficiário indireto, espera conveniência no agendamento via WhatsApp). 

A estratégia de Go-to-Market prioriza canais de baixo CAC nos primeiros 6 meses (SEO, conteúdo orgânico, parcerias estratégicas), transitando para investimento controlado em mídia paga (Google Ads, Meta) a partir do 7º mês, com foco em conversão de trials gratuitos de 21 dias em assinaturas recorrentes, aproveitando o diferencial competitivo de WhatsApp 100% incluído e pricing transparente para capturar clientes frustrados com a complexidade e custos ocultos dos concorrentes (Booksy R$ 99,99 sem WhatsApp, Fresha TCO R$ 565, Trinks/Frizzar com pricing opaco).

## 2. Glossário de Termos Financeiros

**CAC (Customer Acquisition Cost)** representa o custo total para adquirir um novo cliente pagante, calculado pela soma de investimentos em marketing, vendas e onboarding dividido pelo número de clientes convertidos no período. No contexto da plataforma, inclui gastos com Google Ads, Meta, parcerias, produção de conteúdo e horas de suporte durante trials, com meta de manter CAC inferior a 1,5x o MRR médio para garantir retorno viável do investimento em aquisição.

**LTV (Lifetime Value)** é o valor total que um cliente gera durante todo seu relacionamento com a plataforma, calculado como (Ticket Médio Mensal × Margem Bruta) / Churn Rate Mensal. Para sustentabilidade financeira, o ratio LTV/CAC deve ser superior a 3:1, garantindo que cada real investido em aquisição retorne pelo menos três reais em valor ao longo do tempo, validando a eficiência do modelo de negócio.

**MRR (Monthly Recurring Revenue) e ARR (Annual Recurring Revenue)** mensuram a receita recorrente previsível do negócio. O MRR soma todas as assinaturas ativas no mês (exemplo: 100 clientes × R$ 70 médio = R$ 7.000 MRR), enquanto o ARR multiplica o MRR por 12 para projeção anual. Essas são métricas críticas para avaliação de crescimento, previsibilidade de caixa e valoração da empresa por investidores.

**Churn Rate** indica a taxa de cancelamento de clientes, calculada como (Clientes Perdidos no Período / Clientes no Início do Período) × 100. Um churn mensal saudável para SaaS B2B voltado a pequenos negócios é de 5-8%, enquanto o churn anual deve ficar abaixo de 50% para garantir crescimento líquido sustentável. Taxas superiores indicam problemas de product-market fit ou satisfação do cliente.

**Burn Rate** mede a velocidade de consumo de capital, calculando quanto dinheiro a empresa gasta por mês além da receita gerada (Custos Totais - Receita Total). Esse indicador define o "runway" ou quantos meses a operação pode sobreviver com o capital disponível antes de atingir break-even ou necessitar nova rodada de captação. É calculado como: Runway (meses) = Caixa Disponível / Burn Rate Mensal.

**Break-even Point** é o momento em que as receitas igualam os custos totais (MRR = Custos Fixos + Custos Variáveis), quando a empresa deixa de queimar capital e passa a ser autossustentável operacionalmente. Para SaaS com bom product-market fit e execução disciplinada, esse ponto tipicamente ocorre entre 12-18 meses após o lançamento, dependendo do crescimento de MRR e controle de custos.

**Margem de Contribuição** representa quanto cada cliente contribui para cobrir custos fixos e gerar lucro, calculada como (Receita por Cliente - Custos Variáveis Diretos) / Receita por Cliente × 100. Margens acima de 70% são consideradas saudáveis para modelos SaaS, permitindo absorver o CAC em poucos meses, cobrir custos fixos estruturais e ainda reinvestir em crescimento sustentável do negócio.

## 3. Estrutura de Custos da Solução

### 3.1. Visão Geral da Arquitetura Econômica

A solução foi desenhada para operar em modo **ultra-econômico**, priorizando *bootstrapping* e maximização de runway. A estratégia é:

* Manter **infraestrutura técnica com custo mínimo absoluto**, aceitando maior responsabilidade operacional no início.
* Concentrar os principais gastos naquilo que gera valor direto para o cliente: **comunicação via WhatsApp**.
* Planejar desde o início um caminho de migração gradual para serviços gerenciados, quando a receita justificar.

Em alto nível, a arquitetura se baseia em:

1. **VPS de baixo custo** para banco de dados (PostgreSQL) e cache (Redis).
2. **AWS** apenas nos componentes com **free tier permanente** ou custo marginal muito baixo (Lambda, SQS, SES, S3).
3. **Vercel** para hospedagem de frontend dentro do plano gratuito.
4. Comunicação via **WhatsApp Business API centralizado da plataforma**, com desenho de fluxo otimizado para reduzir o número de conversas.

Assim, a infra técnica "bruta" torna-se barata a ponto de os custos significativos estarem quase todos concentrados na camada de comunicação.

---

### 3.2. Blocos de Custo

Para facilitar a análise e o acompanhamento, os custos são agrupados em quatro blocos:

1. Infraestrutura técnica
2. Comunicação (WhatsApp, e-mail, SMS)
3. Ferramentas/SaaS auxiliares
4. Overhead administrativo e operacional

Os números abaixo consideram três horizontes temporais:

* **Mês 6** – validação inicial
* **Mês 12** – produto validado com tração
* **Mês 24** – operação em escala relevante

> Valores em USD são usados para referência de fornecedores; entre parênteses apresenta-se o equivalente aproximado em BRL.

---

#### 3.2.1. Infraestrutura Técnica

A infraestrutura técnica segue a filosofia de minimizar custos fixos:

* **Banco de Dados + Redis:**
  Hospedados em um único **VPS** em provedor de baixo custo (por exemplo, Hetzner).

  * Mês 6: VPS com 2 vCPU e 4 GB RAM
  * Mês 12: VPS com 2 vCPU e 8 GB RAM
  * Mês 24: VPS com 4 vCPU e 16 GB RAM
    Inclui armazenamento em SSD e backups via snapshots + dump para S3.

* **Lógica de aplicação (backend):**
  Implementada em **AWS Lambda**, dimensionada para permanecer **dentro do free tier permanente**, mesmo na projeção de Mês 24.

* **Frontend:**
  Hospedagem em **Vercel (Free Tier)** com Next.js, com tráfego estimado abaixo do limite de banda gratuito.

* **Filas e orquestração assíncrona:**
  Uso de **AWS SQS**, também mantido dentro do free tier nos horizontes considerados.

* **Storage:**

  * **S3** para backups críticos do banco.
  * **Storage externo econômico** (por exemplo, Hetzner Storage Box) para arquivos de usuário (imagens, assets).

**Resumo de custos de infraestrutura técnica:**

| Horizonte | VPS (DB + Redis) | Storage e demais | **Total Infra (USD/mês)** | **Total Infra (BRL/mês)** |
| --------- | ---------------- | ---------------- | ------------------------- | ------------------------- |
| Mês 6     | ~US$ 7,0         | ~US$ 0,5         | **~US$ 7,5**              | **~R$ 40**                |
| Mês 12    | ~US$ 11,5        | ~US$ 1,5         | **~US$ 13**               | **~R$ 70**                |
| Mês 24    | ~US$ 21          | ~US$ 4,0         | **~US$ 25**               | **~R$ 130**               |

Conclusão deste bloco:
**a infraestrutura técnica não é o fator limitante de custo**. Mesmo em Mês 24, permanece em ordem de grandeza muito baixa.

---

#### 3.2.2. Comunicação (WhatsApp, E-mail, SMS)

Este bloco concentra os custos mais relevantes e está diretamente ligado ao uso real da plataforma (agendamentos, lembretes e interações).

A arquitetura de comunicação considera:

* **WhatsApp Business API único da plataforma** como canal principal (via provedor como Gupshup/Twilio).
* **Fluxos de mensagens consolidados**, reduzindo o número de conversas por agendamento (aprox. 4 conversas por agendamento).
* **E-mail (Amazon SES)** como canal de backup e notificações administrativas.
* **SMS (Amazon SNS ou similar)** apenas para casos críticos (falhas em WhatsApp, indisponibilidades, etc.).

Projeções de custos de **WhatsApp**:

| Horizonte | Agendamentos/mês | Conversas/mês (≈4/agendamento) | Custo/conversa | Taxa fixa número | **Total WhatsApp (USD/mês)** |
| --------- | ---------------- | ------------------------------ | -------------- | ---------------- | ---------------------------- |
| Mês 6     | ~1.400           | ~5.700                         | US$ 0,006      | ~US$ 6           | **~US$ 40**                  |
| Mês 12    | ~14.800          | ~59.000                        | US$ 0,006      | ~US$ 6           | **~US$ 361**                 |
| Mês 24    | ~66.000          | ~265.000                       | US$ 0,006      | ~US$ 6           | **~US$ 1.594**               |

E-mail (SES) e SMS aparecem como complementares:

* SES: custos de centavos a poucos dólares.
* SMS: volume muito baixo pela estratégia de uso restrito (1% dos agendamentos ou menos).

Assim, para simplificação:

| Horizonte | WhatsApp   | E-mail + SMS | **Total Comunicação (USD/mês)** | **Total Comunicação (BRL/mês)** |
| --------- | ---------- | ------------ | ------------------------------- | ------------------------------- |
| Mês 6     | ~US$ 40    | ~US$ 1       | **~US$ 41**                     | **~R$ 213**                     |
| Mês 12    | ~US$ 361   | ~US$ 9       | **~US$ 370**                    | **~R$ 1.925**                   |
| Mês 24    | ~US$ 1.594 | ~US$ 42      | **~US$ 1.636**                  | **~R$ 8.507**                   |

**Conclusão deste bloco:**
Mesmo em arquitetura ultra-econômica, **comunicação continua sendo o principal centro de custo**, representando a maior parte das despesas operacionais à medida que a base de clientes cresce. Isso é alinhado ao negócio, pois esse custo está diretamente ligado ao valor percebido (agendamentos e lembretes efetivos).

---

#### 3.2.3. Ferramentas e SaaS Auxiliares

A solução faz uso intensivo de **ferramentas em free tier** ou gratuitas:

* Observabilidade: CloudWatch (AWS), Grafana Cloud free, Sentry free, UptimeRobot free.
* DevOps: GitHub (repositório + CI/CD), Terraform, Docker Hub (plano gratuito).
* DNS e CDN: Cloudflare free.
* SSL: Let's Encrypt.

O único custo recorrente relevante neste bloco é:

* **Domínio** (por exemplo, .com via Namecheap): ~US$ 10/ano ≈ **US$ 0,83/mês** (~R$ 4,50/mês).

Portanto, este bloco se mantém praticamente constante e irrelevante em termos de impacto total.

---

#### 3.2.4. Overhead Administrativo e Buffer

Este bloco inclui:

* **Contabilidade** (MEI e posterior migração para ME/Simples).
* **Tributos fixos** (DAS MEI etc.).
* **Buffer de contingência** (10% sobre custos técnicos) para absorver variações e imprevistos.

Ordem de grandeza:

| Horizonte | Contábil + Legal (USD/mês) | Buffer 10% | **Total Overhead (USD/mês)** | **Total Overhead (BRL/mês)** |
| --------- | -------------------------- | ---------: | ---------------------------- | ---------------------------- |
| Mês 6     | ~US$ 14                    |     ~US$ 5 | **~US$ 19**                  | **~R$ 99**                   |
| Mês 12    | ~US$ 43                    |    ~US$ 38 | **~US$ 81**                  | **~R$ 421**                  |
| Mês 24    | ~US$ 85                    |   ~US$ 166 | **~US$ 251**                 | **~R$ 1.305**                |

---

### 3.3. Consolidado de Custos

Reunindo os quatro blocos (infra + comunicação + ferramentas + overhead):

| Horizonte  | Infra (USD) | Comunicação (USD) | Ferramentas (USD) | Overhead (USD) | **Total (USD/mês)** | **Total (BRL/mês)** |
| ---------- | ----------- | ----------------- | ----------------- | -------------- | ------------------- | ------------------- |
| **Mês 6**  | ~7,5        | ~41               | ~0,8              | ~19            | **~US$ 68**         | **~R$ 354**         |
| **Mês 12** | ~13         | ~370              | ~0,8              | ~81            | **~US$ 465**        | **~R$ 2.418**       |
| **Mês 24** | ~25         | ~1.636            | ~0,8              | ~251           | **~US$ 1.913**      | **~R$ 9.942**       |

Principais conclusões desta seção:

1. **Infraestrutura técnica é barata** e permanece sob controle mesmo em Mês 24.
2. **Comunicação (principalmente WhatsApp)** é o verdadeiro motor de custo variável.
3. Overhead cresce com formalização e escala, mas continua pequeno frente à receita projetada.
4. O custo total projetado para Mês 24 (~R$ 9,9 mil/mês) é baixo para o nível de receita estimada.

---

## 4. Receita, Margem e Unit Economics

### 4.1. Modelo de Pricing

O modelo de monetização é baseado em **cobrança por profissional ativo**:

> **Preço por profissional:** R$ 29,90 / mês
> (modelo "seat-based", linear e transparente)

Não há tabelas complexas de planos (básico/pró/enterprise) nem limites rígidos de agendamentos. Isso facilita:

* Comunicação do valor para o cliente final.
* Crescimento orgânico de receita à medida que cada estabelecimento adiciona mais profissionais.

---

### 4.2. Custo por Cliente e por Profissional

A partir dos custos mapeados (em especial WhatsApp + diluição de custos fixos), obtém-se a seguinte ordem de grandeza:

#### 4.2.1. Custo total por tenant

| Horizonte | Custo variável por tenant | Custo fixo diluído por tenant | **Custo total por tenant** |
| --------- | ------------------------- | ----------------------------- | -------------------------- |
| Mês 6     | ~R$ 5,13                  | ~R$ 5,08                      | **~R$ 10,21**              |
| Mês 12    | ~R$ 5,13                  | ~R$ 4,75                      | **~R$ 9,88**               |
| Mês 24    | ~R$ 5,13                  | ~R$ 2,31                      | **~R$ 7,44**               |

Com o crescimento da base, os custos fixos são mais bem diluídos, reduzindo o custo total por tenant.

#### 4.2.2. Custo por profissional

Considerando que o número médio de profissionais por tenant aumenta ao longo do tempo (por exemplo, de ~1,8 para ~2,4), chega-se a:

| Horizonte | Custo total por tenant | Profissionais/tenant (média) | **Custo por profissional/mês** |
| --------- | ---------------------- | ---------------------------- | ------------------------------ |
| Mês 6     | ~R$ 10,21              | ~2,2                         | **~R$ 4,64**                   |
| Mês 12    | ~R$ 9,88               | ~2,2                         | **~R$ 4,49**                   |
| Mês 24    | ~R$ 7,44               | ~2,2–2,4                     | **~R$ 3,14–3,38**              |

Comparando com o preço de **R$ 29,90/profissional**, a margem bruta por profissional é bastante elevada.

---

### 4.3. Projeção de Receita Recorrente

Com base nas projeções de crescimento de clientes (tenants) e número de profissionais ativos, a receita recorrente (MRR) é estimada da seguinte forma:

#### 4.3.1. MRR Base (apenas assinatura por profissional)

| Horizonte | Profissionais ativos (aprox.) | Preço/profissional | **MRR Base (R$/mês)** |
| --------- | ----------------------------- | ------------------ | --------------------- |
| Mês 6     | ~130                          | R$ 29,90           | **~R$ 3.900**         |
| Mês 12    | ~1.250                        | R$ 29,90           | **~R$ 37.500**        |
| Mês 24    | ~7.600                        | R$ 29,90           | **~R$ 227.000**       |

A partir de Mês 18, a documentação original considera ainda:

* **Expansion revenue** (clientes adicionando mais profissionais).
* **Add-ons** (pagamentos online, API, white-label, multi-unidade).

Incluindo esses itens, a projeção aponta para:

* **MRR total aproximado no Mês 24:** ~**R$ 250 mil/mês**
* **Receita total (incluindo serviços pontuais):** ~**R$ 263 mil/mês**

Para fins de estudo e viabilidade, o ponto central é que, com ~7–8 mil profissionais ativos, a solução atinge a casa de **R$ 3 milhões/ano de receita recorrente (ARR)**.

---

### 4.4. Receita x Custos: Foto em 3 Horizontes

A seguir, uma visão simplificada de **Receita x Custos**, desconsiderando por enquanto gastos de marketing (CAC) para isolar o "motor do produto".

#### 4.4.1. Mês 6 – Validação

* **Receita total estimada:** ~**R$ 4.200/mês**
* **Custos totais:** ~**R$ 354/mês**

Mesmo que se inclua um nível moderado de marketing, o projeto se aproxima rapidamente do **ponto de equilíbrio operacional**. Nessa fase, o objetivo principal é validação de produto e uso real, com risco financeiro baixo.

---

#### 4.4.2. Mês 12 – Tração comprovada

* **Receita total estimada:** ~**R$ 40.900/mês**
* **Custos totais:** ~**R$ 2.418/mês**

Neste estágio:

* A **infraestrutura está amplamente paga** pela receita.
* Existe margem suficiente para:

  * investir em aquisição de clientes (ads, parcerias);
  * começar a profissionalizar partes da operação (contabilidade, suporte, etc.).

---

#### 4.4.3. Mês 24 – Escala relevante

* **Receita total estimada:** ~**R$ 263.000/mês**
* **Custos totais:** ~**~R$ 9.942/mês**

Mesmo considerando futuros incrementos de custo (equipe, marketing, ferramentas pagas), a **margem operacional potencial é muito alta**.

Conclusão:
A solução, se atingir as metas de adoção projetadas, **não é limitada por custo de infraestrutura**. O fator crítico passa a ser **aquisição e retenção de clientes**.

---

### 4.5. Unit Economics Resumidos

Com os números descritos, pode-se resumir a economia por unidade (profissional) da seguinte forma, em Mês 24:

* **Preço por profissional:** R$ 29,90/mês
* **Custo total por profissional:** ~R$ 3,14/mês
* **Margem bruta por profissional:** ~89%

Considerando um churn mensal em torno de 3,5%:

* **Lifetime médio estimado:** ~28 meses
* **LTV por profissional:** ~R$ 750

Se o custo médio de aquisição (CAC) por profissional for mantido na faixa de R$ 100–120, obtém-se:

* **LTV/CAC entre 6,2x e 7,5x** (faixa excelente para SaaS).
* **Payback de CAC em 4–5 meses**.

Esses indicadores mostram um modelo de negócio com **unit economics saudáveis**, desde que as premissas de conversão e churn sejam razoavelmente próximas do esperado.

## 5. Análise de Viabilidade

### 5.1. Ponto de equilíbrio (break-even)

O ponto de equilíbrio operacional é atingido quando a receita recorrente mensal cobre:

* custos fixos (infraestrutura, comunicação mínima, overhead) e
* custos variáveis por profissional/tenant.

Com base nas estimativas:

* **Custo total mensal (Mês 6):** ~R$ 354
* **Ticket médio por profissional:** R$ 29,90/mês

O break-even operacional ocorre aproximadamente em:

* **≈ 23 profissionais ativos** (algo em torno de 10–12 tenants pequenos).

Nos horizontes posteriores:

* **Mês 12:** a base projetada ultrapassa amplamente o break-even (centenas de tenants).
* **Mês 24:** o projeto opera muito acima do ponto de equilíbrio, com folga de margem para absorver CAC, equipe e upgrades de infraestrutura.

Em outras palavras, **o risco de não atingir o break-even é concentrado nos primeiros meses**; uma vez atingido um pequeno volume de clientes pagantes, o modelo tende a se sustentar com facilidade.

---

### 5.2. Runway (quantos meses de operação com capital atual)

A análise de runway depende diretamente do capital disponível. A documentação parte do princípio de que:

* Os **custos técnicos e administrativos iniciais são muito baixos** (ordem de centenas de reais/mês).
* O crescimento de custos é relativamente suave até que a receita seja suficiente para cobri-los.

De forma ilustrativa:

* Com **R$ 10.000 de caixa**, e custo técnico total próximo de **R$ 600/mês** nos primeiros meses (incluindo algum marketing leve), o projeto teria, **sem nenhuma receita**, algo como **16+ meses de runway**.
* Na prática, como o modelo prevê receita já a partir dos primeiros clientes, a **runway real tende a ser maior**, porque parte dos custos começa a ser coberta pelo próprio negócio a partir do Mês 6–12.

A mensagem principal é que a **arquitetura econômica aumenta significativamente a runway**, reduzindo a pressão por capital externo na fase de validação.

---

### 5.3. ROI projetado

O ROI (retorno sobre o investimento) pode ser analisado em duas camadas:

1. **ROI operacional do produto (unit economics)**

   * Custo total por profissional (Mês 24): ~R$ 3,14/mês
   * Receita por profissional: R$ 29,90/mês
   * **Margem bruta:** ~89%

   Esse diferencial de margem indica que, depois de pago o CAC, cada cliente tende a gerar um fluxo de caixa altamente positivo ao longo de sua vida útil.

2. **ROI do projeto em 24 meses (visão macro)**
   Considerando:

   * MRR projetado ~**R$ 250.000** no Mês 24
   * Custos técnicos + overhead ~**R$ 9.942/mês**
   * Mesmo com adição posterior de marketing e equipe, o resultado tende a ser **fortemente positivo**, com boa capacidade de reinvestimento e/ou distribuição de lucro.

Assim, o projeto se caracteriza como **alto ROI potencial**, desde que as premissas de adoção, churn e CAC se mantenham em níveis próximos aos estimados.

---

### 5.4. Margem de contribuição por "plano" (perfil de cliente)

Embora o pricing seja linear por profissional, é útil analisar a **margem de contribuição por perfil de uso**, por exemplo:

#### 5.4.1. Cliente pequeno – 1 profissional

* Receita: R$ 29,90/mês
* Custo estimado por profissional (Mês 24): ~R$ 3,14
* **Margem de contribuição:** ~R$ 26,76 (≈ 89%)

#### 5.4.2. Cliente típico – 3 profissionais

* Receita: 3 × R$ 29,90 = **R$ 89,70**
* Custo total estimado: ~3 × R$ 3,14 = **R$ 9,42**
* **Margem de contribuição:** ~R$ 80,28

#### 5.4.3. Cliente em crescimento – 5 profissionais

* Receita: 5 × R$ 29,90 = **R$ 149,50**
* Custo estimado: ~5 × R$ 3,14 = **R$ 15,70**
* **Margem de contribuição:** ~R$ 133,80

A contribuição cresce de forma quase linear com o número de profissionais, o que incentiva **expansão dentro da base existente** (clientes adicionando seats ao longo do tempo).

---

## 6. Métricas de Saúde do Negócio

### 6.1. LTV/CAC ratio

O LTV/CAC é um dos principais indicadores de saúde em SaaS.

* **LTV por profissional (Mês 24):** ~R$ 750 (considerando margem bruta ~89% e churn ~3,5%/mês).
* **CAC estimado médio:** ~R$ 120 por profissional (mistura de orgânico, paid e referrals).

Portanto:

> **LTV/CAC ≈ 6,2x**

Como referência, valores **acima de 3x** já são considerados saudáveis em SaaS. O projeto, nas premissas atuais, opera em faixa considerada **excelente**.

---

### 6.2. Payback period do CAC

O payback do CAC indica em quantos meses o investimento para adquirir um cliente é recuperado via margem bruta.

* Receita mensal por profissional: R$ 29,90
* Margem bruta: ~89% → ~R$ 26,76 de margem por mês
* CAC: ~R$ 120

Logo:

> **Payback ≈ 120 / 26,76 ≈ 4–5 meses**

Benchmarks de mercado apontam que:

* Payback **< 12 meses** é saudável;
* Payback **< 6 meses** é excelente.

Assim, o payback projetado posiciona o negócio em um patamar **de alta eficiência em aquisição**.

---

### 6.3. Net Revenue Retention (NRR)

A **Net Revenue Retention (NRR)** mede quanto da receita recorrente é mantida e expandida ao longo do tempo, considerando:

* expansão (clientes adicionando profissionais ou add-ons),
* contração,
* churn.

Com base nas premissas:

* Parte da base adiciona profissionais ao longo do tempo (expansion revenue).
* Existe churn mensal em torno de 3,5%.
* Há planos de add-ons a partir de Mês 18 (pagamentos online, API etc.).

A NRR projetada, em regime mais maduro (após Mês 18–24), tende a ficar em uma faixa de:

> **NRR anual projetada na ordem de 110–120%**

Ou seja, mesmo considerando perdas (churn), a receita recorrente líquida se mantém e cresce dentro da própria base, o que é um sinal importante de **fit de produto e valor percebido**.

---

### 6.4. Quick Ratio (crescimento / churn)

O **SaaS Quick Ratio** mede a qualidade do crescimento da receita recorrente:

> Quick Ratio = (Nova MRR + Expansion + Reactivation) / (Churn + Contraction)

Embora o valor exato dependa da dinâmica real mês a mês, a projeção conceitual é:

* Crescimento relevante em MRR nos primeiros 24 meses.
* Churn controlado (~3,5%) e com algum nível de expansão dentro da base.

Em um cenário dentro das premissas desta documentação, o Quick Ratio tende a se situar em uma faixa:

> **Quick Ratio na ordem de 4–5**

Valores acima de 4 já indicam **crescimento saudável**, em que a nova receita adicionada supera com folga a receita perdida por churn/contraction.

---

## 7. Análise de Sensibilidade

A análise de sensibilidade ajuda a entender como pequenas variações em parâmetros críticos afetam o negócio.

### 7.1. Impacto de variações no churn (+/- 5%)

Considerando um cenário base com **churn mensal de 3,5%**:

* **Cenário melhor (churn reduzido para ~2%):**

  * A vida útil média do cliente aumenta.
  * O LTV cresce de aproximadamente **R$ 750 para algo acima de R$ 980–1.270**.
  * O LTV/CAC sobe para faixas superiores a **8–10x**.

* **Cenário pior (churn elevado para ~5%):**

  * A vida útil média cai.
  * O LTV tende a cair para a faixa de **R$ 490**.
  * O LTV/CAC cai para algo próximo de **4x**, ainda aceitável, mas com menos folga.

Conclusão:
O negócio é **bastante sensível ao churn**. Pequenas melhorias de retenção geram ganho expressivo em LTV; por outro lado, aumento de churn reduz a gordura para investir em marketing e expansão.

---

### 7.2. Impacto de variações no CAC (+/- 20%)

Tomando o **CAC base de R$ 120/profissional**:

* **Cenário otimista (CAC -20% → ~R$ 96):**

  * LTV/CAC sobe para ~7,8x.
  * Payback cai para algo em torno de **3–4 meses**.
  * Há maior liberdade para acelerar aquisição de clientes.

* **Cenário pessimista (CAC +20% → ~R$ 144):**

  * LTV/CAC cai para ~5,2x.
  * Payback sobe para algo em torno de **5–6 meses**.
  * Ainda saudável, mas com necessidade de maior disciplina em campanhas.

Conclusão:
O modelo comporta variações moderadas de CAC sem "quebrar" a lógica de negócio, graças à margem bruta alta. Ainda assim, monitorar CAC por canal é essencial para manter a eficiência.

---

### 7.3. Impacto de variações no pricing (+/- 15%)

Considerando o preço base de **R$ 29,90/profissional**:

* **Redução de preço (~R$ 25,40 / -15%):**

  * LTV cai proporcionalmente, mas ainda permanece acima de R$ 637 (com margens altas).
  * Pode aumentar a taxa de conversão e reduzir sensibilidade a preço, especialmente para profissionais solo.

* **Aumento de preço (~R$ 34,40 / +15%):**

  * LTV aumenta, mas há risco de:

    * redução na taxa de conversão trial→pago;
    * aumento de churn em clientes mais sensíveis a preço.

Nesse ponto, o equilíbrio ideal tende a ser:

> Manter o preço base de R$ 29,90 no início,
> e testar pequenos ajustes (ou bundles/add-ons) apenas após validar o comportamento real de churn e conversão.

---

## 8. Riscos Financeiros e Mitigações

### 8.1. Dependência de API WhatsApp (custos variáveis)

**Risco:**
A principal despesa variável está concentrada nas conversas de WhatsApp Business API. Alterações de preço pela Meta ou pelo provedor podem impactar diretamente a margem.

**Mitigações:**

* Otimização contínua de fluxos para reduzir conversas desnecessárias (consolidação de mensagens, reuso de janelas de 24h).
* Negociação com mais de um provedor (multi-homing) para reduzir risco de concentração.
* Possível criação de limites internos de uso por cliente em planos específicos (se necessário).

---

### 8.2. Elasticidade-preço do mercado

**Risco:**
O público-alvo (profissionais de serviços, pequenos estabelecimentos) pode ser sensível a aumentos de preço mensais, especialmente em fases iniciais de adoção digital.

**Mitigações:**

* Manter pricing simples e transparente (R$/profissional) com forte argumentação de valor (redução de no-show, ganho de tempo, aumento de receita).
* Testar ajustes de preço apenas quando houver dados sólidos de retenção e NPS.
* Explorar descontos por tempo limitado ou campanhas de onboarding para reduzir atrito inicial.

---

### 8.3. Concorrência agressiva em pricing

**Risco:**
Concorrentes podem praticar descontos agressivos, planos freemium ou bundling com outros serviços, pressionando o preço médio do mercado.

**Mitigações:**

* Diferenciação clara em **experiência via WhatsApp**, automação e simplicidade.
* Foco em **TCO (custo total de propriedade)**, mostrando que concorrentes podem parecer mais baratos na mensalidade, mas cobram por mensagens ou add-ons essenciais.
* Uso da alta margem bruta para, se necessário, responder taticamente com promoções segmentadas, sem comprometer a saúde financeira.

---

### 8.4. Sazonalidade (se aplicável)

**Risco:**
Alguns segmentos atendidos (por exemplo, estética, saúde, educação) podem apresentar sazonalidade, com meses de pico e meses de menor volume de agendamentos.

**Mitigações:**

* Diversificar segmentos atendidos (beleza, saúde, bem-estar, serviços recorrentes) para suavizar a sazonalidade global.
* Oferecer recursos que incentivem o uso em períodos de baixa (campanhas, promoções, pacotes, lembretes de retorno).
* Manter estrutura de custos fixa enxuta, de forma que oscilações moderadas de receita não comprometam a operação.