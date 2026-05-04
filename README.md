# 🧑‍💻 Sistema de Tickets e Ordem de Serviço para Assistência Técnica

## 📌 Visão Geral

Este documento descreve de forma completa o conceito, estrutura e boas práticas para a implementação e uso de um sistema de **tickets** e **ordens de serviço (OS)** em assistências técnicas de qualquer segmento (informática, eletrônicos, eletrodomésticos, automotivo, etc.).

O objetivo é padronizar o atendimento, melhorar a rastreabilidade dos serviços e garantir organização operacional.

---

## 📋 Sumário
1. [Visão Geral do Sistema](#visão-geral-do-sistema)
2. [Fluxo de Atendimento (Workflow)](#fluxo-de-atendimento-workflow)
3. [Estrutura de Dados](#estrutura-de-dados)
4. [Módulos do Sistema](#módulos-do-sistema)
5. [Exemplo de Ordem de Serviço (OS)](#exemplo-de-ordem-de-serviço-os)
6. [Boas Práticas de Implementação](#boas-práticas-de-implementação)
7. [Contribuição](#contribuição)

---
Descrição da Automação
O sistema automatiza o ciclo de vida do atendimento técnico, permitindo que a equipe foque na execução do serviço enquanto a parte burocrática é executada em segundo plano.

Principais benefícios da automação:

Agilidade: Geração automática da Ordem de Serviço em formato de texto estruturado.

Comunicação: Envio instantâneo de atualizações para o cliente por meio de canais de mensagem.

Organização: Atualização em tempo real do banco de dados e controle do status do equipamento.

---
⚙️Explicação do Fluxo criado no n8n
O workflow no n8n é dividido em quatro etapas executadas sequencialmente:

Webhook (Trigger de Entrada): Recebe o evento de abertura ou atualização de um ticket (ex: "Diagnóstico realizado").

Transformação de Dados (Code): Concatena as informações do cliente, equipamentos e peças utilizadas no formato de Ordem de Serviço textual.

Consulta de Serviços (Switch/Router): Define a próxima ação com base no status atual (ex: enviar orçamento ou iniciar o serviço).

Notificação / Documentação: Exporta o arquivo para PDF e dispara a notificação para o cliente via API de comunicação.

---

## 🎯 Objetivos do Sistema

- Centralizar o registro de atendimentos
- Organizar demandas técnicas
- Acompanhar o status dos serviços
- Melhorar a comunicação com o cliente
- Gerar histórico e relatórios
- Aumentar a produtividade da equipe

---

## 🧾 Conceitos Fundamentais

### 🎫 Ticket

Um **ticket** representa uma solicitação inicial de atendimento. Pode ser aberto por:

- Cliente
- Atendimento interno
- Sistema automático

#### Características:
- Registro inicial do problema
- Pode ou não virar uma ordem de serviço
- Usado para triagem e suporte rápido

---

### 🛠️ Ordem de Serviço (OS)

A **ordem de serviço** é o documento formal que autoriza e descreve a execução de um serviço técnico.

#### Características:
- Contém informações detalhadas
- Representa um serviço em execução
- Pode estar vinculada a um ticket

---
## Fluxo do atendimento

1. CLIENTE REGISTRA TICKET
   ↓
2. TÉCNICO ANALISA E CRIA OS
   ↓
3. ORÇAMENTO APROVADO
   ↓
4. EXECUÇÃO DO SERVIÇO
   ↓
5. FATURAMENTO E CONCLUIÇÃO
   ↓
6. AVALIAÇÃO DO CLIENTE

## 🔄 Fluxo de Atendimento (Workflow)

O ciclo de vida do atendimento é dividido em 5 etapas principais:

1. **Abertura do Ticket:** Registro da solicitação pelo cliente via canal (telefone, e-mail ou portal).
2. **Triagem e Diagnóstico:** Análise inicial da equipe de suporte para definir a prioridade e natureza do problema.
3. **Aprovação de Orçamento:** Caso o reparo exija peças não cobertas ou seja fora de garantia, é gerado um orçamento para aprovação.
4. **Execução do Serviço:** O técnico realiza os procedimentos necessários (reparo, troca de componentes, testes).
5. **Encerramento e Feedback:** Finalização da OS, baixa no estoque de peças e pesquisa de satisfação.

---


- ID: TCK-001
- Cliente: Mirela Dornes
- Contato: Mirela Dornes@email.com
- Problema: Notebook não liga
- Data de abertura: 04/05/2026
- Status: Aberto | Em andamento | Fechado

---



## 💾 Estrutura de Dados

Abaixo estão as principais entidades do banco de dados relacional que suportam este sistema:

### 1. Tabela: `clientes`
* `id` (INT, PK)
* `nome` (VARCHAR)
* `telefone` (VARCHAR)
* `email` (VARCHAR)
* `endereco` (VARCHAR)

### 2. Tabela: `tickets`
* `id` (INT, PK)
* `cliente_id` (INT, FK)
* `titulo` (VARCHAR)
* `descricao` (TEXT)
* `status` (ENUM: 'Aberto', 'Em Andamento', 'Aguardando Peças', 'Concluído')
* `data_abertura` (DATETIME)

### 3. Tabela: `ordens_servico` (OS)
* `id` (INT, PK)
* `ticket_id` (INT, FK)
* `data_inicio` (DATETIME)
* `data_fim` (DATETIME)
* `valor_servico` (DECIMAL)
* `status_os` (ENUM: 'Diagnóstico', 'Aprovando Orçamento', 'Em Execução', 'Finalizado')

### 4. Tabela: `pecas_os`
* `id` (INT, PK)
* `os_id` (INT, FK)
* `nome_peca` (VARCHAR)
* `quantidade` (INT)
* `valor_unitario` (DECIMAL)

---

## 📄 Exemplo de Ordem de Serviço (OS)

O modelo textual abaixo representa a visualização padrão de uma OS em um terminal ou sistema legado:

```text
┌──────────────────────────────────────────────────────────────┐
│  ⚙️ OS #245 - Maria Silva - Ar-Condicionado                  │
├──────────────────────────────────────────────────────────────┤
│  👤 CLIENTE: Maria Silva           📞 (11)99999-1234         │
│  📍 ENDEREÇO: Rua das Flores, 123 - SP                    🗺️ │
│  📅 DATA: 14/12/2024  09:30                                  ⏰ │
│                                                              │
│  🔧 SERVIÇO:                                                 │
│  [x] Diagnóstico realizado                                   │
│  [x] Troca de capacitor                                      │
│  [ ] Recarga gás (aguardando aprova.)                        │
│  [ ] Teste final                                             │
│                                                              │
│  🛠️ PEÇAS UTILIZADAS:                                        │
│  ┌──────────────┬──────┬──────────┐                          │
│  │ Capacitor    │ 1 un │ R$45,00  │                          │
│  │ Freon R410A  │ 1 kg │ R$120,00 │                          │
│  └──────────────┴──────┴──────────┘                          │
│                                                              │
│  💰 TOTAL: R$ 285,00  [ENVIAR ORÇAMENTO] [INICIAR SERVIÇO]   │
└──────────────────────────────────────────────────────────────┘
