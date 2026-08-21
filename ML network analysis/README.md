# Machine Learning for Cybersecurity & Network Analysis

## 📌 Visão Geral
Este repositório explora a interseção crítica entre a Ciência de Dados e a Cibersegurança. O foco principal é a aplicação prática de algoritmos de **Machine Learning Supervisionado** para automatizar a deteção de ameaças, otimizar a triagem de incidentes em ambientes SOC (*Security Operations Center*) e prever a severidade de falhas de segurança.

Através da engenharia de dados e da criação de modelos preditivos e de classificação, estes laboratórios demonstram como ferramentas de Inteligência Artificial podem transformar dados brutos (tráfego de rede, atributos de ficheiros) em inteligência acionável e defesas proativas.

## 📂 Estrutura do Repositório

Esta diretoria está dividida em dois laboratórios práticos, cada um focado num conjunto específico de algoritmos e problemas de cibersegurança:

### 1. [Supervised Learning for Network Traffic Analysis](./Supervised-Learning-Network-Traffic/README.md)
Focado na análise de pacotes e deteção de intrusões de rede em tempo real.
* **Técnicas utilizadas:** Regressão Logística (*Logistic Regression*) e Máquinas de Vetores de Suporte (*Support Vector Machines - SVM* com kernel RBF).
* **Casos de Uso:**
  * Desenvolvimento de um sistema de deteção de intrusões (NIDS) para identificar tráfego anómalo (DDoS, Port Scans, Exfiltração).
  * Simulação de um painel de monitorização SOC (*Live Dashboard*) com geração de alertas baseados em níveis de confiança.
  * Classificação complexa de pacotes individuais (Benigno vs. Malicioso) e análise rigorosa de falsos negativos.

### 2. [Supervised ML for Cyber Analytics](Supervised-ML-Cyber-Analytics/README.md)
Orientado para a modelação de risco, previsão de impactos e análise estática de ficheiros.
* **Técnicas utilizadas:** Regressão Linear multivariável (*Linear Regression*) e Árvores de Decisão (*Decision Trees*).
* **Casos de Uso:**
  * Quantificação matemática do risco e previsão da severidade de *data breaches* com base na duração do ataque e vulnerabilidade do sistema.
  * Automação da análise estática de *malware*, classificando ficheiros de forma interpretável (Explainable AI) através da análise de entropia, permissões e *imports*.

## 🛠️ Tecnologias e Ferramentas

Todo o código e análise de dados foram desenvolvidos utilizando o ecossistema padrão de Ciência de Dados em Python:
* **Ambiente:** Python 3, Jupyter Notebooks.
* **Processamento de Dados:** `pandas`, `numpy`.
* **Machine Learning:** `scikit-learn`.
* **Visualização:** `matplotlib`, `seaborn` (Matrizes de Confusão, Curvas ROC, Gráficos de Dispersão e Estruturas de Árvores).

## 🎯 Competências e Resultados (Learning Outcomes)
* **Engenharia de Dados (SecOps):** Capacidade de pré-processar, escalar e normalizar *logs* de segurança e atributos de rede para ingestão em modelos de IA.
* **Métricas de Avaliação:** Interpretação crítica do desempenho de modelos através de *Accuracy*, *Precision*, *Recall*, *F1-Score*, *ROC-AUC* e *Mean Squared Error (MSE)*.
* **Threat Hunting & XAI:** Foco na interpretabilidade dos modelos (*Explainable AI*), compreendendo o peso matemático de cada *feature* para traduzir previsões algorítmicas em regras de deteção estáticas e acionáveis (ex: regras SIEM ou YARA).