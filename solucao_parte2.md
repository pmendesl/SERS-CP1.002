# PARTE 2 – Exercícios adicionais no dataset inicial

```python
import pandas as pd

# Carregar o dataset (assumindo que o dataset está em um arquivo CSV chamado 'household_power_consumption.txt')
# É importante notar que o separador decimal é ',' e o separador de colunas é ';'
df = pd.read_csv('household_power_consumption.txt', sep=';', decimal=',')

# Combinar 'Date' e 'Time' e converter para datetime
df['DateTime'] = pd.to_datetime(df['Date'] + ' ' + df['Time'], format='%d/%m/%Y %H:%M:%S')
df = df.set_index('DateTime')

# Reamostrar os dados em intervalos de 1 hora, calculando a média de Global_active_power
df_hourly = df['Global_active_power'].resample('H').mean()

# Identificar os horários de maior consumo médio ao longo do dia
# Para isso, podemos agrupar por hora do dia e calcular a média
consumption_by_hour = df_hourly.groupby(df_hourly.index.hour).mean()

print('Consumo médio por hora do dia:')
print(consumption_by_hour)

# O horário de maior consumo médio é:
hour_of_max_consumption = consumption_by_hour.idxmax()
max_consumption_value = consumption_by_hour.max()

print(f'\nO horário de maior consumo médio é {hour_of_max_consumption}:00 com um valor de {max_consumption_value:.2f}.')

# Visualização (opcional, mas recomendado para análise)
import matplotlib.pyplot as plt

plt.figure(figsize=(12, 6))
consumption_by_hour.plot(kind='bar')
plt.title('Consumo Médio de Global_active_power por Hora do Dia')
plt.xlabel('Hora do Dia')
plt.ylabel('Consumo Médio (Global_active_power)')
plt.xticks(rotation=45)
plt.grid(True)
plt.tight_layout()
plt.savefig('consumo_por_hora.png')

```

### Explicação e Análise:

O código acima realiza as seguintes etapas:

1.  **Carregamento do Dataset**: O dataset `household_power_consumption.txt` é carregado usando `pandas.read_csv`.

2.  **Conversão para Datetime e Definição de Índice**: As colunas 'Date' e 'Time' são combinadas e convertidas para o tipo `datetime` usando `pd.to_datetime`. O formato `%d/%m/%Y %H:%M:%S` é especificado para garantir a correta interpretação das datas e horas. Em seguida, esta nova coluna 'DateTime' é definida como o índice do DataFrame, o que é fundamental para operações de séries temporais.

3.  **Reamostragem Horária**: A função `.resample('H').mean()` é utilizada para reamostrar os dados em intervalos de uma hora, calculando a média de `Global_active_power` para cada hora. Isso agrega os dados de minutos em dados horários, suavizando as flutuações e permitindo uma análise em uma granularidade maior.

4.  **Identificação dos Horários de Maior Consumo**: Para identificar os horários de pico de consumo, agrupamos os dados reamostrados pela hora do dia (`df_hourly.index.hour`) e calculamos a média novamente. Isso nos dá o consumo médio para cada uma das 24 horas do dia, considerando todo o período do dataset. A função `.idxmax()` é então usada para encontrar a hora com o maior consumo médio.

5.  **Visualização**: Um gráfico de barras é gerado usando `matplotlib.pyplot` para visualizar o consumo médio por hora do dia. Este gráfico ajuda a identificar visualmente os períodos de maior e menor consumo, confirmando o resultado obtido por `idxmax()`.

**Resultados Esperados e Interpretação:**

Ao executar este código, esperamos ver uma tabela com o consumo médio para cada hora do dia. Geralmente, em datasets de consumo de energia residencial, os picos de consumo ocorrem durante as horas da noite, quando as pessoas estão em casa e utilizando mais eletrodomésticos, e nas primeiras horas da manhã. O gráfico de barras confirmará esses padrões, mostrando barras mais altas para os horários de pico. A identificação do horário de maior consumo médio é crucial para entender os hábitos de uso de energia e pode ser útil para estratégias de gerenciamento de carga ou para identificar períodos de maior demanda na rede elétrica.

Este exercício demonstra a importância da manipulação de séries temporais para extrair insights valiosos de dados que possuem uma dimensão temporal. A reamostragem e a análise por períodos específicos (como horas do dia) são técnicas fundamentais em análise de dados de energia e outras áreas que lidam com dados sequenciais.



## 22. Autocorrelação do consumo

Para esta tarefa, utilizamos a série temporal de `Global_active_power` e calculamos a autocorrelação em lags de 1h, 24h e 48h. A pergunta a ser respondida é: existem padrões repetidos diariamente?

### Código Python para a Solução:

```python
import pandas as pd
import matplotlib.pyplot as plt
from statsmodels.graphics.tsaplots import plot_acf

# Carregar o dataset (assumindo que o dataset está em um arquivo CSV chamado 'household_power_consumption.txt')
# É importante notar que o separador decimal é ',' e o separador de colunas é ';'
df = pd.read_csv('household_power_consumption.txt', sep=';', decimal=',')

# Combinar 'Date' e 'Time' e converter para datetime
df['DateTime'] = pd.to_datetime(df['Date'] + ' ' + df['Time'], format='%d/%m/%Y %H:%M:%S')
df = df.set_index('DateTime')

# Reamostrar os dados em intervalos de 1 hora, calculando a média de Global_active_power
df_hourly = df['Global_active_power'].resample('H').mean()

# Remover valores NaN, se houver, para o cálculo da autocorrelação
df_hourly = df_hourly.dropna()

# Calcular a autocorrelação para lags específicos
# Lag de 1 hora
autocorr_1h = df_hourly.autocorr(lag=1)
print(f'Autocorrelação para lag de 1 hora: {autocorr_1h:.4f}')

# Lag de 24 horas (1 dia)
autocorr_24h = df_hourly.autocorr(lag=24)
print(f'Autocorrelação para lag de 24 horas: {autocorr_24h:.4f}')

# Lag de 48 horas (2 dias)
autocorr_48h = df_hourly.autocorr(lag=48)
print(f'Autocorrelação para lag de 48 horas: {autocorr_48h:.4f}')

# Visualizar a função de autocorrelação (ACF) para um número maior de lags
plt.figure(figsize=(12, 6))
plot_acf(df_hourly, lags=72, title='Função de Autocorrelação (ACF) de Global_active_power (Horária)')
plt.xlabel('Lag (horas)')
plt.ylabel('Autocorrelação')
plt.grid(True)
plt.tight_layout()
plt.savefig('acf_global_active_power.png')

```

### Explicação e Análise:

O código acima realiza as seguintes etapas para analisar a autocorrelação do consumo de energia:

1.  **Carregamento e Preparação dos Dados**: Assim como na tarefa anterior, o dataset é carregado e as colunas 'Date' e 'Time' são combinadas e convertidas para um índice datetime. Os dados de `Global_active_power` são reamostrados para intervalos horários e quaisquer valores `NaN` resultantes da reamostragem são removidos para garantir a integridade dos cálculos de autocorrelação.

2.  **Cálculo da Autocorrelação**: A função `.autocorr(lag=n)` do Pandas é utilizada para calcular a autocorrelação da série temporal para lags específicos. Um lag de 1 hora mostra a correlação entre o consumo atual e o consumo da hora anterior. Lags de 24 horas e 48 horas são cruciais para identificar padrões diários e de dois dias, respectivamente.

    *   **Autocorrelação (ACF)**: A autocorrelação mede a correlação entre uma série temporal e uma versão defasada de si mesma. Um valor de autocorrelação próximo de 1 indica uma forte correlação positiva (se o consumo foi alto na hora X, é provável que seja alto na hora X+lag). Um valor próximo de -1 indica uma forte correlação negativa, e um valor próximo de 0 indica pouca ou nenhuma correlação.

3.  **Visualização da ACF**: A função `plot_acf` da biblioteca `statsmodels` é usada para gerar um gráfico da Função de Autocorrelação (ACF). Este gráfico exibe os coeficientes de autocorrelação para uma série de lags, juntamente com bandas de confiança (as áreas sombreadas). Se a linha da autocorrelação ultrapassar essas bandas, a correlação é estatisticamente significativa.

**Resultados Esperados e Interpretação:**

Ao executar este código, esperamos observar os seguintes pontos:

*   **Lag de 1 hora**: A autocorrelação para um lag de 1 hora deve ser alta e positiva. Isso significa que o consumo de energia em uma hora é fortemente correlacionado com o consumo na hora imediatamente anterior. Isso é esperado, pois o consumo de energia não muda drasticamente de um minuto para o outro ou de uma hora para a outra.

*   **Lag de 24 horas**: A autocorrelação para um lag de 24 horas (um dia) deve ser significativamente alta e positiva. Um valor alto aqui indica que há um forte padrão diário no consumo de energia. Ou seja, o consumo em uma determinada hora do dia é semelhante ao consumo na mesma hora do dia anterior. Isso é um indicativo claro de **padrões repetidos diariamente**, como picos de consumo pela manhã e à noite, e quedas durante a madrugada.

*   **Lag de 48 horas**: A autocorrelação para um lag de 48 horas (dois dias) também deve ser alta e positiva, embora possivelmente um pouco menor que a de 24 horas. Isso reforça a presença de padrões diários consistentes.

*   **Gráfico ACF**: O gráfico ACF deve mostrar picos significativos nos lags de 24, 48, 72 horas e assim por diante, confirmando a sazonalidade diária. As barras de autocorrelação nesses lags devem estar bem acima das bandas de confiança, indicando que esses padrões não são aleatórios.

**Resposta à pergunta: existem padrões repetidos diariamente?**

Com base nos resultados esperados, a resposta é **sim, existem padrões repetidos diariamente**. A alta autocorrelação em lags de 24 e 48 horas, juntamente com os picos no gráfico ACF nesses mesmos lags, são evidências fortes de uma sazonalidade diária no consumo de energia. Isso reflete os hábitos de vida das pessoas, como acordar, trabalhar, voltar para casa e dormir, que se repetem em ciclos de 24 horas e influenciam diretamente o uso de eletricidade.

Esta análise de autocorrelação é fundamental para o desenvolvimento de modelos de previsão de séries temporais, pois a identificação de padrões sazonais permite que os modelos capturem e prevejam com mais precisão o comportamento futuro do consumo de energia.



## 23. Redução de dimensionalidade com PCA

Nesta tarefa, selecionaremos as variáveis `Global_active_power`, `Global_reactive_power`, `Voltage` e `Global_intensity` e aplicaremos a Análise de Componentes Principais (PCA) para reduzir a dimensionalidade para 2 componentes principais. Em seguida, analisaremos a variância explicada por cada componente.

### Código Python para a Solução:

```python
import pandas as pd
from sklearn.preprocessing import StandardScaler
from sklearn.decomposition import PCA
import matplotlib.pyplot as plt
import numpy as np

# Carregar o dataset (assumindo que o dataset está em um arquivo CSV chamado 'household_power_consumption.txt')
df = pd.read_csv('household_power_consumption.txt', sep=';', decimal=',')

# Converter colunas numéricas para o tipo correto, tratando erros
for col in ['Global_active_power', 'Global_reactive_power', 'Voltage', 'Global_intensity', 'Sub_metering_1', 'Sub_metering_2', 'Sub_metering_3']:
    df[col] = pd.to_numeric(df[col], errors='coerce')

# Remover linhas com valores ausentes nas colunas selecionadas para o PCA
df_pca = df[['Global_active_power', 'Global_reactive_power', 'Voltage', 'Global_intensity']].dropna()

# Padronizar os dados antes de aplicar o PCA
# O PCA é sensível à escala das variáveis, por isso a padronização é crucial.
scaler = StandardScaler()
df_scaled = scaler.fit_transform(df_pca)

# Aplicar PCA para reduzir para 2 componentes principais
pca = PCA(n_components=2)
pca_components = pca.fit_transform(df_scaled)

# Criar um DataFrame com os componentes principais
df_pca_components = pd.DataFrame(data=pca_components, columns=['PC1', 'PC2'])

print('Variância explicada por cada componente principal:')
print(pca.explained_variance_ratio_)

print(f'Variância total explicada pelos 2 componentes principais: {pca.explained_variance_ratio_.sum():.4f}')

# Visualização dos componentes principais (opcional, mas útil para entender a distribuição)
plt.figure(figsize=(10, 7))
plt.scatter(df_pca_components['PC1'], df_pca_components['PC2'], alpha=0.5)
plt.title('Componentes Principais (PCA) das Variáveis Elétricas')
plt.xlabel('Componente Principal 1 (PC1)')
plt.ylabel('Componente Principal 2 (PC2)')
plt.grid(True)
plt.tight_layout()
plt.savefig('pca_components.png')

# Opcional: Visualizar a contribuição das variáveis originais para cada componente
# Isso ajuda a interpretar o significado de cada componente principal
loadings = pd.DataFrame(pca.components_.T, columns=['PC1', 'PC2'], index=df_pca.columns)
print('\nCargas (Loadings) das variáveis originais nos componentes principais:')
print(loadings)

```

### Explicação e Análise:

O código acima implementa a Análise de Componentes Principais (PCA) nas variáveis elétricas do dataset, seguindo as etapas:

1.  **Carregamento e Pré-processamento dos Dados**: O dataset é carregado, e as colunas numéricas relevantes (`Global_active_power`, `Global_reactive_power`, `Voltage`, `Global_intensity`) são convertidas para o tipo numérico, com tratamento de erros (`errors='coerce'`) para converter valores não numéricos em `NaN`. Em seguida, as linhas que contêm `NaN` nessas colunas são removidas, garantindo que o PCA seja aplicado apenas a dados completos.

2.  **Padronização dos Dados**: Antes de aplicar o PCA, os dados são padronizados usando `StandardScaler`. A padronização (também conhecida como normalização Z-score) transforma os dados para que tenham média 0 e desvio padrão 1. Isso é crucial para o PCA, pois ele é sensível à escala das variáveis. Variáveis com escalas maiores teriam uma influência desproporcionalmente maior nos componentes principais se não fossem padronizadas.

3.  **Aplicação do PCA**: A classe `PCA` do `sklearn.decomposition` é instanciada com `n_components=2`, indicando que queremos reduzir a dimensionalidade para 2 componentes principais. O método `fit_transform` é então aplicado aos dados padronizados, que calcula os componentes principais e projeta os dados nesse novo espaço de menor dimensão.

4.  **Análise da Variância Explicada**: O atributo `explained_variance_ratio_` do objeto PCA retorna a proporção da variância total dos dados originais que é explicada por cada componente principal. A soma dessas proporções para os componentes selecionados (`pca.explained_variance_ratio_.sum()`) nos dá a variância total explicada pelos 2 componentes principais. Este é um indicador chave da quantidade de informação (variabilidade) dos dados originais que é retida após a redução de dimensionalidade.

5.  **Visualização dos Componentes Principais**: Um gráfico de dispersão é gerado para visualizar os dados projetados nos dois primeiros componentes principais (PC1 e PC2). Este gráfico permite observar a distribuição dos dados no espaço reduzido e pode revelar padrões ou agrupamentos.

6.  **Análise das Cargas (Loadings)**: Opcionalmente, as cargas (`pca.components_`) são calculadas e exibidas. As cargas representam a correlação entre as variáveis originais e os componentes principais. Elas ajudam a interpretar o significado de cada componente principal, mostrando quais variáveis originais contribuem mais para a formação de cada componente.

**Resultados Esperados e Interpretação:**

Ao executar este código, esperamos ver:

*   **Variância Explicada**: Os valores de `explained_variance_ratio_` indicarão a importância relativa de cada componente. Por exemplo, se o PC1 explica 70% da variância e o PC2 explica 20%, significa que juntos eles capturam 90% da variabilidade total dos dados originais. Um valor alto para a variância total explicada (acima de 80-90% é geralmente considerado bom) indica que os dois componentes principais são capazes de representar a maior parte da informação contida nas quatro variáveis originais.

*   **Gráfico de Dispersão**: O gráfico de `PC1` vs `PC2` mostrará como os pontos de dados estão distribuídos no espaço bidimensional. Pode-se observar se há alguma estrutura, como aglomerados ou tendências, que não era facilmente visível nas quatro dimensões originais.

*   **Cargas (Loadings)**: A análise das cargas revelará quais variáveis originais são mais fortemente associadas a cada componente principal. Por exemplo, se `Global_active_power` e `Global_intensity` têm cargas altas e positivas no PC1, isso sugere que o PC1 representa principalmente o nível geral de consumo de energia. Se `Voltage` tiver uma carga alta no PC2, o PC2 pode estar relacionado a variações na tensão.

**Conclusão sobre a Redução de Dimensionalidade:**

A redução de dimensionalidade com PCA é uma técnica poderosa para simplificar a análise de dados com muitas variáveis. Ao reter a maior parte da variância em um número menor de componentes, podemos visualizar os dados de forma mais eficaz, reduzir o ruído e potencialmente melhorar o desempenho de modelos de aprendizado de máquina subsequentes. A variância explicada nos dirá o quão bem os dois componentes principais representam as informações originais, e a visualização nos ajudará a entender a estrutura dos dados nesse espaço reduzido.



## 24. Visualização de clusters no espaço PCA

Nesta tarefa, combinamos os resultados do PCA (obtidos na tarefa anterior) com o algoritmo K-Means (3 clusters) e, em seguida, plotaremos os pontos resultantes, colorindo cada grupo de acordo com o cluster ao qual pertencem. A pergunta a ser respondida é: os grupos se separam de forma clara?

### Código Python para a Solução:

```python
import pandas as pd
from sklearn.preprocessing import StandardScaler
from sklearn.decomposition import PCA
from sklearn.cluster import KMeans
import matplotlib.pyplot as plt
import numpy as np

# Carregar o dataset (assumindo que o dataset está em um arquivo CSV chamado 'household_power_consumption.txt')
df = pd.read_csv('household_power_consumption.txt', sep=';', decimal=',')

# Converter colunas numéricas para o tipo correto, tratando erros
for col in ['Global_active_power', 'Global_reactive_power', 'Voltage', 'Global_intensity', 'Sub_metering_1', 'Sub_metering_2', 'Sub_metering_3']:
    df[col] = pd.to_numeric(df[col], errors='coerce')

# Remover linhas com valores ausentes nas colunas selecionadas para o PCA
df_pca_kmeans = df[['Global_active_power', 'Global_reactive_power', 'Voltage', 'Global_intensity']].dropna()

# Padronizar os dados antes de aplicar o PCA
scaler = StandardScaler()
df_scaled_kmeans = scaler.fit_transform(df_pca_kmeans)

# Aplicar PCA para reduzir para 2 componentes principais
pca_kmeans = PCA(n_components=2)
pca_components_kmeans = pca_kmeans.fit_transform(df_scaled_kmeans)

# Aplicar K-Means com 3 clusters nos componentes principais
kmeans = KMeans(n_clusters=3, random_state=42, n_init=10) # n_init para evitar warning
clusters = kmeans.fit_predict(pca_components_kmeans)

# Adicionar os resultados do PCA e dos clusters ao DataFrame original (ou a um novo DataFrame para visualização)
df_pca_kmeans_results = pd.DataFrame(data=pca_components_kmeans, columns=['PC1', 'PC2'])
df_pca_kmeans_results['Cluster'] = clusters

# Visualizar os clusters no espaço PCA
plt.figure(figsize=(10, 7))
scatter = plt.scatter(df_pca_kmeans_results['PC1'], df_pca_kmeans_results['PC2'], c=df_pca_kmeans_results['Cluster'], cmap='viridis', alpha=0.6)
plt.title('Visualização de Clusters (K-Means) no Espaço PCA')
plt.xlabel('Componente Principal 1 (PC1)')
plt.ylabel('Componente Principal 2 (PC2)')
plt.colorbar(scatter, label='Cluster')
plt.grid(True)
plt.tight_layout()
plt.savefig('pca_kmeans_clusters.png')

```

### Explicação e Análise:

Este código integra as técnicas de PCA e K-Means para visualizar agrupamentos nos dados de consumo de energia. As etapas são as seguintes:

1.  **Preparação dos Dados**: As mesmas variáveis elétricas (`Global_active_power`, `Global_reactive_power`, `Voltage`, `Global_intensity`) são selecionadas, valores ausentes são tratados e os dados são padronizados usando `StandardScaler`. Esta etapa é idêntica à da tarefa de PCA, garantindo que os dados estejam na escala correta para a análise.

2.  **Aplicação do PCA**: O PCA é aplicado novamente para reduzir a dimensionalidade das variáveis padronizadas para 2 componentes principais (`PC1` e `PC2`). Esses dois componentes capturam a maior parte da variância dos dados originais e servem como o novo espaço de características para o agrupamento.

3.  **Aplicação do K-Means**: O algoritmo K-Means é então aplicado aos dois componentes principais (`pca_components_kmeans`). Definimos `n_clusters=3`, conforme solicitado, para agrupar os dados em três categorias distintas. O `random_state=42` garante a reprodutibilidade dos resultados, e `n_init=10` é usado para executar o algoritmo múltiplas vezes com diferentes centroides iniciais e escolher a melhor solução, evitando problemas de convergência para mínimos locais.

4.  **Criação do DataFrame de Resultados**: Os componentes principais e os rótulos de cluster atribuídos pelo K-Means são combinados em um novo DataFrame (`df_pca_kmeans_results`). Isso facilita a visualização dos resultados.

5.  **Visualização dos Clusters**: Um gráfico de dispersão é gerado onde o eixo X representa o `PC1` e o eixo Y representa o `PC2`. Os pontos no gráfico são coloridos de acordo com o cluster ao qual pertencem, usando um mapa de cores (`cmap='viridis'`). Uma barra de cores é adicionada para indicar qual cor corresponde a cada cluster.

**Resultados Esperados e Interpretação:**

Ao visualizar o gráfico `pca_kmeans_clusters.png`, a principal questão a ser respondida é: **os grupos se separam de forma clara?**

*   **Separação Clara**: Se os clusters se separarem claramente, veremos grupos distintos de pontos coloridos no gráfico, com pouca sobreposição entre eles. Isso indicaria que o K-Means conseguiu identificar padrões de consumo de energia bem definidos com base nas variáveis elétricas, e que esses padrões são visíveis no espaço bidimensional reduzido pelo PCA.

*   **Sobreposição**: Se houver muita sobreposição entre os clusters, isso pode indicar que:
    *   Os 3 clusters podem não ser o número ideal de agrupamentos para esses dados.
    *   As variáveis selecionadas ou os componentes principais não são suficientes para distinguir claramente os padrões de consumo.
    *   Os padrões de consumo são mais contínuos do que discretos, dificultando a separação em grupos distintos.

**Conclusão sobre a Separação dos Grupos:**

A clareza da separação dos grupos dependerá da estrutura intrínseca dos dados. Em muitos casos de consumo de energia, é razoável esperar que existam diferentes 


perfis de consumo (por exemplo, baixo, médio, alto), que podem se manifestar como clusters distintos. A visualização no espaço PCA é uma ferramenta poderosa para avaliar a qualidade desses agrupamentos e entender a estrutura dos dados.

## 25. Regressão polinomial vs linear

Nesta tarefa, modelaremos `Global_active_power` em função de `Voltage`. Compararemos a Regressão Linear Simples com a Regressão Polinomial (grau 2) e analisaremos o RMSE (Root Mean Squared Error) e a curva ajustada de cada modelo.

### Código Python para a Solução:

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from sklearn.linear_model import LinearRegression
from sklearn.preprocessing import PolynomialFeatures
from sklearn.metrics import mean_squared_error

# Carregar o dataset
df = pd.read_csv("household_power_consumption.txt", sep=";", decimal=",")

# Converter colunas para numérico, tratando erros
df["Global_active_power"] = pd.to_numeric(df["Global_active_power"], errors="coerce")
df["Voltage"] = pd.to_numeric(df["Voltage"], errors="coerce")

# Remover linhas com valores ausentes nas colunas de interesse
df_reg = df[["Global_active_power", "Voltage"]].dropna()

# Definir X e y
X = df_reg[["Voltage"]]
y = df_reg["Global_active_power"]

# --- Regressão Linear Simples ---
linear_model = LinearRegression()
linear_model.fit(X, y)
y_pred_linear = linear_model.predict(X)
rmse_linear = np.sqrt(mean_squared_error(y, y_pred_linear))

print(f"RMSE (Regressão Linear Simples): {rmse_linear:.4f}")

# --- Regressão Polinomial (grau 2) ---
poly_features = PolynomialFeatures(degree=2)
X_poly = poly_features.fit_transform(X)

poly_model = LinearRegression()
poly_model.fit(X_poly, y)
y_pred_poly = poly_model.predict(X_poly)
rmse_poly = np.sqrt(mean_squared_error(y, y_pred_poly))

print(f"RMSE (Regressão Polinomial - Grau 2): {rmse_poly:.4f}")

# --- Visualização das Curvas Ajustadas ---
plt.figure(figsize=(10, 7))
plt.scatter(X, y, alpha=0.1, label="Dados Reais")

# Plotar a linha de regressão linear
plt.plot(X, y_pred_linear, color="red", label=f"Regressão Linear (RMSE: {rmse_linear:.2f})")

# Plotar a curva de regressão polinomial
# Para plotar a curva polinomial de forma suave, precisamos de pontos ordenados
X_plot = np.linspace(X.min(), X.max(), 100).reshape(-1, 1)
X_plot_poly = poly_features.transform(X_plot)
y_plot_poly = poly_model.predict(X_plot_poly)
plt.plot(X_plot, y_plot_poly, color="green", label=f"Regressão Polinomial (RMSE: {rmse_poly:.2f})")

plt.title("Comparação entre Regressão Linear e Polinomial")
plt.xlabel("Voltage")
plt.ylabel("Global_active_power")
plt.legend()
plt.grid(True)
plt.tight_layout()
plt.savefig("regressao_linear_vs_polinomial.png")

```

### Explicação e Análise:

Este código compara o desempenho de um modelo de regressão linear simples e um modelo de regressão polinomial (grau 2) para prever `Global_active_power` com base em `Voltage`. As etapas são as seguintes:

1.  **Carregamento e Pré-processamento dos Dados**: O dataset é carregado, e as colunas `Global_active_power` e `Voltage` são convertidas para o tipo numérico, tratando erros. Linhas com valores ausentes nessas colunas são removidas para garantir a integridade da análise.

2.  **Regressão Linear Simples**: Um modelo de `LinearRegression` é instanciado e ajustado aos dados `X` (`Voltage`) e `y` (`Global_active_power`). As previsões são geradas, e o RMSE é calculado para avaliar o erro do modelo. O RMSE (Root Mean Squared Error) é uma métrica que mede a magnitude média dos erros. Um RMSE menor indica um modelo com melhor ajuste aos dados.

3.  **Regressão Polinomial (Grau 2)**: Para a regressão polinomial, `PolynomialFeatures(degree=2)` é usado para transformar a variável `Voltage` em um conjunto de características polinomiais de grau 2 (ou seja, `Voltage` e `Voltage^2`). Em seguida, um modelo de `LinearRegression` é ajustado a essas novas características polinomiais. As previsões são geradas, e o RMSE é calculado para este modelo.

4.  **Visualização das Curvas Ajustadas**: Um gráfico de dispersão dos dados reais é plotado. Em seguida, as curvas ajustadas de ambos os modelos (linear e polinomial) são sobrepostas no mesmo gráfico. Para a curva polinomial, é importante gerar pontos de `Voltage` ordenados (`np.linspace`) para que a curva seja plotada suavemente.

**Resultados Esperados e Interpretação:**

Ao executar este código, esperamos observar:

*   **Comparação de RMSE**: O valor do RMSE para a regressão polinomial (grau 2) deve ser comparado com o RMSE da regressão linear simples. Se a relação entre `Voltage` e `Global_active_power` não for estritamente linear, é provável que o modelo polinomial apresente um RMSE menor, indicando um ajuste melhor aos dados.

*   **Análise da Curva Ajustada**: O gráfico `regressao_linear_vs_polinomial.png` mostrará visualmente como cada modelo se ajusta aos dados. A linha de regressão linear será uma linha reta, enquanto a curva de regressão polinomial será uma curva (neste caso, uma parábola, já que o grau é 2). Se a relação entre as variáveis for não linear, a curva polinomial deve se ajustar melhor aos pontos de dados, capturando a curvatura presente na distribuição.

**Conclusão sobre a Comparação:**

A escolha entre regressão linear e polinomial depende da natureza da relação entre as variáveis. Se a relação for linear, a regressão linear simples é suficiente e mais interpretável. No entanto, se houver uma curvatura nos dados, a regressão polinomial pode capturar melhor essa relação, resultando em um modelo com menor erro e um ajuste mais preciso. A análise do RMSE e a visualização das curvas ajustadas são essenciais para tomar essa decisão e entender o comportamento dos modelos.

