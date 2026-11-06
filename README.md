Projeto de Análise Comercial e BI (Power BI & DAX)

Este repositório contém uma solução completa de Business Intelligence desenvolvida para análise de desempenho comercial, auditoria de políticas de desconto e inteligência de mix de produtos, voltada para pequenos comércios e negócios locais.

📊 Visão Geral do Dashboard

(Insira aqui uma captura de tela principal do seu painel do Power BI após salvá-lo na pasta /images/dashboard_overview.png)

![Dashboard Overview](images/dashboard_overview.png)


1. Problema de Negócio

Pequenos comércios frequentemente enfrentam dificuldades para identificar quais produtos geram faturamento real versus quais consomem margem de lucro por meio de descontos descontrolados. O objetivo deste projeto foi auditar uma base transacional para responder a perguntas críticas de gestão:

Quais produtos lideram a receita e quais lideram o volume de unidades vendidas?

O crescimento do volume em março foi impulsionado por queima de preços (descontos agressivos) ou manutenção do valor real?

Qual produto concentra o maior esforço de abatimento comercial e qual é o risco financeiro associado?

2. Preparação e Limpeza de Dados (ETL)

A base inicial continha inconsistências operacionais comuns. O tratamento foi rigorosamente executado no Power Query (Linguagem M):

Remoção de Colunas Inexistentes/Lixo: Exclusão de colunas estruturais vazias (Column11 e Column12).

Normalização de Textos: Padronização de maiúsculas/minúsculas e remoção de espaços em branco nas colunas dimensionais (Produto e Regiao).

Auditoria de Linhas Duplicadas: Identificação e expurgo de registros duplicados, consolidando exatamente 49 registros válidos no trimestre (Qtr 1 de 2026).

Parametrização da Fonte: Implementação de parâmetro de caminho (CaminhoArquivo) para garantir a reprodutibilidade completa do projeto em qualquer máquina local ou no GitHub.

3. Dicionário de Dados

A tabela principal base_vendas_inicial é composta pelas seguintes dimensões e métricas auditadas:

Data: Data da transação (Tipo: Data).

Produto: Nome do item comercializado (Mochila, Garrafa, Caderno, Estojo, Caneta Azul).

Categoria: Classificação mercadológica (Papelaria, Acessórios).

Regiao: Localização geográfica da venda (Sudeste, Nordeste, Centro-Oeste, Norte, Sul).

Quantidade: Volume de unidades comercializadas por transação.

Preco_Unitario: Preço de tabela unitário padrão.

Desconto: Alíquota percentual de abatimento concedida.

Faturamento bruto: Receita potencial de tabela (Quantidade * Preço_Unitario).

Valor de desconto: Montante financeiro absoluto abatido (Faturamento bruto * Desconto).

Faturamento Líquido: Receita efetiva realizada (Faturamento bruto - Valor de desconto).

4. Arquitetura do Modelo e Medidas DAX

O modelo utiliza o esquema estrela simplificado e medidas DAX validadas para evitar armadilhas estatísticas (como média de médias ou somas indevidas de taxas unitárias):

A. Preço Médio Unitário (Ponderado por Volume)

Calcula a receita líquida média por unidade vendida, isolando o impacto do mix:

Preço Médio Unitário = DIVIDE(SUM('base_vendas_inicial'[Faturamento Líquido]), SUM('base_vendas_inicial'[Quantidade]))


B. Taxa de Desconto Efetiva

Mede o percentual real de abatimento concedido sobre o faturamento bruto da loja:

Taxa de Desconto = DIVIDE(SUM('base_vendas_inicial'[Valor de desconto]), SUM('base_vendas_inicial'[Faturamento bruto]), 0)


(Resultado geral validado: 3,48%)

C. Participação Regional no Faturamento Líquido

Calcula o peso relativo de cada região sobre a receita total da empresa:

Participação Regional = DIVIDE(SUM('base_vendas_inicial'[Faturamento Líquido]), CALCULATE(SUM('base_vendas_inicial'[Faturamento Líquido]), ALL('base_vendas_inicial'[Regiao])))


5. Resultados Executivos e Insights de Negócios

A. A Polarização do Mix: Faturamento vs. Volume

Mochila: Líder absoluta em faturamento líquido (R$ 5.094,00 / 34,96% do total), apesar de representar apenas 4,96% das unidades (45 unidades no trimestre). Possui o maior preço médio unitário (R$ 114,29 em março).

Caneta Azul: Líder absoluta em volume (480 unidades, 52,86% do total), mas com menor representatividade em receita (R$ 1.169,75, 8,03% do total) devido ao baixo preço unitário (R$ 2,44).

B. O Comportamento de Março (Fato vs. Hipótese)

Hipótese Refutada: A explosão de vendas em março (que saltou para 401 unidades frente a 225 em janeiro e 282 em fevereiro) não foi gerada por queima generalizada de preços. 4 dos 5 produtos mantiveram ou elevaram levemente o preço médio realizado no mês.

C. Auditoria Crítica de Descontos

A Mochila concentrou 5,67% de taxa de desconto efetiva, respondendo por 58,29% de todo o dinheiro de desconto concedido pela empresa no trimestre (R$ 306,00 de um total de R$ 524,95).

Recomendação de Gestão: Identificou-se que a Mochila recebeu descontos de até 15% em transações de baixo volume, evidenciando falha de alçada de desconto no balcão sem contrapartida de volume de compras.

6. Limitações Técnicas do Modelo Atual

Para manter a integridade analítica e evitar suposições falsas, documenta-se que:

Ausência de Custos (CMV): A base não dispõe de custo unitário de aquisição, impedindo no momento o cálculo de Margem de Contribuição e Lucro Líquido por produto.

Ausência de Identificadores (ID de Pedido/Cliente): Impede a mensuração precisa de Ticket Médio por transação isolada e Lifetime Value (LTV) de clientes.

7. 🛠️ Competências Aplicadas

Engenharia e Limpeza de Dados: Power Query, Linguagem M, Parametrização de Fontes, Tratamento de Nulos e Deduplicação.

Modelagem e DAX: Medidas Agregadas Ponderadas (DIVIDE, SUM), Indicadores de Rentabilidade e Auditoria de Descontos.

Data Storytelling & BI: Design de Relatórios Executivos, Cartões de KPI e Gráficos de Comportamento Comercial.

Visão de Negócios: Análise de Mix de Produtos (Curva ABC de Faturamento vs. Volume) e Governança de Alçadas de Desconto.

8. ⚙️ Pré-requisitos e Instalação

Power BI Desktop (versão atualizada para suporte a DAX avançado e parâmetros do Power Query).

Microsoft Excel (caso deseje inspecionar a base de dados bruta em /data/Resumo_Produtos.xlsx).

Clone este repositório em sua máquina local e atualize o parâmetro CaminhoArquivo no Power Query caso o diretório local exija ajuste.

🚀 Próximos Passos (Roadmap)

[ ] Integração com tabela de Custos (CMV) para cálculo dinâmico de Margem de Contribuição.

[ ] Implementação de IDs de Pedidos e Clientes para análise real de Ticket Médio e Recorrência.

[ ] Publicação do relatório interativo no Power BI Service (Web).

👤 Autor

Bruno Guimarães

Atuação: Analista de Dados & Especialista em Soluções de BI para Pequenos Negócios e Turismo.

LinkedIn:https://www.linkedin.com/in/bruno-marcio-de-jesus-guimar%C3%A3es-460247360/ | E-mail:guimaraesbruno78@gmail.com
