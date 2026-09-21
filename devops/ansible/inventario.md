---
layout: default
title: Inventários Ansible
---

# Inventários Ansible

## Introdução

O **inventário Ansible** é o arquivo que define os servidores que serão administrados pelo Ansible.

Ele informa, principalmente:

* quais servidores existem;
* quais servidores pertencem a cada grupo;
* endereço ou hostname dos servidores;
* usuário utilizado na conexão;
* porta SSH;
* variáveis específicas de hosts ou grupos;
* outras informações necessárias para a automação.

De forma simples:

```text
Inventário
    ↓
Quais servidores existem?
    ↓
Quais pertencem a cada grupo?
    ↓
Como o Ansible deve acessá-los?
```

O inventário é uma das primeiras coisas que devemos entender antes de trabalhar com Playbooks.

---

# 1. Control Node e Managed Nodes

Em uma estrutura Ansible temos normalmente:

```text
                    CONTROL NODE
                         |
                       Ansible
                         |
          +--------------+--------------+
          |              |              |
       servidor        servidor       servidor
          01             02             03
```

O computador onde o Ansible está instalado é o **Control Node**.

Os servidores administrados são os **Managed Nodes**.

O inventário fica no Control Node.

Exemplo:

```text
/opt/ansible/inventory/hosts.ini
```

---

# 2. Para que serve o inventário?

Imagine que existam os seguintes servidores:

```text
10.0.27.72
10.0.27.73
10.0.27.74
10.0.27.80
10.0.27.81
```

Podemos criar grupos:

```text
Squid
Web
Linux
```

O inventário permite organizar esses servidores:

```text
                Linux
                  |
       +----------+----------+
       |                     |
     Squid                  Web
       |                     |
  .72 .73 .74             .80 .81
```

Assim, um Playbook pode executar somente nos servidores desejados.

Por exemplo:

```yaml
hosts: squid
```

significa:

> Execute este Play somente nos hosts pertencentes ao grupo `squid`.

---

# 3. Inventário no formato INI

O formato INI é um dos formatos mais simples e comuns para começar a estudar Ansible.

Exemplo:

```ini
[squid]
10.0.27.72
10.0.27.73
10.0.27.74

[web]
10.0.27.80
10.0.27.81
```

A estrutura é:

```text
[nome_do_grupo]
host
host
host
```

---

# 4. Grupos

Os nomes entre colchetes representam grupos.

Exemplo:

```ini
[squid]
10.0.27.72
10.0.27.73
10.0.27.74
```

O grupo é:

```text
squid
```

E seus hosts são:

```text
10.0.27.72
10.0.27.73
10.0.27.74
```

Podemos criar quantos grupos forem necessários.

Exemplo:

```ini
[squid]
10.0.27.72
10.0.27.73
10.0.27.74

[web]
10.0.27.80
10.0.27.81

[banco]
10.0.27.90
10.0.27.91
```

---

# 5. Um host pode pertencer a vários grupos

Um mesmo servidor pode participar de vários grupos.

Exemplo:

```ini
[squid]
10.0.27.72
10.0.27.73

[linux]
10.0.27.72
10.0.27.73
10.0.27.80

[producao]
10.0.27.72
10.0.27.80
```

O servidor:

```text
10.0.27.72
```

pertence aos três grupos:

```text
squid
linux
producao
```

Isso permite organizar o inventário por diferentes critérios.

---

# 6. Grupo geral

Podemos criar um grupo contendo todos os servidores Linux.

Por exemplo:

```ini
[linux]
10.0.27.72
10.0.27.73
10.0.27.74
10.0.27.80
10.0.27.81
```

Agora podemos executar:

```bash
ansible -i inventory/hosts.ini linux -m ping -k
```

O Ansible tentará acessar todos os servidores desse grupo.

---

# 7. Executando um comando em um grupo

Podemos utilizar o comando:

```bash
ansible -i inventory/hosts.ini squid -m ping -k
```

Nesse caso:

```text
-i inventory/hosts.ini
```

define o inventário.

```text
squid
```

define o grupo.

```text
-m ping
```

define o módulo.

```text
-k
```

solicita a senha SSH.

---

# 8. Executando em todos os hosts

Podemos utilizar:

```bash
ansible -i inventory/hosts.ini all -m ping -k
```

`all` representa todos os hosts do inventário.

Exemplo:

```text
10.0.27.72
10.0.27.73
10.0.27.74
10.0.27.80
10.0.27.81
```

---

# 9. Listando os hosts

Podemos pedir ao Ansible para mostrar os hosts existentes:

```bash
ansible -i inventory/hosts.ini all --list-hosts
```

Exemplo:

```text
hosts (5):
    10.0.27.72
    10.0.27.73
    10.0.27.74
    10.0.27.80
    10.0.27.81
```

Também podemos consultar somente um grupo:

```bash
ansible -i inventory/hosts.ini squid --list-hosts
```

Resultado:

```text
hosts (3):
    10.0.27.72
    10.0.27.73
    10.0.27.74
```

Esse comando é muito útil para verificar se o inventário está correto antes de executar um Playbook.

---

# 10. Hostnames em vez de IPs

O inventário não precisa utilizar somente endereços IP.

Podemos usar nomes:

```ini
[squid]
s-sesu2772
s-sesu2773
s-sesu2775
```

Se o DNS resolver esses nomes, o Ansible poderá utilizá-los diretamente.

---

# 11. Nome do inventário x endereço real

Podemos criar um nome lógico para o host.

Exemplo:

```ini
[squid]
proxy01 ansible_host=10.0.27.72
proxy02 ansible_host=10.0.27.73
proxy03 ansible_host=10.0.27.74
```

Agora o Ansible conhece:

```text
proxy01
proxy02
proxy03
```

mas se conecta aos respectivos IPs:

```text
proxy01 → 10.0.27.72
proxy02 → 10.0.27.73
proxy03 → 10.0.27.74
```

Isso é muito útil porque permite usar nomes mais fáceis de entender nos Playbooks.

---

# 12. ansible_host

A variável:

```text
ansible_host
```

define o endereço real utilizado para conexão.

Exemplo:

```ini
[squid]
proxy01 ansible_host=10.0.27.72
```

No Playbook:

```yaml
hosts: squid
```

O Ansible encontrará:

```text
proxy01
```

e utilizará:

```text
10.0.27.72
```

para estabelecer a conexão.

---

# 13. ansible_user

Podemos definir o usuário SSH no inventário.

Exemplo:

```ini
[squid]
proxy01 ansible_host=10.0.27.72 ansible_user=root
```

Assim o Ansible utilizará:

```text
root
```

na conexão.

Sem isso, o Ansible normalmente utilizará o usuário local ou uma configuração definida em outro local.

---

# 14. ansible_port

Podemos especificar uma porta SSH diferente da padrão.

A porta padrão é:

```text
22
```

Exemplo:

```ini
[squid]
proxy01 ansible_host=10.0.27.72 ansible_port=2222
```

Nesse caso:

```text
SSH → 10.0.27.72:2222
```

---

# 15. Exemplo completo de host

Podemos combinar várias variáveis:

```ini
[squid]
proxy01 ansible_host=10.0.27.72 ansible_user=root ansible_port=22
```

Temos:

```text
Nome lógico:
proxy01

Endereço:
10.0.27.72

Usuário:
root

Porta:
22
```

---

# 16. ansible_password

Também é possível definir uma senha no inventário:

```ini
[squid]
proxy01 ansible_host=10.0.27.72 ansible_user=root ansible_password=senha
```

Porém, **não é recomendado armazenar senhas diretamente no inventário**.

Senhas devem ser protegidas utilizando mecanismos como **Ansible Vault** ou outros métodos seguros de gerenciamento de credenciais.

No ambiente de estudo, podemos utilizar:

```bash
-k
```

para solicitar a senha durante a execução.

---

# 17. Grupos de grupos

O Ansible permite criar grupos que agrupam outros grupos.

Exemplo:

```ini
[squid]
10.0.27.72
10.0.27.73

[web]
10.0.27.80
10.0.27.81

[infra:children]
squid
web
```

Agora:

```text
infra
├── squid
│   ├── 10.0.27.72
│   └── 10.0.27.73
│
└── web
    ├── 10.0.27.80
    └── 10.0.27.81
```

Podemos executar:

```bash
ansible -i inventory/hosts.ini infra -m ping -k
```

O comando será executado nos servidores dos grupos `squid` e `web`.

---

# 18. Estrutura hierárquica

Uma organização maior pode ser:

```ini
[producao_squid]
10.0.27.72
10.0.27.73

[homologacao_squid]
10.0.28.72
10.0.28.73

[squid:children]
producao_squid
homologacao_squid

[producao]
producao_squid

[homologacao]
homologacao_squid

[infra:children]
producao
homologacao
```

Isso permite trabalhar com diferentes níveis de organização.

---

# 19. Variáveis de grupo

Podemos associar variáveis a um grupo.

Exemplo:

```ini
[web]
web01 ansible_host=10.0.27.80
web02 ansible_host=10.0.27.81

[web:vars]
porta_http=80
ambiente=producao
```

Os hosts do grupo `web` receberão essas variáveis.

No Playbook:

```yaml
- name: Mostrar ambiente
  ansible.builtin.debug:
    msg: "Ambiente: {{ ambiente }}"
```

Resultado:

```text
Ambiente: producao
```

---

# 20. Variáveis de host

Também podemos definir variáveis específicas para um host.

Exemplo:

```ini
[web]
web01 ansible_host=10.0.27.80 porta_http=80
web02 ansible_host=10.0.27.81 porta_http=8080
```

Agora cada servidor possui um valor diferente.

```text
web01 → porta 80
web02 → porta 8080
```

---

# 21. inventory_hostname

O Ansible possui uma variável especial chamada:

```text
inventory_hostname
```

Ela representa o nome do host conforme aparece no inventário.

Exemplo:

```ini
[squid]
proxy01 ansible_host=10.0.27.72
```

No Playbook:

```yaml
- name: Mostrar host
  ansible.builtin.debug:
    msg: "{{ inventory_hostname }}"
```

Resultado:

```text
proxy01
```

---

# 22. ansible_host

Já:

```text
ansible_host
```

representa o endereço utilizado para conexão.

Exemplo:

```ini
[squid]
proxy01 ansible_host=10.0.27.72
```

Podemos ter:

```text
inventory_hostname = proxy01
ansible_host       = 10.0.27.72
```

Essa diferença é importante.

---

# 23. Exemplo prático

Inventário:

```ini
[squid]
proxy01 ansible_host=10.0.27.72
```

Playbook:

```yaml
---
- name: Teste
  hosts: squid
  gather_facts: false

  tasks:

    - name: Mostrar informações
      ansible.builtin.debug:
        msg:
          - "Nome: {{ inventory_hostname }}"
          - "IP: {{ ansible_host }}"
```

Resultado:

```text
Nome: proxy01
IP: 10.0.27.72
```

---

# 24. Inventário baseado em funções

Uma boa forma de organizar infraestrutura é utilizar a função do servidor.

Exemplo:

```ini
[proxy]
10.0.27.72
10.0.27.73

[web]
10.0.27.80
10.0.27.81

[banco]
10.0.27.90
10.0.27.91

[monitoramento]
10.0.27.100
```

Isso permite criar Playbooks específicos:

```text
diagnostico_proxy.yml
diagnostico_web.yml
diagnostico_banco.yml
diagnostico_monitoramento.yml
```

---

# 25. Inventário baseado em ambiente

Outra possibilidade é organizar por ambiente:

```ini
[producao]
10.0.27.72
10.0.27.80
10.0.27.90

[homologacao]
10.0.28.72
10.0.28.80
10.0.28.90

[desenvolvimento]
10.0.29.72
10.0.29.80
10.0.29.90
```

Isso facilita executar uma automação em somente um ambiente.

Exemplo:

```bash
ansible-playbook playbook.yml --limit producao
```

---

# 26. Inventário baseado em localização

Também podemos organizar servidores por localidade.

Exemplo:

```ini
[brasilia]
10.0.27.72
10.0.27.73

[sao_paulo]
10.0.30.72
10.0.30.73

[rio]
10.0.31.72
10.0.31.73
```

Podemos combinar grupos.

```ini
[linux]
10.0.27.72
10.0.27.73
10.0.30.72
10.0.30.73
10.0.31.72
10.0.31.73
```

---

# 27. Selecionando hosts

O comando `ansible` permite selecionar grupos.

Todos:

```bash
ansible all -m ping
```

Somente Squid:

```bash
ansible squid -m ping
```

Somente Web:

```bash
ansible web -m ping
```

Podemos utilizar `--limit` em Playbooks:

```bash
ansible-playbook playbook.yml --limit squid
```

---

# 28. Limiting

Imagine que o grupo tenha:

```text
proxy01
proxy02
proxy03
proxy04
```

Podemos testar somente:

```bash
ansible-playbook playbook.yml --limit proxy01
```

Isso é uma excelente prática durante desenvolvimento.

Fluxo recomendado:

```text
Playbook
   ↓
--limit proxy01
   ↓
teste
   ↓
--limit squid
   ↓
teste em grupo
   ↓
produção
```

---

# 29. Padrões de seleção

O Ansible possui padrões para selecionar hosts.

Exemplo:

```bash
ansible 'squid:web' -m ping
```

Seleciona hosts de `squid` ou `web`.

Para interseção:

```bash
ansible 'linux:&producao' -m ping
```

Seleciona hosts que pertencem aos dois grupos.

Para exclusão:

```bash
ansible 'all:!homologacao' -m ping
```

Seleciona todos, exceto os hosts de `homologacao`.

---

# 30. Inventário YAML

Além do formato INI, o Ansible também suporta inventários em YAML.

Exemplo:

```yaml
all:
  children:

    squid:
      hosts:
        proxy01:
          ansible_host: 10.0.27.72
        proxy02:
          ansible_host: 10.0.27.73

    web:
      hosts:
        web01:
          ansible_host: 10.0.27.80
        web02:
          ansible_host: 10.0.27.81
```

Visualmente:

```text
all
├── squid
│   ├── proxy01
│   └── proxy02
│
└── web
    ├── web01
    └── web02
```

---

# 31. INI ou YAML?

Para começar, o formato INI costuma ser mais simples:

```ini
[squid]
10.0.27.72
10.0.27.73
```

O YAML pode ser interessante em inventários maiores:

```yaml
all:
  children:
    squid:
      hosts:
        proxy01:
          ansible_host: 10.0.27.72
```

O mais importante é manter consistência no projeto.

---

# 32. Inventários separados

Em ambientes maiores, podemos utilizar inventários diferentes:

```text
inventory/
├── producao.ini
├── homologacao.ini
└── desenvolvimento.ini
```

Executar produção:

```bash
ansible-playbook \
  -i inventory/producao.ini \
  playbooks/diagnostico.yml
```

Executar homologação:

```bash
ansible-playbook \
  -i inventory/homologacao.ini \
  playbooks/diagnostico.yml
```

Isso reduz o risco de executar uma automação no ambiente errado.

---

# 33. Um inventário para vários ambientes

Também podemos manter tudo em um único inventário:

```ini
[producao]
proxy01
web01
db01

[homologacao]
proxy02
web02
db02
```

E selecionar:

```bash
ansible-playbook playbook.yml --limit producao
```

ou:

```bash
ansible-playbook playbook.yml --limit homologacao
```

---

# 34. Variáveis em arquivos separados

À medida que o projeto cresce, pode ser melhor separar as variáveis do inventário.

Uma estrutura comum é:

```text
inventory/
├── hosts.ini
├── group_vars/
└── host_vars/
```

Por exemplo:

```text
group_vars/
├── linux.yml
├── squid.yml
└── web.yml
```

E:

```text
host_vars/
├── proxy01.yml
└── proxy02.yml
```

---

# 35. group_vars

Imagine:

```text
inventory/
├── hosts.ini
└── group_vars/
    └── squid.yml
```

Arquivo:

```yaml
memoria_alerta: 80
cpu_alerta: 90
```

Todos os hosts do grupo `squid` poderão utilizar essas variáveis.

No Playbook:

```yaml
- name: Mostrar limite
  ansible.builtin.debug:
    msg: "CPU: {{ cpu_alerta }}%"
```

---

# 36. host_vars

Podemos definir valores específicos para um servidor.

Estrutura:

```text
inventory/
├── hosts.ini
└── host_vars/
    └── proxy01.yml
```

Arquivo:

```yaml
ambiente: producao
prioridade: alta
```

Somente `proxy01` receberá essas variáveis.

---

# 37. Precedência de variáveis

Em projetos grandes, é possível definir a mesma variável em vários lugares.

Por isso é importante entender que o Ansible possui regras de precedência.

De forma simplificada:

```text
valores gerais
     ↓
variáveis de grupo
     ↓
variáveis de host
     ↓
variáveis passadas na execução
```

Existem várias outras categorias e regras de precedência.

A recomendação é evitar definir a mesma variável em muitos lugares sem necessidade.

---

# 38. Inventário dinâmico

Até aqui utilizamos inventários estáticos.

Exemplo:

```ini
[web]
10.0.27.80
10.0.27.81
```

Em ambientes de nuvem ou grandes datacenters, a infraestrutura pode mudar constantemente.

Nesse cenário, podemos utilizar **inventário dinâmico**.

A fonte pode ser:

```text
AWS
Azure
Google Cloud
VMware
Nutanix
Kubernetes
CMDB
API
```

O inventário é então obtido automaticamente da infraestrutura.

---

# 39. Inventário estático x dinâmico

## Estático

```text
arquivo
   ↓
hosts definidos manualmente
```

Exemplo:

```ini
[web]
10.0.27.80
10.0.27.81
```

## Dinâmico

```text
fonte externa
   ↓
API/plugin
   ↓
inventário
   ↓
Ansible
```

Em ambientes pequenos, inventário estático costuma ser suficiente.

Em ambientes grandes e dinâmicos, o inventário dinâmico pode reduzir bastante a manutenção manual.

---

# 40. ansible-inventory

O comando:

```bash
ansible-inventory
```

permite consultar o inventário.

Exemplo:

```bash
ansible-inventory \
  -i inventory/hosts.ini \
  --list
```

Ele apresenta a estrutura do inventário em JSON.

---

# 41. Visualizando a árvore

Um comando muito útil:

```bash
ansible-inventory \
  -i inventory/hosts.ini \
  --graph
```

Exemplo:

```text
@all:
  |--@squid:
  |  |--proxy01
  |  |--proxy02
  |
  |--@web:
     |--web01
     |--web02
```

Esse comando é excelente para estudar e validar a organização do inventário.

---

# 42. Consultando um host

Também podemos utilizar:

```bash
ansible-inventory \
  -i inventory/hosts.ini \
  --host proxy01
```

Isso mostra as variáveis associadas ao host.

---

# 43. Exemplo completo

Inventário:

```ini
[squid]
proxy01 ansible_host=10.0.27.72 ansible_user=root
proxy02 ansible_host=10.0.27.73 ansible_user=root

[web]
web01 ansible_host=10.0.27.80 ansible_user=root
web02 ansible_host=10.0.27.81 ansible_user=root

[linux:children]
squid
web
```

Estrutura:

```text
linux
├── squid
│   ├── proxy01
│   └── proxy02
│
└── web
    ├── web01
    └── web02
```

---

# 44. Testando o inventário

Primeiro:

```bash
ansible-inventory \
  -i inventory/hosts.ini \
  --graph
```

Depois:

```bash
ansible \
  -i inventory/hosts.ini \
  linux \
  -m ping \
  -k
```

Depois:

```bash
ansible \
  -i inventory/hosts.ini \
  squid \
  -m ping \
  -k
```

Somente depois de validar:

```bash
ansible-playbook \
  -i inventory/hosts.ini \
  playbooks/diagnostico_squid.yml \
  -k
```

---

# 45. Inventário utilizado no projeto Infra Linux

Uma estrutura prática para o projeto pode ser:

```text
/opt/ansible/
├── inventory/
│   └── hosts.ini
│
└── playbooks/
    ├── diagnostico_squid.yml
    └── diagnostico_watchguard.yml
```

O inventário pode começar simples:

```ini
[squid]
10.0.27.72
```

Depois podemos adicionar outros hosts:

```ini
[squid]
10.0.27.72
10.0.27.73
10.0.27.74
```

E futuramente organizar grupos maiores.

---

# 46. Inventário e Playbook trabalham juntos

O inventário responde:

> **Onde executar?**

O Playbook responde:

> **O que executar?**

Exemplo:

```text
inventory
    |
    +-- squid
         |
         +-- 10.0.27.72
         +-- 10.0.27.73
         +-- 10.0.27.74
                 |
                 ↓
             Playbook
                 |
                 ↓
       diagnóstico do Squid
```

Essa separação é fundamental.

---

# 47. Inventário não é apenas uma lista de IPs

Um inventário simples pode parecer:

```ini
[squid]
10.0.27.72
10.0.27.73
```

Mas ele pode representar toda a organização lógica da infraestrutura:

```text
Ambiente
├── Produção
│   ├── Proxy
│   ├── Web
│   └── Banco
│
└── Homologação
    ├── Proxy
    ├── Web
    └── Banco
```

Portanto, o inventário pode funcionar como uma **visão lógica da infraestrutura**.

---

# 48. Boas práticas

## Utilize nomes significativos

Prefira:

```ini
[squid]
proxy01
proxy02
```

em vez de grupos genéricos como:

```ini
[servidores1]
```

---

## Organize por função

Exemplo:

```ini
[squid]
[web]
[banco]
[monitoramento]
```

---

## Utilize grupos de grupos

Quando necessário:

```ini
[linux:children]
squid
web
banco
```

---

## Evite senhas no inventário

Não faça:

```ini
ansible_password=MinhaSenha
```

em um arquivo que possa ser versionado ou compartilhado.

Prefira mecanismos seguros como:

```text
Ansible Vault
SSH keys
secret management
```

---

## Teste o inventário antes do Playbook

Utilize:

```bash
ansible-inventory -i inventory/hosts.ini --graph
```

e:

```bash
ansible -i inventory/hosts.ini all -m ping -k
```

---

# 49. Fluxo recomendado

Quando adicionar um novo servidor:

```text
1. Adicionar ao inventário
        ↓
2. Verificar --graph
        ↓
3. Verificar --list-hosts
        ↓
4. Testar ansible ping
        ↓
5. Executar Playbook com --limit
        ↓
6. Validar resultado
        ↓
7. Liberar execução no grupo
```

Exemplo:

```bash
ansible-inventory -i inventory/hosts.ini --graph
```

Depois:

```bash
ansible -i inventory/hosts.ini squid --list-hosts
```

Depois:

```bash
ansible -i inventory/hosts.ini squid -m ping -k
```

Depois:

```bash
ansible-playbook \
  -i inventory/hosts.ini \
  playbooks/diagnostico_squid.yml \
  --limit 10.0.27.72 \
  -k
```

---

# 50. Exercícios

## Exercício 1 — Inventário básico

Crie:

```text
inventory/estudo.ini
```

com:

```ini
[linux]
10.0.27.72
10.0.27.73
10.0.27.74
```

Execute:

```bash
ansible-inventory -i inventory/estudo.ini --graph
```

---

## Exercício 2 — Teste de conexão

Execute:

```bash
ansible \
  -i inventory/estudo.ini \
  linux \
  -m ping \
  -k
```

---

## Exercício 3 — Criar grupos

Crie:

```ini
[squid]
10.0.27.72
10.0.27.73

[web]
10.0.27.80
10.0.27.81
```

Depois visualize:

```bash
ansible-inventory -i inventory/estudo.ini --graph
```

---

## Exercício 4 — Grupo de grupos

Adicione:

```ini
[infra:children]
squid
web
```

Execute:

```bash
ansible \
  -i inventory/estudo.ini \
  infra \
  --list-hosts
```

---

## Exercício 5 — Nome lógico

Transforme:

```ini
[squid]
10.0.27.72
```

em:

```ini
[squid]
proxy01 ansible_host=10.0.27.72
```

Depois:

```bash
ansible-inventory -i inventory/estudo.ini --graph
```

---

## Exercício 6 — Variável

Adicione:

```ini
[squid:vars]
ambiente=producao
```

Crie um Playbook que mostre:

```text
Servidor
Ambiente
```

---

## Exercício 7 — Host específico

Crie:

```text
host_vars/proxy01.yml
```

e defina:

```yaml
funcao: proxy
```

Depois mostre a variável no Playbook.

---

# 51. Checklist de estudo

Antes de avançar, você deve conseguir explicar:

* [ ] O que é um inventário;
* [ ] O que é um host;
* [ ] O que é um grupo;
* [ ] O que é `all`;
* [ ] O que é `ansible_host`;
* [ ] O que é `ansible_user`;
* [ ] O que é `ansible_port`;
* [ ] O que é `inventory_hostname`;
* [ ] Como criar grupos;
* [ ] Como colocar um host em vários grupos;
* [ ] Como criar grupos de grupos;
* [ ] Como usar `--limit`;
* [ ] Como utilizar `ansible-inventory`;
* [ ] Como visualizar `--graph`;
* [ ] Como testar com `ansible -m ping`;
* [ ] Diferença entre inventário estático e dinâmico;
* [ ] Diferença entre `group_vars` e `host_vars`;
* [ ] Por que não colocar senhas diretamente no inventário.

---

# 52. Resumo

O inventário é a representação dos servidores que o Ansible administra.

A estrutura mais simples é:

```ini
[grupo]
host1
host2
host3
```

Podemos evoluir para:

```ini
[squid]
proxy01 ansible_host=10.0.27.72
proxy02 ansible_host=10.0.27.73

[web]
web01 ansible_host=10.0.27.80
web02 ansible_host=10.0.27.81

[linux:children]
squid
web
```

E então utilizar:

```bash
ansible-inventory -i inventory/hosts.ini --graph
```

para visualizar a estrutura.

Para testar:

```bash
ansible -i inventory/hosts.ini linux -m ping -k
```

Para executar um Playbook:

```bash
ansible-playbook \
  -i inventory/hosts.ini \
  playbooks/diagnostico.yml \
  -k
```

E para testar primeiro em um único servidor:

```bash
ansible-playbook \
  -i inventory/hosts.ini \
  playbooks/diagnostico.yml \
  --limit proxy01 \
  -k
```

A ideia principal é:

```text
                 INVENTÁRIO
                     |
             Onde executar?
                     |
        +------------+------------+
        |            |            |
      squid         web          banco
        |            |            |
      hosts        hosts        hosts
        |
        ↓
     PLAYBOOK
        |
        ↓
   O que executar?
```

**Inventário = onde**

**Playbook = o que**

**Task = ação**

**Module = ferramenta usada para executar a ação**

Essa separação é um dos fundamentos para construir automações Ansible organizadas e escaláveis.
