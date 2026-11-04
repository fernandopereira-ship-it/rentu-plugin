# rentu-calculadora

Plugin do Claude que liga a calculadora da Rentu (`https://rentu.com.br/api/mcp/calculadora`, sem login) e traz a skill
`calculadora-faturamento`, que explica as ferramentas de diagnóstico de faturamento baseado em dados e os seus limites. Feito para claude.ai, Cowork e Claude Code (veja Instalar).

## Instalar

- No Claude Code: `claude plugin install rentu-calculadora --marketplace fernandopereira-ship-it/rentu-plugin`
- No claude.ai e no Cowork: o envio ao diretório da Anthropic está em andamento; ainda não dá para instalar por lá.

## Ferramentas

As 4 da calculadora, sem login:

- `estimar_faturamento_mercado`: faixa anual do diagnóstico de faturamento baseado em dados (P40, mediana e P70; P30 e P80 com precisão menor) por cidade, bairro, quartos
  e padrão, com erro medido (ou o aviso de que não foi medido) e fonte.
- `comparar_configuracoes`: 2 a 4 configurações lado a lado, sem ranking.
- `listar_bairros_estimativa`: onde há estimativa publicada.
- `simular_financiamento`: parcela pelo código de um imóvel à venda na Rentu ou por um preço informado, sem receita. Condomínio e IPTU entram no custo mensal.

Busca e ficha de imóveis, lançamentos, mercado por bairro, simulação de retorno, favoritos e pedido de visita pelo plugin: em breve com login. Hoje, use o site https://rentu.com.br.

## O que o plugin envia a rentu.com.br

O plugin não guarda nada no seu computador e não roda programa local. A cada uso de uma ferramenta, o assistente envia a `https://rentu.com.br/api/mcp/calculadora` os argumentos daquela ferramenta.
O servidor registra cada chamada, com IP em hash, navegador e argumentos, e não repassa esse registro a outros serviços. O prazo de guarda e os seus direitos estão em https://rentu.com.br/privacidade.

## Limites

O diagnóstico de faturamento baseado em dados é do mercado, nunca de um imóvel anunciado. Estimativa com dados reais do Airbnb. Não é promessa de renda. A simulação de financiamento não usa receita.
Documentação: https://rentu.com.br/mcp.
