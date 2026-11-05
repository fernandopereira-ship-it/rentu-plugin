# rentu-calculadora

Plugin do Claude que liga a calculadora da Rentu (`https://rentu.com.br/api/mcp/calculadora`, sem login) e traz a skill
`calculadora-faturamento`, que explica as ferramentas da calculadora e os seus limites. Feito para claude.ai, Cowork e Claude Code (veja Instalar).

## Instalar

- No Claude Code: `claude plugin install rentu-calculadora --marketplace fernandopereira-ship-it/rentu-plugin`
- No claude.ai e no Cowork: ainda não está no diretório da Anthropic; por enquanto, só pelo Claude Code.

## Ferramentas

As 6 da calculadora, sem login:

- `estimar_faturamento_mercado`: faixa anual do diagnóstico de faturamento baseado em dados (P40, mediana e P70; P30 e P80 com precisão menor) por cidade, bairro, quartos
  e padrão, com erro medido (ou o aviso de que não foi medido) e fonte.
- `comparar_configuracoes`: 2 a 4 configurações lado a lado, sem ranking.
- `listar_bairros_estimativa`: onde há estimativa publicada.
- `simular_financiamento`: parcela pelo código de um imóvel à venda na Rentu ou por um preço informado, sem receita. Condomínio e IPTU entram no custo mensal.
- `tir_investimento`: simulação à vista × financiado pelo código de um imóvel à venda na Rentu (só se a página dele mostra TIR, com a mesma receita, os mesmos custos, a fonte e o aviso da página; senão, recusa) ou por um preço e uma receita informados.
- `comparar_imoveis`: 2 ou 3 imóveis à venda na Rentu lado a lado pelo código, com os números da página Comparar imóveis do site, cada um com rótulo, fonte e aviso.

Busca e ficha de imóveis, lançamentos, mercado por bairro, favoritos e pedido de visita pelo plugin: em breve com login. Hoje, use o site https://rentu.com.br.

## O que o plugin envia a rentu.com.br

O plugin não guarda nada no seu computador e não roda programa local. A cada uso de uma ferramenta, o assistente envia a `https://rentu.com.br/api/mcp/calculadora` os argumentos daquela ferramenta.
O servidor registra cada chamada, com IP em hash, navegador e argumentos, e não repassa esse registro a outros serviços. O prazo de guarda e os seus direitos estão em https://rentu.com.br/privacidade.

## Limites

O diagnóstico de faturamento baseado em dados é do mercado, nunca de um imóvel anunciado. Estimativa com dados reais do Airbnb. Não é promessa de renda. A simulação de financiamento não usa receita. Em `tir_investimento` e `comparar_imoveis`, cada número de imóvel vem com rótulo, fonte e aviso, e nenhum é garantia de retorno.
Documentação: https://rentu.com.br/mcp.
