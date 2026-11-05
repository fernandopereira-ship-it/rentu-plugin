---
name: calculadora-faturamento
description: "Usar a calculadora da Rentu pelo servidor MCP rentu (https://rentu.com.br/api/mcp/calculadora). Quando usar: a pessoa quer saber a faixa do diagnóstico de faturamento baseado em dados de aluguel por temporada de MERCADO de uma configuração (cidade, bairro, quartos, padrão do prédio), comparar 2 a 4 configurações, ver em quais bairros há estimativa, simular a parcela de um financiamento ou o investimento à vista × financiado, pelo código de um imóvel à venda na Rentu ou por valores que ela informa, ou comparar 2 ou 3 imóveis à venda na Rentu pelo código. Quando não usar: para prometer faturamento ou retorno, ou para estimar um imóvel com a faixa de mercado."
---

# Calculadora da Rentu

O plugin liga a calculadora da Rentu, sem login (`rentu`, `https://rentu.com.br/api/mcp/calculadora`). Se `estimar_faturamento_mercado`
não estiver na lista de ferramentas, diga que a calculadora está indisponível agora e não invente número.

## Ferramentas

| Ferramenta | Para quê | Argumentos |
|---|---|---|
| `listar_bairros_estimativa` | cidades e bairros com estimativa publicada | nenhum |
| `estimar_faturamento_mercado` | faixa anual do diagnóstico de faturamento baseado em dados (P40, mediana e P70; P30 e P80 com precisão menor, veja `percentis`) de uma configuração com 10 ou mais anúncios comparáveis (de 10 a 29, com o aviso de amostra pequena em `aviso_amostra`), com ocupação estimada e diária medianas, sazonalidade, amostra, período, fontes, exclusões e erro | `cidade`, `bairro`, `quartos` (`studio`, `1` ou `2`), `padrao` (`JR`, `SUP`, `TOP` ou `todos`) |
| `comparar_configuracoes` | 2 a 4 configurações lado a lado, na ordem pedida, sem ranking | `configuracoes`: lista de `{cidade, bairro, quartos, padrao}` |
| `simular_financiamento` | parcela Price ou SAC pelo código de um imóvel à venda na Rentu ou por um preço informado (juros médio do Banco Central por padrão), com seguro estimado e custo mensal com condomínio e IPTU; não usa receita | `codigo` ou `preco`; opcionais: `entrada_pct`, `juros_aa_pct`, `prazo_anos`, `sistema`, `condominio_mensal`, `iptu_anual` |
| `tir_investimento` | simulação à vista × financiado (TIR, desembolso, caixa do 1º ano, lucro e payback): pelo código de um imóvel à venda na Rentu, só calcula se a página dele mostra TIR, com o preço, os custos e a receita dessa seção da página, com rótulo, fonte e aviso (sem TIR na página, recusa com o motivo, sem número); ou pelo preço e pela receita que a pessoa informar | `codigo` ou `preco` + `receita_bruta_anual`; opcionais: `condominio_mensal`, `iptu_anual`, `entrada_pct`, `juros_aa_pct`, `prazo_anos`, `valorizacao_aa_pct`, `anos`, `gestao_pct` |
| `comparar_imoveis` | 2 ou 3 imóveis à venda na Rentu lado a lado, com os números da página Comparar imóveis: preço, situação, área, R$/m², quartos, banheiros, condomínio, mobiliado, faturamento bruto com rótulo, fonte e aviso, yield bruto, cap rate, cash on cash, fluxo de caixa e parcela estimada; `melhor_por_linha` traz o melhor valor de cada linha, como a página | `codigos`: lista de códigos |

Comece por `listar_bairros_estimativa` quando não souber se o bairro tem estimativa. A mesma calculadora existe na API
REST (`GET https://rentu.com.br/api/v1/predicao`, `/api/v1/comparar`, `/api/v1/predicao/bairros`,
`/api/v1/financiamento`) e nas páginas `https://rentu.com.br/calculadora-faturamento`, `https://rentu.com.br/simular` e
`https://rentu.com.br/comparar`. Na API REST, `/api/v1/financiamento`
só aceita preço; o `codigo` vale na ferramenta `simular_financiamento`.

## O que ainda não está no plugin

Busca e ficha de imóveis, lançamentos, mercado por bairro, favoritos e pedido de visita: em breve com login. Diga isso à pessoa e ofereça o site
https://rentu.com.br. Não invente preço nem dado de imóvel.

## Limites (obrigatórios)

- **É o diagnóstico de faturamento baseado em dados do mercado, não de um imóvel.** A faixa vem de anúncios comparáveis do Airbnb na configuração
  escolhida. Nunca apresente o número como o faturamento de um imóvel anunciado, à venda ou de um código de imóvel.
  `estimar_faturamento_mercado` e `comparar_configuracoes` não recebem código de imóvel: recusam `codigo` ou `imovel`,
  ou um código no lugar do bairro (a API REST responde 400). `simular_financiamento` recebe
  `codigo`, mas é simulação de parcela, não diagnóstico de faturamento. `tir_investimento` e `comparar_imoveis` também
  recebem código: o número de imóvel que eles trazem é o que vem na resposta, com o próprio rótulo e aviso. Não use a calculadora para o bairro, os quartos e o padrão de um imóvel que a pessoa está olhando
  para comprar, nem ponha a faixa ao lado do preço dele.
- **Regiões sem número.** Em algumas regiões a resposta é "Sem diagnóstico de faturamento baseado em dados para esta região."; repita isso e
  não estime por conta própria nem com a cidade ou um bairro vizinho.
- **Sem promessa de renda.** Não escreva "vai render", "garante", "renda garantida", "o aluguel cobre a parcela" nem
  nada parecido. É faturamento **bruto**, sem desconto de comissão das plataformas, gestão, condomínio, IPTU nem
  impostos, e o resultado de um imóvel pode ficar fora da faixa. Em `estimar_faturamento_mercado` e `comparar_configuracoes`,
  repita o `aviso` que vem na resposta, que termina com "Estimativa com dados reais do Airbnb. Não é promessa de renda.".
  Em `simular_financiamento`, repita o aviso próprio da simulação. Em `tir_investimento` e `comparar_imoveis`, repita o
  `aviso` e, em cada imóvel, o rótulo e o aviso do faturamento (`faturamento_rotulo`, `faturamento_aviso`, ou `faturamento_nota` em
  `comparar_imoveis`). Se o faturamento vier `null`, diga que o imóvel não tem esse número.
- **Sem conta de retorno fora das ferramentas.** Não divida a faixa pelo preço de um imóvel, não junte a estimativa com a
  simulação de financiamento (nada de "a receita paga a parcela") e não calcule yield, cap rate nem retorno por conta própria.
  Yield, cap rate, cash on cash, fluxo e TIR só valem como vêm em `comparar_imoveis` e `tir_investimento`, com as premissas da resposta.
  Se a TIR financiada ficar abaixo da TIR à vista, diga isso como a `leitura` da resposta explica.
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
- **Comparação sem ranking.** Mostre configurações e imóveis na ordem pedida; não diga qual é "a melhor" para investir.
  Em `comparar_imoveis`, `melhor_por_linha` vale só linha a linha (menor preço, maior área), não como recomendação.
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
