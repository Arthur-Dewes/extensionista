**README.md**

---

## 1. Visão Geral

> **Importante:** recomenda-se executar o código dentro de um ambiente Jupyter Notebook para garantir a criação automática das pastas de saída (`output/metricas` e `output/correlacoes`) e dos arquivos de trabalho necessários para as análises e visualizações.

Este repositório contém o código e os notebooks utilizados no projeto de análise de indicadores educacionais para os anos de 2018, 2019, 2022 e 2023. O objetivo é integrar diferentes bases de dados (AFD, DSU, IRD, IED), realizar o pré-processamento, conduzir análises estatísticas (distribuições, correlações e variações) e construir um dashboard interativo para suporte à tomada de decisão.

## 2. Processos de Preparação de Dados Executados

Durante o processo de preparação dos dados, foram realizadas as seguintes etapas:

1. **Leitura e nomeação de colunas**

   * Utilização de `pd.read_excel` com `names=...` para atribuir nomes personalizados a cada planilha.
   * Remoção de cabeçalhos e linhas iniciais irrelevantes com `skiprows` e `nrows`.
2. **Conversão de tipos**

   * Identificação de colunas numéricas a partir do quarto índice em cada DataFrame (`float_cols`).
   * Conversão para `float` via `pd.to_numeric(..., errors='coerce')`, substituindo entradas inválidas por `NaN`.
3. **Limpeza de colunas**

   * Exclusão de variáveis não utilizadas (`EJAf_*`, `EJAm_*`, `EP`, `EJA`, `ES`) com `.drop(...)`.
4. **Filtragem por região**

   * Seleção apenas das linhas correspondentes às 6 regiões geográficas do Brasil (`Sul`, `Sudeste`, `Norte`, `Nordeste`, `Centro-Oeste` e `Brasil`).
   * Uso da função `filtrar_regioes_por_ano(...)` para gerar DataFrames segmentados por ano e por região.
5. **Verificação de consistência**

   * Comparação de `shape` entre DataFrames de diferentes anos para garantir estrutura uniforme (`df_` e `regioes_`).
6. **Organização em dicionário**

   * Agrupamento dos DataFrames por ano e tipo de base em `dfs_por_ano`, facilitando iterações nas etapas seguintes.

## 3. Análises Estatísticas Automatizadas

1. **Cálculo de distribuições**

   * Função `calcular_distribuicoes(...)`:

     * Para cada ano e região, concatena as variáveis numéricas de todas as bases.
     * Gera resumo estatístico com média, desvio-padrão e coeficiente de variação.
     * Salva em arquivos `metricas_distribuicao_{ano}.xlsx`.
2. **Cálculo de correlações**

   * Função `calcular_correlacoes_completas(...)`:

     * Concatena variáveis numéricas de AFD, DSU, IRD e IED com prefixos.
     * Calcula matriz de correlação para cada região e exporta para `correlacoes_{ano}.xlsx`.
3. **Identificação de pares de alta correlação**

   * Função `pares_correlacao_alta(...)`:

     * Carrega matriz de correlação de `output/correlacoes`
     * Seleciona pares acima de um corte de 0.7 (positivo ou negativo)
     * Gera DataFrame com os principais pares e plota séries temporais desses pares.

## 4. Dashboard Criado

O dashboard interativo foi desenvolvido em **Plotly Dash** (ou outra ferramenta especificada) e está publicado em: [link para versão online do dashboard](#).

### Principais Componentes

* **Visão Geral por Região e Ano**: Seleção dinâmica que permite comparar indicadores entre regiões e anos.
* **Gráficos de Distribuição**: Boxplots, histogramas e violinos para examinar a dispersão dos indicadores.
* **Matriz de Correlação**: Heatmap interativo que exibe coeficientes de correlação entre variáveis.
* **Séries Temporais de Pares Correlacionados**: Linha dupla mostrando a evolução de variáveis com alta correlação.
* **Filtros e Widgets**: Permitem escolher a base (AFD, DSU, IRD, IED), indicador específico e intervalo de anos.

## 5. Insights Obtidos

* Foi observada alta correlação (>0.8) entre o percentual de docentes com habilitação superior (DSU) e a adequação de formação (AFD) nas regiões Sudeste e Sul.
* Regiões com maior coeficiente de variação nos indicadores de regularidade do corpo docente (IRD) apresentaram maior instabilidade nas métricas de esforço docente (IED).
* A análise de séries temporais revelou tendências de melhoria na adequação da formação (AFD) de 2018 a 2023, especialmente no Centro-Oeste.

## 6. Conclusão

Com base na variação percentual das médias dos indicadores entre 2018 e 2023, observam-se avanços relevantes, especialmente na formação e atuação de docentes no Ensino Infantil, com alguns indicadores apresentando crescimentos superiores a 100%. Contudo, os maiores declínios foram registrados em grupos mais vulneráveis, com quedas acentuadas na regularidade e qualificação do corpo docente. Em síntese, há progresso, mas desigual: reforça-se a necessidade de políticas educacionais mais equitativas e focadas nas regiões e grupos historicamente desfavorecidos.

