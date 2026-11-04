# Rentu para o Claude

Plugin do Claude (claude.ai, Cowork e Claude Code) que liga o servidor MCP público da Rentu
(`https://rentu.com.br/api/mcp`) e traz a skill `calculadora-faturamento`, que explica a calculadora de diagnóstico de
faturamento baseado em dados e os seus limites. A Rentu é uma imobiliária de Florianópolis/SC que vende compactos para
aluguel por temporada.

## Instalação

No Claude Code:

```
/plugin install rentu-calculadora --marketplace fernandopereira-ship-it/rentu-plugin
```

No claude.ai e no Cowork, pelo diretório de plugins.

## Ferramentas sem login

- `estimar_faturamento_mercado`: faixa anual do diagnóstico de faturamento baseado em dados (P40, mediana e P70; P30 e
  P80 com precisão menor) por cidade, bairro, quartos e padrão, com erro medido (ou o aviso de que não foi medido) e fonte.
- `comparar_configuracoes`: 2 a 4 configurações lado a lado, sem ranking.
- `listar_bairros_estimativa`: onde há estimativa publicada.
- `simular_financiamento`: parcela Price ou SAC de um preço informado, sem receita.
- `tir_investimento`: simulação de TIR à vista e financiada, pelo código de um imóvel à venda na Rentu ou por preço e
  receita informados, com fonte e aviso.
- `buscar_imoveis`, `detalhe_imovel`, `listar_lancamentos` e `mercado_bairro`: imóveis à venda, lançamentos e o
  mercado de temporada dos bairros onde a Rentu tem imóveis.

## Ferramentas com conta Rentu

- `salvar_favorito`: salva um imóvel nos favoritos da conta.
- `pedir_visita`: pede uma visita à especialista da Rentu, só com o consentimento explícito da pessoa.

As duas agem na conta da pessoa e pedem login. Hoje o acesso é pelo token pessoal gerado em https://rentu.com.br/conta
(cabeçalho `Authorization: Bearer`); o login pela conta Rentu direto no assistente está em implantação.

## Dados e limites

Os números de mercado são estimativas com dados reais de anúncios do Airbnb (lista diária), nunca o resultado de um
imóvel. Estimativa com dados reais do Airbnb. Não é promessa de renda.

O plugin envia a `rentu.com.br` só os argumentos de cada chamada (e o token, nas duas ferramentas com conta). O servidor
guarda um registro de cada chamada (IP em hash, navegador e argumentos filtrados) por 5 anos e não o repassa a outros
serviços. Privacidade: https://rentu.com.br/privacidade. Termos: https://rentu.com.br/termos. Documentação:
https://rentu.com.br/mcp.

## Licença

MIT. Veja `LICENSE`.
