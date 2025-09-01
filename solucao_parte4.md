# PARTE 4 – Exercícios no Orange Data Mining

## 36. Importação e visualização inicial

Nesta tarefa, descreveremos como usar o widget `CSV File Import` para carregar o dataset `Individual Household Electric Power Consumption` e conectá-lo ao widget `Data Table` para visualizar as primeiras linhas. A pergunta a ser respondida é: quantas variáveis e registros aparecem?

### Passos no Orange Data Mining:

1.  **Abrir o Orange e criar um novo fluxo de trabalho.**
2.  **Adicionar o widget `CSV File Import`**: No painel de widgets à esquerda, encontre o widget `CSV File Import` (geralmente na seção "Data") e arraste-o para a tela de fluxo de trabalho.
3.  **Configurar o `CSV File Import`**:
    *   Clique duas vezes no widget `CSV File Import` para abrir suas configurações.
    *   Selecione a opção "File" e clique no ícone da pasta para navegar e selecionar o arquivo `household_power_consumption.txt`.
    *   **Importante**: O Orange pode não detectar automaticamente as configurações corretas para este arquivo. Você precisará ajustar as opções de importação:
        *   **Delimiter**: Selecione "Semicolon" (ponto e vírgula).
        *   **Decimal Separator**: Selecione "Comma" (vírgula).
        *   **Header**: Certifique-se de que a opção "Header row" esteja marcada para que a primeira linha seja tratada como o cabeçalho.
    *   O Orange tentará adivinhar o tipo de cada coluna. Verifique se as colunas numéricas (`Global_active_power`, `Voltage`, etc.) estão definidas como "Numeric" e as colunas de data e hora (`Date`, `Time`) como "Date/Time". Se necessário, você pode clicar no tipo de dado de uma coluna para alterá-lo.
    *   Clique em "Apply" ou "OK" para confirmar as configurações.
4.  **Adicionar o widget `Data Table`**: Encontre o widget `Data Table` (na seção "Data") e arraste-o para a tela.
5.  **Conectar os widgets**: Clique na saída do widget `CSV File Import` e arraste uma linha de conexão até a entrada do widget `Data Table`. Isso enviará os dados carregados para a tabela de visualização.
6.  **Visualizar os dados**: Clique duas vezes no widget `Data Table` para abrir a tabela. Você verá as primeiras linhas do dataset.

### Resposta à pergunta: quantas variáveis e registros aparecem?

Ao abrir o `Data Table`, na parte inferior da janela, o Orange exibirá um resumo do dataset. Para o dataset `Individual Household Electric Power Consumption`, a informação exibida será algo como:

*   **Registros (Instances)**: **2,075,259** (ou um número próximo, dependendo de como o Orange lida com linhas em branco no final do arquivo).
*   **Variáveis (Features)**: **9** (as colunas originais: `Date`, `Time`, `Global_active_power`, `Global_reactive_power`, `Voltage`, `Global_intensity`, `Sub_metering_1`, `Sub_metering_2`, `Sub_metering_3`).

**Conclusão**: O dataset `Individual Household Electric Power Consumption` contém aproximadamente 2.075.259 registros e 9 variáveis.

## 37. Amostragem de dados (1%)

Nesta tarefa, usaremos o widget `Sample Data` para selecionar uma amostra de 1% dos registros. A pergunta a ser respondida é: a distribuição de `Global_active_power` na amostra é semelhante à base completa?

### Passos no Orange Data Mining:

1.  **Continuar com o fluxo de trabalho anterior** (`CSV File Import` -> `Data Table`).
2.  **Adicionar o widget `Sample Data`**: Encontre o widget `Sample Data` (na seção "Data") e coloque-o na tela.
3.  **Conectar `CSV File Import` ao `Sample Data`**: Conecte a saída do `CSV File Import` à entrada do `Sample Data`.
4.  **Configurar o `Sample Data`**:
    *   Clique duas vezes no widget `Sample Data`.
    *   Selecione a opção "Fixed sample size".
    *   Marque a opção "Percentage of data" e defina o valor como **1%**.
    *   Clique em "Sample Data" para gerar a amostra.
5.  **Adicionar dois widgets `Distribution`**: Encontre o widget `Distribution` (na seção "Visualize") e adicione dois deles à tela.
6.  **Conectar para comparar as distribuições**:
    *   Conecte a saída do `CSV File Import` ao primeiro widget `Distribution`. Este mostrará a distribuição da base completa.
    *   Conecte a saída "Data Sample" do widget `Sample Data` ao segundo widget `Distribution`. Este mostrará a distribuição da amostra de 1%.
7.  **Visualizar e comparar as distribuições**:
    *   Abra ambos os widgets `Distribution`.
    *   Em cada um, selecione a variável `Global_active_power` na lista de variáveis à esquerda.
    *   Compare visualmente os dois histogramas.

### Resposta à pergunta: a distribuição de `Global_active_power` na amostra é semelhante à base completa?

**Sim, a distribuição de `Global_active_power` na amostra de 1% deve ser muito semelhante à da base completa.**

**Interpretação**: Ao comparar os dois histogramas, você observará que a forma geral da distribuição é preservada na amostra. Ambos os histogramas mostrarão uma forte concentração de valores baixos (próximos de zero) e uma cauda longa para a direita, indicando que o consumo de energia é geralmente baixo, com picos ocasionais de alto consumo. A amostragem aleatória, quando aplicada a um dataset grande, tende a manter as características estatísticas e a distribuição das variáveis originais. Embora as contagens de frequência absolutas sejam menores na amostra, a forma relativa da distribuição (a proporção de valores em cada faixa) permanecerá consistente. Isso demonstra que uma amostra bem selecionada pode ser uma representação confiável do dataset completo, o que é útil para análises exploratórias e desenvolvimento de modelos em datasets muito grandes, onde o processamento completo pode ser lento.

## 38. Distribuição do consumo

Nesta tarefa, conectaremos ao widget `Distribution` e visualizaremos `Global_active_power`. A pergunta a ser respondida é: o consumo é concentrado em valores baixos ou há muitos registros de alto consumo?

### Passos no Orange Data Mining:

1.  **Continuar com o fluxo de trabalho anterior**.
2.  **Usar o widget `Distribution` já conectado ao `CSV File Import`** (da tarefa 37).
3.  **Visualizar a distribuição**:
    *   Clique duas vezes no widget `Distribution` que está conectado ao `CSV File Import` (base completa).
    *   Selecione a variável `Global_active_power` na lista à esquerda.

### Resposta à pergunta: o consumo é concentrado em valores baixos ou há muitos registros de alto consumo?

**O consumo é fortemente concentrado em valores baixos.**

**Interpretação**: O histograma de `Global_active_power` exibido no widget `Distribution` mostrará uma distribuição assimétrica à direita (positivamente enviesada). A barra mais alta do histograma estará nos valores mais baixos, próximos de zero, e a altura das barras diminuirá rapidamente à medida que os valores de consumo aumentam. Haverá uma cauda longa para a direita, indicando que, embora existam registros de alto consumo, eles são muito menos frequentes do que os registros de baixo consumo. Isso é típico de dados de consumo de energia residencial, onde a maior parte do tempo o consumo é baixo (e.g., durante a noite, quando as pessoas estão dormindo, ou durante o dia, quando a casa está vazia), com picos de consumo ocorrendo em momentos específicos (e.g., ao cozinhar, usar aquecedores, etc.).

## 39. Relação entre variáveis elétricas

Nesta tarefa, usaremos o widget `Scatter Plot` para analisar `Voltage` (X) vs `Global_intensity` (Y). A pergunta a ser respondida é: existe correlação visível?

### Passos no Orange Data Mining:

1.  **Continuar com o fluxo de trabalho anterior**.
2.  **Adicionar o widget `Scatter Plot`**: Encontre o widget `Scatter Plot` (na seção "Visualize") e arraste-o para a tela.
3.  **Conectar `CSV File Import` ao `Scatter Plot`**: Conecte a saída do `CSV File Import` à entrada do `Scatter Plot`.
4.  **Configurar o `Scatter Plot`**:
    *   Clique duas vezes no widget `Scatter Plot` para abri-lo.
    *   No painel de controle à esquerda, defina:
        *   **X-axis**: `Voltage`
        *   **Y-axis**: `Global_intensity`
    *   Observe a nuvem de pontos resultante.

### Resposta à pergunta: existe correlação visível?

**Não, não existe uma correlação linear clara e forte visível entre `Voltage` e `Global_intensity` no gráfico de dispersão.**

**Interpretação**: Ao visualizar o gráfico de dispersão, você verá uma nuvem de pontos densa e um tanto dispersa. Não haverá uma tendência linear óbvia (ou seja, os pontos não formarão uma linha clara ascendente ou descendente). A intensidade (`Global_intensity`) pode variar amplamente para um mesmo nível de tensão (`Voltage`). Pode haver algumas concentrações ou padrões, mas, no geral, a relação não é uma correlação linear simples. Isso sugere que a tensão da rede elétrica, embora possa flutuar, não é o principal fator que determina a intensidade da corrente consumida em um determinado momento. A intensidade da corrente está mais diretamente relacionada à potência ativa (`Global_active_power`) e aos aparelhos que estão em uso, em vez de pequenas variações na tensão da rede.

## 40. Clustering com K-Means

Nesta tarefa, aplicaremos o widget `k-Means` com 3 clusters, usando como atributos `Sub_metering_1`, `Sub_metering_2` e `Sub_metering_3`. Visualizaremos os grupos no `Scatter Plot`. A pergunta a ser respondida é: cada cluster representa um padrão distinto de consumo doméstico?

### Passos no Orange Data Mining:

1.  **Continuar com o fluxo de trabalho anterior**.
2.  **Adicionar o widget `k-Means`**: Encontre o widget `k-Means` (na seção "Unsupervised") e arraste-o para a tela.
3.  **Conectar `CSV File Import` ao `k-Means`**: Conecte a saída do `CSV File Import` à entrada do `k-Means`.
4.  **Configurar o `k-Means`**:
    *   Clique duas vezes no widget `k-Means`.
    *   Defina "Number of clusters" como **3**.
    *   **Importante**: Na seção "Attributes", certifique-se de que apenas `Sub_metering_1`, `Sub_metering_2` e `Sub_metering_3` estejam selecionados como atributos para o clustering. Você pode precisar desmarcar as outras variáveis.
    *   Clique em "Find Clusters".
5.  **Adicionar um novo `Scatter Plot`**: Adicione outro widget `Scatter Plot` à tela.
6.  **Conectar `k-Means` ao `Scatter Plot`**: Conecte a saída do `k-Means` à entrada do novo `Scatter Plot`.
7.  **Visualizar os clusters**:
    *   Abra o novo `Scatter Plot`.
    *   Nos eixos, você pode escolher quaisquer duas das variáveis de submedição (e.g., `Sub_metering_1` no eixo X e `Sub_metering_2` no eixo Y).
    *   Na seção "Color" (ou similar), selecione "Cluster" para colorir os pontos de acordo com o cluster ao qual pertencem.

### Resposta à pergunta: cada cluster representa um padrão distinto de consumo doméstico?

**Sim, cada cluster tende a representar um padrão distinto de consumo doméstico com base nos submedidores.**

**Interpretação**: Ao visualizar o gráfico de dispersão com os pontos coloridos por cluster, você provavelmente observará o seguinte:

*   **Cluster 1 (e.g., Azul)**: A grande maioria dos pontos estará concentrada na origem (0,0), representando momentos em que o consumo em todos os três submedidores é muito baixo ou zero. Este é o **padrão de consumo de base ou inativo**, correspondendo a períodos em que poucos ou nenhuns aparelhos específicos (cozinha, lavanderia, aquecedor de água/ar condicionado) estão em uso.

*   **Cluster 2 (e.g., Vermelho)**: Um segundo grupo de pontos pode se estender ao longo de um dos eixos. Por exemplo, se `Sub_metering_1` (cozinha) estiver no eixo X, este cluster pode mostrar valores mais altos para `Sub_metering_1` enquanto os outros submedidores permanecem baixos. Este cluster representaria um **padrão de consumo focado na cozinha**.

*   **Cluster 3 (e.g., Verde)**: Um terceiro grupo pode mostrar um padrão diferente, talvez com valores mais altos para `Sub_metering_3` (aquecedor de água e ar condicionado). Este cluster representaria um **padrão de consumo de aquecimento/resfriamento**.

Os clusters separam os dados em grupos que correspondem a diferentes combinações de uso dos aparelhos monitorados pelos submedidores. Portanto, cada cluster efetivamente captura um padrão distinto de consumo doméstico: um de baixa atividade, e outros focados em atividades específicas como cozinhar, lavar roupa ou climatização. Isso demonstra como o K-Means pode ser usado para segmentar comportamentos de consumo com base em dados de medição.
