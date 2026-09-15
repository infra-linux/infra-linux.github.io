# Como contribuir

Obrigado por melhorar o Ninja Linux. Este guia ajuda a manter os procedimentos claros, seguros e fáceis de consultar.

## Fluxo de contribuição

1. Atualize a `main` e crie uma branch descritiva, por exemplo `docs/guia-docker`.
2. Escreva ou revise o conteúdo com base no [padrão de documentação](docs/padrao-de-documentacao.md).
3. Revise comandos, caminhos, links e dados sensíveis.
4. Revise a renderização Markdown, os links e os exemplos de comando.
5. Abra um Pull Request explicando o objetivo, a validação realizada e o impacto da alteração.

## Segurança e qualidade

- Nunca inclua senhas, tokens, chaves privadas, IPs internos ou dados de clientes.
- Identifique comandos destrutivos com um aviso e descreva como validar o alvo antes de executá-los.
- Prefira exemplos com valores fictícios e nomes genéricos.
- Mantenha um assunto por página e use títulos objetivos.
- Revise o diff antes de enviar: `git diff --staged`.

O Pull Request deve ser revisado antes do merge.
