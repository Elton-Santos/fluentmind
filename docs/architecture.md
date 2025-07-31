---
layout: default
title: "Arquitetura da Solução"
---

# Arquitetura da Solução FluentMind

## Visão Geral da Arquitetura

FluentMind é construído como uma solução nativa do Salesforce que estende as capacidades da plataforma com inteligência artificial e integrações multi-canal.

```
┌─────────────────────────────────────────────────────────────┐
│                    CLIENTE FINAL                           │
│  WhatsApp │ SMS │ E-mail │ Webchat │ Futuras Integrações   │
└─────────────────┬───────────────────────────────────────────┘
                  │
┌─────────────────▼───────────────────────────────────────────┐
│                 FLUENTMIND CORE                            │
│  ┌─────────────┐ ┌─────────────┐ ┌─────────────────────┐   │
│  │ Channel Mgr │ │ Session Mgr │ │    AI Orchestrator  │   │
│  └─────────────┘ └─────────────┘ └─────────────────────┘   │
└─────────────────┬───────────────────────────────────────────┘
                  │
┌─────────────────▼───────────────────────────────────────────┐
│                SALESFORCE PLATFORM                         │
│  ┌─────────────┐ ┌─────────────┐ ┌─────────────────────┐   │
│  │Custom Objects│ │  Apex APIs  │ │    LWC Frontend     │   │
│  └─────────────┘ └─────────────┘ └─────────────────────┘   │
└─────────────────┬───────────────────────────────────────────┘
                  │
┌─────────────────▼───────────────────────────────────────────┐
│              EXTERNAL SERVICES                              │
│ ┌────────────┐ ┌────────────┐ ┌────────────┐ ┌───────────┐ │
│ │Google Gemini│ │ WhatsApp  │ │   Twilio   │ │   SMTP    │ │
│ │     API     │ │    API     │ │    SMS     │ │ Providers │ │
│ └────────────┘ └────────────┘ └────────────┘ └───────────┘ │
└─────────────────────────────────────────────────────────────┘
```

---

## Componentes e Módulos Principais

### 1. Channel Manager (Gerenciador de Canais)
**Responsabilidade**: Gerenciar comunicação com canais externos

#### Subcomponentes:
- **WhatsApp Handler**: Processamento de webhooks e API calls
- **SMS Gateway**: Integração com provedores SMS (Twilio)
- **Email Processor**: Envio e recebimento de e-mails
- **Webchat Engine**: Interface web para chat direto

#### Funcionalidades:
- Normalização de mensagens entre canais
- Gestão de rate limits por canal
- Retry logic para falhas de comunicação
- Monitoramento de health de cada canal

### 2. Session Manager (Gerenciador de Sessões)
**Responsabilidade**: Controlar fluxo e estado das conversas

#### Subcomponentes:
- **Session Store**: Armazenamento de estado da conversa
- **Context Manager**: Manutenção de contexto entre interações
- **Flow Controller**: Orquestração de fluxos conversacionais
- **History Tracker**: Rastreamento de histórico completo

#### Funcionalidades:
- Criação e finalização de sessões
- Persistência de contexto conversacional
- Roteamento baseado em regras de negócio
- Escalonamento para atendimento humano

### 3. AI Orchestrator (Orquestrador de IA)
**Responsabilidade**: Integração e otimização da inteligência artificial

#### Subcomponentes:
- **Gemini Connector**: Cliente para Google Gemini API
- **Prompt Manager**: Gestão e versionamento de prompts
- **Response Processor**: Pós-processamento de respostas da IA
- **Learning Engine**: Análise de padrões para melhorias

#### Funcionalidades:
- Construção dinâmica de prompts
- Cache inteligente de respostas
- Fallback para múltiplos modelos
- Análise de sentimento e intenção

---

## Stack Tecnológica Inicial

### Salesforce Platform
```yaml
Frontend:
  - Lightning Web Components (LWC)
  - Lightning Design System (SLDS)
  - Lightning App Builder

Backend:
  - Apex Classes
  - Custom Objects
  - Platform Events
  - Named Credentials

Integration:
  - REST API Callouts
  - Webhook Endpoints
  - External Services
  - Connected Apps
```

### Integrações Externas
```yaml
AI Services:
  - Google Gemini API (REST)
  - Modelo: gemini-pro
  - Autenticação: API Key / OAuth 2.0

Communication Channels:
  - WhatsApp Business API
  - Twilio SMS Gateway
  - SMTP/Email Services
  - Native Webchat

Security:
  - OAuth 2.0
  - Named Credentials
  - Platform Encryption
  - Field-Level Security
```

### DevOps e Qualidade
```yaml
Version Control:
  - Git (GitHub)
  - Salesforce DX
  - Package Development

Testing:
  - Apex Unit Tests (>85% coverage)
  - LWC Jest Tests
  - Integration Testing
  - User Acceptance Testing

Deployment:
  - Salesforce CLI
  - Scratch Orgs
  - Managed Packages
  - AppExchange Distribution
```

---

## API de IA: Google Gemini (REST)

### Configuração da Integração

#### Named Credential Setup
```
Name: Google_Gemini_API
URL: https://generativelanguage.googleapis.com
Authentication Protocol: No Authentication
Certificate: Use Platform Certificate
```

#### Endpoint Configuration
```
Base URL: /v1/models/gemini-pro:generateContent
Method: POST
Content-Type: application/json
Authorization: Bearer {API_KEY}
```

### Estrutura de Request/Response

#### Request Payload
```json
{
  "contents": [{
    "parts": [{
      "text": "Prompt contextualizado com histórico da conversa"
    }]
  }],
  "generationConfig": {
    "temperature": 0.7,
    "topK": 40,
    "topP": 0.95,
    "maxOutputTokens": 1024
  },
  "safetySettings": [
    {
      "category": "HARM_CATEGORY_HARASSMENT",
      "threshold": "BLOCK_MEDIUM_AND_ABOVE"
    }
  ]
}
```

#### Response Handling
```json
{
  "candidates": [{
    "content": {
      "parts": [{
        "text": "Resposta gerada pela IA"
      }]
    },
    "finishReason": "STOP",
    "safetyRatings": []
  }],
  "usageMetadata": {
    "promptTokenCount": 150,
    "candidatesTokenCount": 300,
    "totalTokenCount": 450
  }
}
```

---

## Arquitetura de Dados

### Fluxo de Dados Principal
```
1. Mensagem recebida → Channel Manager
2. Sessão identificada/criada → Session Manager
3. Contexto carregado → AI Orchestrator
4. Prompt construído → Google Gemini API
5. Resposta processada → Channel Manager
6. Mensagem enviada → Cliente
7. Interação registrada → Salesforce Objects
```

### Cache Strategy
- **Session Cache**: Redis-like storage no Salesforce (Platform Cache)
- **AI Response Cache**: Cache de respostas para perguntas frequentes
- **Channel State**: Estado de conexão com canais externos

### Error Handling
- **Retry Policies**: Exponential backoff para APIs externas
- **Circuit Breaker**: Proteção contra falhas em cascata
- **Fallback Responses**: Respostas padrão quando IA indisponível
- **Dead Letter Queue**: Mensagens que falharam múltiplas vezes

---

## Segurança e Compliance

### Salesforce Security
- **Field-Level Security**: Controle granular de acesso
- **Object Permissions**: Permissões baseadas em perfis
- **Sharing Rules**: Regras de compartilhamento de dados
- **Platform Encryption**: Criptografia de dados sensíveis

### API Security
- **Named Credentials**: Gerenciamento seguro de credenciais
- **SSL/TLS**: Comunicação criptografada
- **Rate Limiting**: Controle de frequência de requests
- **Input Validation**: Validação rigorosa de entrada

### Data Privacy
- **GDPR Compliance**: Direito ao esquecimento
- **Data Retention**: Políticas de retenção de dados
- **Audit Trail**: Rastreamento completo de ações
- **Data Anonymization**: Anonimização de dados sensíveis

---

## Performance e Escalabilidade

### Optimizations
- **Bulk Processing**: Processamento em lote de mensagens
- **Asynchronous Processing**: Queueable Apex para operações longas
- **Database Optimization**: Índices e consultas otimizadas
- **Caching Strategy**: Cache em múltiplas camadas

### Monitoring
- **Custom Metrics**: Métricas específicas do FluentMind
- **Error Tracking**: Monitoramento de erros e exceções
- **Performance Monitoring**: Tempo de resposta e throughput
- **Health Checks**: Verificação de saúde dos componentes

### Scalability Patterns
- **Horizontal Scaling**: Múltiplas instâncias de processamento
- **Load Balancing**: Distribuição de carga entre canais
- **Queue Management**: Gestão de filas de mensagens
- **Resource Pooling**: Pool de conexões para APIs externas

---

## Deployment Architecture

### Environments
```
Development → Sandbox → UAT → Production → AppExchange
```

### Package Structure
```
FluentMind Managed Package
├── Objects/
│   ├── Channel__c
│   ├── Interaction__c
│   ├── Prompt__c
│   ├── Session__c
│   └── AI_Response__c
├── Classes/
│   ├── ChannelManager.cls
│   ├── SessionController.cls
│   ├── GeminiConnector.cls
│   └── [Other Apex Classes]
├── LWC/
│   ├── fluentMindChat
│   ├── channelConfiguration
│   └── [Other Components]
└── Configuration/
    ├── Named Credentials
    ├── Remote Site Settings
    └── Custom Metadata Types
```

---

[← Voltar: Roadmap](./roadmap.md) | [Próximo: Modelagem de Dados →](./data-model.md)