# FluentMind

> Plataforma de comunicação inteligente com IA para Salesforce

## 📋 Sobre o Projeto

FluentMind é uma aplicação web integrada ao Salesforce que permite a automação de interações com clientes através de múltiplos canais de comunicação utilizando inteligência artificial (Google Gemini).

### Canais Suportados
- 💬 **WhatsApp Business**
- 📱 **SMS**
- 📧 **E-mail**
- 🌐 **Webchat**

## 📚 Documentação

A documentação completa está disponível no GitHub Pages:

**🔗 [Acessar Documentação](https://[seu-usuario].github.io/FluentMind/)**

### Índice da Documentação

#### 📋 Planejamento e Visão
- [Visão Geral do Projeto](./docs/overview.md)
- [Roadmap do Projeto](./docs/roadmap.md)

#### 🏗️ Arquitetura e Design
- [Arquitetura da Solução](./docs/architecture.md)
- [Modelagem de Dados](./docs/data-model.md)

#### 📖 Especificações Técnicas
- [Requisitos Funcionais](./docs/functional-requirements.md)
- [Requisitos Não Funcionais](./docs/non-functional-requirements.md)

#### 🔧 Desenvolvimento
- [Estrutura de Branches](./docs/git-workflow.md)
- [Padrões de Desenvolvimento](./docs/development-standards.md)

## 🚀 Status do Projeto

| Fase | Status | Previsão |
|------|--------|----------|
| 📚 **Documentação** | ✅ Concluído | Jan 2025 |
| 🌐 **MVP Webchat** | 🔄 Em andamento | Fev 2025 |
| 📱 **Integração WhatsApp** | ⏳ Planejado | Mar 2025 |
| 📧 **SMS + E-mail** | ⏳ Planejado | Abr 2025 |
| 📦 **AppExchange** | ⏳ Planejado | Mai 2025 |

## 🛠️ Stack Tecnológica

### Salesforce Platform
- **Frontend**: Lightning Web Components (LWC)
- **Backend**: Apex Classes
- **Database**: Custom Objects
- **Integration**: REST API Callouts

### Integrações Externas
- **IA**: Google Gemini API
- **WhatsApp**: WhatsApp Business API
- **SMS**: Twilio
- **E-mail**: SMTP/API Providers

## 📦 Estrutura do Projeto

```
FluentMind/
├── docs/                          # Documentação GitHub Pages
│   ├── index.md                  # Página inicial
│   ├── overview.md               # Visão geral
│   ├── roadmap.md                # Roadmap
│   ├── architecture.md           # Arquitetura
│   ├── data-model.md             # Modelo de dados
│   ├── functional-requirements.md
│   ├── non-functional-requirements.md
│   ├── git-workflow.md           # Workflow Git
│   ├── development-standards.md  # Padrões de código
│   └── _config.yml               # Configuração Jekyll
├── force-app/                     # Código Salesforce (futuro)
├── config/                        # Configurações SFDX (futuro)
└── README.md                      # Este arquivo
```

## 🔧 Configuração para Desenvolvedores

### Pré-requisitos
- Salesforce CLI (SFDX)
- Node.js 16+ (para LWC local development)
- Git
- VS Code com Salesforce Extension Pack

### Setup do Ambiente
```bash
# 1. Clone o repositório
git clone https://github.com/[seu-usuario]/FluentMind.git
cd FluentMind

# 2. Instale dependências
npm install

# 3. Configure autenticação Salesforce
sfdx auth:web:login --setalias FluentMind-Dev

# 4. Crie uma scratch org
sfdx force:org:create -f config/project-scratch-def.json --setalias FluentMind-Scratch --durationdays 30

# 5. Push metadata para scratch org
sfdx force:source:push
```

## 🤝 Como Contribuir

### Workflow de Desenvolvimento
1. **Fork** o repositório
2. Crie uma **feature branch**: `git checkout -b feature/FM-001-nova-funcionalidade`
3. **Commit** suas mudanças: `git commit -m 'feat: adiciona nova funcionalidade'`
4. **Push** para a branch: `git push origin feature/FM-001-nova-funcionalidade`
5. Abra um **Pull Request**

### Padrões de Commit
Utilizamos [Conventional Commits](https://www.conventionalcommits.org/):

```
feat: nova funcionalidade
fix: correção de bug
docs: atualização de documentação
style: formatação de código
refactor: refatoração
test: adição de testes
chore: tarefas de build/config
```

## 📋 Roadmap

### Mês 1 - Documentação (Janeiro 2025) ✅
- [x] Documentação técnica completa
- [x] Arquitetura da solução
- [x] Modelagem de dados
- [x] Requisitos funcionais e não funcionais

### Mês 2 - MVP Webchat (Fevereiro 2025) 🔄
- [ ] Interface web básica
- [ ] Integração com Google Gemini
- [ ] Objetos Salesforce básicos
- [ ] Testes unitários

### Mês 3 - WhatsApp (Março 2025) ⏳
- [ ] Integração WhatsApp Business API
- [ ] Gestão de templates
- [ ] Sincronização de conversas

### Mês 4 - Multi-canal (Abril 2025) ⏳
- [ ] Integração SMS (Twilio)
- [ ] Integração E-mail
- [ ] Orquestração multi-canal

### Mês 5+ - AppExchange (Maio 2025) ⏳
- [ ] Managed package
- [ ] Security review
- [ ] Publicação no AppExchange

## 📞 Suporte

Para dúvidas, sugestões ou reportar problemas:

- 📧 **E-mail**: [seu-email@exemplo.com]
- 🐛 **Issues**: [GitHub Issues](https://github.com/[seu-usuario]/FluentMind/issues)
- 📚 **Documentação**: [GitHub Pages](https://[seu-usuario].github.io/FluentMind/)

## 📄 Licença

Este projeto está licenciado sob a [MIT License](LICENSE).

---

**FluentMind** - Transformando atendimento ao cliente com IA inteligente no Salesforce.

---

⭐ **Gostou do projeto?** Deixe uma estrela no repositório!