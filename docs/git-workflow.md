# Estrutura de Branches Git - FluentMind

## Estratégia de Branching

O FluentMind utiliza uma estratégia **Git Flow adaptada** para desenvolvimento Salesforce, combinando as melhores práticas de desenvolvimento colaborativo com as especificidades da plataforma Salesforce.

```
main (protected)
├── release/v1.0.0
├── dev (protected)
│   ├── feature/FM-001-channel-management
│   ├── feature/FM-002-ai-integration
│   └── feature/FM-003-whatsapp-connector
├── hotfix/FM-H001-critical-bug
└── docs (GitHub Pages)
```

---

## Branches Principais

### 1. Main Branch (`main`)
**Propósito**: Código de produção estável

**Características**:
- ✅ Sempre deployável para AppExchange
- ✅ Somente merges via Pull Request aprovado
- ✅ Requer review de 2+ desenvolvedores
- ✅ Todos os testes automáticos passando
- ✅ Security scan aprovado

**Proteções Configuradas**:
```yaml
Branch Protection Rules:
  - Require pull request reviews: 2
  - Dismiss stale reviews: true
  - Require status checks: true
  - Require up-to-date branches: true
  - Include administrators: true
  - Restrict pushes: true
```

**Policies**:
- 🚫 Push direto proibido
- ✅ Merge apenas após approval
- ✅ Squash merge obrigatório
- ✅ Tag automática para releases

### 2. Development Branch (`dev`)
**Propósito**: Integração contínua de features

**Características**:
- ✅ Branch base para novas features
- ✅ Integração automática via CI/CD
- ✅ Deploy automático para Sandbox
- ✅ Testes automáticos executados

**Proteções Configuradas**:
```yaml
Branch Protection Rules:
  - Require pull request reviews: 1
  - Require status checks: true
  - Auto-merge when checks pass: true
```

**Policies**:
- 🔄 Merge automático quando testes passam
- ✅ 1 approval mínimo
- ✅ CI/CD automático para Sandbox

---

## Branches de Feature

### Convenção de Nomenclatura
```
feature/FM-{TICKET_NUMBER}-{brief-description}
```

**Exemplos**:
- `feature/FM-001-channel-management`
- `feature/FM-002-ai-integration`
- `feature/FM-015-whatsapp-webhook`

### Ciclo de Vida de Feature

#### 1. Criação
```bash
# Criar e mudar para nova feature branch
git checkout dev
git pull origin dev
git checkout -b feature/FM-001-channel-management

# Primeira push
git push -u origin feature/FM-001-channel-management
```

#### 2. Desenvolvimento
```bash
# Commits frequentes com mensagens descritivas
git add .
git commit -m "feat(channels): add Channel__c object definition

- Create custom object with required fields
- Add validation rules for API configuration
- Implement basic CRUD operations
- Add unit tests for ChannelManager class

Resolves FM-001"

git push origin feature/FM-001-channel-management
```

#### 3. Pull Request
```yaml
PR Template:
  Title: "[FM-001] Implement Channel Management"
  Description:
    - Summary of changes
    - Testing performed
    - Screenshots (if UI changes)
    - Breaking changes (if any)
  Labels: ["feature", "enhancement"]
  Reviewers: ["@tech-lead", "@salesforce-expert"]
  Linked Issues: ["#1"]
```

#### 4. Review e Merge
- ✅ Code review aprovado
- ✅ Testes automáticos passando
- ✅ Conflitos resolvidos
- ✅ Squash merge para `dev`

---

## Branches de Release

### Convenção de Nomenclatura
```
release/v{MAJOR}.{MINOR}.{PATCH}
```

**Exemplos**:
- `release/v1.0.0` (Initial release)
- `release/v1.1.0` (Feature release)
- `release/v1.0.1` (Patch release)

### Processo de Release

#### 1. Criação da Release Branch
```bash
# Criar release branch a partir de dev
git checkout dev
git pull origin dev
git checkout -b release/v1.0.0

# Atualizar versão no código
# Update version in package.xml, README, etc.

git add .
git commit -m "chore: bump version to 1.0.0"
git push -u origin release/v1.0.0
```

#### 2. Preparação para Release
- 🧪 Testes extensivos em UAT environment
- 📋 Validação de requirements
- 📚 Atualização de documentação
- 🔍 Security review preparatório
- 🎯 Performance testing

#### 3. Bug Fixes na Release
```bash
# Fix bugs diretamente na release branch
git checkout release/v1.0.0
# ... fix bugs ...
git commit -m "fix: resolve authentication issue"

# Merge fixes de volta para dev
git checkout dev
git merge release/v1.0.0
```

#### 4. Finalização da Release
```bash
# Merge para main
git checkout main
git merge --no-ff release/v1.0.0
git tag -a v1.0.0 -m "Release version 1.0.0"

# Merge para dev (se houver commits adicionais)
git checkout dev
git merge release/v1.0.0

# Push tudo
git push origin main --tags
git push origin dev

# Deletar release branch
git branch -d release/v1.0.0
git push origin --delete release/v1.0.0
```

---

## Branches de Hotfix

### Convenção de Nomenclatura
```
hotfix/FM-H{NUMBER}-{brief-description}
```

**Exemplos**:
- `hotfix/FM-H001-api-timeout-fix`
- `hotfix/FM-H002-security-vulnerability`

### Processo de Hotfix

#### 1. Criação Emergencial
```bash
# Criar hotfix a partir de main
git checkout main
git pull origin main
git checkout -b hotfix/FM-H001-api-timeout-fix
```

#### 2. Implementação Rápida
```bash
# Fix implementado e testado
git add .
git commit -m "fix: increase API timeout to 30 seconds

- Update Gemini API timeout configuration
- Add retry logic for timeout scenarios
- Update error handling for better UX

Fixes critical production issue with API timeouts"

git push -u origin hotfix/FM-H001-api-timeout-fix
```

#### 3. Deploy Emergencial
```bash
# Merge direto para main após approval
git checkout main
git merge --no-ff hotfix/FM-H001-api-timeout-fix
git tag -a v1.0.1 -m "Hotfix v1.0.1 - API timeout fix"

# Merge para dev também
git checkout dev
git merge hotfix/FM-H001-api-timeout-fix

# Push e limpeza
git push origin main --tags
git push origin dev
git branch -d hotfix/FM-H001-api-timeout-fix
git push origin --delete hotfix/FM-H001-api-timeout-fix
```

---

## Branch Docs (GitHub Pages)

### Propósito
Branch dedicado para GitHub Pages com documentação sempre atualizada.

### Estrutura
```
docs/
├── index.html (gerado automaticamente)
├── _config.yml (Jekyll configuration)
├── assets/
│   ├── css/
│   ├── js/
│   └── images/
└── documentation/
    ├── overview.md
    ├── architecture.md
    └── api-reference.md
```

### Processo de Atualização
```bash
# Automatizado via GitHub Actions
name: Update Docs
on:
  push:
    paths: ['docs/**']
    branches: [main, dev]
```

---

## Convenções de Commit

### Formato Padrão
```
<type>(<scope>): <description>

<body>

<footer>
```

### Tipos de Commit
- **feat**: Nova funcionalidade
- **fix**: Correção de bug
- **docs**: Mudanças na documentação
- **style**: Formatação, espaços em branco
- **refactor**: Refatoração de código
- **test**: Adição ou correção de testes
- **chore**: Tarefas de build, configuração

### Exemplos
```bash
feat(ai): implement sentiment analysis

- Add sentiment scoring using Gemini API
- Create new field in Interaction__c object
- Add dashboard widget for sentiment trends
- Include unit tests for sentiment logic

Resolves FM-045

fix(whatsapp): handle webhook timeout correctly

- Increase webhook timeout to 30 seconds
- Add retry mechanism for failed webhooks
- Improve error logging for debugging

Fixes #123

docs(api): update integration documentation

- Add examples for new sentiment API
- Update authentication section
- Fix broken links in overview

chore(deps): update Salesforce API version

- Bump API version to 59.0
- Update SFDX project configuration
- Regenerate metadata types
```

---

## Integração com Salesforce DX

### Configuração de Ambientes

#### Scratch Orgs por Branch
```json
{
  "orgName": "FluentMind Feature Dev",
  "edition": "Developer",
  "features": ["EnableSetPasswordInApi"],
  "settings": {
    "lightningExperienceSettings": {
      "enableS1DesktopEnabled": true
    }
  }
}
```

#### Deployment Pipeline
```yaml
# .github/workflows/salesforce-ci.yml
name: Salesforce CI/CD

on:
  pull_request:
    branches: [dev]
  push:
    branches: [main, dev]

jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Install SFDX
        run: npm install -g sfdx-cli
      - name: Authenticate
        run: sfdx auth:jwt:grant --clientid ${{ secrets.SALESFORCE_CLIENT_ID }}
      - name: Deploy Check
        run: sfdx force:source:deploy --checkonly --testlevel RunLocalTests
```

---

## Políticas de Code Review

### Checklist para Reviewers

#### Funcionalidade
- [ ] Código atende aos requisitos especificados
- [ ] Lógica de negócio está correta
- [ ] Edge cases são tratados adequadamente
- [ ] Performance é aceitável

#### Qualidade
- [ ] Código segue padrões de coding style
- [ ] Nomenclatura é clara e consistente
- [ ] Comentários são apropriados
- [ ] Não há código duplicado

#### Salesforce Específico
- [ ] Bulk operations são utilizadas
- [ ] SOQL queries são otimizadas
- [ ] Governor limits são respeitados
- [ ] Security (CRUD/FLS) é implementada

#### Testes
- [ ] Cobertura de teste adequada (>85%)
- [ ] Testes são significativos
- [ ] Mock data é utilizada apropriadamente
- [ ] Negative test cases incluídos

### Aprovação Automática
```yaml
# Condições para auto-merge
conditions:
  - tests_passing: true
  - security_scan: passed
  - reviews_approved: 1+
  - merge_conflicts: false
  - branch_up_to_date: true
```

---

## Estratégia de Versionamento

### Semantic Versioning (SemVer)
```
MAJOR.MINOR.PATCH

MAJOR: Breaking changes
MINOR: New features (backward compatible)  
PATCH: Bug fixes (backward compatible)
```

### Versionamento por Branch
- **main**: Production versions (1.0.0, 1.1.0, 1.2.0)
- **dev**: Development versions (1.1.0-dev.1, 1.1.0-dev.2)
- **feature**: Feature versions (1.1.0-feat.channel-mgmt.1)

### Tags e Releases
```bash
# Production release
git tag -a v1.0.0 -m "Release v1.0.0 - Initial AppExchange release"

# Pre-release
git tag -a v1.1.0-beta.1 -m "Beta release v1.1.0-beta.1"

# Release candidate
git tag -a v1.1.0-rc.1 -m "Release candidate v1.1.0-rc.1"
```

---

## Monitoramento e Métricas

### Branch Health Metrics
- **Merge Frequency**: Frequência de merges para dev/main
- **PR Lead Time**: Tempo entre criação e merge de PR
- **Review Time**: Tempo médio de review
- **Bug Escape Rate**: Bugs que chegam à main

### Automated Reports
```yaml
Weekly Branch Report:
  - Active features: 5
  - PRs merged: 12
  - Average review time: 4.2 hours
  - Test coverage: 87.3%
  - Security issues: 0
```

---

[← Voltar: Requisitos Não Funcionais](./non-functional-requirements.md) | [Próximo: Padrões de Desenvolvimento →](./development-standards.md)