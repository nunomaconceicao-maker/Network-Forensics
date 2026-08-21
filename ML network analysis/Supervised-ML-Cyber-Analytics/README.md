# Introduction to Supervised Machine Learning for Cybersecurity Analytics

## Cenário
Um analista de cibersegurança necessita frequentemente de compreender e aplicar técnicas fundamentais de *Machine Learning* supervisionado a problemas práticos do mundo real — desde a previsão da gravidade de uma falha de segurança (*data breach*) até à classificação automatizada de ficheiros maliciosos. Este módulo proporciona experiência prática e aprofundada na construção, treino e avaliação de modelos analíticos preditivos e de classificação.

## Objetivo
Demonstrar a aplicação prática de modelos de Regressão Linear (*Linear Regression*) e classificação baseada em Árvores de Decisão (*Decision Trees*) em contextos analíticos de cibersegurança.

## Visão Geral
Este módulo introduz dois algoritmos centrais da aprendizagem supervisionada: a **Regressão Linear**, utilizada para prever resultados numéricos contínuos (como a métrica de impacto de um incidente), e as **Árvores de Decisão**, focadas na classificação de resultados discretos (como classificar um ficheiro como benigno ou *malware*). 

Através de exercícios práticos num ambiente de desenvolvimento Jupyter Notebook, o laboratório abrange a geração de *datasets* sintéticos orientados para a cibersegurança, o treino e avaliação rigorosa dos modelos, e a interpretação tática dos resultados através de várias métricas de desempenho e visualizações de dados.

## Tarefas do Laboratório

1. [Exercise 01: Predicting Cyber Breach Severity with Linear Regression](#exercise-01-predicting-cyber-breach-severity-with-linear-regression)
2. [Exercise 02: Classifying Malware with Decision Trees](#exercise-02-classifying-malware-with-decision-trees)

---

## Exercise 01: Predicting Cyber Breach Severity with Linear Regression

### Cenário
Na gestão de incidentes de cibersegurança, não basta apenas detetar a ameaça; é fundamental quantificar o seu impacto potencial. Como Cientista de Dados focado em segurança, a missão consiste em desenvolver um modelo preditivo capaz de estimar a gravidade (Severidade) de uma falha de segurança (*data breach*). O modelo baseia-se em fatores determinantes, nomeadamente a duração do ataque e a pontuação de vulnerabilidade do sistema afetado, aplicando Regressão Linear (*Linear Regression*) para modelar matematicamente esta relação.

### Objetivo
Construir, treinar e avaliar um modelo de Regressão Linear multivariável para prever uma pontuação contínua de severidade de incidentes cibernéticos, validando as premissas estatísticas do modelo.

### Metodologia e Execução

O laboratório foi desenvolvido em Python, através do Jupyter Notebook, com recurso às bibliotecas `pandas`, `numpy`, `matplotlib` e `scikit-learn`.

1. **Geração de Dados Sintéticos e Exploração (EDA):**
   * Criou-se um *dataset* com 100 amostras simuladas representando incidentes de segurança.
   * **Features (Variáveis Independentes):** `attack_duration` (duração em horas, de 0.5 a 10) e `vulnerability_score` (índice de fraqueza do sistema normalizado entre 0 e 1).
   * **Target (Variável Dependente):** O cálculo da severidade baseou-se numa função ponderada das *features*, à qual foi adicionado ruído Gaussiano (ruído aleatório) para mimetizar a imprevisibilidade de dados reais num SOC.
   * *Visualização:* A geração de gráficos de dispersão (*scatter plots*) confirmou visualmente uma correlação linear positiva entre ambas as variáveis independentes e a pontuação de severidade.

2. **Divisão dos Dados (Train-Test Split):**
   * O *dataset* foi particionado numa proporção padrão de 80/20. O conjunto de treino ficou com 80 amostras para ajustamento do modelo, e o de teste com 20 amostras para avaliação cega de desempenho.

3. **Treino do Modelo (Model Fitting) e Extração de Coeficientes:**
   * O algoritmo `LinearRegression` foi instanciado e treinado (`.fit()`). O processo de otimização encontrou a reta de melhor ajuste, originando a seguinte equação preditiva:
     $Severity = -0.387 + (1.947 \times Duration) + (11.574 \times Vulnerability)$
   * **Interpretabilidade:** Os coeficientes extraídos indicam que, mantendo a vulnerabilidade constante, cada hora adicional de ataque agrava a severidade em cerca de 1.95 pontos. Simultaneamente, o peso da vulnerabilidade (11.574) indica que sistemas estruturalmente mais fracos amplificam drasticamente o impacto da intrusão.

4. **Avaliação de Desempenho e Métricas de Regressão:**
   * Foram geradas previsões contra o conjunto de teste e calculadas as métricas fundamentais:
     * **Mean Squared Error (MSE):** ~2.66 (Média dos erros quadráticos).
     * **Root Mean Squared Error (RMSE):** ~1.63 (O modelo erra as previsões em média por cerca de 1.63 pontos de severidade).
     * **Coeficiente de Determinação ($R^2$):** ~0.94.

5. **Diagnóstico e Validação Estatística do Modelo:**
   * **Actual vs. Predicted Plot:** Um gráfico traçando os valores reais contra os previstos demonstrou que os pontos de dados se aglomeram de forma muito estreita em torno da linha diagonal ideal, confirmando a precisão visual sugerida pelo $R^2$.
   * **Análise de Resíduos (Residual Plot):** Calculou-se a diferença entre os valores previstos e os reais (resíduos). O gráfico demonstrou que os erros estão dispersos aleatoriamente em redor do eixo zero. Não se verificaram padrões óbvios em funil (*heterocedasticidade*) nem curvaturas, comprovando que a escolha por um modelo linear foi estatisticamente correta e que as premissas da regressão não foram violadas.

### Análise e Conclusão
O modelo de Regressão Linear apresentou um desempenho excecional com um $R^2$ de 94%, o que significa que o modelo consegue explicar 94% da variância na severidade dos ataques utilizando apenas duas *features* (Duração e Vulnerabilidade). 

No contexto prático de cibersegurança, este modelo serve como uma ferramenta poderosa para a quantificação de risco. Permite às equipas de Resposta a Incidentes (IR) estimar imediata e matematicamente o impacto de um compromisso assim que as variáveis iniciais da intrusão são conhecidas, facilitando a alocação prioritária de recursos para mitigar os ataques com maior potencial de severidade.

---

## Exercise 02: Classifying Malware with Decision Trees

### Cenário
A triagem manual de ficheiros suspeitos é um processo insustentável perante o volume de ameaças diárias. Como analista de cibersegurança, o desafio consiste em construir um sistema automatizado capaz de analisar os atributos estáticos de um ficheiro e classificá-lo como benigno ou malicioso (*malware*). Para tal, utiliza-se um algoritmo de Árvores de Decisão (*Decision Tree Classifier*), cuja maior vantagem é fornecer uma lógica de classificação completamente transparente e interpretável por humanos.

### Objetivo
Desenvolver, treinar e avaliar um modelo de classificação baseado em Árvores de Decisão para detetar *malware* com base nas características estruturais dos ficheiros, analisando o seu desempenho através de métricas de classificação e matrizes de confusão.

### Metodologia e Execução

O laboratório foi executado em ambiente Python (Jupyter Notebook), utilizando a biblioteca `scikit-learn` para a modelação e `matplotlib` para as visualizações.

1. **Geração de Dados Sintéticos (Atributos de Análise Estática):**
   * Criou-se um *dataset* de 200 amostras simulando a extração de características estáticas de ficheiros.
   * **Features:** 
     * `file_size`: Tamanho do ficheiro (distribuição log-normal).
     * `permissions`: Permissões do ficheiro (0=Read-Only, 1=Read-Write, 2=Executable).
     * `entropy`: Nível de aleatoriedade dos dados (1-8 bits/byte) – um indicador clássico de ofuscação ou código empacotado (*packed malware*).
     * `num_imports`: Número de chamadas a bibliotecas externas importadas (distribuição de Poisson).
   * O *dataset* foi concebido de forma realista, apresentando um desequilíbrio de classes (*Imbalanced Data*): 71.5% de ficheiros benignos e 28.5% de *malware*.

2. **Análise Exploratória de Dados (EDA):**
   * A visualização das distribuições (Histogramas) confirmou a validade das *features*. Os ficheiros maliciosos revelaram uma clara tendência para níveis de entropia mais elevados (próximos de 7-8) e um maior número de *imports* no código, quando comparados com os ficheiros benignos.

3. **Divisão de Dados (Stratified Split):**
   * Os dados foram divididos em conjuntos de treino (150 amostras) e teste (50 amostras) numa proporção de 75/25.
   * Utilizou-se o parâmetro `stratify=y` para garantir que a proporção exata de 71.5% vs 28.5% (Benigno/*Malware*) foi mantida em ambos os conjuntos, prevenindo enviesamentos estatísticos no treino.

4. **Treino do Modelo e Interpretabilidade:**
   * O algoritmo `DecisionTreeClassifier` foi treinado definindo o parâmetro `max_depth=4`. Limitar a profundidade da árvore é uma técnica fundamental para evitar o sobreajuste (*overfitting*), garantindo que o modelo generalize bem para ficheiros desconhecidos e mantendo o diagrama de decisão humanamente legível.
   * *Estrutura da Árvore:* A visualização do modelo (`plot_tree`) revelou a sequência de tomada de decisão. O modelo aprendeu autonomamente a separar os dados testando primeiro o nível de permissões e cruzando-o posteriormente com o número de *imports* e os limites críticos de entropia.

5. **Avaliação de Desempenho e Métricas:**
   * **Accuracy (Exatidão):** 90% (O modelo acertou em 90% de todas as classificações no conjunto de teste).
   * **Precision (Precisão):** ~84.6% (Alta capacidade de minimizar Falsos Positivos, evitando bloquear ficheiros legítimos).
   * **Recall (Sensibilidade):** ~78.6% (Capacidade sólida de encontrar o *malware* real camuflado no sistema).
   * **F1-Score:** ~81.5% (Uma excelente harmonia entre *Precision* e *Recall*).
   * **Matriz de Confusão:** Validou analiticamente o desempenho num universo de 50 amostras de teste, registando 34 Verdadeiros Negativos, 11 Verdadeiros Positivos, apenas 2 Falsos Positivos e 3 Falsos Negativos.

6. **Curva ROC e AUC:**
   * Foi gerada a Curva ROC (Receiver Operating Characteristic) para avaliar a capacidade diagnóstica do modelo em vários limiares de classificação.
   * O modelo atingiu um **AUC (Area Under the Curve) de 0.96**. Sendo 1.0 a perfeição estatística, um AUC de 0.96 comprova que o modelo possui uma capacidade de discriminação excecional entre ficheiros limpos e ameaças.

### Análise e Conclusão
O modelo de Árvores de Decisão provou ser uma ferramenta notável para a triagem automatizada de ameaças. Em cibersegurança, a "explicabilidade" de um modelo (XAI - *Explainable AI*) é crítica. Ao contrário das redes neuronais, onde a lógica de decisão permanece oculta, a Árvore de Decisão permitiu visualizar exatamente as regras matemáticas aplicadas. 

Isto confere uma vantagem tática enorme às equipas defensivas (Blue Teams), que podem analisar o modelo treinado para extrair novos limiares críticos (por exemplo, "Entropia $> 6.599$ AND imports $> 14.5$") e injetar essas regras nativamente nas suas soluções de EDR (Endpoint Detection and Response) ou SIEM, fortalecendo proativamente a postura de segurança da organização.