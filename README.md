# SERS-CP1.002

# Análise de Dados e Machine Learning - Projeto de Consumo de Energia

Este repositório contém a solução para as Partes 2, 3 e 4 de um projeto de análise de dados e machine learning focado em datasets de consumo de energia. A Parte 1 foi fornecida separadamente.

## Estrutura do Projeto

*   `solucao_parte2.md`: Contém a solução detalhada para os exercícios da Parte 2, utilizando o dataset `Individual Household Electric Power Consumption`.
*   `solucao_parte3.md`: Contém a solução detalhada para os exercícios da Parte 3, utilizando o dataset `Appliances Energy Prediction`.
*   `solucao_parte4.md`: Contém a solução detalhada para os exercícios da Parte 4, utilizando o Orange Data Mining para o dataset `Individual Household Electric Power Consumption`.
*   `README.md` (este arquivo): Visão geral do projeto e resumo das soluções.

## Resumo das Partes Concluídas



### PARTE 2 – Exercícios adicionais no dataset inicial

Esta parte focou em análises mais aprofundadas do dataset `Individual Household Electric Power Consumption`, abordando:

*   **Séries temporais por hora**: Conversão de dados para séries temporais horárias e identificação de horários de pico de consumo.
*   **Autocorrelação do consumo**: Análise da autocorrelação em diferentes lags (1h, 24h, 48h) para identificar padrões diários repetidos no consumo de energia.
*   **Redução de dimensionalidade com PCA**: Aplicação de PCA nas variáveis elétricas (`Global_active_power`, `Global_reactive_power`, `Voltage`, `Global_intensity`) para reduzir a dimensionalidade para 2 componentes principais e análise da variância explicada.
*   **Visualização de clusters no espaço PCA**: Combinação dos resultados do PCA com K-Means (3 clusters) para visualizar e avaliar a separação dos grupos no espaço reduzido.
*   **Regressão polinomial vs linear**: Comparação do desempenho de modelos de regressão linear simples e polinomial (grau 2) para prever `Global_active_power` a partir de `Voltage`, utilizando o RMSE e a visualização das curvas ajustadas.

Os códigos e análises detalhadas para esta parte podem ser encontrados em `solucao_parte2.md`.




### PARTE 3 – Novo dataset Appliances Energy Prediction

Esta parte explorou um novo dataset, `Appliances Energy Prediction`, com foco em consumo de energia e variáveis ambientais:

*   **Carregamento e inspeção inicial**: Carregamento do dataset e análise de tipos de dados e estatísticas descritivas (`.info()` e `.describe()`).
*   **Distribuição do consumo**: Criação de histogramas e séries temporais para a variável `Appliances` para entender sua distribuição e padrões ao longo do tempo.
*   **Correlações com variáveis ambientais**: Cálculo e visualização das correlações entre `Appliances` e diversas variáveis ambientais para identificar os fatores mais relacionados ao consumo.
*   **Normalização dos dados**: Aplicação de Min-Max Scaling às variáveis numéricas para padronizá-las para uso em modelos de machine learning.
*   **PCA**: Aplicação de PCA aos dados normalizados para reduzir a dimensionalidade para 2 componentes principais e visualização para identificar padrões ou agrupamentos naturais.
*   **Regressão Linear Múltipla**: Treinamento de um modelo de regressão linear para prever `Appliances` com base nas variáveis ambientais e avaliação do R² e RMSE.
*   **Random Forest Regressor**: Treinamento de um modelo Random Forest para prever `Appliances` e comparação do RMSE com o modelo de regressão linear, além da análise de importância das características.
*   **K-Means clustering**: Aplicação de K-Means com 3 a 5 clusters para identificar e interpretar perfis de consumo distintos.

Os códigos e análises detalhadas para esta parte podem ser encontrados em `solucao_parte3.md`.




### PARTE 4 – Exercícios no Orange Data Mining

Esta parte utilizou a ferramenta Orange Data Mining para explorar o dataset `Individual Household Electric Power Consumption` de forma visual e interativa:

*   **Importação e visualização inicial**: Utilização dos widgets `CSV File Import` e `Data Table` para carregar e inspecionar o dataset, verificando o número de variáveis e registros.
*   **Amostragem de dados (1%)**: Uso do widget `Sample Data` para criar uma amostra de 1% e comparação da distribuição de `Global_active_power` com a base completa usando o widget `Distribution`.
*   **Distribuição do consumo**: Análise da distribuição de `Global_active_power` usando o widget `Distribution` para verificar se o consumo se concentra em valores baixos ou altos.
*   **Relação entre variáveis elétricas**: Utilização do widget `Scatter Plot` para analisar a relação entre `Voltage` e `Global_intensity` e identificar a presença de correlação visível.
*   **Clustering com K-Means**: Aplicação do widget `k-Means` com 3 clusters nos atributos `Sub_metering_1`, `Sub_metering_2` e `Sub_metering_3`, e visualização dos grupos no `Scatter Plot` para interpretar padrões distintos de consumo doméstico.

Os passos detalhados e as interpretações para esta parte podem ser encontrados em `solucao_parte4.md`.



