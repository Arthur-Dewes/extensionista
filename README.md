**README.md**

# Indicadores Educacionais - Descrição dos Dados

Este conjunto de dados apresenta diferentes aspectos da atuação docente na educação básica brasileira. Abaixo, estão descritos os principais indicadores utilizados:
## Adequação da Formação do Docente

Classifica os docentes com base na compatibilidade entre sua formação e a disciplina que lecionam:

    Grupo 1: Licenciatura ou bacharelado com complementação pedagógica na mesma disciplina.

    Grupo 2: Bacharelado na mesma disciplina, sem licenciatura ou complementação.

    Grupo 3: Licenciatura ou complementação pedagógica em área diferente da que leciona.

    Grupo 4: Outra formação superior não enquadrada nas categorias anteriores.

    Grupo 5: Sem curso superior completo.

Abrangência: Educação Infantil, Ensino Fundamental (total, anos iniciais e finais), Ensino Médio e EJA (fundamental e médio).
Desagregações: Ano, Unidade Geográfica, Localização, Dependência Administrativa.
# Esforço do Docente

Indicador baseado na carga de trabalho dos docentes (número de alunos, turnos, escolas e etapas atendidas):

    Nível 1: Até 25 alunos, 1 turno, 1 escola, 1 etapa.

    Nível 2: 25 a 150 alunos, 1 turno, 1 escola, 1 etapa.

    Nível 3: 25 a 300 alunos, 1-2 turnos, 1 escola, 1 etapa.

    Nível 4: 50 a 400 alunos, 2 turnos, até 2 escolas e 2 etapas.

    Nível 5: Mais de 300 alunos, 3 turnos, até 3 escolas e 3 etapas.

    Nível 6: Mais de 400 alunos, 3 turnos, até 3 escolas e 3 etapas.

Abrangência: Ensino Fundamental (total, anos iniciais e finais) e Ensino Médio.
Desagregações: Ano, Unidade Geográfica, Localização, Dependência Administrativa.
# Regularidade do Corpo Docente

Índice de regularidade da presença dos docentes, classificado em:

    Baixa: 0 a <2

    Média-Baixa: 2 a <3

    Média-Alta: 3 a <4

    Alta: 4 a <5

Quanto mais próximo de 5, mais regular a presença do docente.

Desagregações: Ano, Unidade Geográfica, Localização, Dependência Administrativa, e percentual de escolas por faixa do IRD.
# Porcentual de Docentes com Curso Superior

Indica a proporção de docentes com curso superior por etapa/modalidade de ensino.
Exemplo: no EJA, 90,8% dos docentes têm curso superior, implicando que 9,2% não têm.

Abrangência: Educação Infantil (total, creche, pré-escola), Ensino Fundamental (total, anos iniciais, anos finais), Ensino Médio, Educação Profissional, EJA e Educação Especial.
Desagregações: Ano, Unidade Geográfica, Localização, Dependência Administrativa.

---

## 1. Visão Geral

> **Importante:** deve-se retirar o zip do arquivo extensionista.zip (que contém os arquivos) e logo após executar o código dentro de um ambiente Jupyter Notebook para garantir a criação automática das pastas de saída (`output/metricas` e `output/correlacoes`) e dos arquivos de trabalho necessários para as análises e visualizações.

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

