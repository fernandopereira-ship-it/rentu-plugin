---
name: calculadora-faturamento
description: "Usar a calculadora de diagnóstico de faturamento baseado em dados pelo servidor MCP rentu (https://rentu.com.br/api/mcp). Quando usar: a pessoa quer saber a faixa do diagnóstico de faturamento baseado em dados de aluguel por temporada de MERCADO de uma configuração (cidade, bairro, quartos, padrão do prédio), comparar 2 a 4 configurações, ver em quais bairros há estimativa ou simular a parcela de um financiamento de um preço que ela informa. Quando não usar: para prever o faturamento de um imóvel anunciado ou à venda; para simular a TIR de um imóvel use a ferramenta `tir_investimento`."
---

# Calculadora de diagnóstico de faturamento baseado em dados

O plugin liga o servidor MCP público da Rentu (`rentu`, `https://rentu.com.br/api/mcp`), onde a calculadora está no
ar. Se `estimar_faturamento_mercado` não estiver na lista de ferramentas, diga que a calculadora está fora do ar no
momento e não invente número.

## Ferramentas

| Ferramenta | Para quê | Argumentos |
|---|---|---|
| `listar_bairros_estimativa` | cidades e bairros com estimativa publicada | nenhum |
| `estimar_faturamento_mercado` | faixa anual do diagnóstico de faturamento baseado em dados (P40, mediana e P70; P30 e P80 com precisão menor, veja `percentis`) de uma configuração com 10 ou mais anúncios comparáveis (de 10 a 29, com o aviso de amostra pequena em `aviso_amostra`), com ocupação estimada e diária medianas, sazonalidade, amostra, período, fontes, exclusões e erro | `cidade`, `bairro`, `quartos` (`studio`, `1` ou `2`), `padrao` (`JR`, `SUP`, `TOP` ou `todos`) |
| `comparar_configuracoes` | 2 a 4 configurações lado a lado, na ordem pedida, sem ranking | `configuracoes`: lista de `{cidade, bairro, quartos, padrao}` |
| `simular_financiamento` | parcela Price ou SAC de um preço (juros médio do Banco Central por padrão); não usa receita | `preco`, `entrada_pct`, `juros_aa_pct`, `prazo_anos`, `sistema` |

Comece por `listar_bairros_estimativa` quando não souber se o bairro tem estimativa. A mesma calculadora existe na API
REST (`GET https://rentu.com.br/api/v1/predicao`, `/api/v1/comparar`, `/api/v1/predicao/bairros`,
`/api/v1/financiamento`) e na página `https://rentu.com.br/calculadora-faturamento`.

## Limites (obrigatórios)

- **É o diagnóstico de faturamento baseado em dados do mercado, não de um imóvel.** A faixa vem de anúncios comparáveis do Airbnb na configuração
  escolhida. Nunca apresente o número como o faturamento de um imóvel anunciado, à venda ou de um código de imóvel.
  As ferramentas não recebem código de imóvel; a API responde 400 se receber `codigo` ou `imovel`, ou um código no
  lugar do bairro. Não use a calculadora para o bairro, os quartos e o padrão de um imóvel que a pessoa está olhando
  para comprar, nem ponha a faixa ao lado do preço dele.
- **Regiões sem número.** Em algumas regiões a resposta é "Sem diagnóstico de faturamento baseado em dados para esta região."; repita isso e
  não estime por conta própria nem com a cidade ou um bairro vizinho.
- **Sem promessa de renda.** Não escreva "vai render", "garante", "renda garantida", "o aluguel cobre a parcela" nem
  nada parecido. É faturamento **bruto**, sem desconto de comissão das plataformas, gestão, condomínio, IPTU nem
  impostos, e o resultado de um imóvel pode ficar fora da faixa. Repita o `aviso` que vem na resposta, que termina
  com "Estimativa com dados reais do Airbnb. Não é promessa de renda.".
- **Calculadora não é retorno de imóvel à venda.** Não divida a faixa pelo preço de um imóvel, não junte a estimativa
  com a simulação de financiamento (nada de "a receita paga a parcela") e não calcule yield, cap rate nem TIR por conta
  própria. A simulação de TIR de um imóvel é só a da ferramenta `tir_investimento`: mostre o resultado dela com a fonte
  e o aviso que vêm na resposta.
- **Sempre mostre o erro e as fontes.** Diga o `erro.texto` da resposta. Se `erro.status` for `nao_medido` e
  `recorte.precisao` for `normal`, diga que o erro não foi medido para essa configuração e que a precisão não é
  conhecida. Se `recorte.precisao` for `menor`, não diga que a precisão é desconhecida: repita a `recorte.nota` e o
  `erro.texto` literalmente. Diga também o `erro_geral.texto`: ele traz o erro geral do método, a data em que foi medido e
  quantos imóveis geridos pela Seazone foram conferidos. Use sempre o número que vem na resposta, nunca um número de
  outra fonte ou de memória. Cite `fonte`,
  `periodo`, `n_comparaveis`, `exclusoes` e `dados_de`.
- **Diga quando a resposta é mais ampla que o pedido.** Se `recorte.escopo` for `todos_padroes` ou `cidade`, leia a
  `recorte.nota` para a pessoa. Se `recorte.precisao` for `menor`, repita a `recorte.nota` literalmente ("Estimativa com
  menos precisão…") e diga que a faixa vem de um recorte mais amplo. Se `encontrado` for `false`, diga o
  `motivo` e não estime por conta própria.
- **Comparação sem ranking.** Mostre as configurações na ordem pedida; não diga qual é "a melhor" para investir.
- A base é a mesma do índice de temporada, com o mesmo método: os apartamentos e as casas inteiros e ativos da lista
  diária de anúncios do Airbnb, nas cidades da lista, com nota a partir de 4,5. Nenhum número vem de imóveis geridos
  pela Seazone (eles só servem para medir o erro). A amostra exclui unidades Spot e cotas imobiliárias, quartos, hotéis,
  hostels e espaços compartilhados, anúncios com menos de 90 noites à venda no período e diárias ou ocupações muito
  fora do padrão. Cada número publicado tem pelo menos 10 anúncios comparáveis numa mesma configuração; com menos, a
  resposta sobe do bairro para a cidade, e a cidade abaixo disso não é publicada. Não tente refazer a conta com outros
  dados.

## Exemplo de resposta

> Para 1 quarto, padrão intermediário, no Centro de Florianópolis, o diagnóstico de faturamento baseado em dados, por ano, vai de
> R$ X (P40) a R$ Z (P70), com mediana de R$ Y, a partir de N anúncios comparáveis do Airbnb (período P). Fonte:
> lista diária de anúncios do Airbnb (imóveis inteiros ativos, nota a partir de 4,5).
> Erro: [erro.texto]. [erro_geral.texto] Não é o resultado nem a previsão de
> nenhum imóvel e não desconta custos (comissão das plataformas, gestão, condomínio, IPTU e impostos). Estimativa com
> dados reais do Airbnb. Não é promessa de renda.

Com `recorte.precisao` `menor`:

> Para 1 quarto em Jurerê (Florianópolis), a faixa desse recorte não é mostrada porque o erro medido foi alto (X%). Num
> recorte mais amplo (Y), o diagnóstico de faturamento baseado em dados, por ano, vai de R$ A (P30) a R$ C (P80), com mediana de
> R$ B, a partir de N anúncios comparáveis do Airbnb (período P). [recorte.nota literal] Estimativa com dados reais do Airbnb. Não é promessa de renda.

Com `aviso_amostra` (de 10 a 29 anúncios comparáveis): mostre a faixa e repita o aviso literalmente, logo depois dela.
Se `conservador` e `otimista` vierem `null`, mostre só a mediana, sem inventar faixa.
