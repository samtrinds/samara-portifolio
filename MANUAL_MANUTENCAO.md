# Manual de Manutenção do Portfólio

## Samara da Trindade - Analista de Infraestrutura e Redes

Este manual foi criado para ajudar você a manter e atualizar seu portfólio profissional de forma fácil e eficiente.

---

## 📋 Visão Geral

Seu portfólio foi construído usando:
- **Jekyll** - Gerador de sites estáticos
- **Academic Pages** - Template profissional
- **GitHub Pages** - Hospedagem gratuita
- **Markdown** - Linguagem simples para escrever conteúdo

## 🚀 Como Publicar no GitHub Pages

### Passo 1: Criar Conta no GitHub
1. Acesse [github.com](https://github.com)
2. Clique em "Sign up" e crie sua conta
3. Confirme seu email

### Passo 2: Criar Repositório
1. Clique no botão "+" no canto superior direito
2. Selecione "New repository"
3. Nome do repositório: `seu-usuario.github.io` (substitua "seu-usuario" pelo seu nome de usuário do GitHub)
4. Marque como "Public"
5. Clique em "Create repository"

### Passo 3: Fazer Upload dos Arquivos
1. No repositório criado, clique em "uploading an existing file"
2. Arraste todos os arquivos da pasta `samara-portfolio-simple` para o GitHub
3. Escreva uma mensagem como "Primeiro commit do portfólio"
4. Clique em "Commit changes"

### Passo 4: Ativar GitHub Pages
1. Vá em "Settings" do repositório
2. Role até a seção "Pages"
3. Em "Source", selecione "Deploy from a branch"
4. Escolha "main" branch
5. Clique em "Save"

**Seu site estará disponível em:** `https://seu-usuario.github.io`

---

## ✏️ Como Editar o Conteúdo

### Editando Informações Pessoais

**Arquivo:** `_config.yml`

```yaml
# Suas informações básicas
name: "Seu Nome Completo"
bio: "Sua descrição profissional"
location: "Sua Cidade, Estado"
employer: "Sua Empresa Atual"
email: "seu.email@exemplo.com"
```

### Editando a Página Inicial

**Arquivo:** `_pages/about.md`

Este arquivo contém sua biografia principal. Você pode editar:
- Sua apresentação pessoal
- Suas competências principais
- Sua trajetória profissional
- Seus objetivos de carreira

### Adicionando Novos Projetos

**Pasta:** `_portfolio/`

Para adicionar um novo projeto:

1. Crie um novo arquivo: `5_novo_projeto.md`
2. Use este template:

```markdown
---
title: "Nome do Projeto"
excerpt: "Breve descrição do projeto"
collection: portfolio
---

## Descrição do Projeto

Descreva aqui os detalhes do seu projeto...

### Tecnologias Utilizadas
- Tecnologia 1
- Tecnologia 2
- Tecnologia 3

### Resultados Alcançados
- Resultado 1
- Resultado 2
- Resultado 3
```

### Editando Projetos Existentes

Seus projetos atuais estão em:
- `_portfolio/1_automacao_shell_script.md`
- `_portfolio/2_infraestrutura_virtualizacao.md`
- `_portfolio/3_redes_mikrotik_samba.md`
- `_portfolio/4_monitoramento_zabbix_graylog.md`

Para editar, abra o arquivo e modifique o conteúdo após as linhas `---`.

---

## 🖼️ Como Adicionar Imagens

### Passo 1: Upload da Imagem
1. Coloque suas imagens na pasta `images/`
2. Use nomes descritivos: `projeto-automacao.jpg`

### Passo 2: Referenciar no Projeto
```markdown
![Descrição da imagem](../images/nome-da-imagem.jpg)
```

### Foto de Perfil
Para trocar sua foto de perfil, substitua o arquivo `images/profile.png` por sua nova foto.

---

## 📝 Dicas de Escrita

### Use Markdown
Markdown é uma linguagem simples para formatar texto:

```markdown
# Título Principal
## Subtítulo
### Subtítulo Menor

**Texto em negrito**
*Texto em itálico*

- Lista com bullets
- Item 2
- Item 3

1. Lista numerada
2. Item 2
3. Item 3

[Link para site](https://exemplo.com)
```

### Estrutura Recomendada para Projetos

1. **Visão Geral** - O que foi o projeto
2. **Contexto e Desafios** - Por que foi necessário
3. **Solução Desenvolvida** - Como você resolveu
4. **Tecnologias Utilizadas** - Ferramentas e linguagens
5. **Resultados Alcançados** - Impacto e benefícios
6. **Lições Aprendidas** - O que você aprendeu

---

## 🔧 Manutenção Regular

### Mensal
- [ ] Revisar informações de contato
- [ ] Verificar se todos os links funcionam
- [ ] Atualizar projetos em andamento

### Trimestral
- [ ] Adicionar novos projetos concluídos
- [ ] Atualizar competências técnicas
- [ ] Revisar descrição profissional

### Anual
- [ ] Fazer backup completo do repositório
- [ ] Revisar design e layout
- [ ] Atualizar foto de perfil se necessário

---

## 🆘 Resolução de Problemas

### Site não está carregando
1. Verifique se o repositório tem o nome correto: `seu-usuario.github.io`
2. Confirme que GitHub Pages está ativado nas configurações
3. Aguarde até 10 minutos para mudanças aparecerem

### Erro de formatação
1. Verifique se todas as linhas `---` estão corretas nos arquivos
2. Confirme que não há caracteres especiais quebrados
3. Use um editor de texto simples (Notepad, TextEdit)

### Imagens não aparecem
1. Verifique se o caminho da imagem está correto
2. Confirme que a imagem foi enviada para a pasta `images/`
3. Use apenas letras minúsculas e hífens nos nomes dos arquivos

---

## 📞 Suporte

### Recursos Úteis
- [Documentação do Jekyll](https://jekyllrb.com/docs/)
- [Guia do Markdown](https://www.markdownguide.org/)
- [Ajuda do GitHub Pages](https://docs.github.com/en/pages)

### Comunidades
- [Jekyll Talk](https://talk.jekyllrb.com/)
- [GitHub Community](https://github.community/)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/jekyll)

---

## 🎯 Próximos Passos Recomendados

1. **Domínio Personalizado** - Considere comprar um domínio próprio (ex: samaratrindade.com)
2. **Google Analytics** - Adicione para acompanhar visitantes
3. **SEO** - Otimize para mecanismos de busca
4. **Blog** - Considere adicionar uma seção de blog técnico
5. **Certificações** - Adicione suas certificações AWS quando obtiver

---

**Lembre-se:** Seu portfólio é um reflexo da sua carreira. Mantenha-o sempre atualizado com seus projetos mais recentes e conquistas profissionais!

*Manual criado em Agosto de 2025*

