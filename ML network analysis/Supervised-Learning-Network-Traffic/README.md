# Supervised Learning for Network Traffic Analysis

## Cenário
No panorama digital interligado atual, as organizações enfrentam ameaças constantes provenientes de intrusões de rede e atividades maliciosas. Os analistas de segurança necessitam de sistemas robustos e automatizados para identificar e classificar tráfego de rede suspeito de forma eficiente. A adoção destas tecnologias permite uma resposta célere a potenciais ciberataques, reduzindo o tempo de triagem e fortalecendo significativamente a postura geral de cibersegurança da infraestrutura.

## Objetivo
Desenvolver competências práticas para criar, implementar e avaliar modelos de aprendizagem automática (*Machine Learning*) focados na deteção de intrusões de rede e na classificação automatizada de pacotes maliciosos.

## Visão Geral
Este módulo proporciona experiência prática na construção de modelos de *Machine Learning* orientados para a segurança de redes. O projeto abrange todo o ciclo de vida dos dados: desde a geração e pré-processamento de dados sintéticos de tráfego de rede, até ao treino de classificadores avançados, especificamente Regressão Logística (*Logistic Regression*) e Máquinas de Vetores de Suporte (*Support Vector Machines - SVM*). Por fim, inclui a avaliação rigorosa do desempenho dos modelos e a simulação de cenários de deteção em tempo real para validar a sua aplicação prática em cibersegurança.

## Tarefas do Laboratório

1. [Exercise 01: Develop a Network Intrusion Detection System using Logistic Regression](#exercise-01-develop-a-network-intrusion-detection-system-using-logistic-regression)
2. [Exercise 02: Classify Malicious Network Packets using Support Vector Machines](#exercise-02-classify-malicious-network-packets-using-support-vector-machines)

---

## Exercise 01: Develop a Network Intrusion Detection System using Logistic Regression

### Cenário
As organizações lidam diariamente com o desafio de analisar volumes massivos de tráfego de rede para detetar e prevenir intrusões. A análise manual é insustentável perante a escala e a velocidade dos ciberataques modernos. As equipas de segurança (SOC) necessitam de ferramentas eficazes baseadas em Inteligência Artificial para analisar padrões de tráfego, distinguir atividades normais de comportamentos suspeitos e gerar alertas automatizados em tempo real, mantendo assim um ambiente operacional seguro.

### Objetivo
Desenvolver, treinar e avaliar um modelo de *Machine Learning* baseado em Regressão Logística (*Logistic Regression*) para a deteção de intrusões de rede, simulando a sua aplicação prática num painel de monitorização (SOC Dashboard) em tempo real.

### Metodologia e Execução

O projeto foi desenvolvido em linguagem Python, utilizando o ambiente **Jupyter Notebook** e recorrendo a bibliotecas padrão de Ciência de Dados (`pandas`, `numpy`, `matplotlib`, `seaborn` e `scikit-learn`).

1. **Geração de Dados Sintéticos e Engenharia de Atributos (Feature Engineering):**
   * Desenvolveu-se uma função para gerar um *dataset* de 2000 amostras simulando tráfego de rede. 
   * Para refletir cenários reais, aplicou-se um desequilíbrio de classes (*Class Imbalance*): 84.2% de tráfego normal e 15.8% de tráfego malicioso.
   * O tráfego malicioso foi modelado para simular assinaturas de três vetores de ataque distintos: **DDoS** (alto volume de pacotes, alta frequência), **Port Scans** (duração muito curta, pacotes minúsculos) e **Exfiltração de Dados** (sessões longas, transferência massiva de bytes).

2. **Análise Exploratória de Dados (EDA):**
   * Através do `matplotlib` e `seaborn`, geraram-se histogramas para comparar a distribuição das quatro *features* principais (`packet_count`, `duration_seconds`, `bytes_transferred`, `connection_frequency`).
   * A análise estatística confirmou que os ataques apresentavam assinaturas quantificáveis (ex: picos drásticos na frequência de ligações durante anomalias), provando que os dados eram linearmente separáveis.

3. **Pré-Processamento de Dados:**
   * O *dataset* foi dividido numa proporção de 70/30 (Treino/Teste) utilizando o parâmetro `stratify=y` para garantir a mesma proporção de ataques em ambos os conjuntos.
   * Aplicou-se o `StandardScaler` para normalizar as variáveis, centrando-as no zero com um desvio padrão de 1. Esta padronização é crítica na Regressão Logística para evitar que *features* com grandezas maiores (como `bytes_transferred`) dominem o modelo.

4. **Treino e Interpretabilidade do Modelo (Logistic Regression):**
   * O modelo de Regressão Logística foi treinado com os dados escalados.
   * *Interpretabilidade:* A extração dos coeficientes do modelo revelou a importância de cada atributo. Verificou-se que a frequência de ligações (`connection_frequency`) e a contagem de pacotes (`packet_count`) apresentavam os coeficientes positivos mais elevados, significando que o aumento destas variáveis impulsiona drasticamente a probabilidade de o tráfego ser classificado como malicioso.

5. **Avaliação de Desempenho e Métricas (Evaluation):**
   * **Accuracy:** ~94.7% (Precisão global do modelo).
   * **Precision:** ~86.9% (Dos alertas gerados, quase 87% eram realmente ataques, minimizando a fadiga de alertas / *False Positives* no SOC).
   * **Recall:** ~77.7% (O modelo conseguiu detetar cerca de 78% de todas as intrusões reais).
   * **AUC-ROC:** ~0.968 (Um valor excelente, indicando uma capacidade fortíssima de distinguir entre tráfego normal e malicioso).
   * Gerou-se uma Matriz de Confusão (*Confusion Matrix*) que validou o baixo número de Falsos Positivos (11) e Falsos Negativos (21) num universo de 600 amostras de teste.

6. **Simulação de Deteção em Tempo Real (Live SOC Dashboard):**
   * Desenvolveu-se um *script* de simulação que injeta 15 novas ligações (tráfego *live*) no modelo treinado.
   * O sistema classificou o risco de cada ligação em tempo real (🟢 NORMAL, 🟡 MEDIUM RISK, 🔴 HIGH RISK) com base no nível de confiança da probabilidade prevista.
   * Para cada alerta gerado, o painel indicou os "Fatores Chave de Risco" (*Key Risk Factors*), explicando de forma interpretável ao analista de segurança por que motivo o modelo bloqueou aquele tráfego (ex: aumento anormal da contagem de pacotes).

### Análise e Conclusão
Este laboratório demonstra que a Regressão Logística, embora seja um algoritmo fundamental e relativamente simples, é extremamente eficaz na triagem inicial de ameaças de rede. A sua maior vantagem em cibersegurança reside na **interpretabilidade**: ao contrário das redes neuronais (*Black Boxes*), a Regressão Logística permite à equipa de segurança extrair os coeficientes e compreender exatamente as regras matemáticas que levaram a um alerta. 

As métricas alcançadas provam que a implementação de *Machine Learning* no pré-processamento de *logs* de rede pode reduzir drasticamente o ruído e os Falsos Positivos, permitindo que os analistas humanos foquem a sua atenção apenas nos incidentes classificados como *High Risk*.

---

## Exercise 02: Classify Malicious Network Packets using Support Vector Machines

### Cenário
Para reforçar as defesas de uma infraestrutura moderna, é crucial conseguir classificar com precisão pacotes de rede individuais como benignos ou maliciosos. Esta tarefa exige o desenvolvimento de modelos robustos de *Machine Learning* capazes de interpretar múltiplas características de rede em simultâneo (como dimensões de pacotes, protocolos e durações) para identificar indicadores de comprometimento (IoCs) associados a exfiltração de dados, varrimento de portas (*port scanning*) ou intrusões persistentes.

### Objetivo
Desenvolver, treinar e avaliar um modelo de Máquinas de Vetores de Suporte (*Support Vector Machine* - SVM) para classificar pacotes de rede individuais, analisando criticamente o seu desempenho, a sua fronteira de decisão e as suas taxas de falsos positivos/negativos.

### Metodologia e Execução

O projeto foi executado em ambiente Python (Jupyter Notebook) utilizando a biblioteca `scikit-learn`.

1. **Geração de Dados e Engenharia de Rótulos (Rule-Based Labeling):**
   * Gerou-se um *dataset* de 1000 amostras com atributos típicos de pacotes de rede (`protocol_type`, `packet_size`, `port_number`, `connection_duration`, `bytes_sent`, `bytes_received`).
   * Para simular o trabalho inicial de um analista de segurança, implementou-se uma função heurística para classificar o tráfego. O algoritmo atribuía uma pontuação de risco com base em limites predefinidos:
     - Pacotes muito grandes (>800 bytes) = Risco de exfiltração.
     - Portas anómalas (<1024 ou >49152) = Vetores comuns de ataque.
     - Ligações prolongadas (>5s) = Risco de ameaças persistentes.
     - Transferência massiva de dados = Potencial roubo de dados.
   * Pacotes com pontuação $\ge$ 2 foram rotulados como maliciosos. O *dataset* final obteve 142 pacotes maliciosos (classe 1) e 858 benignos (classe 0).

2. **Pré-Processamento e Normalização (Feature Scaling):**
   * A variável categórica `protocol_type` (TCP/UDP/ICMP) foi convertida em formato numérico utilizando o `LabelEncoder`.
   * Os dados foram divididos em conjuntos de treino (700) e teste (300) com proporção estratificada (`stratify=y`).
   * **Passo Crítico:** Aplicou-se o `StandardScaler` a todas as *features*. Como o algoritmo SVM tenta maximizar a margem (distância espacial) entre as classes, é extremamente sensível à escala dos dados. A normalização garantiu que variáveis de grande magnitude (como `port_number`) não dominassem o modelo.

3. **Treino do Modelo SVM (Radial Basis Function):**
   * Instanciou-se o modelo `SVC` utilizando o *kernel* **RBF** (*Radial Basis Function*). 
   * A escolha do RBF foi deliberada: ao projetar os dados num espaço de dimensionalidade superior, este *kernel* consegue encontrar fronteiras de decisão não-lineares, adequando-se perfeitamente à complexidade do tráfego de rede (onde ataques e tráfego benigno frequentemente se sobrepõem visualmente em gráficos de dispersão bidimensionais).

4. **Avaliação de Desempenho e Matriz de Confusão:**
   * O modelo alcançou uma **Exatidão (Accuracy) Global de 92.3%**.
   * **Análise de Classes:** 
     - **Tráfego Benigno (0):** Excelente desempenho (Precision 0.93 / Recall 0.98).
     - **Tráfego Malicioso (1):** Desempenho moderado (Precision 0.86 / Recall 0.56). 
   * A Matriz de Confusão confirmou estes resultados: o modelo identificou corretamente 253 pacotes benignos e 24 maliciosos, mas gerou 4 Falsos Positivos e 19 Falsos Negativos.

5. **Análise Forense de Falhas e Simulação em Tempo Real:**
   * Injetaram-se novos pacotes não vistos pelo modelo, gerando previsões acompanhadas de **Índices de Confiança** (através da `decision_function`), demonstrando como o sistema priorizaria alertas num SOC real.
   * Foi desenvolvido um *script* analítico focado exclusivamente nos pacotes classificados incorretamente (Misclassified Packets). A análise focou-se nos **19 Falsos Negativos** (ataques reais que o modelo classificou como tráfego benigno).

### Análise e Conclusão
Este laboratório ilustra o imenso potencial e os desafios práticos das Máquinas de Vetores de Suporte (SVM) na cibersegurança. Embora a precisão global do modelo tenha sido alta (92.3%), a métrica de *Recall* para a classe maliciosa (56%) revelou uma lacuna crítica: o modelo deixou passar uma porção significativa de tráfego malicioso (19 pacotes em 300).

Num ambiente de Centro de Operações de Segurança (SOC), **Falsos Negativos são substancialmente mais perigosos do que Falsos Positivos**, pois representam ataques bem-sucedidos e não detetados. Esta análise conclui que, apesar do *kernel* RBF ser a abordagem correta para dados não-lineares, a arquitetura do modelo necessita de otimização adicional (*Hyperparameter Tuning* dos parâmetros `C` e `gamma`, ou técnicas de *Class Weighting*) para penalizar mais severamente os erros na classe minoritária (ataques), sacrificando alguma precisão geral em favor de uma taxa de deteção de intrusões (Recall) muito superior.