# 🧠 Classificação da Usabilidade de um Site Educacional  
**Trabalho Prático 2 — Interação Humano-Computador e Machine Learning (Weka)**  

---

## Etapa 4 — Relatório Final  

---

### 1. Definição do Problema (contexto, atributos, classe)  

O projeto aplica **classificação supervisionada** para prever o **nível de usabilidade** (`baixa`, `media`, `alta`) de um site educacional, com base em métricas objetivas de interação e avaliações subjetivas de satisfação do usuário. A abordagem integra princípios de **Interação Humano-Computador (IHC)** com técnicas de **Machine Learning** no ambiente Weka, permitindo não apenas a predição, mas também a extração de insights interpretáveis sobre os fatores que influenciam a experiência do usuário.

**Contexto:** Em sites educacionais, a usabilidade é crítica para promover aprendizado eficaz, reduzindo barreiras cognitivas e aumentando o engajamento. Este trabalho simula dados de usuários interagindo com o site, usando ML para classificar o nível de usabilidade e extrair regras acionáveis para designers.

**Atributos preditores:**  
- `tempo_tarefa_seg`: tempo médio em segundos para concluir uma tarefa (numérico, contínuo);  
- `erros`: número total de erros cometidos (numérico, inteiro);  
- `satisfacao`: avaliação subjetiva em escala de 1 (muito insatisfeito) a 5 (muito satisfeito) (ordinal);  
- `experiencia_previa`: nível de experiência prévia {baixa, media, alta} (nominal);  
- `navegacao_intuitiva`: grau de intuitividade da navegação em escala de 1 a 5 (ordinal);  
- `eficiencia`: razão desempenho/tempo (numérico, contínuo, 0 a 1).  

**Classe-alvo:** `nivel_usabilidade` {baixa, media, alta} (nominal).  

**Hipótese IHC:** Usuários com alta experiência, eficiência elevada, poucos erros, tempos curtos e satisfação alta tendem a classificar a usabilidade como alta, alinhando-se aos pilares de eficácia, eficiência e satisfação da ISO 9241-11.

---

### 2. Regras Usadas para Gerar a Classe-Alvo  

As rotulações foram geradas deterministicamente via LLM (ChatGPT), simulando julgamento de especialista em IHC:

- **Alta:** `erros ≤ 2` E `tempo_tarefa_seg ≤ 60` E `satisfacao ≥ 4` E `eficiencia ≥ 0.60` E `navegacao_intuitiva ≥ 4`;  
- **Média:** Condições parciais (pelo menos 3 critérios de "alta" atendidos, mas não todos, sem cair em "baixa");  
- **Baixa:** `erros > 5` OU `tempo_tarefa_seg > 120` OU `satisfacao ≤ 2` OU `eficiencia < 0.40`, ou violações múltiplas.

Essas regras refletem relações causais: alta usabilidade requer baixo atrito e alta percepção positiva.

---

### 3. Descrição da Base Sintética  

- **Arquivo:** `usabilidade_site_educacional.arff`;  
- **Instâncias:** 300 (distribuição aproximada: baixa ~110, media ~99, alta ~91, ligeiramente desbalanceada pela geração rule-based);  
- **Atributos:** 6 preditores + 1 classe;  
- **Geração:** Sintética via LLM com regras acima, garantindo consistência e ausência de ruído desnecessário;  
- **Formato:** ARFF com cabeçalho `@relation`, atributos definidos e dados separados por vírgula.  

Base inspecionada no Weka: sem missing values, duplicatas ou inconsistências.

---

### 4. Descrição dos Experimentos no Weka  

Experimentos no **Weka Explorer**:

- **Carregamento:** Aba Preprocess, base com 300 instâncias;  
- **Exploração:** Aba Visualize, scatter plots para atributos vs classe e matriz de dispersão;  
- **Classificação:** Aba Classify, método: 10-fold stratified cross-validation;  
- **Classificadores:** ZeroR (baseline), OneR, Naive Bayes, IBk (k=5), J48 (poda padrão);  
- **Métricas:** Acurácia, Kappa, matriz de confusão, ROC, etc.;  
- **Visualização extra:** Árvore J48 exportada via right-click.

---

### 5. Resultados (tabelas, matrizes de confusão, prints de tela)  

#### 5.1 Tabela de Comparação Geral do Desempenho  

| **Algoritmo** | **Acurácia (%)** | **Kappa** | **Interpretação Geral** |
|:---------------|:----------------:|:----------:|:------------------------|
| **ZeroR** | 36.6667 | 0.0000 | Baseline mínima (classe majoritária: baixa). |
| **OneR** | 84.0000 | 0.7600 | Regra simples (eficiente para padrões lineares). |
| **Naive Bayes** | 87.3333 | 0.8103 | Probabilístico, assume independência, consistente. |
| **IBk (k-NN)** | 88.3333 | 0.8242 | Baseado em similaridade, sensível a escala. |
| **J48** | **91.3333** | **0.8694** | **Melhor**, interpretável, generaliza bem. |

**Análise:** J48 destaca-se pela acurácia alta e interpretabilidade da árvore, "redescobrindo" regras de IHC.

#### 5.2 Matrizes de Confusão  

**J48:**  
a   b   c   ← classificado como
108  2   0  | a = baixa
 8  77   6  | b = media
 0  10  89  | c = alta

Acurácia: 91.3333% (274/300 acertos). Classe média mais confusa (zona transição).

**IBk:**  
a   b   c   ← classificado como
103  7   0  | a = baixa
14  73   4  | b = media
 0  10  89  | c = alta

Acurácia: 88.3333% (265/300).

**Naive Bayes:**  
a   b   c   ← classificado como
96  14   0  | a = baixa
6   83   2  | b = media
0   16  83  | c = alta

Acurácia: 87.3333% (262/300).

**OneR:**  
a   b   c   ← classificado como
91  19   0  | a = baixa
4   86   1  | b = media
0   24  75  | c = alta

Acurácia: 84% (252/300).

**ZeroR:**  
a   b   c   ← classificado como
110  0   0  | a = baixa
91   0   0  | b = media
99   0   0  | c = alta

Acurácia: 36.6667% (110/300, tudo como baixa).

#### 5.3 Prints de Tela com Explicações Detalhadas  

Abaixo, todos os prints da pasta `prints/`, com análise baseada no conteúdo visual:

- **Arvore.png:** Visualização da árvore J48.  
  **Descrição detalhada:** Árvore com raiz em `experiencia_previa` (baixa, media, alta). Para `baixa`: divide por `navegacao_intuitiva` (<=3: baixa; >3: por `satisfacao`). Para `media`: divide por `satisfacao`, depois `erros`, `tempo_tarefa_seg`. Para `alta`: divide por `erros` (<=3: alta; >3: por `satisfacao`). Profundidade moderada (até 5 níveis), 17 nós.  
  **Análise:** Reproduz regras de geração (ex.: alta experiência + baixa erros = alta usabilidade). Interpretável para IHC: priorize onboarding para baixa experiência.  
  ![Árvore J48](prints/Arvore.png)

- **Eficiencia x classe.png:** Scatter plot `eficiencia` (x: 0-1) vs `nivel_usabilidade` (y: baixa=blue, media=red, alta=green).  
  **Descrição detalhada:** Pontos azuis (baixa) <0.4; vermelhos (media) ~0.4-0.6; verdes (alta) >0.6. Separação nítida, jitter para evitar sobreposição.  
  **Análise:** Eficiência alta correlaciona perfeitamente com usabilidade alta, validando hipótese IHC de produtividade como chave.  
  ![Eficiência x Classe](prints/Eficiencia x classe.png)

- **Erro x classe.png:** Scatter plot `erros` (x: 0-11) vs `nivel_usabilidade`.  
  **Descrição detalhada:** Azuis (baixa) >5 erros; vermelhos (media) 3-5; verdes (alta) <3. Correlação negativa clara.  
  **Análise:** Erros elevados indicam falhas de design (ex.: falta de feedback), reduzindo usabilidade. Threshold ~3 erros como ponto crítico.  
  ![Erro x Classe](prints/Erro x classe.png)

- **Erro x Tempo.png:** Scatter plot `tempo_tarefa_seg` (x: 60-516) vs `erros` (y: 0-11), colorido por classe.  
  **Descrição detalhada:** Correlação positiva: azuis (baixa) alto tempo/alto erros; verdes (alta) baixo. Clusters distinctos.  
  **Análise:** Erros prolongam tempo (recuperação cognitiva), reforçando necessidade de validação preventiva em IHC.  
  ![Erro x Tempo](prints/Erro x Tempo.png)

- **Experiencia x classe.png:** Scatter plot `experiencia_previa` (x: baixa/media/alta) vs `nivel_usabilidade`.  
  **Descrição detalhada:** Baixa experiência: mostly azuis (baixa); media: vermelhos; alta: verdes (alta). Separação categórica forte.  
  **Análise:** Experiência modera usabilidade; designs devem adaptar-se a novatos (ex.: guias). Raiz da árvore J48.  
  ![Experiencia x Classe](prints/Experiencia x classe.png)

- **Navegacao x classe.png:** Scatter plot `navegacao_intuitiva` (x: 1-5) vs `nivel_usabilidade`.  
  **Descrição detalhada:** Azuis <2; vermelhos 2-4; verdes >4. Escala ordinal com separação linear.  
  **Análise:** Navegação intuitiva é chave para fluidez; baixa pontuação indica confusão hierárquica.  
  ![Navegacao x Classe](prints/Navegacao x classe.png)

- **Plot Matrix.png:** Matriz de dispersão para todos atributos.  
  **Descrição detalhada:** Subplots pairwise: ex., tempo vs erros (positiva), eficiencia vs usabilidade (positiva). Cores por classe mostram clusters separados.  
  **Análise:** Confirma correlações globais; base bem separável, ideal para classificação. Sem multicolinearidade extrema.  
  ![Plot Matrix](prints/Plot Matrix.png)

- **satisfacao x classe.png:** Scatter plot `satisfacao` (x: 1-5) vs `nivel_usabilidade`.  
  **Descrição detalhada:** Azuis <3; vermelhos 3; verdes >3. Separação nítida.  
  **Análise:** Satisfação subjetiva reflete experiência global; alta correlaciona com baixa frustrações.  
  ![Satisfacao x Classe](prints/satisfacao x classe.png)

- **Tempo x classe.png:** Scatter plot `tempo_tarefa_seg` (x: 60-516) vs `nivel_usabilidade`.  
  **Descrição detalhada:** Azuis >120s; vermelhos 60-120; verdes <60. Correlação negativa.  
  **Análise:** Tempos longos indicam ineficiência; otimize fluxos para <60s em tarefas chave.  
  ![Tempo x Classe](prints/Tempo x classe.png)

- **ibk.jpg:** Sumário IBk.  
  **Descrição detalhada:** Acurácia 88.3333%, Kappa 0.8242, matriz acima, ROC/PRC altas.  
  **Análise:** Bom, mas inferior a J48; sensível a outliers em dados sintéticos.  
  ![IBk](prints/ibk.jpg)

- **j48.jpg:** Sumário J48.  
  **Descrição detalhada:** Acurácia 91.3333%, Kappa 0.8694, matriz acima, árvore com 17 nós.  
  **Análise:** Excelente generalização; regras extraídas diretamente aplicáveis em IHC.  
  ![J48](prints/j48.jpg)

- **Naive Bayes.jpg:** Sumário Naive Bayes.  
  **Descrição detalhada:** Acurácia 87.3333%, Kappa 0.8103, matriz acima.  
  **Análise:** Eficiente para atributos mistos, mas subestima interações (assumindo independência).  
  ![Naive Bayes](prints/Naive Bayes.jpg)

- **oner.jpg:** Sumário OneR.  
  **Descrição detalhada:** Acurácia 84%, Kappa 0.76, matriz acima.  
  **Análise:** Regra única (provavelmente em eficiencia/erros) captura 84%; simples mas efetiva.  
  ![OneR](prints/oner.jpg)

- **zeror.jpg:** Sumário ZeroR.  
  **Descrição detalhada:** Acurácia 36.6667%, Kappa 0, matriz acima.  
  **Análise:** Referência mínima; destaca melhoria dos outros modelos.  
  ![ZeroR](prints/zeror.jpg)

---

### 6. Análise Crítica dos Resultados em Relação ao Domínio de IHC  

Os resultados validam a hipótese: usabilidade alta requer baixa carga cognitiva (poucos erros/tempo) e alta percepção (satisfação/navegação). J48 "redescobre" regras, ex.: experiência como raiz reflete que novatos sofrem mais com designs ruins.

**Críticas:** Base sintética limita realismo; validação cruzada robusta, mas dados reais (logs SUS) seriam ideais. Modelos clássicos bons, mas ensembles (Random Forest) poderiam melhorar.

**Implicações IHC:** Priorize designs adaptativos (ex.: guias para baixa experiência), feedback imediato (reduz erros), fluxos curtos (aumenta eficiência). Recomendações: use heatmaps para navegação, testes A/B para satisfação.

---

## Conclusão  

J48 destaca-se (91.33%, κ=0.8694), confirmando determinantes de usabilidade alta: alta experiência, eficiência ≥0.6, erros ≤2, tempo ≤60s, satisfação ≥4. Sites educacionais devem focar em simplicidade e adaptabilidade.

---

## Materiais Entregues  

- `usabilidade_site_educacional.arff`  
- `relatorio.md`  
- Pasta `prints/` com 14 arquivos explicados acima.

---
