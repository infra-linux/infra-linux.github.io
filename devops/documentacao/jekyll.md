# Jekyll — Guia Completo e Didático

> Guia prático para aprender Jekyll e utilizar o conhecimento no projeto **Infra Linux**.

---

## 1. O que é Jekyll?

**Jekyll** é um gerador de sites estáticos escrito em Ruby.

Ele transforma arquivos como:

* Markdown
* HTML
* CSS
* JavaScript
* imagens
* dados
* templates

em um site HTML pronto para ser publicado.

A ideia básica é:

```text
Arquivos do projeto
        ↓
      Jekyll
        ↓
   HTML estático
        ↓
    Navegador
```

Por exemplo:

```text
pagina.md
   ↓
Jekyll + Layout + Liquid
   ↓
pagina.html
```

O navegador não precisa executar Ruby ou Jekyll.

Ele recebe apenas os arquivos finais:

```text
HTML
CSS
JavaScript
imagens
```

---

# 2. Jekyll é um framework?

Não exatamente.

Jekyll é principalmente um **Static Site Generator — SSG**.

Isso significa que ele gera previamente as páginas do site.

Uma aplicação tradicional poderia funcionar assim:

```text
Navegador
   ↓
Servidor
   ↓
Aplicação
   ↓
Banco de dados
   ↓
HTML
```

No Jekyll:

```text
Markdown
   ↓
Jekyll
   ↓
HTML
   ↓
Servidor web
   ↓
Navegador
```

Isso torna sites Jekyll muito adequados para:

* documentação
* blogs
* páginas pessoais
* portais técnicos
* documentação de projetos
* bases de conhecimento
* GitHub Pages

---

# 3. Jekyll e GitHub Pages

O GitHub Pages possui suporte para sites gerados com Jekyll.

No projeto Infra Linux:

```text
GitHub
   ↓
infra-linux/infra-linux.github.io
   ↓
GitHub Pages
   ↓
Jekyll
   ↓
Site publicado
```

O resultado final pode ser acessado pelo navegador.

No seu caso:

```text
infra-linux.github.io
```

O GitHub Pages faz o processo de construção do site e publica o resultado.

---

# 4. Estrutura básica de um projeto Jekyll

Um projeto Jekyll pode ter uma estrutura semelhante a:

```text
meu-site/
│
├── _config.yml
├── _layouts/
├── _includes/
├── _posts/
├── _data/
├── _sass/
├── assets/
├── images/
│
├── index.md
├── sobre.md
└── Gemfile
```

Cada diretório possui uma função específica.

---

# 5. `_config.yml`

O arquivo:

```text
_config.yml
```

é o principal arquivo de configuração do Jekyll.

Exemplo:

```yaml
title: Infra Linux
description: Base de conhecimento sobre Linux e infraestrutura
url: "https://infra-linux.github.io"
```

Podemos acessar essas informações através do Liquid:

```liquid
{{ site.title }}
```

Resultado:

```text
Infra Linux
```

E:

```liquid
{{ site.description }}
```

Resultado:

```text
Base de conhecimento sobre Linux e infraestrutura
```

---

# 6. Front Matter

O **Front Matter** é uma das partes mais importantes do Jekyll.

Ele aparece no início de um arquivo.

Exemplo:

```yaml
---
layout: default
title: Ansible
description: Guia de Ansible
---
```

O bloco começa e termina com:

```text
---
```

Exemplo completo:

```markdown
---
layout: default
title: Ansible
description: Guia de Ansible
---

# Ansible

Conteúdo da página.
```

O Jekyll utiliza essas informações durante a geração da página.

---

# 7. Principais campos do Front Matter

## `layout`

Define qual layout será utilizado.

```yaml
layout: default
```

---

## `title`

Define o título da página.

```yaml
title: Ansible - Playbooks
```

Pode ser utilizado no layout:

```liquid
{{ page.title }}
```

---

## `description`

Descrição da página:

```yaml
description: Guia completo sobre Playbooks do Ansible.
```

Pode ser utilizada:

```liquid
{{ page.description }}
```

---

## Variáveis personalizadas

Também podemos criar nossas próprias variáveis.

```yaml
---
layout: default
title: Ansible
categoria: DevOps
nivel: intermediário
---
```

No Liquid:

```liquid
{{ page.categoria }}
```

Resultado:

```text
DevOps
```

---

# 8. Markdown dentro do Jekyll

Jekyll trabalha muito bem com Markdown.

Exemplo:

````markdown
# Ansible

## Introdução

O Ansible é uma ferramenta de automação.

## Comando básico

```bash
ansible --version
````

````

O Jekyll transforma isso em HTML.

O:

```markdown
# Ansible
````

vira aproximadamente:

```html
<h1>Ansible</h1>
```

E:

```markdown
## Introdução
```

vira:

```html
<h2>Introdução</h2>
```

---

# 9. Markdown + Jekyll

Uma página típica do Infra Linux pode ser:

```text
ansible-playbooks.md
```

Conteúdo:

````markdown
---
layout: default
title: Ansible - Playbooks
description: Guia de Playbooks do Ansible.
---

# Playbooks

## Introdução

Playbooks são arquivos YAML utilizados para
descrever automações.

## Estrutura

```yaml
- name: Teste
  hosts: all
  tasks:
    - name: Verificar hostname
      command: hostname
````

````

O Jekyll combina:

```text
Front Matter
     +
Markdown
     +
Layout
     +
Liquid
     ↓
HTML
````

---

# 10. Layouts

Layouts são modelos reutilizáveis.

Normalmente ficam em:

```text
_layouts/
```

Exemplo:

```text
_layouts/
└── default.html
```

Um layout simples:

```html
<!DOCTYPE html>

<html lang="pt-BR">

<head>

  <title>{{ page.title }}</title>

</head>

<body>

  <header>
    <h1>Infra Linux</h1>
  </header>

  <main>
    {{ content }}
  </main>

</body>

</html>
```

A parte:

```liquid
{{ content }}
```

é extremamente importante.

Ela representa o conteúdo da página.

---

# 11. Como `{{ content }}` funciona

Imagine:

```markdown
---
layout: default
title: Linux
---

# Linux

Estudo de Linux.
```

O layout possui:

```html
<main>
  {{ content }}
</main>
```

O Jekyll substitui:

```liquid
{{ content }}
```

por:

```html
<h1>Linux</h1>

<p>Estudo de Linux.</p>
```

Resultado aproximado:

```html
<main>

  <h1>Linux</h1>

  <p>Estudo de Linux.</p>

</main>
```

---

# 12. Fluxo de processamento

Podemos visualizar:

```text
linux.md
   │
   ├── Front Matter
   │
   ├── Markdown
   │
   ▼
  Jekyll
   │
   ├── Layout
   ├── Liquid
   ├── Configuração
   └── Assets
   │
   ▼
 HTML
```

---

# 13. Includes

Includes permitem reutilizar pedaços de HTML.

Ficam normalmente em:

```text
_includes/
```

Exemplo:

```text
_includes/
├── header.html
├── footer.html
└── navigation.html
```

Podemos criar:

```html
<!-- _includes/header.html -->

<header>

  <h1>Infra Linux</h1>

</header>
```

Depois:

```liquid
{% include header.html %}
```

O Jekyll insere o conteúdo.

---

# 14. Por que usar Includes?

Imagine que o site tenha:

```text
Header
Menu
Footer
Busca
```

Sem includes, poderíamos repetir o mesmo HTML em várias páginas.

Com includes:

```text
_layouts/
    default.html

_includes/
    header.html
    navigation.html
    footer.html
```

O layout pode ficar:

```html
<body>

  {% include header.html %}

  {% include navigation.html %}

  <main>
    {{ content }}
  </main>

  {% include footer.html %}

</body>
```

Isso facilita muito a manutenção.

---

# 15. Liquid

**Liquid** é a linguagem de templates utilizada pelo Jekyll.

Ela permite acessar dados e executar lógica durante a geração do site.

Existem três elementos fundamentais:

```liquid
{{ }}
```
{% raw %}
```liquid
{% %}
```
{% endraw %}

```liquid
{# #}
```

No Jekyll, comentários Liquid normalmente são:

```liquid
{% comment %}
Comentário
{% endcomment %}
```

---

# 16. Imprimir variáveis

Para imprimir uma variável:

```liquid
{{ page.title }}
```

Exemplo:

```yaml
---
title: Ansible
---
```

No HTML:

```liquid
<h1>{{ page.title }}</h1>
```

Resultado:

```html
<h1>Ansible</h1>
```

---

# 17. Variáveis `site` e `page`

Duas variáveis muito importantes são:

```text
site
page
```

## `site`

Representa informações globais do site.

Exemplo:

```liquid
{{ site.title }}
```

---

## `page`

Representa informações da página atual.

Exemplo:

```liquid
{{ page.title }}
```

---

# 18. Diferença entre `site` e `page`

Imagine:

```yaml
# _config.yml

title: Infra Linux
```

E:

```yaml
---
title: Ansible
---
```

Então:

```liquid
{{ site.title }}
```

resulta:

```text
Infra Linux
```

Enquanto:

```liquid
{{ page.title }}
```

resulta:

```text
Ansible
```

---

# 19. Condicionais

Liquid permite:

```liquid
{% if page.toc %}

  <div id="toc">
    Índice
  </div>

{% endif %}
```

Se:

```yaml
toc: true
```

o bloco aparece.

Se:

```yaml
toc: false
```

não aparece.

---

# 20. `if`, `else`

Exemplo:

```liquid
{% if page.categoria == "Linux" %}

  <p>Documento Linux.</p>

{% else %}

  <p>Outro documento.</p>

{% endif %}
```

---

# 21. Loops

Liquid também permite percorrer listas.

Exemplo:

```liquid
{% for item in site.data.comandos %}

  <p>{{ item }}</p>

{% endfor %}
```

Isso é muito útil para:

* menus
* listas
* categorias
* servidores
* comandos
* documentação
* tabelas

---

# 22. Arquivos `_data`

O diretório:

```text
_data/
```

permite armazenar dados estruturados.

Exemplo:

```text
_data/
└── categorias.yml
```

Conteúdo:

```yaml
- nome: Linux
  url: /linux/

- nome: DevOps
  url: /devops/

- nome: Redes
  url: /redes/
```

No Liquid:

```liquid
{% for categoria in site.data.categorias %}

  <a href="{{ categoria.url }}">
    {{ categoria.nome }}
  </a>

{% endfor %}
```

Isso permite criar menus dinamicamente.

---

# 23. Collections

Collections são utilizadas para organizar grupos de documentos.

Exemplo:

```text
_ansible/
```

Poderíamos ter:

```text
_ansible/
├── playbooks.md
├── inventarios.md
├── modulos.md
└── variaveis.md
```

No `_config.yml`:

```yaml
collections:
  ansible:
    output: true
```

Isso transforma a coleção em uma estrutura organizada de documentos.

---

# 24. Quando usar Collections?

Collections são úteis quando existe uma grande quantidade de documentos relacionados.

Por exemplo:

```text
Linux
Ansible
Docker
Kubernetes
Nutanix
VMware
Squid
WatchGuard
```

Uma estrutura baseada em collections pode facilitar:

```text
Documentação
├── Linux
├── Ansible
├── Docker
├── Kubernetes
└── Redes
```

Para o Infra Linux, collections podem ser úteis quando a quantidade de documentação crescer bastante.

---

# 25. `_posts`

O diretório:

```text
_posts/
```

é tradicionalmente utilizado para posts.

Os arquivos seguem um padrão de nome:

```text
YYYY-MM-DD-titulo.md
```

Exemplo:

```text
2026-09-23-novo-documento.md
```

É especialmente útil para:

* blog
* notícias
* changelog
* atualizações
* artigos cronológicos

Para documentação técnica permanente, páginas comuns ou collections geralmente são mais adequadas.

---

# 26. Assets

Arquivos estáticos podem ficar em:

```text
assets/
```

Exemplo:

```text
assets/
├── css/
│   └── style.css
│
└── js/
    └── script.js
```

No layout:

```html
<link rel="stylesheet" href="/assets/css/style.css">
```

JavaScript:

```html
<script src="/assets/js/script.js"></script>
```

---

# 27. CSS

O CSS controla a aparência do site.

Exemplo:

```css
body {
  font-family: Arial, sans-serif;
}

h1 {
  font-size: 2rem;
}
```

No projeto Infra Linux, o CSS existente controla elementos como:

* menu lateral
* conteúdo
* cabeçalho
* rodapé
* código
* botões
* responsividade
* menu mobile

---

# 28. JavaScript

JavaScript adiciona comportamento ao site.

Por exemplo:

```javascript
document.addEventListener("DOMContentLoaded", () => {

  console.log("Infra Linux carregado");

});
```

No seu site, JavaScript pode ser utilizado para recursos como:

```text
Busca
Menu mobile
Copiar código
Índice automático
Navegação
Interações
```

---

# 29. Markdown não é HTML

É importante entender a diferença.

Markdown:

```markdown
# Linux
```

HTML:

```html
<h1>Linux</h1>
```

O Jekyll utiliza um processador Markdown para transformar o primeiro no segundo.

Fluxo:

```text
Markdown
   ↓
Processador Markdown
   ↓
HTML
```

---

# 30. Markdown pode conter HTML

Também podemos misturar:

```markdown
# Linux

Texto normal.

<div class="aviso">
  Atenção!
</div>
```

Isso pode ser útil quando o Markdown não oferece determinado recurso.

---

# 31. Links

Markdown:

```markdown
[Linux](https://www.linux.org/)
```

Também podemos utilizar Liquid:

```liquid
<a href="{{ site.baseurl }}/linux/">
  Linux
</a>
```

---

# 32. `site.baseurl`

Uma variável importante:

```liquid
{{ site.baseurl }}
```

Ela representa o caminho base do site.

Pode ser utilizada para criar links internos:

```liquid
<a href="{{ site.baseurl }}/linux/">
  Linux
</a>
```

Isso ajuda a evitar problemas quando o site é publicado em um subdiretório.

---

# 33. `site.url`

Outra variável importante:

```liquid
{{ site.url }}
```

Pode representar:

```text
https://infra-linux.github.io
```

Uma URL completa poderia ser construída como:

```liquid
{{ site.url }}{{ site.baseurl }}/linux/
```

---

# 34. Permalinks

Podemos controlar a URL de uma página.

Exemplo:

```yaml
---
layout: default
title: Ansible
permalink: /devops/ansible/
---
```

A página será disponibilizada em:

```text
/devops/ansible/
```

Isso é útil para organizar URLs.

---

# 35. Layouts hierárquicos

Um layout pode utilizar outro layout.

Exemplo:

```text
_layouts/
├── default.html
└── documentacao.html
```

`documentacao.html`:

```yaml
---
layout: default
---
```

E então:

```text
Página
   ↓
documentacao.html
   ↓
default.html
```

Isso permite criar diferentes tipos de página.

---

# 36. Exemplo de arquitetura

Uma estrutura mais organizada:

```text
_layouts/
├── default.html
├── documentacao.html
└── post.html
```

Podemos ter:

```text
documentacao.md
```

com:

```yaml
---
layout: documentacao
title: Ansible
---
```

Enquanto:

```text
artigo.md
```

usa:

```yaml
---
layout: post
title: Novo artigo
---
```

---

# 37. Navegação dinâmica

Podemos armazenar o menu em `_data`.

Exemplo:

```text
_data/menu.yml
```

```yaml
- nome: Linux
  url: /linux/

- nome: DevOps
  url: /devops/

- nome: Redes
  url: /redes/

- nome: Monitoramento
  url: /monitoramento/
```

No layout:

```liquid
<nav>

{% for item in site.data.menu %}

  <a href="{{ item.url }}">
    {{ item.nome }}
  </a>

{% endfor %}

</nav>
```

Agora o menu pode ser alterado sem modificar o HTML principal.

---

# 38. Índice automático

Um recurso interessante para documentação é gerar automaticamente um índice.

Podemos marcar uma página:

```yaml
---
layout: default
title: Ansible
toc: true
---
```

No layout:

```liquid
{% if page.toc %}

<nav id="toc">
  <strong>Índice</strong>
  <div id="toc-list"></div>
</nav>

{% endif %}
```

JavaScript pode localizar:

```html
<h2>
<h3>
```

e gerar os links automaticamente.

Arquitetura:

```text
Markdown
   ↓
<h2>
<h3>
   ↓
JavaScript
   ↓
Índice
   ↓
Links para as seções
```

---

# 39. Plugins

Jekyll possui suporte a plugins.

Um plugin pode adicionar funcionalidades ao processo de geração.

Exemplos de funcionalidades:

* sitemap
* feed
* processamento
* filtros Liquid
* geração de conteúdo

Porém, no GitHub Pages existe uma lista de plugins suportados.

Portanto, antes de adicionar um plugin:

```text
Plugin
   ↓
Verificar compatibilidade
   ↓
GitHub Pages
```

Evite instalar plugins simplesmente porque funcionam no computador local.

---

# 40. Gemfile

Projetos Jekyll normalmente utilizam:

```text
Gemfile
```

Exemplo:

```ruby
source "https://rubygems.org"

gem "github-pages", group: :jekyll_plugins
```

O Gemfile define as dependências Ruby do projeto.

---

# 41. Bundler

Bundler é utilizado para controlar as versões das gems utilizadas pelo projeto.

Instalação:

```bash
gem install bundler
```

Instalar dependências:

```bash
bundle install
```

Executar Jekyll:

```bash
bundle exec jekyll serve
```

---

# 42. Por que usar `bundle exec`?

Em vez de:

```bash
jekyll serve
```

é preferível utilizar:

```bash
bundle exec jekyll serve
```

porque o Bundler executa o Jekyll utilizando as versões definidas pelo projeto.

Isso reduz problemas de:

```text
versão diferente
dependência incompatível
ambiente diferente
```

---

# 43. Servidor local

Para testar o site:

```bash
bundle exec jekyll serve
```

Normalmente o servidor fica disponível em:

```text
http://localhost:4000
```

Também podemos utilizar:

```bash
bundle exec jekyll serve --livereload
```

O LiveReload permite atualizar automaticamente a página quando arquivos são modificados.

---

# 44. Processo de desenvolvimento

Um fluxo típico:

```text
Editar arquivo
     ↓
Salvar
     ↓
Jekyll processa
     ↓
Abrir navegador
     ↓
Verificar resultado
     ↓
Corrigir
     ↓
Commit
     ↓
Push
```

---

# 45. Desenvolvimento local do Infra Linux

No seu projeto, o fluxo pode ser:

```bash
cd ~/Projetos/infra-linux.github.io
```

Depois:

```bash
bundle install
```

E:

```bash
bundle exec jekyll serve
```

Abrir:

```text
http://localhost:4000
```

---

# 46. Estrutura recomendada para documentação

Para um projeto como o Infra Linux:

```text
infra-linux.github.io/
│
├── _config.yml
├── _layouts/
│   └── default.html
│
├── _includes/
│
├── _data/
│
├── assets/
│   ├── css/
│   └── js/
│
├── images/
│
├── linux/
│
├── devops/
│
├── redes/
│
├── squid/
│
├── monitoramento/
│
├── nutanix/
│
├── vmware/
│
├── watchguard/
│
├── windows/
│
├── troubleshooting/
│
├── index.md
├── Gemfile
└── Gemfile.lock
```

Essa organização separa:

```text
Código do site
     +
Documentação
     +
Dados
     +
Assets
```

---

# 47. Exemplo de página completa

Arquivo:

```text
devops/ansible/playbooks.md
```

Conteúdo:

````markdown
---
layout: default
title: Ansible - Playbooks
description: Guia prático de Playbooks do Ansible.
toc: true
---

# Playbooks

## Introdução

Playbooks são arquivos YAML utilizados para
descrever automações do Ansible.

## Estrutura

Um playbook básico:

```yaml
- name: Verificar servidores
  hosts: all
  tasks:

    - name: Verificar hostname
      command: hostname
````

## Tasks

### command

Executa comandos diretamente.

```yaml
- name: Hostname
  command: hostname
```

### shell

Executa comandos utilizando um shell.

```yaml
- name: Verificar espaço
  shell: df -h
```

## Execução

```bash
ansible-playbook playbook.yml
```

## Validação

Verifique:

```bash
ansible-playbook --syntax-check playbook.yml
```

## Troubleshooting

Em caso de erro, verificar:

```bash
ansible-playbook playbook.yml -vvv
```

````

---

# 48. Como o Jekyll interpreta essa página

O arquivo contém:

```text
Front Matter
````

Depois:

```text
Markdown
```

O Jekyll lê:

```yaml
layout: default
```

Então procura:

```text
_layouts/default.html
```

Depois processa o Markdown.

O conteúdo gerado é colocado em:

```liquid
{{ content }}
```

Resultado:

```text
playbooks.md
      ↓
Front Matter
      ↓
Markdown
      ↓
Jekyll
      ↓
default.html
      ↓
HTML
```

---

# 49. `_site`

Durante a construção local, Jekyll normalmente gera:

```text
_site/
```

Esse diretório contém o site final.

Exemplo:

```text
_site/
├── index.html
├── linux/
├── devops/
├── assets/
└── images/
```

É importante entender:

```text
Markdown
    ↓
Jekyll
    ↓
_site/
```

O `_site` é o resultado da construção.

---

# 50. Não editar `_site`

Normalmente não devemos editar:

```text
_site/
```

diretamente.

Devemos editar:

```text
.md
.html
.css
.js
.yml
```

e deixar o Jekyll gerar novamente:

```text
_site/
```

---

# 51. `.gitignore`

O projeto pode ignorar arquivos gerados localmente.

Exemplo:

```gitignore
_site/
.sass-cache/
.jekyll-cache/
.jekyll-metadata
.bundle/
```

Isso evita enviar arquivos temporários para o Git.

---

# 52. Erros comuns

## Erro 1 — Front Matter inválido

Errado:

```text
--
layout: default
--
```

Correto:

```text
---
layout: default
---
```

---

## Erro 2 — YAML inválido

Errado:

```yaml
title Infra Linux
```

Correto:

```yaml
title: Infra Linux
```

---

## Erro 3 — Layout inexistente

Página:

```yaml
layout: documentacao
```

mas não existe:

```text
_layouts/documentacao.html
```

Isso causará problema na geração.

---

# 53. Erro com Liquid

Errado:

```liquid
{{ page.title
```

Correto:

```liquid
{{ page.title }}
```

---

# 54. Erro com `{% %}`

Errado:

{% raw %}
```liquid
{% if page.toc %}
```
{% endraw %}

sem:

{% raw %}
```liquid
{% endif %}
```
{% endraw %}

Correto:

{% raw %}
```liquid
{% if page.toc %}

...

{% endif %}
```
{% endraw %}
---

# 55. YAML e indentação

YAML depende de indentação.

Errado:

```yaml
collections:
ansible:
  output: true
```

Correto:

```yaml
collections:
  ansible:
    output: true
```

Use espaços, preferencialmente dois por nível.

---

# 56. Como investigar erros

Execute:

```bash
bundle exec jekyll serve
```

Leia a mensagem de erro.

Para obter mais detalhes:

```bash
bundle exec jekyll build --trace
```

O `--trace` pode fornecer informações adicionais sobre onde o erro aconteceu.

---

# 57. Verificar configuração

Um bom processo é:

```bash
bundle exec jekyll build
```

Se terminar sem erro, a geração foi concluída.

Depois:

```bash
bundle exec jekyll serve
```

e testar no navegador.

---

# 58. Git + Jekyll

O Git controla os arquivos fonte.

Exemplo:

```text
Git
 │
 ├── Markdown
 ├── HTML
 ├── CSS
 ├── JavaScript
 ├── YAML
 └── configuração
```

Jekyll transforma esses arquivos em:

```text
HTML final
```

Podemos visualizar:

```text
Git
 ↓
Código-fonte
 ↓
Jekyll
 ↓
Site
 ↓
GitHub Pages
```

---

# 59. Fluxo recomendado para o Infra Linux

Uma rotina prática:

```bash
git pull
```

Editar:

```text
arquivo.md
```

Testar:

```bash
bundle exec jekyll build
```

Executar:

```bash
bundle exec jekyll serve
```

Testar no navegador.

Depois:

```bash
git status
```

Verificar:

```bash
git diff
```

Adicionar:

```bash
git add .
```

Commit:

```bash
git commit -m "Adiciona documentação sobre X"
```

Enviar:

```bash
git push
```

---

# 60. Conceito importante: fonte versus resultado

No Jekyll existem duas coisas diferentes.

## Fonte

```text
Markdown
HTML
CSS
JS
YAML
Liquid
```

## Resultado

```text
HTML
CSS
JS
imagens
```

O Jekyll é o processo que transforma a primeira estrutura na segunda.

```text
SOURCE
  │
  │ Jekyll
  ▼
OUTPUT
```

---

# 61. O que aprender primeiro

Para estudar Jekyll sem se perder, recomendo esta ordem:

```text
1. Markdown
       ↓
2. Estrutura do projeto
       ↓
3. Front Matter
       ↓
4. Layouts
       ↓
5. {{ content }}
       ↓
6. Liquid
       ↓
7. Includes
       ↓
8. CSS
       ↓
9. JavaScript
       ↓
10. Collections
       ↓
11. Data
       ↓
12. Plugins
       ↓
13. GitHub Pages
```

---

# 62. Exercício 1 — Criar uma página

Crie:

```text
teste.md
```

Com:

```markdown
---
layout: default
title: Página de teste
---

# Página de teste

Esta é minha primeira página Jekyll.

## Linux

Estudando Linux.

## Ansible

Estudando Ansible.
```

Execute:

```bash
bundle exec jekyll serve
```

Acesse a página.

---

# 63. Exercício 2 — Criar variável

No Front Matter:

```yaml
---
layout: default
title: Página de teste
autor: João
---
```

No conteúdo:

```liquid
Autor: {{ page.autor }}
```

Observe o resultado.

---

# 64. Exercício 3 — Criar condição

No Front Matter:

```yaml
---
layout: default
title: Página de teste
mostrar_aviso: true
---
```

No conteúdo:

```liquid
{% if page.mostrar_aviso %}

> Esta página possui um aviso.

{% endif %}
```

Depois altere:

```yaml
mostrar_aviso: false
```

e observe a diferença.

---

# 65. Exercício 4 — Criar Include

Crie:

```text
_includes/aviso.html
```

Conteúdo:

```html
<div class="aviso">
  Conteúdo incluído pelo Jekyll.
</div>
```

Na página:

```liquid
{% include aviso.html %}
```

Observe como o Jekyll insere o conteúdo.

---

# 66. Exercício 5 — Criar dados

Crie:

```text
_data/servidores.yml
```

Com:

```yaml
- nome: Proxy 01
  ip: 10.8.17.71

- nome: Proxy 02
  ip: 10.8.17.72

- nome: Proxy 03
  ip: 10.8.17.73
```

No Markdown:

```liquid
{% for servidor in site.data.servidores %}

- {{ servidor.nome }} — {{ servidor.ip }}

{% endfor %}
```

Isso gera a lista automaticamente.

---

# 67. Exercício 6 — Índice automático

Crie uma página:

```yaml
---
layout: default
title: Teste de índice
toc: true
---
```

Depois:

```markdown
# Teste

## Introdução

Texto.

## Linux

Texto.

### Comandos

Texto.

### Processos

Texto.

## Ansible

Texto.

### Playbooks

Texto.
```

Depois implemente o JavaScript de índice automático no layout.

---

# 68. Exercício 7 — Criar um layout

Crie:

```text
_layouts/documentacao.html
```

Faça esse layout utilizar:

```yaml
layout: default
```

Depois crie uma página:

```yaml
---
layout: documentacao
title: Teste
---
```

Observe a cadeia:

```text
Página
   ↓
documentacao.html
   ↓
default.html
```

---

# 69. Jekyll + Infra Linux

O conhecimento adquirido pode ser aplicado diretamente na arquitetura do projeto:

```text
                Infra Linux
                     │
          ┌──────────┴──────────┐
          │                     │
      Conteúdo              Interface
          │                     │
      Markdown             HTML/CSS/JS
          │                     │
          └──────────┬──────────┘
                     │
                   Jekyll
                     │
                  HTML
                     │
               GitHub Pages
```

---

# 70. Arquitetura mental do Jekyll

Uma forma simples de memorizar:

```text
_CONFIG
   │
   ├── Configuração
   │
   ▼
_MARKDOWN
   │
   ├── Conteúdo
   │
   ▼
_LAYOUTS
   │
   ├── Estrutura
   │
   ▼
_INCLUDES
   │
   ├── Componentes
   │
   ▼
_DATA
   │
   ├── Dados
   │
   ▼
LIQUID
   │
   ├── Lógica
   │
   ▼
Jekyll
   │
   ▼
HTML
```

---

# 71. Resumo dos principais diretórios

| Diretório/arquivo | Função                    |
| ----------------- | ------------------------- |
| `_config.yml`     | Configuração do site      |
| `_layouts/`       | Modelos de páginas        |
| `_includes/`      | Componentes reutilizáveis |
| `_data/`          | Dados estruturados        |
| `_posts/`         | Posts cronológicos        |
| `_sass/`          | Sass/CSS                  |
| `assets/`         | CSS, JS e outros assets   |
| `images/`         | Imagens                   |
| `_site/`          | Site gerado               |
| `Gemfile`         | Dependências Ruby         |
| `*.md`            | Documentação Markdown     |
| `*.html`          | HTML/Liquid               |

---

# 72. Resumo do Liquid

| Sintaxe          | Função            |
| ---------------- | ----------------- |
| `{{ variável }}` | Exibir valor      |
| `{% if %}`       | Condição          |
| `{% else %}`     | Alternativa       |
| `{% endif %}`    | Final da condição |
| `{% for %}`      | Loop              |
| `{% endfor %}`   | Final do loop     |
| `{% include %}`  | Inserir arquivo   |
| `{% assign %}`   | Criar variável    |
| `{% comment %}`  | Comentário        |

---

# 73. Resumo do Front Matter

Exemplo:

```yaml
---
layout: default
title: Ansible
description: Documentação sobre Ansible
categoria: DevOps
toc: true
permalink: /devops/ansible/
---
```

O Front Matter funciona como os **metadados da página**.

---

# 74. O que você deve dominar para trabalhar bem com Jekyll

Para utilizar Jekyll profissionalmente em um projeto como o Infra Linux, concentre-se principalmente em:

```text
Markdown
   ↓
Front Matter
   ↓
Layouts
   ↓
Liquid
   ↓
Includes
   ↓
Collections
   ↓
Data
   ↓
CSS
   ↓
JavaScript
   ↓
Git
   ↓
GitHub Pages
```

Você não precisa aprender tudo de uma vez.

---

# 75. Checklist de estudo

## Básico

* [ ] Entender o que é Jekyll
* [ ] Entender Static Site Generator
* [ ] Entender Markdown
* [ ] Entender Front Matter
* [ ] Entender `_config.yml`
* [ ] Entender `_layouts`
* [ ] Entender `{{ content }}`

## Intermediário

* [ ] Aprender Liquid
* [ ] Variáveis
* [ ] `if`
* [ ] `for`
* [ ] Includes
* [ ] `_data`
* [ ] Collections
* [ ] Permalinks
* [ ] Assets

## Avançado

* [ ] Layouts hierárquicos
* [ ] Plugins
* [ ] Sass
* [ ] JavaScript integrado ao Jekyll
* [ ] SEO
* [ ] Sitemap
* [ ] Feed
* [ ] GitHub Pages
* [ ] CI/CD
* [ ] Automação de build

---

# 76. Fluxo completo para memorizar

O principal conceito deste documento pode ser resumido em:

```text
                 PROJETO
                    │
       ┌────────────┼────────────┐
       │            │            │
    Markdown      Layout       Dados
       │            │            │
       │         Liquid           │
       │            │            │
       └────────────┼────────────┘
                    │
                  Jekyll
                    │
                    ▼
             HTML ESTÁTICO
                    │
                    ▼
             GitHub Pages
                    │
                    ▼
               NAVEGADOR
```

---

# 77. Regra prática

Quando estiver trabalhando no Infra Linux, pense:

> **Markdown contém o conhecimento.**
>
> **Front Matter descreve a página.**
>
> **Layout define a estrutura.**
>
> **Include reutiliza componentes.**
>
> **Liquid adiciona lógica.**
>
> **CSS define a aparência.**
>
> **JavaScript adiciona comportamento.**
>
> **Jekyll transforma tudo em um site estático.**
>
> **GitHub Pages publica o resultado.**

Essa é a base conceitual necessária para entender o funcionamento do seu site.

---

# 78. Próxima etapa recomendada

Depois de estudar este documento, a sequência prática no **Infra Linux** é:

```text
1. Entender o _config.yml
        ↓
2. Entender completamente o default.html
        ↓
3. Entender {{ content }}
        ↓
4. Entender Front Matter
        ↓
5. Criar um layout secundário
        ↓
6. Criar um include
        ↓
7. Criar _data/menu.yml
        ↓
8. Criar navegação dinâmica
        ↓
9. Criar índice automático
        ↓
10. Organizar a documentação com Collections
```

Assim, em vez de apenas aprender Jekyll teoricamente, você aprende **Jekyll modificando o próprio Infra Linux**.
