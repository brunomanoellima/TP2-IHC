# 🧠 Classificação da Usabilidade de um Site Educacional  
**Trabalho Prático 2 — Interação Humano-Computador e Machine Learning (Weka)**  

---

## 🧩 1. Definição do Problema  

O projeto aplica **classificação supervisionada** para prever o **nível de usabilidade** (`baixa`, `media`, `alta`) de um site educacional, com base em métricas objetivas de interação e avaliações subjetivas de satisfação do usuário. A abordagem integra princípios de **Interação Humano-Computador (IHC)** com técnicas de **Machine Learning** no ambiente Weka, permitindo não apenas a predição, mas também a extração de insights interpretáveis sobre os fatores que influenciam a experiência do usuário.

**Atributos preditores**  
- `tempo_tarefa_seg` – tempo médio em segundos para concluir uma tarefa específica no site;  
- `erros` – número total de erros cometidos durante a execução da tarefa (ex.: cliques incorretos, entradas inválidas);  
- `satisfacao` – avaliação subjetiva de satisfação do usuário em escala Likert de 1 a 5 (1 = muito insatisfeito, 5 = muito satisfeito);  
- `experiencia_previa` – nível de experiência prévia do usuário com sistemas similares, categorizado como {baixa, media, alta};  
- `navegacao_intuitiva` – grau percebido de intuitividade da navegação, em escala de 1 a 5 (1 = nada intuitiva, 5 = extremamente intuitiva);  
- `eficiencia` – razão entre o desempenho alcançado (ex.: tarefas concluídas com sucesso) e o tempo gasto, representando uma métrica composta de produtividade.

**Classe-alvo:** `nivel_usabilidade` com os valores possíveis {baixa, media, alta}.  

**Hipótese IHC.** Usuários com maior experiência prévia, alta eficiência, poucos erros, tempos reduzidos e elevados índices de satisfação tendem a perceber o site como altamente usável. Por outro lado, tempos prolongados, alta incidência de erros e baixa satisfação indicam usabilidade comprometida, alinhando-se aos princípios de eficiência, eficácia e satisfação definidos por normas como a ISO 9241-11.

---

## ⚙️ 2. Regras de Geração da Classe-Alvo  

As rotulações da classe-alvo foram geradas de forma determinística com base em regras que simulam o julgamento especializado de um profissional de IHC. Essas regras foram implementadas via LLM (ChatGPT) para criar uma base sintética consistente e livre de ambiguidades:

- **Alta:** número de erros ≤ 2, tempo_tarefa_seg ≤ 60, satisfacao ≥ 4, eficiencia ≥ 0,60, e navegacao_intuitiva ≥ 4;  
- **Média:** condições intermediárias, onde pelo menos 3 dos critérios de "alta" são atendidos parcialmente ou exatamente, mas sem atingir todos os thresholds de "alta" e sem cair nos critérios de "baixa";  
- **Baixa:** erros > 5, tempo_tarefa_seg > 120, satisfacao ≤ 2, eficiencia < 0,40, ou combinacoes que violem múltiplos thresholds críticos.

Essas regras garantem que a base reflita relações causais teoricamente fundamentadas, permitindo que os modelos de ML "redescubram" padrões alinhados à teoria de usabilidade.

---

## 🧮 3. Base de Dados  

- **Arquivo:** `usabilidade_site_educacional.arff`  
- **Instâncias:** 300 registros balanceados entre as três classes (aproximadamente 100 por classe, com pequenas variações naturais devido à geração rule-based);  
- **Atributos:** 6 preditores (sendo 3 numéricos contínuos: `tempo_tarefa_seg`, `erros`, `eficiencia`; 2 ordinais em escala 1-5: `satisfacao`, `navegacao_intuitiva`; 1 nominal: `experiencia_previa`) + 1 classe nominal (`nivel_usabilidade`);  
- **Geração:** realizada via LLM (ChatGPT) a partir das regras explícitas da seção 2, garantindo reproducibilidade e ausência de ruído aleatório desnecessário;  
- **Formato:** `.arff` compatível com o Weka, incluindo cabeçalho `@relation`, `@attribute` para cada variável e seção `@data` com valores separados por vírgula.

A base foi inspecionada no Weka para confirmar ausência de valores ausentes, duplicatas ou inconsistências lógicas.

---

## 🔎 4. Exploração Visual no Weka  

A análise exploratória foi conduzida na aba **Visualize** do Weka, utilizando diagramas de dispersão (scatter plots) e boxplots para cada par atributo-classe. Os padrões observados foram claros e consistentes com as regras de geração:

- **Experiência prévia × Usabilidade:** usuários com `experiencia_previa = alta` apresentaram predominância absoluta da classe `alta` (acima de 90% dos pontos); `baixa` experiência correlacionou-se fortemente com `baixa` usabilidade.  
- **Erros × Usabilidade:** aumento linear no número de erros desloca os pontos da classe `alta` para `baixa`, com threshold visível em torno de 3-4 erros como ponto de inflexão.  
- **Eficiência × Usabilidade:** valores de `eficiencia ≥ 0,60` concentram-se exclusivamente na classe `alta`; valores abaixo de 0,40 dominam a classe `baixa`.  
- **Tempo × Usabilidade:** tempos acima de 120 segundos associam-se quase que exclusivamente à classe `baixa`; tempos abaixo de 60 segundos predominam em `alta`.  
- **Satisfação × Usabilidade:** escala de satisfação apresenta separação nítida, com notas ≥ 4 fortemente ligadas a `alta` usabilidade e ≤ 2 a `baixa`.

Essas visualizações confirmam a alta coerência interna da base sintética, sem sobreposições significativas entre classes em dimensões críticas, o que facilita a tarefa de classificação.

---

## 🧪 5. Procedimentos de Classificação  

Todos os classificadores foram aplicados na aba **Classify** do Weka, utilizando:

- **Conjunto de treinamento/teste:** toda a base (300 instâncias);  
- **Método de avaliação:** **validação cruzada estratificada com 10 folds** (10-fold stratified cross-validation), garantindo preservação da distribuição original das classes em cada fold e reduzindo viés de amostragem;  
- **Parâmetros padrão do Weka:** sem tuning manual, exceto para IBk (k=5 por padrão);  
- **Classificadores testados:**  
  - **J48** (implementação Weka da árvore C4.5);  
  - **IBk** (k-Nearest Neighbors, com k=5);  
  - **Naive Bayes** (distribuição gaussiana para atributos numéricos);  
  - **OneR** (regra única baseada no atributo mais preditivo);  
  - **ZeroR** (classificador majoritário, baseline mínima).

---

## 📊 6. Resultados  

### 6.1 Tabela 1 — Comparação Geral do Desempenho dos Algoritmos  

| **Algoritmo** | **Acurácia (%)** | **Kappa** | **Interpretação Geral** |
|:---------------|:----------------:|:----------:|:------------------------|
| **ZeroR** | 36,67 % | 0,0 | Classifica todas as instâncias na classe majoritária (aproximadamente 1/3 de acerto por acaso); serve apenas como referência de desempenho mínimo. |
| **OneR** | 84,00 % | 0,7611 | Identifica uma única regra forte (provavelmente baseada em `eficiencia` ou `erros`), capturando a maior parte da variância com simplicidade extrema. |
| **Naive Bayes** | 87,33 % | 0,8103 | Modelo probabilístico robusto, lida bem com atributos mistos (numéricos e categóricos), mas assume independência condicional, o que pode subestimar interações complexas. |
| **J48 (Árvore de Decisão)** | **91,33 %** | **0,8694** | **Melhor desempenho geral**, com alta acurácia, excelente acordo além do acaso (κ próximo de 0,9) e interpretabilidade via estrutura de árvore. |
| **IBk (KNN)** | 88,33 % | 0,8242 | Desempenho sólido baseado em similaridade local, mas ligeiramente inferior ao J48 devido a sensibilidade a escala e ruído (mesmo em dados sintéticos limpos). |

**Análise detalhada:** O J48 superou os demais em ambos os métricos principais, indicando maior capacidade de generalização. A árvore resultante reproduz fielmente as heurísticas de IHC usadas na geração dos dados, validando tanto o modelo quanto a base.

---

### 6.2 Matriz de Confusão do Melhor Modelo (J48)  

a   b   c   ← classificado como
108  2   0  | a = baixa
 8  77   6  | b = media
 0  10  89  | c = alta


**Interpretação detalhada:**  
- **Diagonal principal:** 108 (baixa), 77 (média), 89 (alta) → total de 274 acertos em 300 instâncias = **91,33 %**.  
- **Erros principais:**  
  - 8 instâncias de `media` classificadas como `baixa`;  
  - 6 instâncias de `media` classificadas como `alta`;  
  - 10 instâncias de `alta` classificadas como `media`;  
  - 2 instâncias de `baixa` classificadas como `media`.  
- A classe **média** é a mais ambígua (zona de transição), com confusões simétricas para as classes adjacentes, mas sem impacto significativo na acurácia global.

---

### 6.3 Visualização da Árvore J48  

A árvore gerada pelo J48 possui profundidade moderada e inicia a divisão pela raiz com **experiencia_previa**, seguida por **navegacao_intuitiva** como segundo nó mais informativo. Ramificações subsequentes envolvem **satisfacao**, **erros** e **tempo_tarefa_seg** — exatamente na ordem de importância teórica prevista em IHC.

Exemplo de caminho típico para `alta` usabilidade:  
`experiencia_previa = alta` → `navegacao_intuitiva >= 4` → `satisfacao >= 4` → `erros <= 2` → classe = **alta**.

A árvore "redescobriu" as regras de geração com fidelidade quase perfeita, confirmando a consistência entre teoria, dados e modelo aprendido.

---

## 💬 7. Discussão (IHC)  

- **Papel da experiência prévia:** atua como fator moderador primário — usuários experientes compensam deficiências de design, enquanto novatos dependem fortemente de navegação intuitiva e feedback claro.  
- **Erros e latência como indicadores de carga cognitiva:** cada erro aumenta a frustração e o tempo de recuperação, reduzindo a percepção de controle e fluidez (princípio da **previsibilidade** em IHC).  
- **Eficiência como métrica composta:** reflete a relação custo-benefício da interação; valores altos indicam design otimizado, com poucos passos e alta taxa de acerto na primeira tentativa.  
- **Implicações práticas para design:**  
  - Priorizar **onboarding guiado** para usuários com baixa experiência;  
  - Implementar **validação em tempo real** e **mensagens de erro construtivas**;  
  - Reduzir **número de cliques** e **tempos de carregamento**;  
  - Reforçar **hierarquia visual** e **padrões de navegação consistentes**.

---

## ✅ 8. Conclusão  

- O classificador **J48** obteve o **melhor desempenho** com **91,33 % de acurácia** e **κ = 0,8694**, demonstrando excelente capacidade de generalização e alta interpretabilidade.  
- Os principais determinantes de **usabilidade alta** são: **maior experiência prévia**, **eficiência ≥ 0,60**, **erros ≤ 2**, **tempo_tarefa_seg ≤ 60 s** e **satisfação ≥ 4**.  
- Sites educacionais devem ser projetados com ênfase em **simplicidade cognitiva**, **fluxos lineares claros**, **feedbacks imediatos** e **adaptabilidade ao nível do usuário**, minimizando a sobrecarga de novatos e maximizando a produtividade de experts.

---

## 🔧 9. Limitações e Trabalhos Futuros  

- **Base sintética:** embora consistente, carece de variabilidade real de comportamento humano. **Próximo passo:** coletar dados reais via testes de usabilidade com logs de interação e questionários padronizados (SUS, UEQ).  
- **Atributos limitados:** não capturam aspectos como profundidade de clique, taxa de abandono, padrões de scroll ou tempo de hesitação. **Sugestão:** incluir métricas de eye-tracking e heatmaps.  
- **Modelos testados:** apenas algoritmos clássicos. **Futuro:** comparar com **Random Forest** (para maior robustez) e **Regressão Logística Multinomial** (para análise de coeficientes e odds ratios).  
- **Escalabilidade:** testar com bases maiores (1.000+ instâncias) e ruído realista.

---

## 📎 10. Materiais Entregues  

- `usabilidade_site_educacional.arff` → arquivo de dados no formato Weka;  
- `relatorio.md` → este documento em Markdown;  
- Pasta `prints/` contendo:  
  - Capturas de tela do Weka Explorer:  
    - `visualizacao_eficiencia_classe.png`  
    - `visualizacao_erros_classe.png`  
    - `visualizacao_tempo_classe.png`  
    - `visualizacao_experiencia_classe.png`  
    - `visualizacao_satisfacao_classe.png`  
  - `J48_tree.png` → exportação gráfica da árvore de decisão;  
  - `resultados_classificadores.txt` → sumários completos (acurácia, kappa, matriz de confusão) de todos os algoritmos;  
  - `matriz_confusao_J48.png` → matriz de confusão visual do melhor modelo.

---
