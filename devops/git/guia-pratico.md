---
layout: default
title: Git — guia prático
---

# Git — guia prático

Este guia reúne o fluxo essencial e os recursos de colaboração do Git para administrar a documentação e outros projetos.

## Antes de começar

Verifique a instalação e identifique seus commits:

```bash
git --version
git config --global user.name "Seu Nome"
git config --global user.email "voce@exemplo.com"
```

Nunca versione senhas, tokens, chaves privadas ou arquivos de configuração com dados sensíveis. Use um `.gitignore` para evitar inclusões acidentais.

## Fluxo diário

Para trabalhar em um repositório existente, clone-o uma vez e entre no diretório:

```bash
git clone https://github.com/usuario/repositorio.git
cd repositorio
```

Antes de iniciar uma alteração, atualize sua cópia e crie uma branch com um nome descritivo:

```bash
git switch main
git pull --ff-only origin main
git switch -c docs/tutorial-git
```

Depois de editar os arquivos, revise exatamente o que será enviado, registre a alteração e publique a branch:

```bash
git status
git diff
git add arquivo.md
git diff --staged
git commit -m "docs: adiciona tutorial de Git"
git push -u origin docs/tutorial-git
```

Abra um Pull Request para revisão. Após a aprovação e o merge, atualize sua `main` local antes de começar a próxima tarefa.

> `git add .` é útil apenas quando você conferiu o `git status` e sabe que todos os arquivos modificados devem entrar no commit.

## Comandos de consulta

| Comando | Uso |
| --- | --- |
| `git status` | Exibe arquivos alterados e o estado da branch. |
| `git diff` | Mostra alterações ainda fora da área de stage. |
| `git diff --staged` | Mostra o conteúdo que irá para o próximo commit. |
| `git log --oneline --graph --decorate` | Exibe um histórico compacto. |
| `git fetch origin` | Atualiza referências remotas sem alterar seus arquivos. |
| `git branch -a` | Lista branches locais e remotas. |
| `git remote -v` | Confirma os endereços dos repositórios remotos. |

## Branches e integração

Liste branches com `git branch`; o asterisco indica a atual. Para alternar, prefira:

```bash
git switch nome-da-branch
git switch -c nova-branch
```

Para integrar uma branch concluída localmente:

```bash
git switch main
git pull --ff-only origin main
git merge nome-da-branch
git push origin main
```

Em equipes, prefira o Pull Request como caminho de merge. Remova a branch somente depois de confirmar que ela foi integrada:

```bash
git branch -d nome-da-branch
git push origin --delete nome-da-branch
```

## Atualizações, conflitos e rebase

`git pull` obtém alterações remotas e as integra. `git fetch` apenas as baixa, permitindo inspecioná-las antes. O uso de `git pull --ff-only` evita criar merges inesperados.

Se houver conflito, o Git marca os trechos envolvidos. Edite o arquivo, mantenha a versão correta, remova os marcadores `<<<<<<<`, `=======` e `>>>>>>>`, então conclua:

```bash
git add arquivo-com-conflito.md
git commit
```

`git rebase main` reaplica os commits da sua branch sobre a `main`, deixando o histórico linear. Use-o apenas em commits que ainda não são compartilhados, ou combine-o previamente com a equipe. Não use `git push --force` em uma branch compartilhada; se for indispensável numa branch própria, prefira `git push --force-with-lease`.

## Desfazer com segurança

| Situação | Comando |
| --- | --- |
| Descartar alterações não adicionadas em um arquivo | `git restore arquivo.md` |
| Retirar um arquivo da área de stage, mantendo a edição | `git restore --staged arquivo.md` |
| Guardar trabalho temporariamente | `git stash` |
| Restaurar o último stash | `git stash pop` |
| Reverter um commit já publicado | `git revert ID_DO_COMMIT` |

`git reset --hard` apaga alterações locais e pode causar perda de trabalho. Use-o somente quando tiver certeza do alvo e não houver conteúdo a preservar.

## Recursos úteis

Marque versões estáveis com tags:

```bash
git tag -a v1.0 -m "Versão 1.0"
git push origin v1.0
```

Para descobrir a origem de uma linha:

```bash
git blame arquivo.md
```

## Checklist antes do push

- Confirme a branch com `git status`.
- Revise `git diff --staged`.
- Use uma mensagem de commit curta e objetiva.
- Não envie credenciais ou arquivos gerados sem necessidade.
- Atualize a branch e resolva conflitos antes de abrir o Pull Request.
