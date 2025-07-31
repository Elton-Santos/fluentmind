---
layout: default
title: "Modelagem de Dados"
---

# Modelagem de Dados - Objetos Salesforce

## Visão Geral do Modelo

O FluentMind utiliza cinco objetos customizados principais para gerenciar todo o ciclo de vida das interações com clientes via IA. O modelo é projetado para escalabilidade, auditoria completa e flexibilidade para futuras expansões.

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│    Channel__c   │    │   Session__c    │    │   Contact/Lead  │
│   (1 to many)   │────│   (master)      │────│   (standard)    │
└─────────────────┘    └─────────────────┘    └─────────────────┘
                              │
                              │ (1 to many)
                              ▼
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Prompt__c     │    │ Interaction__c  │    │ AI_Response__c  │
│  (many to many) │────│   (master)      │────│  (1 to many)    │
└─────────────────┘    └─────────────────┘    └─────────────────┘
```

---

## 1. Channel__c (Canal de Comunicação)

### Descrição
Representa os diferentes canais através dos quais os clientes podem interagir com o sistema (WhatsApp, SMS, E-mail, Webchat).

### Campos

| Campo | Tipo | Descrição | Obrigatório |
|-------|------|-----------|-------------|
| `Name` | Text(80) | Nome do canal (ex: "WhatsApp Vendas") | ✅ |
| `Channel_Type__c` | Picklist | Tipo do canal | ✅ |
| `API_Configuration__c` | Long Text Area(32768) | Configurações JSON da API | ✅ |
| `Status__c` | Picklist | Status operacional do canal | ✅ |
| `Default_Prompt__c` | Lookup(Prompt__c) | Prompt padrão para este canal | ❌ |
| `Max_Session_Duration__c` | Number(18,0) | Duração máxima da sessão (minutos) | ❌ |
| `Rate_Limit_Per_Minute__c` | Number(18,0) | Limite de mensagens por minuto | ❌ |
| `Auto_Close_Inactive__c` | Checkbox | Fechar sessões inativas automaticamente | ❌ |
| `Created_By_User__c` | Lookup(User) | Usuário que criou o canal | ✅ |
| `Last_Health_Check__c` | DateTime | Última verificação de saúde | ❌ |
| `Error_Count__c` | Number(18,0) | Contador de erros nas últimas 24h | ❌ |

### Picklist Values

#### Channel_Type__c
- `WhatsApp`
- `SMS`
- `Email`
- `Webchat`
- `Future_Integration`

#### Status__c
- `Active` (Ativo)
- `Inactive` (Inativo)
- `Maintenance` (Manutenção)
- `Error` (Erro)

### Validation Rules
```apex
// Ensure API Configuration is valid JSON
AND(
  NOT(ISBLANK(API_Configuration__c)),
  NOT(REGEX(API_Configuration__c, "^\\s*\\{.*\\}\\s*$"))
)
```

### Relationships
- **1:N** com `Session__c`
- **N:1** com `Prompt__c` (Default_Prompt__c)

---

## 2. Session__c (Sessão de Conversa)

### Descrição
Representa uma sessão de conversa entre um cliente e o sistema de IA, agregando todas as interações relacionadas.

### Campos

| Campo | Tipo | Descrição | Obrigatório |
|-------|------|-----------|-------------|
| `Name` | Auto Number | Identificador único da sessão | ✅ |
| `Channel__c` | Master-Detail(Channel__c) | Canal utilizado na sessão | ✅ |
| `Contact__c` | Lookup(Contact) | Contato relacionado | ❌ |
| `Lead__c` | Lookup(Lead) | Lead relacionado | ❌ |
| `External_Customer_ID__c` | Text(255) | ID do cliente no sistema externo | ❌ |
| `Customer_Phone__c` | Phone | Telefone do cliente | ❌ |
| `Customer_Email__c` | Email | E-mail do cliente | ❌ |
| `Session_Status__c` | Picklist | Status atual da sessão | ✅ |
| `Started_At__c` | DateTime | Início da sessão | ✅ |
| `Ended_At__c` | DateTime | Fim da sessão | ❌ |
| `Duration_Minutes__c` | Formula(Number) | Duração em minutos | ❌ |
| `Total_Interactions__c` | Rollup Summary | Total de interações | ❌ |
| `Customer_Satisfaction__c` | Number(2,1) | Nota de satisfação (1-5) | ❌ |
| `Resolution_Type__c` | Picklist | Tipo de resolução | ❌ |
| `Escalated_To_Human__c` | Checkbox | Escalonado para atendimento humano | ❌ |
| `Escalation_Reason__c` | Text(255) | Motivo do escalonamento | ❌ |
| `Session_Context__c` | Long Text Area(32768) | Contexto JSON da sessão | ❌ |
| `Customer_Intent__c` | Text(255) | Intenção identificada do cliente | ❌ |
| `Tags__c` | Text(255) | Tags para categorização | ❌ |

### Picklist Values

#### Session_Status__c
- `Active` (Ativa)
- `Paused` (Pausada)
- `Completed` (Concluída)
- `Escalated` (Escalonada)
- `Abandoned` (Abandonada)
- `Error` (Erro)

#### Resolution_Type__c
- `Self_Service` (Autoatendimento)
- `AI_Resolved` (Resolvido pela IA)
- `Human_Required` (Requer humano)
- `Escalated` (Escalonado)
- `No_Resolution` (Não resolvido)

### Validation Rules
```apex
// Contact OR Lead must be populated if not external customer
AND(
  ISBLANK(Contact__c),
  ISBLANK(Lead__c),
  ISBLANK(External_Customer_ID__c)
)
```

### Formula Fields
```apex
// Duration_Minutes__c
IF(
  AND(NOT(ISBLANK(Started_At__c)), NOT(ISBLANK(Ended_At__c))),
  (Ended_At__c - Started_At__c) * 24 * 60,
  NULL
)
```

### Relationships
- **N:1** com `Channel__c` (Master-Detail)
- **N:1** com `Contact` (Lookup)
- **N:1** com `Lead` (Lookup)
- **1:N** com `Interaction__c`

---

## 3. Interaction__c (Interação Individual)

### Descrição
Representa cada troca de mensagens individual dentro de uma sessão (pergunta do cliente + resposta da IA).

### Campos

| Campo | Tipo | Descrição | Obrigatório |
|-------|------|-----------|-------------|
| `Name` | Auto Number | Identificador da interação | ✅ |
| `Session__c` | Master-Detail(Session__c) | Sessão pai | ✅ |
| `Sequence_Number__c` | Number(18,0) | Ordem da interação na sessão | ✅ |
| `Customer_Message__c` | Long Text Area(32768) | Mensagem do cliente | ✅ |
| `AI_Response__c` | Long Text Area(32768) | Resposta da IA | ❌ |
| `Message_Type__c` | Picklist | Tipo da mensagem | ✅ |
| `Timestamp__c` | DateTime | Momento da interação | ✅ |
| `Processing_Time_Ms__c` | Number(18,0) | Tempo de processamento (ms) | ❌ |
| `Prompt_Used__c` | Lookup(Prompt__c) | Prompt utilizado | ❌ |
| `Customer_Intent__c` | Text(255) | Intenção detectada | ❌ |
| `Sentiment_Score__c` | Number(3,2) | Score de sentimento (-1 a 1) | ❌ |
| `Confidence_Score__c` | Number(3,2) | Confiança da resposta (0 a 1) | ❌ |
| `Error_Occurred__c` | Checkbox | Erro durante processamento | ❌ |
| `Error_Message__c` | Text(255) | Mensagem de erro | ❌ |
| `Requires_Human__c` | Checkbox | Requer intervenção humana | ❌ |
| `Media_Attachments__c` | Long Text Area(32768) | URLs de mídias (JSON) | ❌ |
| `Context_Variables__c` | Long Text Area(32768) | Variáveis de contexto (JSON) | ❌ |

### Picklist Values

#### Message_Type__c
- `Text` (Texto)
- `Image` (Imagem)
- `Document` (Documento)
- `Audio` (Áudio)
- `Video` (Vídeo)
- `Location` (Localização)
- `Contact` (Contato)

### Validation Rules
```apex
// AI Response required if no error occurred
AND(
  NOT(Error_Occurred__c),
  ISBLANK(AI_Response__c)
)
```

### Relationships
- **N:1** com `Session__c` (Master-Detail)
- **N:1** com `Prompt__c` (Lookup)
- **1:N** com `AI_Response__c`

---

## 4. Prompt__c (Prompt de IA)

### Descrição
Armazena os prompts utilizados para instruir a IA, permitindo versionamento e configuração personalizada.

### Campos

| Campo | Tipo | Descrição | Obrigatório |
|-------|------|-----------|-------------|
| `Name` | Text(80) | Nome do prompt | ✅ |
| `Prompt_Content__c` | Long Text Area(32768) | Conteúdo do prompt | ✅ |
| `Version__c` | Text(20) | Versão do prompt | ✅ |
| `Is_Active__c` | Checkbox | Prompt ativo | ✅ |
| `Category__c` | Picklist | Categoria do prompt | ✅ |
| `Language__c` | Picklist | Idioma do prompt | ✅ |
| `Temperature__c` | Number(3,2) | Parâmetro temperature (0-1) | ❌ |
| `Max_Tokens__c` | Number(18,0) | Máximo de tokens | ❌ |
| `Top_P__c` | Number(3,2) | Parâmetro top_p (0-1) | ❌ |
| `Top_K__c` | Number(18,0) | Parâmetro top_k | ❌ |
| `Created_By_User__c` | Lookup(User) | Usuário criador | ✅ |
| `Approved_By__c` | Lookup(User) | Usuário que aprovou | ❌ |
| `Approval_Date__c` | Date | Data da aprovação | ❌ |
| `Usage_Count__c` | Rollup Summary | Quantas vezes foi usado | ❌ |
| `Average_Confidence__c` | Rollup Summary | Confiança média das respostas | ❌ |
| `Description__c` | Text(255) | Descrição do prompt | ❌ |
| `Tags__c` | Text(255) | Tags para organização | ❌ |

### Picklist Values

#### Category__c
- `General` (Geral)
- `Sales` (Vendas)
- `Support` (Suporte)
- `Marketing` (Marketing)
- `Technical` (Técnico)
- `Onboarding` (Integração)

#### Language__c
- `Portuguese` (Português)
- `English` (Inglês)
- `Spanish` (Espanhol)
- `French` (Francês)
- `German` (Alemão)

### Validation Rules
```apex
// Temperature must be between 0 and 1
AND(
  NOT(ISBLANK(Temperature__c)),
  OR(Temperature__c < 0, Temperature__c > 1)
)
```

### Relationships
- **1:N** com `Channel__c` (Default_Prompt__c)
- **1:N** com `Interaction__c`
- **N:N** com `AI_Response__c` (via Interaction__c)

---

## 5. AI_Response__c (Resposta da IA)

### Descrição
Armazena detalhes técnicos das respostas geradas pela IA, incluindo métricas de performance e metadata.

### Campos

| Campo | Tipo | Descrição | Obrigatório |
|-------|------|-----------|-------------|
| `Name` | Auto Number | Identificador da resposta | ✅ |
| `Interaction__c` | Master-Detail(Interaction__c) | Interação relacionada | ✅ |
| `AI_Model__c` | Text(100) | Modelo de IA utilizado | ✅ |
| `Response_Content__c` | Long Text Area(32768) | Conteúdo da resposta | ✅ |
| `Prompt_Tokens__c` | Number(18,0) | Tokens do prompt | ❌ |
| `Completion_Tokens__c` | Number(18,0) | Tokens da resposta | ❌ |
| `Total_Tokens__c` | Number(18,0) | Total de tokens | ❌ |
| `Processing_Time_Ms__c` | Number(18,0) | Tempo de processamento | ❌ |
| `Cost_USD__c` | Currency(16,4) | Custo em USD | ❌ |
| `Finish_Reason__c` | Picklist | Motivo da finalização | ❌ |
| `Safety_Ratings__c` | Long Text Area(32768) | Avaliações de segurança (JSON) | ❌ |
| `Model_Parameters__c` | Long Text Area(32768) | Parâmetros usados (JSON) | ❌ |
| `Response_Quality__c` | Picklist | Qualidade avaliada | ❌ |
| `Human_Feedback__c` | Picklist | Feedback humano | ❌ |
| `Error_Code__c` | Text(50) | Código de erro da API | ❌ |
| `Retry_Count__c` | Number(18,0) | Número de tentativas | ❌ |
| `Cache_Hit__c` | Checkbox | Resposta do cache | ❌ |

### Picklist Values

#### Finish_Reason__c
- `STOP` (Finalização normal)
- `MAX_TOKENS` (Limite de tokens)
- `SAFETY` (Filtro de segurança)
- `RECITATION` (Recitação detectada)
- `OTHER` (Outro motivo)

#### Response_Quality__c
- `Excellent` (Excelente)
- `Good` (Boa)
- `Average` (Média)
- `Poor` (Ruim)
- `Not_Evaluated` (Não avaliada)

#### Human_Feedback__c
- `Helpful` (Útil)
- `Not_Helpful` (Não útil)
- `Inappropriate` (Inadequada)
- `Accurate` (Precisa)
- `Inaccurate` (Imprecisa)

### Relationships
- **N:1** com `Interaction__c` (Master-Detail)

---

## Índices e Performance

### Índices Customizados Recomendados

```sql
-- Session__c
CREATE INDEX ON Session__c (Channel__c, Session_Status__c, Started_At__c);
CREATE INDEX ON Session__c (Contact__c, Started_At__c);
CREATE INDEX ON Session__c (External_Customer_ID__c);

-- Interaction__c
CREATE INDEX ON Interaction__c (Session__c, Sequence_Number__c);
CREATE INDEX ON Interaction__c (Timestamp__c, Customer_Intent__c);
CREATE INDEX ON Interaction__c (Processing_Time_Ms__c, Error_Occurred__c);

-- AI_Response__c
CREATE INDEX ON AI_Response__c (AI_Model__c, Processing_Time_Ms__c);
CREATE INDEX ON AI_Response__c (Total_Tokens__c, Cost_USD__c);

-- Channel__c
CREATE INDEX ON Channel__c (Channel_Type__c, Status__c);
```

### Considerações de Storage

| Objeto | Volume Estimado (Anual) | Crescimento |
|--------|-------------------------|-------------|
| `Channel__c` | < 100 registros | Baixo |
| `Session__c` | 100K - 1M | Alto |
| `Interaction__c` | 500K - 10M | Muito Alto |
| `Prompt__c` | < 1K registros | Baixo |
| `AI_Response__c` | 500K - 10M | Muito Alto |

---

## Data Retention e Archiving

### Política de Retenção
- **Hot Data**: Últimos 3 meses (acesso frequente)
- **Warm Data**: 3-12 meses (acesso ocasional)
- **Cold Data**: > 12 meses (acesso raro, candidato a archive)

### Archive Strategy
```apex
// Scheduled job para archive de dados antigos
global class DataArchivalBatch implements Database.Batchable<sObject> {
    global Database.QueryLocator start(Database.BatchableContext bc) {
        Date cutoffDate = Date.today().addMonths(-12);
        return Database.getQueryLocator([
            SELECT Id FROM Session__c 
            WHERE Started_At__c < :cutoffDate 
            AND Session_Status__c = 'Completed'
        ]);
    }
    // Implementation...
}
```

---

[← Voltar: Arquitetura](./architecture.md) | [Próximo: Requisitos Funcionais →](./functional-requirements.md)