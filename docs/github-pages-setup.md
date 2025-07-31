---
layout: default
title: "Configuração GitHub Pages"
---

# Configuração do GitHub Pages - FluentMind

## 📖 Sobre o GitHub Pages

O GitHub Pages é usado para hospedar a documentação técnica do FluentMind de forma gratuita e acessível. A documentação é automaticamente atualizada a partir do conteúdo da pasta `docs/`.

## 🚀 Configuração Inicial

### 1. Habilitar GitHub Pages

1. Acesse o repositório no GitHub
2. Vá em **Settings** → **Pages**
3. Em **Source**, selecione:
   - Source: `Deploy from a branch`
   - Branch: `main`
   - Folder: `/ (root)` ou `/docs`
4. Clique em **Save**

### 2. Configurar Jekyll (Opcional)

O GitHub Pages usa Jekyll automaticamente. O arquivo `_config.yml` já está configurado com:

```yaml
title: "FluentMind - Documentação Técnica"
description: "Plataforma de comunicação inteligente com IA para Salesforce"
theme: minima
```

### 3. URL da Documentação

Após a configuração, a documentação estará disponível em:
```
https://[seu-usuario].github.io/FluentMind/
```

## 📝 Atualizando a Documentação

### Fluxo Automático

A documentação é atualizada automaticamente quando:
- ✅ Commits são feitos na branch `main`
- ✅ Arquivos na pasta `docs/` são modificados
- ✅ Pull requests são mergeados

### Fluxo Manual

Para atualizar manualmente:

```bash
# 1. Edite os arquivos na pasta docs/
vi docs/overview.md

# 2. Commit as mudanças
git add docs/
git commit -m "docs: update overview documentation"

# 3. Push para main
git push origin main
```

### Tempo de Propagação
- **Build time**: 1-3 minutos
- **Propagação**: Até 10 minutos
- **Cache CDN**: Até 24 horas

## 📁 Estrutura de Arquivos

### Arquivos Principais
```
docs/
├── index.md                    # Página inicial (obrigatório)
├── _config.yml                 # Configuração Jekyll
├── overview.md                 # Visão geral do projeto
├── roadmap.md                  # Cronograma e fases
├── architecture.md             # Arquitetura técnica
├── data-model.md               # Modelo de dados
├── functional-requirements.md  # Requisitos funcionais
├── non-functional-requirements.md # Requisitos não funcionais
├── git-workflow.md             # Workflow Git
└── development-standards.md    # Padrões de código
```

### Arquivos de Configuração
```yaml
# _config.yml - Configurações principais
title: "FluentMind - Documentação Técnica"
description: "..."
theme: minima
markdown: kramdown
highlighter: rouge
```

## 🎨 Personalização Visual

### Temas Disponíveis

O GitHub Pages oferece temas prontos:
- `minima` (padrão, clean e profissional)
- `jekyll-theme-minimal`
- `jekyll-theme-architect`
- `jekyll-theme-cayman`

### Customização CSS

Para personalizar o estilo, crie:
```
docs/assets/css/style.scss
```

```scss
---
---

@import "{{ site.theme }}";

/* Customizações */
.site-header {
    background-color: #1976d2;
}

.page-content {
    max-width: 1200px;
}
```

## 🔗 Navegação Entre Páginas

### Links Relativos
```markdown
[Próximo: Arquitetura →](./architecture.md)
[← Voltar: Visão Geral](./overview.md)
[Voltar ao Índice](./index.md)
```

### Menu de Navegação

Configure no `_config.yml`:
```yaml
header_pages:
  - overview.md
  - roadmap.md
  - architecture.md
  - functional-requirements.md
```

## 📊 Analytics e Monitoramento

### Google Analytics

Para adicionar analytics, configure no `_config.yml`:
```yaml
google_analytics: UA-XXXXXXXX-X
```

### Métricas GitHub

Visualize estatísticas em:
- **Insights** → **Traffic**
- Views, visitors, referrers
- Popular content

## 🐛 Troubleshooting

### Problemas Comuns

#### 1. Página não carrega
```yaml
# Verifique _config.yml
title: "Seu Título"    # ✅ Correto
title: Seu Título      # ❌ Sem aspas pode causar erro
```

#### 2. Links quebrados
```markdown
[Link](./arquivo.md)     # ✅ Correto (relativo)
[Link](/arquivo.md)      # ❌ Pode não funcionar
```

#### 3. Markdown não renderiza
- Verifique se arquivos têm extensão `.md`
- Confirme se está na pasta `docs/`
- Verifique sintaxe do front matter

### Log de Build

Para ver erros de build:
1. Vá em **Actions** no GitHub
2. Procure por "pages build and deployment"
3. Clique no último run para ver logs

### Validação Local

Para testar localmente:
```bash
# Instalar Jekyll
gem install bundler jekyll

# No diretório docs/
cd docs/
bundle exec jekyll serve

# Acesse http://localhost:4000
```

## 📋 Checklist de Publicação

Antes de publicar uma nova versão da documentação:

### Conteúdo
- [ ] Todos os links funcionam
- [ ] Imagens carregam corretamente
- [ ] Formatação está consistente
- [ ] Informações estão atualizadas

### Técnico
- [ ] `_config.yml` está válido
- [ ] Navegação entre páginas funciona
- [ ] Build do Jekyll está passando
- [ ] Responsividade foi testada

### SEO
- [ ] Títulos são descritivos
- [ ] Meta descriptions estão configuradas
- [ ] URL é amigável
- [ ] Sitemap é gerado automaticamente

## 🔄 Workflow de Atualização

### Para Documentação Regular
```bash
# Branch para atualizações
git checkout -b docs/update-architecture
git add docs/architecture.md
git commit -m "docs(architecture): update AI integration details"
git push origin docs/update-architecture

# Pull Request → Review → Merge
```

### Para Correções Urgentes
```bash
# Hotfix diretamente na main
git checkout main
git pull origin main
git add docs/
git commit -m "docs: fix broken links in overview"
git push origin main
```

## 📞 Suporte

Para problemas com GitHub Pages:

- 📚 **Documentação oficial**: [GitHub Pages Docs](https://docs.github.com/pages)
- 🧰 **Jekyll Docs**: [Jekyll Documentation](https://jekyllrb.com/docs/)
- ❓ **GitHub Community**: [GitHub Community Forum](https://github.community/)

---

**Próximos Passos:**
1. Configure o GitHub Pages seguindo este guia
2. Customize o tema conforme necessário
3. Configure analytics (opcional)
4. Estabeleça processo de revisão para updates

---

[← Voltar ao Índice](./index.md)