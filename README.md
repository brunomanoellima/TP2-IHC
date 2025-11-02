# 🧠 Classificação da Usabilidade de um Site Educacional  
**Trabalho Prático 2 — Interação Humano-Computador e Machine Learning (Weka)**  

---

## 📘 Etapa 4 — Relatório Final  

---

### 1️⃣ Definição do Problema (Contexto, Atributos e Classe)  

O projeto aplica **classificação supervisionada** para prever o **nível de usabilidade** (`baixa`, `media`, `alta`) de um site educacional, com base em métricas objetivas de interação e avaliações subjetivas de satisfação do usuário.  
A abordagem integra princípios de **Interação Humano-Computador (IHC)** com **Machine Learning (ML)** no ambiente **Weka**, permitindo tanto a predição quanto a extração de insights interpretáveis sobre os fatores que influenciam a experiência do usuário.

**Contexto:**  
Em sites educacionais, a usabilidade é crítica para promover aprendizado eficaz, reduzir barreiras cognitivas e aumentar o engajamento.  
Este trabalho simula dados de usuários interagindo com o site, usando ML para classificar o nível de usabilidade e extrair **regras acionáveis** para designers.

**Atributos preditores:**
| Atributo | Descrição | Tipo |
|:-----------|:------------|:------|
| `tempo_tarefa_seg` | Tempo médio (s) para concluir uma tarefa | Numérico (contínuo) |
| `erros` | Número total de erros cometidos | Numérico (inteiro) |
| `satisfacao` | Escala de 1 (muito insatisfeito) a 5 (muito satisfeito) | Ordinal |
| `experiencia_previa` | Nível de experiência {baixa, media, alta} | Nominal |
| `navegacao_intuitiva` | Grau de intuitividade (1–5) | Ordinal |
| `eficiencia` | Desempenho/tempo (0–1) | Contínuo |

**Classe-alvo:** `nivel_usabilidade` {baixa, media, alta}  

**Hipótese IHC:**  
Usuários com **alta experiência**, **alta eficiência**, **poucos erros**, **tempos curtos** e **satisfação alta** tendem a classificar a usabilidade como alta, alinhando-se aos pilares da **ISO 9241-11** (eficácia, eficiência e satisfação).

---

### 2️⃣ Regras Usadas para Gerar a Classe-Alvo  

As rotulações foram geradas deterministicamente via LLM (ChatGPT), simulando julgamento de especialista em IHC:

- **Alta:** `erros ≤ 2` ∧ `tempo_tarefa_seg ≤ 60` ∧ `satisfacao ≥ 4` ∧ `eficiencia ≥ 0.60` ∧ `navegacao_intuitiva ≥ 4`  
- **Média:** Pelo menos 3 critérios de “alta” atendidos, sem cair em “baixa”  
- **Baixa:** `erros > 5` ∨ `tempo_tarefa_seg > 120` ∨ `satisfacao ≤ 2` ∨ `eficiencia < 0.40`

Essas regras refletem relações causais coerentes com a teoria de IHC: **alta usabilidade requer baixo atrito e alta percepção positiva**.

---

### 3️⃣ Descrição da Base Sintética  

| Item | Descrição |
|:------|:-----------|
| **Arquivo** | `usabilidade_site_educacional.arff` |
| **Instâncias** | 300 (baixa ≈110, média ≈99, alta ≈91) |
| **Atributos** | 6 preditores + 1 classe |
| **Geração** | Sintética via LLM com regras definidas |
| **Formato** | ARFF com cabeçalho `@relation`, atributos e dados separados por vírgula |
| **Qualidade** | Sem missing values, duplicatas ou inconsistências |

---

### 4️⃣ Descrição dos Experimentos no Weka  

**Configuração dos experimentos:**
- **Carregamento:** Aba *Preprocess* (300 instâncias)  
- **Exploração:** Aba *Visualize* (dispersões e correlações)  
- **Classificação:** Aba *Classify*, método: *10-fold cross-validation*  
- **Classificadores:**  
  - ZeroR (baseline)  
  - OneR  
  - Naive Bayes  
  - IBk (k=5)  
  - J48 (árvore de decisão)  
- **Métricas:** Acurácia, Kappa, matriz de confusão, ROC  
- **Visualização:** Árvore J48 exportada via *right-click*

---

### 5️⃣ Resultados  

#### 📊 5.1 Comparação Geral de Desempenho  

| **Algoritmo** | **Acurácia (%)** | **Kappa** | **Interpretação Geral** |
|:---------------|:----------------:|:----------:|:------------------------|
| ZeroR | 36.67 | 0.0000 | Baseline mínima |
| OneR | 84.00 | 0.7600 | Regras simples, boa precisão |
| Naive Bayes | 87.33 | 0.8103 | Consistente, assume independência |
| IBk (k=5) | 88.33 | 0.8242 | Similaridade, sensível à escala |
| **J48** | **91.33** | **0.8694** | **Melhor desempenho e interpretabilidade** |

**Resumo:**  
O **J48** apresentou melhor equilíbrio entre precisão e interpretabilidade, recriando logicamente as regras de IHC.

---

#### 🧮 5.2 Matrizes de Confusão  

**J48 (melhor modelo):**
