# rentu-calculadora

Plugin do Claude que liga o servidor MCP público da Rentu (`https://rentu.com.br/api/mcp`) e traz a skill
`calculadora-faturamento`, que explica as ferramentas da calculadora de diagnóstico de faturamento baseado em dados e os seus limites. Feito para claude.ai, Cowork e Claude Code (veja Instalar).

## Instalar

- No Claude Code: `claude plugin install rentu-calculadora --marketplace fernandopereira-ship-it/rentu-plugin`
- No claude.ai e no Cowork: o envio ao diretório da Anthropic está em andamento; ainda não dá para instalar por lá.

## Ferramentas

Calculadora, sem login:

- `estimar_faturamento_mercado`: faixa anual do diagnóstico de faturamento baseado em dados (P40, mediana e P70; P30 e P80 com precisão menor) por cidade, bairro, quartos
  e padrão, com erro medido (ou o aviso de que não foi medido) e fonte.
- `comparar_configuracoes`: 2 a 4 configurações lado a lado, sem ranking.
- `listar_bairros_estimativa`: onde há estimativa publicada.
- `simular_financiamento`: parcela pelo código de um imóvel à venda na Rentu ou por um preço informado, sem receita. Condomínio e IPTU entram no custo mensal.

Imóveis e simulação, sem login:

- `buscar_imoveis`: imóveis à venda na Rentu, com os filtros da página de compra.
- `detalhe_imovel`: ficha de um imóvel pelo código.
- `listar_lancamentos`: empreendimentos em lançamento.
- `mercado_bairro`: mercado de aluguel por temporada de um bairro onde a Rentu tem imóveis.
- `tir_investimento`: simulação de TIR à vista e financiado, pelo código do imóvel ou por um preço e uma receita informados.

Na conta da pessoa (ainda sem uso pelo plugin, veja o aviso abaixo):

- `salvar_favorito`: salva um imóvel nos favoritos da conta Rentu.
- `pedir_visita`: envia um pedido de visita à especialista da Rentu, com os aceites de privacidade e de WhatsApp.

**Aviso: pelo plugin, `salvar_favorito` e `pedir_visita` ainda não funcionam.** As duas pedem o token pessoal criado em https://rentu.com.br/conta, e o plugin não tem onde usá-lo: o `.mcp.json` não leva cabeçalho. Até o login seguro entrar, elas só respondem como obter o token e nada é gravado. Para salvar um imóvel ou pedir uma visita hoje, use o site https://rentu.com.br.

## O que o plugin envia a rentu.com.br

O plugin não guarda nada no seu computador e não roda programa local. A cada uso de uma ferramenta, o assistente envia a `https://rentu.com.br/api/mcp` os argumentos daquela ferramenta e, se houver, o token.
`pedir_visita` envia também nome, WhatsApp, e-mail e mensagem, que viram o pedido de visita.
O servidor registra cada chamada, com IP em hash, navegador e argumentos sem os dados pessoais, e não repassa esse registro a outros serviços. O prazo de guarda e os seus direitos estão em https://rentu.com.br/privacidade.

## Limites

O diagnóstico de faturamento baseado em dados é do mercado, nunca de um imóvel anunciado. Estimativa com dados reais do Airbnb. Não é promessa de renda. A simulação de `tir_investimento` traz rótulo, fonte e aviso próprios e também não é garantia de retorno.
Documentação: https://rentu.com.br/mcp.
