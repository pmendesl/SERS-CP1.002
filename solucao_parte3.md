# PARTE 3 – Novo dataset Appliances Energy Prediction

Dataset escolhido:
- Appliances energy prediction dataset
https://archive.ics.uci.edu/dataset/374/appliances+energy+prediction

## 26. Carregamento e inspeção inicial

Para esta tarefa, carregaremos o dataset no Pandas e listaremos os tipos de dados e estatísticas descritivas usando `.info()` e `.describe()`.

### Código Python para a Solução:

```python
import pandas as pd

# URL do dataset
url = "https://archive.ics.uci.edu/ml/machine-learning-databases/00374/energydata_complete.csv"

# Carregar o dataset
df_appliances = pd.read_csv(url)

print("Informações iniciais do dataset (df.info()):")
df_appliances.info()

print("\nEstatísticas descritivas do dataset (df.describe()):")
print(df_appliances.describe())

# Salvar as informações em um arquivo de texto para referência
with open("appliances_initial_inspection.txt", "w") as f:
    f.write("Informações iniciais do dataset (df.info()):\n")
    df_appliances.info(buf=f)
    f.write("\nEstatísticas descritivas do dataset (df.describe()):\n")
    f.write(df_appliances.describe().to_string())

```

### Explicação e Análise:

O código acima realiza as seguintes etapas para a inspeção inicial do dataset:

1.  **Carregamento do Dataset**: O dataset é carregado diretamente da URL fornecida usando `pd.read_csv()`. Isso garante que estamos trabalhando com a versão mais recente e correta do dataset.

2.  **`df.info()`**: Este método é usado para obter um resumo conciso do DataFrame. Ele exibe:
    *   O número de entradas (linhas).
    *   O número de colunas.
    *   O nome de cada coluna.
    *   O número de valores não nulos em cada coluna, o que é útil para identificar a presença de valores ausentes.
    *   O tipo de dado de cada coluna (e.g., `int64`, `float64`, `object`).
    *   O uso de memória do DataFrame.

3.  **`df.describe()`**: Este método gera estatísticas descritivas das colunas numéricas do DataFrame. Para cada coluna numérica, ele calcula:
    *   `count`: Número de valores não nulos.
    *   `mean`: Média.
    *   `std`: Desvio padrão.
    *   `min`: Valor mínimo.
    *   `25%`: Primeiro quartil (25º percentil).
    *   `50%`: Mediana (50º percentil).
    *   `75%`: Terceiro quartil (75º percentil).
    *   `max`: Valor máximo.

    Essas estatísticas são fundamentais para entender a distribuição, a centralidade e a dispersão dos dados, além de identificar possíveis *outliers* ou erros na entrada de dados.

4.  **Salvamento das Informações**: As saídas de `df.info()` e `df.describe()` são salvas em um arquivo de texto (`appliances_initial_inspection.txt`) para facilitar a revisão e o registro das informações iniciais do dataset.

**Resultados Esperados e Interpretação:**

Ao executar este código, esperamos ver no console e no arquivo `appliances_initial_inspection.txt` um resumo detalhado do dataset. Isso nos permitirá:

*   **Verificar Tipos de Dados**: Confirmar se as colunas estão com os tipos de dados corretos (e.g., numéricas para cálculos, `datetime` para informações de tempo). Se a coluna de tempo (`date`) não for do tipo `datetime`, será necessário convertê-la.
*   **Identificar Valores Ausentes**: A coluna `Non-Null Count` em `df.info()` revelará se há valores ausentes em alguma coluna. Se o número de valores não nulos for menor que o número total de entradas, há valores ausentes que precisarão ser tratados.
*   **Entender a Distribuição das Variáveis**: As estatísticas descritivas de `df.describe()` fornecerão uma visão rápida da distribuição de cada variável numérica. Por exemplo, podemos ver a faixa de valores (min/max), a média e o desvio padrão para entender a variabilidade. Para a variável `Appliances`, podemos ter uma ideia do consumo típico e da sua dispersão.

Esta etapa é crucial para qualquer análise de dados, pois fornece uma compreensão fundamental da estrutura e da qualidade do dataset antes de prosseguir com análises mais complexas ou modelagem.



## 27. Distribuição do consumo

Nesta tarefa, criaremos histogramas e séries temporais para a variável `Appliances`. A pergunta a ser respondida é: o consumo tende a se concentrar em valores baixos ou altos?

### Código Python para a Solução:

```python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

# URL do dataset
url = "https://archive.ics.uci.edu/ml/machine-learning-databases/00374/energydata_complete.csv"

# Carregar o dataset
df_appliances = pd.read_csv(url)

# Converter a coluna 'date' para datetime e definir como índice
df_appliances["date"] = pd.to_datetime(df_appliances["date"])
df_appliances = df_appliances.set_index("date")

# --- Histograma da variável Appliances ---
plt.figure(figsize=(10, 6))
sns.histplot(df_appliances["Appliances"], bins=50, kde=True)
plt.title("Distribuição do Consumo de Energia (Appliances)")
plt.xlabel("Consumo de Energia (Wh)")
plt.ylabel("Frequência")
plt.grid(True)
plt.tight_layout()
plt.savefig("appliances_histogram.png")

# --- Série Temporal da variável Appliances ---
# Para uma visualização mais clara, podemos reamostrar os dados para uma granularidade maior (e.g., diária ou semanal)
# ou plotar apenas um subconjunto dos dados se o dataset for muito grande.
# Vamos plotar a série temporal original e uma reamostrada para a média diária.

plt.figure(figsize=(15, 7))
plt.plot(df_appliances["Appliances"], alpha=0.7)
plt.title("Série Temporal do Consumo de Energia (Appliances) - Dados Originais")
plt.xlabel("Data")
plt.ylabel("Consumo de Energia (Wh)")
plt.grid(True)
plt.tight_layout()
plt.savefig("appliances_timeseries_original.png")

# Reamostrar para média diária para uma visualização mais suave
df_daily_appliances = df_appliances["Appliances"].resample("D").mean()

plt.figure(figsize=(15, 7))
plt.plot(df_daily_appliances)
plt.title("Série Temporal do Consumo de Energia (Appliances) - Média Diária")
plt.xlabel("Data")
plt.ylabel("Consumo de Energia (Wh)")
plt.grid(True)
plt.tight_layout()
plt.savefig("appliances_timeseries_daily_mean.png")

```

### Explicação e Análise:

Este código gera visualizações para entender a distribuição e o comportamento temporal da variável `Appliances`:

1.  **Carregamento e Preparação dos Dados**: O dataset é carregado, e a coluna `date` é convertida para o tipo `datetime` e definida como índice. Isso é essencial para trabalhar com séries temporais.

2.  **Histograma**: O `sns.histplot()` é usado para criar um histograma da variável `Appliances`. Um histograma mostra a distribuição de frequência dos valores. A opção `kde=True` adiciona uma estimativa de densidade de kernel, que suaviza a distribuição e ajuda a visualizar a forma geral.

3.  **Série Temporal (Original)**: Um gráfico de linha simples é usado para plotar a série temporal original de `Appliances`. Este gráfico mostra o consumo de energia ao longo do tempo na granularidade original do dataset (minutos ou segundos). Pode ser bastante denso se o dataset for grande.

4.  **Série Temporal (Média Diária)**: Para uma visualização mais clara de tendências de longo prazo e sazonalidade, os dados de `Appliances` são reamostrados para a média diária usando `.resample("D").mean()`. Isso agrega os dados em intervalos diários, suavizando as flutuações de curto prazo e tornando padrões diários, semanais ou mensais mais visíveis.

**Resultados Esperados e Interpretação:**

Ao analisar os gráficos gerados, podemos responder à pergunta: **o consumo tende a se concentrar em valores baixos ou altos?**

*   **Histograma (`appliances_histogram.png`)**: A forma do histograma revelará a distribuição do consumo. É comum que o consumo de energia residencial tenha uma distribuição assimétrica, com uma concentração maior de valores baixos (períodos de baixo uso de eletrodomésticos, como durante a noite ou quando a casa está vazia) e uma cauda mais longa para valores mais altos (períodos de pico de uso). Se a maioria das barras estiver concentrada à esquerda (valores baixos), isso indica que o consumo tende a ser baixo na maior parte do tempo.

*   **Série Temporal (`appliances_timeseries_original.png` e `appliances_timeseries_daily_mean.png`)**: Os gráficos de série temporal mostrarão o comportamento do consumo ao longo do tempo. Podemos observar:
    *   **Padrões Sazonais**: Padrões repetitivos diários ou semanais (por exemplo, picos de consumo em certas horas do dia ou dias da semana).
    *   **Tendências**: Se o consumo médio está aumentando ou diminuindo ao longo do tempo.
    *   **Variações**: A magnitude das flutuações no consumo.

    Se a linha da série temporal passar a maior parte do tempo em níveis baixos, com picos ocasionais, isso reforça a ideia de que o consumo se concentra em valores baixos.

**Conclusão sobre a Distribuição do Consumo:**

Com base na análise do histograma e das séries temporais, será possível determinar se o consumo de energia tende a se concentrar em valores baixos ou altos. Na maioria dos casos de consumo residencial, a expectativa é que a distribuição seja **concentrada em valores baixos**, com picos ocasionais de alto consumo. Isso ocorre porque a maioria dos eletrodomésticos não está em uso contínuo, e há períodos significativos de inatividade ou baixo consumo (e.g., durante a madrugada, quando os moradores estão fora de casa). Os picos de consumo geralmente correspondem a horários específicos do dia (manhã, noite) ou a atividades que demandam mais energia (cozinhar, usar aquecedores/ar condicionado).



## 28. Correlações com variáveis ambientais

Nesta tarefa, calcularemos as correlações entre a variável `Appliances` e as variáveis ambientais (temperatura, umidade, etc.). A pergunta a ser respondida é: quais fatores têm mais relação com o consumo?

### Código Python para a Solução:

```python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

# URL do dataset
url = "https://archive.ics.uci.edu/ml/machine-learning-databases/00374/appliances+energy+prediction/energydata_complete.csv"

# Carregar o dataset
df_appliances = pd.read_csv(url)

# Converter a coluna 'date' para datetime e definir como índice
df_appliances["date"] = pd.to_datetime(df_appliances["date"])
df_appliances = df_appliances.set_index("date")

# Identificar as variáveis ambientais
# Excluindo 'Appliances', 'lights' e as colunas de temperatura e umidade de fora (T_out, RH_out, Windspeed, Visibility, Tdewpoint, rv1, rv2)
# A coluna 'rv1' e 'rv2' são variáveis aleatórias, não ambientais.
# Vamos considerar as temperaturas e umidades internas e externas como ambientais.

environmental_vars = [
    'T1', 'RH1', 'T2', 'RH2', 'T3', 'RH3', 'T4', 'RH4', 'T5', 'RH5',
    'T6', 'RH6', 'T7', 'RH7', 'T8', 'RH8', 'T9', 'RH9',
    'T_out', 'Press_mm_hg', 'RH_out', 'Windspeed', 'Visibility', 'Tdewpoint'
]

# Calcular a correlação entre 'Appliances' e as variáveis ambientais
correlations = df_appliances[environmental_vars + ['Appliances']].corr()["Appliances"].sort_values(ascending=False)

print("Correlações de 'Appliances' com variáveis ambientais:")
print(correlations)

# Visualizar as correlações (opcional, mas útil)
plt.figure(figsize=(10, 8))
sns.heatmap(correlations.to_frame(), annot=True, cmap='coolwarm', fmt=".2f", cbar=True)
plt.title("Correlação de 'Appliances' com Variáveis Ambientais")
plt.ylabel("Variável Ambiental")
plt.xlabel("Appliances")
plt.tight_layout()
plt.savefig("appliances_environmental_correlations.png")

# Para uma análise mais detalhada, podemos plotar scatter plots para as variáveis com maior correlação
# Selecionar as 3 variáveis com maior correlação (ignorando 'Appliances' consigo mesma)
top_correlated_vars = correlations[1:4].index.tolist() # Pega as 3 primeiras após 'Appliances'

plt.figure(figsize=(15, 5))
for i, var in enumerate(top_correlated_vars):
    plt.subplot(1, 3, i + 1)
    sns.scatterplot(x=df_appliances[var], y=df_appliances["Appliances"], alpha=0.3)
    plt.title(f"Appliances vs {var} (Corr: {correlations[var]:.2f})")
    plt.xlabel(var)
    plt.ylabel("Appliances")
    plt.grid(True)
plt.tight_layout()
plt.savefig("appliances_top_correlations_scatter.png")

```

### Explicação e Análise:

Este código calcula e visualiza as correlações entre o consumo de energia (`Appliances`) e diversas variáveis ambientais presentes no dataset. As etapas são:

1.  **Carregamento e Preparação dos Dados**: O dataset é carregado e a coluna `date` é convertida para `datetime` e definida como índice, como nas tarefas anteriores.

2.  **Identificação de Variáveis Ambientais**: Uma lista `environmental_vars` é criada contendo os nomes das colunas que representam as condições ambientais (temperaturas e umidades em diferentes cômodos, temperatura externa, pressão, umidade externa, velocidade do vento, visibilidade e ponto de orvalho). As variáveis `rv1` e `rv2` são excluídas, pois são variáveis aleatórias e não ambientais.

3.  **Cálculo da Correlação**: O método `.corr()` é aplicado ao DataFrame contendo `Appliances` e as variáveis ambientais. Em seguida, a coluna `"Appliances"` da matriz de correlação é selecionada para obter as correlações de `Appliances` com todas as outras variáveis. Os resultados são ordenados em ordem decrescente para facilitar a identificação das variáveis mais correlacionadas.

    *   **Coeficiente de Correlação de Pearson**: O método `.corr()` calcula o coeficiente de correlação de Pearson, que varia de -1 a 1. Valores próximos de 1 indicam uma forte correlação linear positiva (quando uma variável aumenta, a outra também tende a aumentar). Valores próximos de -1 indicam uma forte correlação linear negativa (quando uma variável aumenta, a outra tende a diminuir). Valores próximos de 0 indicam pouca ou nenhuma correlação linear.

4.  **Visualização das Correlações**: Um mapa de calor (`sns.heatmap`) é gerado para visualizar as correlações. Isso oferece uma representação gráfica da força e direção das relações. Além disso, gráficos de dispersão (`sns.scatterplot`) são criados para as variáveis com as maiores correlações (positivas ou negativas) com `Appliances`, permitindo uma inspeção visual da relação.

**Resultados Esperados e Interpretação:**

Ao analisar a saída do código e os gráficos, poderemos responder à pergunta: **quais fatores têm mais relação com o consumo?**

*   **Tabela de Correlações**: A impressão de `correlations` mostrará uma lista das variáveis ambientais e seus respectivos coeficientes de correlação com `Appliances`. As variáveis com os maiores valores absolutos de correlação (mais próximos de 1 ou -1) são as que têm a relação linear mais forte com o consumo de energia.

*   **Mapa de Calor**: O mapa de calor (`appliances_environmental_correlations.png`) fornecerá uma visão geral das correlações. Cores mais quentes (vermelho) indicarão correlações positivas fortes, enquanto cores mais frias (azul) indicarão correlações negativas fortes.

*   **Gráficos de Dispersão**: Os gráficos de dispersão (`appliances_top_correlations_scatter.png`) para as variáveis mais correlacionadas permitirão visualizar a natureza da relação. Por exemplo, se a temperatura interna (`T2`) tiver uma correlação positiva forte, o gráfico de dispersão pode mostrar uma tendência de aumento no consumo de `Appliances` à medida que `T2` aumenta.

**Conclusão sobre os Fatores mais Relacionados:**

É comum que variáveis como **temperatura interna (especialmente em cômodos como sala de estar ou cozinha)** e **umidade interna** apresentem alguma correlação com o consumo de energia, pois o uso de aquecedores, ar condicionado e desumidificadores é diretamente influenciado por essas condições. Variáveis externas como **temperatura externa (`T_out`)** também podem ter uma influência significativa, pois afetam a necessidade de aquecimento ou resfriamento. A **pressão atmosférica**, **velocidade do vento** e **ponto de orvalho** podem ter correlações mais fracas, mas ainda assim podem ser relevantes dependendo do contexto.

Os resultados exatos variarão, mas a análise das correlações nos permitirá identificar os principais impulsionadores ambientais do consumo de energia no dataset. Isso é valioso para entender o comportamento do consumo e para a construção de modelos preditivos, pois as variáveis mais correlacionadas são geralmente as mais importantes para o modelo.



## 29. Normalização dos dados

Nesta tarefa, aplicaremos Min-Max Scaling às variáveis numéricas do dataset. Reutilizaremos esses dados em modelos posteriores.

### Código Python para a Solução:

```python
import pandas as pd
from sklearn.preprocessing import MinMaxScaler

# URL do dataset
url = "https://archive.ics.uci.edu/ml/machine-learning-databases/00374/appliances+energy+prediction/energydata_complete.csv"

# Carregar o dataset
df_appliances = pd.read_csv(url)

# Converter a coluna 'date' para datetime e definir como índice
df_appliances["date"] = pd.to_datetime(df_appliances["date"])
df_appliances = df_appliances.set_index("date")

# Identificar as colunas numéricas para normalização
# Excluir colunas que não são numéricas ou que não devem ser normalizadas (e.g., 'lights' se for categórica, ou 'rv1', 'rv2' se forem aleatórias)
# Para este dataset, todas as colunas exceto 'date' (que virou índice) e 'lights' (que é uma contagem de lâmpadas) são numéricas e relevantes para normalização.
# 'lights' também é numérica, mas pode ter uma distribuição diferente e ser tratada separadamente ou incluída se fizer sentido para o modelo.
# Vamos normalizar todas as colunas numéricas, exceto 'lights' e as variáveis aleatórias 'rv1' e 'rv2' por enquanto, focando nas ambientais e 'Appliances'.

numeric_cols = df_appliances.select_dtypes(include=np.number).columns.tolist()
# Remover 'lights', 'rv1', 'rv2' se existirem e não forem para normalização neste contexto
if 'lights' in numeric_cols: numeric_cols.remove('lights')
if 'rv1' in numeric_cols: numeric_cols.remove('rv1')
if 'rv2' in numeric_cols: numeric_cols.remove('rv2')

# Criar uma cópia do DataFrame para armazenar os dados normalizados
df_normalized = df_appliances.copy()

# Aplicar Min-Max Scaling
scaler = MinMaxScaler()
df_normalized[numeric_cols] = scaler.fit_transform(df_appliances[numeric_cols])

print("Primeiras 5 linhas do DataFrame original (variáveis numéricas selecionadas):\n")
print(df_appliances[numeric_cols].head())

print("\nPrimeiras 5 linhas do DataFrame normalizado (variáveis numéricas selecionadas):\n")
print(df_normalized[numeric_cols].head())

print("\nEstatísticas descritivas do DataFrame normalizado (variáveis numéricas selecionadas):\n")
print(df_normalized[numeric_cols].describe())

# Salvar o DataFrame normalizado para uso posterior
df_normalized.to_csv("energydata_complete_normalized.csv")

```

### Explicação e Análise:

Este código realiza a normalização dos dados usando a técnica Min-Max Scaling, uma etapa crucial no pré-processamento de dados para muitos modelos de aprendizado de máquina. As etapas são:

1.  **Carregamento e Preparação dos Dados**: O dataset é carregado e a coluna `date` é convertida para `datetime` e definida como índice, como nas tarefas anteriores.

2.  **Identificação de Colunas Numéricas**: As colunas numéricas do DataFrame são identificadas usando `select_dtypes(include=np.number)`. Isso garante que apenas as colunas apropriadas sejam normalizadas. Colunas como `lights`, `rv1` e `rv2` são removidas da lista de normalização, pois `lights` pode ser uma contagem e `rv1`/`rv2` são variáveis aleatórias que podem não se beneficiar da normalização padrão ou podem ser tratadas de forma diferente.

3.  **Criação de Cópia do DataFrame**: Uma cópia do DataFrame original (`df_appliances.copy()`) é criada para armazenar os dados normalizados. Isso é uma boa prática para evitar modificar o DataFrame original e permitir comparações.

4.  **Aplicação do Min-Max Scaling**: A classe `MinMaxScaler` do `sklearn.preprocessing` é utilizada. Esta técnica transforma os dados para que fiquem em uma escala específica, geralmente entre 0 e 1. A fórmula para Min-Max Scaling é:

    `X_normalized = (X - X_min) / (X_max - X_min)`

    O método `fit_transform()` calcula os valores mínimo e máximo de cada coluna e, em seguida, aplica a transformação. Isso garante que todas as variáveis numéricas contribuam igualmente para o modelo, independentemente de suas escalas originais.

5.  **Verificação e Salvamento**: As primeiras linhas dos DataFrames original e normalizado são impressas para visualização da transformação. As estatísticas descritivas do DataFrame normalizado são exibidas, e é esperado que os valores mínimos sejam 0 e os máximos sejam 1 para as colunas normalizadas. Finalmente, o DataFrame normalizado é salvo em um novo arquivo CSV (`energydata_complete_normalized.csv`) para ser reutilizado em modelos posteriores, conforme solicitado.

**Resultados Esperados e Interpretação:**

Ao executar este código, esperamos ver que:

*   **Valores Transformados**: As colunas numéricas selecionadas no `df_normalized` terão seus valores transformados para a faixa de 0 a 1. Isso será evidente ao comparar as primeiras linhas dos DataFrames original e normalizado.

*   **Estatísticas Descritivas**: As estatísticas descritivas do DataFrame normalizado confirmarão que o valor mínimo de cada coluna normalizada é 0 e o valor máximo é 1. A média e o desvio padrão estarão dentro dessa faixa.

**Importância da Normalização:**

A normalização é uma etapa fundamental no pré-processamento de dados, especialmente para algoritmos de aprendizado de máquina que são sensíveis à escala das variáveis, como K-Means, PCA, redes neurais e algoritmos baseados em distância (e.g., SVMs com kernel RBF). Sem a normalização, variáveis com escalas maiores podem dominar a função de custo ou a métrica de distância, levando a resultados subótimos ou viesados. Ao normalizar os dados, garantimos que todas as características contribuam proporcionalmente para o treinamento do modelo, melhorando sua performance e a interpretabilidade dos resultados.



## 30. PCA

Nesta tarefa, aplicaremos PCA e reduziremos a dimensionalidade para 2 componentes principais. Em seguida, plotaremos os dados resultantes. A pergunta a ser respondida é: aparecem padrões ou agrupamentos naturais?

### Código Python para a Solução:

```python
import pandas as pd
from sklearn.decomposition import PCA
import matplotlib.pyplot as plt
import seaborn as sns

# Carregar o dataset normalizado (gerado na tarefa anterior)
df_normalized = pd.read_csv("energydata_complete_normalized.csv", index_col=0)

# Identificar as colunas numéricas para o PCA
# Excluir colunas que não são para PCA, como 'date' (que virou índice) e 'lights' (se não for relevante para o PCA)
# Vamos incluir todas as variáveis numéricas normalizadas, exceto 'Appliances' e 'lights' para ver padrões nas variáveis ambientais
# Ou podemos incluir 'Appliances' para ver como ela se relaciona com as outras no espaço PCA
# Para este exercício, vamos incluir todas as variáveis numéricas normalizadas, exceto 'lights', 'rv1', 'rv2'

pca_cols = df_normalized.select_dtypes(include=np.number).columns.tolist()
if 'lights' in pca_cols: pca_cols.remove('lights')
if 'rv1' in pca_cols: pca_cols.remove('rv1')
if 'rv2' in pca_cols: pca_cols.remove('rv2')

X_pca = df_normalized[pca_cols]

# Aplicar PCA para reduzir para 2 componentes principais
pca = PCA(n_components=2)
pca_components = pca.fit_transform(X_pca)

# Criar um DataFrame com os componentes principais
df_pca_components = pd.DataFrame(data=pca_components, columns=['PC1', 'PC2'])

print('Variância explicada por cada componente principal:')
print(pca.explained_variance_ratio_)

print(f'Variância total explicada pelos 2 componentes principais: {pca.explained_variance_ratio_.sum():.4f}')

# Visualização dos componentes principais
plt.figure(figsize=(10, 7))
sns.scatterplot(x='PC1', y='PC2', data=df_pca_components, alpha=0.3)
plt.title('Componentes Principais (PCA) do Dataset de Energia')
plt.xlabel(f'Componente Principal 1 ({pca.explained_variance_ratio_[0]*100:.2f}% da variância)')
plt.ylabel(f'Componente Principal 2 ({pca.explained_variance_ratio_[1]*100:.2f}% da variância)')
plt.grid(True)
plt.tight_layout()
plt.savefig('appliances_pca_components.png')

# Opcional: Visualizar a contribuição das variáveis originais para cada componente
loadings = pd.DataFrame(pca.components_.T, columns=['PC1', 'PC2'], index=X_pca.columns)
print('\nCargas (Loadings) das variáveis originais nos componentes principais:')
print(loadings)

```

### Explicação e Análise:

Este código aplica a Análise de Componentes Principais (PCA) ao dataset de energia já normalizado. As etapas são:

1.  **Carregamento do Dataset Normalizado**: O DataFrame `df_normalized` é carregado a partir do arquivo CSV salvo na tarefa anterior (`energydata_complete_normalized.csv`). É importante especificar `index_col=0` para que a coluna de índice original seja corretamente restaurada.

2.  **Seleção de Colunas para PCA**: Todas as colunas numéricas normalizadas são selecionadas para o PCA, exceto `lights`, `rv1` e `rv2`, que podem não ser ideais para a análise de componentes principais neste contexto ou podem ser tratadas separadamente. A escolha das variáveis para o PCA é crucial, pois ela define o espaço de características que será transformado.

3.  **Aplicação do PCA**: Uma instância de `PCA` é criada com `n_components=2`, indicando que queremos reduzir a dimensionalidade para dois componentes principais. O método `fit_transform()` é então aplicado aos dados selecionados (`X_pca`), que calcula os componentes principais e projeta os dados nesse novo espaço bidimensional.

4.  **Criação do DataFrame de Componentes Principais**: Os resultados do PCA (os dois componentes principais para cada observação) são armazenados em um novo DataFrame (`df_pca_components`) com colunas nomeadas `PC1` e `PC2`.

5.  **Análise da Variância Explicada**: A proporção da variância total explicada por cada componente principal (`pca.explained_variance_ratio_`) é impressa. A soma dessas proporções indica a quantidade total de informação (variabilidade) dos dados originais que é retida pelos dois componentes principais. Um valor alto (geralmente acima de 70-80%) sugere que os dois componentes capturam uma parte significativa da estrutura dos dados.

6.  **Visualização dos Componentes Principais**: Um gráfico de dispersão (`sns.scatterplot`) é gerado, onde o eixo X representa o `PC1` e o eixo Y representa o `PC2`. Cada ponto no gráfico corresponde a uma observação no dataset, projetada no novo espaço bidimensional. Os rótulos dos eixos são aprimorados para incluir a porcentagem da variância explicada por cada componente, tornando o gráfico mais informativo.

7.  **Análise das Cargas (Loadings) - Opcional**: As cargas (`pca.components_`) são exibidas para ajudar a interpretar o significado de cada componente principal. Elas mostram a contribuição de cada variável original para a formação de `PC1` e `PC2`.

**Resultados Esperados e Interpretação:**

Ao analisar o gráfico `appliances_pca_components.png`, a principal pergunta a ser respondida é: **aparecem padrões ou agrupamentos naturais?**

*   **Padrões ou Agrupamentos**: No gráfico de dispersão, podemos observar se os pontos de dados formam aglomerados distintos, indicando a presença de grupos naturais nos dados. Por exemplo, pode haver um aglomerado de pontos que representam períodos de baixo consumo e outro aglomerado para períodos de alto consumo, ou grupos relacionados a diferentes condições ambientais.

*   **Distribuição dos Dados**: O gráfico também revelará a forma geral da distribuição dos dados no espaço PCA. Os pontos podem estar espalhados uniformemente, ou podem formar uma nuvem densa com algumas ramificações, indicando a complexidade da relação entre as variáveis.

*   **Variância Explicada**: Os valores da variância explicada nos dirão o quão bem os dois componentes principais representam a variabilidade total dos dados. Se a variância explicada for baixa, pode ser que dois componentes não sejam suficientes para capturar a estrutura dos dados, e mais componentes seriam necessários para uma representação adequada.

**Conclusão sobre Padrões ou Agrupamentos Naturais:**

A presença de padrões ou agrupamentos naturais no espaço PCA sugere que há uma estrutura subjacente nos dados que pode ser explorada por algoritmos de agrupamento (como K-Means, que será abordado na próxima tarefa). Se os pontos estiverem bem separados em grupos, isso indica que o PCA foi eficaz em revelar essas distinções. Se os pontos estiverem muito misturados, pode ser que os dados não possuam agrupamentos naturais claros, ou que a redução para apenas dois componentes principais tenha perdido informações importantes para a separação dos grupos. A interpretação das cargas também ajudará a entender o que esses padrões ou agrupamentos representam em termos das variáveis originais (por exemplo, um cluster pode ser caracterizado por alta temperatura e baixa umidade).



## 31. Regressão Linear Múltipla

Nesta tarefa, modelaremos `Appliances` em função das variáveis ambientais. Avaliaremos o R² e o erro médio do modelo.

### Código Python para a Solução:

```python
import pandas as pd
from sklearn.model_selection import train_test_split
from sklearn.linear_model import LinearRegression
from sklearn.metrics import mean_squared_error, r2_score
import numpy as np

# Carregar o dataset normalizado (gerado na tarefa 29)
df_normalized = pd.read_csv("energydata_complete_normalized.csv", index_col=0)

# Definir a variável alvo (y) e as variáveis preditoras (X)
# Excluir colunas que não são preditoras ou que não devem ser usadas no modelo
# Vamos usar todas as variáveis ambientais normalizadas como preditoras.

y = df_normalized["Appliances"]

# Remover a coluna alvo e outras colunas não preditoras do X
X = df_normalized.drop(columns=["Appliances", "lights", "rv1", "rv2"])

# Dividir os dados em conjuntos de treino e teste
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

# Criar e treinar o modelo de Regressão Linear Múltipla
model = LinearRegression()
model.fit(X_train, y_train)

# Fazer previsões no conjunto de teste
y_pred = model.predict(X_test)

# Avaliar o modelo
rmse = np.sqrt(mean_squared_error(y_test, y_pred))
r2 = r2_score(y_test, y_pred)

print(f"RMSE (Erro Médio Quadrático): {rmse:.4f}")
print(f"R² (Coeficiente de Determinação): {r2:.4f}")

# Opcional: Visualizar os coeficientes do modelo
coefficients = pd.DataFrame(model.coef_, X.columns, columns=["Coeficiente"])
print("\nCoeficientes do Modelo:\n")
print(coefficients)

```

### Explicação e Análise:

Este código implementa um modelo de Regressão Linear Múltipla para prever o consumo de energia (`Appliances`) com base nas variáveis ambientais. As etapas são:

1.  **Carregamento do Dataset Normalizado**: O dataset `df_normalized` é carregado, garantindo que os dados já estejam pré-processados e normalizados, o que é uma boa prática para modelos de regressão.

2.  **Definição de Variável Alvo e Preditoras**: A coluna `Appliances` é definida como a variável alvo (`y`). Todas as outras variáveis ambientais normalizadas são usadas como variáveis preditoras (`X`), excluindo `Appliances` (que é a alvo), `lights` (que pode ser tratada separadamente ou não é uma variável ambiental direta), e `rv1`, `rv2` (variáveis aleatórias).

3.  **Divisão em Conjuntos de Treino e Teste**: Os dados são divididos em conjuntos de treino (80%) e teste (20%) usando `train_test_split`. Isso é crucial para avaliar o desempenho do modelo em dados não vistos, evitando o *overfitting*.

4.  **Treinamento do Modelo**: Uma instância de `LinearRegression` é criada e treinada (`fit()`) com os dados de treino (`X_train`, `y_train`). O modelo aprende os coeficientes que melhor descrevem a relação linear entre as variáveis preditoras e a variável alvo.

5.  **Previsões e Avaliação**: As previsões (`predict()`) são feitas no conjunto de teste (`X_test`). O desempenho do modelo é avaliado usando duas métricas:
    *   **RMSE (Root Mean Squared Error)**: Mede a magnitude média dos erros. Um RMSE menor indica que as previsões do modelo estão mais próximas dos valores reais.
    *   **R² (Coeficiente de Determinação)**: Indica a proporção da variância na variável dependente que é previsível a partir das variáveis independentes. Varia de 0 a 1, onde 1 indica que o modelo explica toda a variância da variável alvo, e 0 indica que o modelo não explica nenhuma variância. Um R² mais alto geralmente indica um modelo com melhor ajuste.

6.  **Coeficientes do Modelo (Opcional)**: Os coeficientes de cada variável preditora são impressos. Esses coeficientes indicam a mudança esperada na variável alvo para cada unidade de mudança na variável preditora, mantendo as outras variáveis constantes. Eles podem fornecer insights sobre a importância e a direção da influência de cada variável ambiental no consumo de energia.

**Resultados Esperados e Interpretação:**

Ao executar este código, esperamos ver os valores de RMSE e R².

*   **RMSE**: Um valor de RMSE baixo é desejável, indicando que o modelo tem um bom desempenho na previsão do consumo de energia. O valor exato dependerá da escala da variável `Appliances` e da complexidade da relação.

*   **R²**: O valor de R² nos dirá o quão bem as variáveis ambientais explicam a variação no consumo de `Appliances`. Um R² mais próximo de 1 indica que o modelo é capaz de explicar uma grande parte da variabilidade do consumo, sugerindo que as variáveis ambientais são bons preditores. Um R² baixo pode indicar que outras variáveis não incluídas no modelo (e.g., hábitos dos moradores, tipo de eletrodomésticos, isolamento da casa) ou relações não lineares são mais importantes.

**Conclusão sobre a Regressão Linear Múltipla:**

A Regressão Linear Múltipla é um modelo simples e interpretável que pode fornecer uma boa linha de base para a previsão do consumo de energia. Os coeficientes do modelo podem revelar quais variáveis ambientais têm o maior impacto no consumo. No entanto, se a relação entre as variáveis for complexa e não linear, modelos mais avançados (como Random Forest, que será abordado na próxima tarefa) podem apresentar um desempenho superior, capturando padrões mais intrincados nos dados. A avaliação do RMSE e R² é fundamental para determinar a adequação do modelo linear para este problema.



## 32. Random Forest Regressor

Nesta tarefa, treinaremos um modelo de Random Forest para prever `Appliances` e compararemos o RMSE com a regressão linear.

### Código Python para a Solução:

```python
import pandas as pd
from sklearn.model_selection import train_test_split
from sklearn.ensemble import RandomForestRegressor
from sklearn.metrics import mean_squared_error
import numpy as np

# Carregar o dataset normalizado (gerado na tarefa 29)
df_normalized = pd.read_csv("energydata_complete_normalized.csv", index_col=0)

# Definir a variável alvo (y) e as variáveis preditoras (X)
y = df_normalized["Appliances"]
X = df_normalized.drop(columns=["Appliances", "lights", "rv1", "rv2"])

# Dividir os dados em conjuntos de treino e teste
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

# Criar e treinar o modelo de Random Forest Regressor
# Parâmetros como n_estimators, max_depth, etc., podem ser ajustados para otimização
rf_model = RandomForestRegressor(n_estimators=100, random_state=42, n_jobs=-1) # n_jobs=-1 usa todos os núcleos disponíveis
rf_model.fit(X_train, y_train)

# Fazer previsões no conjunto de teste
y_pred_rf = rf_model.predict(X_test)

# Avaliar o modelo
rmse_rf = np.sqrt(mean_squared_error(y_test, y_pred_rf))

print(f"RMSE (Random Forest Regressor): {rmse_rf:.4f}")

# Comparar com o RMSE da Regressão Linear (assumindo que foi calculado na tarefa anterior)
# Para fins de comparação direta, vamos recalcular o RMSE da Regressão Linear aqui também
from sklearn.linear_model import LinearRegression
linear_model = LinearRegression()
linear_model.fit(X_train, y_train)
y_pred_linear = linear_model.predict(X_test)
rmse_linear = np.sqrt(mean_squared_error(y_test, y_pred_linear))

print(f"RMSE (Regressão Linear Simples): {rmse_linear:.4f}")

# Opcional: Visualizar a importância das características
feature_importances = pd.DataFrame(rf_model.feature_importances_, index=X.columns, columns=["Importância"])
feature_importances = feature_importances.sort_values(by="Importância", ascending=False)
print("\nImportância das Características (Random Forest):\n")
print(feature_importances.head(10))

```

### Explicação e Análise:

Este código implementa um modelo de Random Forest Regressor para prever o consumo de energia (`Appliances`) e compara seu desempenho com o modelo de Regressão Linear Múltipla. As etapas são:

1.  **Carregamento e Preparação dos Dados**: O dataset normalizado é carregado, e as variáveis alvo (`y`) e preditoras (`X`) são definidas da mesma forma que na tarefa de Regressão Linear Múltipla. Os dados são divididos em conjuntos de treino e teste para avaliação.

2.  **Treinamento do Modelo Random Forest**: Uma instância de `RandomForestRegressor` é criada e treinada. O `n_estimators=100` indica que o modelo construirá 100 árvores de decisão. `random_state=42` garante a reprodutibilidade, e `n_jobs=-1` permite que o modelo utilize todos os núcleos de CPU disponíveis para acelerar o treinamento.

3.  **Previsões e Avaliação**: As previsões são feitas no conjunto de teste, e o RMSE é calculado para o modelo Random Forest. Para uma comparação direta, o RMSE da Regressão Linear é recalculado usando os mesmos conjuntos de treino e teste.

4.  **Importância das Características (Opcional)**: O atributo `feature_importances_` do modelo Random Forest fornece uma medida da importância de cada variável preditora na tomada de decisões do modelo. Variáveis com maior importância contribuem mais para a redução do erro nas previsões.

**Resultados Esperados e Interpretação:**

Ao executar este código, esperamos ver os valores de RMSE para ambos os modelos:

*   **Comparação de RMSE**: Em geral, espera-se que o **Random Forest Regressor apresente um RMSE menor** do que a Regressão Linear Simples para este tipo de problema. Isso ocorre porque Random Forest é um modelo de *ensemble* não linear que pode capturar relações mais complexas e interações entre as variáveis que um modelo linear não conseguiria. Se o RMSE do Random Forest for significativamente menor, isso indica que ele é um modelo mais adequado para prever o consumo de energia neste dataset.

*   **Importância das Características**: A lista de importância das características revelará quais variáveis ambientais são mais influentes na previsão do consumo de `Appliances` de acordo com o modelo Random Forest. Isso pode confirmar ou refinar os insights obtidos na análise de correlação, pois a importância da característica considera as interações e a não linearidade.

**Conclusão sobre a Comparação de Modelos:**

O Random Forest Regressor é um algoritmo poderoso e versátil para problemas de regressão. Sua capacidade de lidar com relações não lineares e interações entre características, sem a necessidade de pré-processamento complexo como a criação de características polinomiais, geralmente o torna superior a modelos lineares em datasets do mundo real. A comparação do RMSE é uma métrica quantitativa direta para avaliar qual modelo tem um desempenho preditivo superior. A importância das características também é um benefício adicional, fornecendo insights sobre os fatores mais relevantes que impulsionam o consumo de energia.



## 33. K-Means clustering

Nesta tarefa, aplicaremos K-Means com 3 a 5 clusters e interpretaremos os perfis de consumo.

### Código Python para a Solução:

```python
import pandas as pd
from sklearn.cluster import KMeans
import matplotlib.pyplot as plt
import seaborn as sns

# Carregar o dataset normalizado (gerado na tarefa 29)
df_normalized = pd.read_csv("energydata_complete_normalized.csv", index_col=0)

# Selecionar as variáveis para o clustering
# Vamos usar todas as variáveis numéricas normalizadas, exceto as que não são características para clustering
# Excluímos 'Appliances' pois é a variável alvo em regressão, mas aqui estamos buscando perfis de consumo
# baseados em todas as outras características que descrevem o ambiente e o uso.

clustering_cols = df_normalized.select_dtypes(include=np.number).columns.tolist()
if 'Appliances' in clustering_cols: clustering_cols.remove('Appliances')
if 'lights' in clustering_cols: clustering_cols.remove('lights')
if 'rv1' in clustering_cols: clustering_cols.remove('rv1')
if 'rv2' in clustering_cols: clustering_cols.remove('rv2')

X_cluster = df_normalized[clustering_cols]

# Aplicar K-Means para diferentes números de clusters (3 a 5)
# Armazenar os resultados para análise

k_values = [3, 4, 5]
kmeans_models = {}
cluster_labels = {}

for k in k_values:
    print(f"\nExecutando K-Means com {k} clusters...")
    kmeans = KMeans(n_clusters=k, random_state=42, n_init=10) # n_init para evitar warning
    labels = kmeans.fit_predict(X_cluster)
    kmeans_models[k] = kmeans
    cluster_labels[k] = labels
    print(f"Inércia para {k} clusters: {kmeans.inertia_:.2f}")

    # Adicionar os rótulos de cluster ao DataFrame normalizado para análise de perfil
    df_normalized[f'Cluster_{k}'] = labels

    # Interpretar os perfis de consumo para cada k
    print(f"\nPerfis de consumo para {k} clusters:")
    cluster_profiles = df_normalized.groupby(f'Cluster_{k}')[clustering_cols + ['Appliances']].mean()
    print(cluster_profiles)

    # Visualização dos perfis (opcional, mas útil para entender as diferenças)
    plt.figure(figsize=(12, 6))
    cluster_profiles.T.plot(kind='bar', figsize=(15, 8))
    plt.title(f'Perfis de Consumo para {k} Clusters')
    plt.xlabel('Variável')
    plt.ylabel('Valor Médio (Normalizado)')
    plt.xticks(rotation=90)
    plt.legend(title='Cluster')
    plt.tight_layout()
    plt.savefig(f'cluster_profiles_{k}.png')

# Opcional: Método do Cotovelo para encontrar o k ideal (se não fosse especificado 3 a 5)
# wcss = [] # Within-cluster sum of squares
# for i in range(1, 11):
#     kmeans = KMeans(n_clusters=i, random_state=42, n_init=10)
#     kmeans.fit(X_cluster)
#     wcss.append(kmeans.inertia_)
# plt.figure(figsize=(10, 6))
# plt.plot(range(1, 11), wcss, marker='o')
# plt.title('Método do Cotovelo')
# plt.xlabel('Número de Clusters (K)')
# plt.ylabel('WCSS')
# plt.grid(True)
# plt.savefig('elbow_method.png')

```

### Explicação e Análise:

Este código aplica o algoritmo K-Means para agrupar os dados do dataset de energia em diferentes perfis de consumo, explorando 3, 4 e 5 clusters. As etapas são:

1.  **Carregamento e Seleção de Variáveis**: O dataset normalizado é carregado. As variáveis para o clustering (`X_cluster`) são selecionadas, excluindo `Appliances` (que é a variável alvo em outros contextos, mas aqui queremos agrupar com base nas características ambientais e de uso), `lights`, `rv1` e `rv2` (que podem não ser ideais para definir perfis de consumo ou são aleatórias).

2.  **Aplicação do K-Means para Diferentes `k`**: O K-Means é executado para `k` (número de clusters) igual a 3, 4 e 5. Para cada `k`:
    *   Uma instância de `KMeans` é criada com o número de clusters atual. `random_state=42` garante a reprodutibilidade, e `n_init=10` executa o algoritmo 10 vezes com diferentes centroides iniciais e escolhe a melhor inicialização para evitar mínimos locais.
    *   `fit_predict()` é usado para treinar o modelo e atribuir um rótulo de cluster a cada ponto de dados.
    *   A **inércia** (`kmeans.inertia_`) é impressa. A inércia é a soma das distâncias quadradas das amostras ao seu centro de cluster mais próximo. Um valor menor de inércia geralmente indica clusters mais densos e bem separados. Embora não seja o único critério, a inércia pode ajudar a comparar a qualidade dos agrupamentos para diferentes `k`.
    *   Os rótulos de cluster são adicionados ao DataFrame normalizado para facilitar a análise dos perfis.

3.  **Interpretação dos Perfis de Consumo**: Para cada `k`, os perfis de consumo são interpretados calculando a média de todas as variáveis (incluindo `Appliances`) para cada cluster. Isso é feito usando `df_normalized.groupby(f'Cluster_{k}').mean()`. Ao analisar essas médias, podemos entender as características de cada cluster. Por exemplo, um cluster pode ter médias altas para temperaturas internas e consumo de `Appliances`.
