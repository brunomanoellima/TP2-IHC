# 🧠 Classificação da Usabilidade de um Site Educacional  
**Trabalho Prático 2 — Interação Humano-Computador e Machine Learning (Weka)**  

---

## 🧩 1. Definição do Problema  

O projeto aplica **classificação supervisionada** para prever o **nível de usabilidade** (`baixa`, `media`, `alta`) de um site educacional, com base em métricas de interação e satisfação do usuário.  

**Atributos preditores**  
- `tempo_tarefa_seg` – tempo médio (s) para concluir uma tarefa;  
- `erros` – número de erros cometidos;  
- `satisfacao` – avaliação subjetiva (1 a 5);  
- `experiencia_previa` – {baixa, media, alta};  
- `navegacao_intuitiva` – grau (1 a 5);  
- `eficiencia` – razão desempenho/tempo.  

**Classe-alvo:** `nivel_usabilidade` {baixa, media, alta}.  

**Hipótese IHC.** Usuários mais experientes, eficientes e satisfeitos → maior usabilidade; tempos longos e erros frequentes → usabilidade baixa.  

---

## ⚙️ 2. Regras de Geração da Classe-Alvo  

- **Alta:** poucos erros, tempo curto, alta satisfação e eficiência ≥ 0,60;  
- **Média:** condições parcialmente atendidas;  
- **Baixa:** muitos erros, tempo longo, baixa satisfação.  

Essas regras simulam o julgamento de um especialista em IHC e foram usadas para gerar as rotulações da base sintética.  

---

## 🧮 3. Base de Dados  

- **Arquivo:** `usabilidade_site_educacional.arff`  
- **Instâncias:** 300  
- **Atributos:** 6 preditores + 1 classe  
- **Geração:** via LLM (ChatGPT) a partir das regras acima  
- **Formato:** `.arff` (Weka-compatível)  

---

## 🔎 4. Exploração Visual no Weka  

As visualizações mostraram padrões nítidos:  

- **Experiência prévia × Usabilidade:** usuários com alta experiência têm predominância de usabilidade alta.  
- **Erros × Usabilidade:** quanto maior o número de erros, menor o nível de usabilidade.  
- **Eficiência × Usabilidade:** valores superiores de eficiência associam-se à classe alta.  
- **Tempo × Usabilidade:** tempos longos correspondem a baixa usabilidade.  
- **Satisfação × Usabilidade:** valores altos de satisfação correlacionam-se com maior usabilidade.  

Os gráficos confirmam a consistência da base sintética com as regras de origem.  

---

## 🧪 5. Procedimentos de Classificação  

Classificadores aplicados:  
- **J48 (Árvore de Decisão)**  
- **IBk (k-NN)**  
- **Naive Bayes**  
- **OneR** e **ZeroR** (baselines)  

Avaliação por **validação cruzada estratificada (10-fold)** para garantir robustez dos resultados.  

---

## 📊 6. Resultados  

### 6.1 Tabela 1 — Comparação Geral do Desempenho dos Algoritmos  

| **Algoritmo** | **Acurácia (%)** | **Kappa** | **Interpretação Geral** |
|:---------------|:----------------:|:----------:|:------------------------|
| **ZeroR** | 36,67 % | 0,0 | Modelo-base usado apenas como referência mínima. |
| **OneR** | 84,00 % | 0,7611 | Bom desempenho; regras simples já capturam grande parte dos padrões. |
| **Naive Bayes** | 87,33 % | 0,8103 | Modelo probabilístico eficiente com performance consistente. |
| **J48 (Árvore de Decisão)** | **91,33 %** | **0,8694** | **Melhor desempenho**, com alto poder de generalização e baixa taxa de erro. |
| **IBk (KNN)** | 88,33 % | 0,8242 | Bom desempenho, porém ligeiramente inferior ao J48 e mais sensível a ruído. |

**Análise:** O **J48** foi o melhor classificador, atingindo 91,33 % de acurácia e κ = 0,8694. Além da alta performance, sua estrutura em árvore permite visualizar as regras de decisão de modo interpretável, reproduzindo as relações definidas na geração da base (eficiência ↑, erros ↓, tempo ↓).  

---

### 6.2 Matriz de Confusão do Melhor Modelo (J48)  

a b c ← classificado como
108 2 0 | a = baixa
8 77 6 | b = media
0 10 89 | c = alta


**Interpretação.** A classe *média* foi a mais confundida com as adjacentes, mas a taxa de acertos geral permanece alta e bem equilibrada.  

---

### 6.3 Visualização da Árvore J48  

A raiz dividiu-se em **experiencia_previa** e **navegacao_intuitiva**, seguida de **satisfacao**, **erros** e **tempo_tarefa_seg** — exatamente as variáveis teóricas de usabilidade. O modelo “redescobriu” as regras criadas, validando a coerência entre teoria e dados.  

---

## 💬 7. Discussão (IHC)  

- **Determinantes de usabilidade.** A experiência prévia e a navegação intuitiva são fatores decisivos para minimizar erros e reduzir tempo de tarefa, aumentando a eficiência global.  
- **Erros e latência.** Mais erros e demoras comprometem a percepção de controle e fluidez, reduzindo a satisfação geral.  
- **Interpretação prática.** As regras extraídas da árvore são diretamente acionáveis por designers: melhorar feedback de erro, diminuir passos desnecessários, reforçar elementos visuais de navegação.  

---

## ✅ 8. Conclusão  

- O **J48** apresentou **melhor desempenho (91,33 %, κ = 0,8694)**, confirmando sua capacidade de generalização e coerência com as regras da base.  
- A **usabilidade alta** está associada a **maior experiência prévia**, **eficiência elevada**, **menor número de erros** e **tempo curto** de tarefa.  
- Interfaces educacionais devem priorizar **simplicidade**, **clareza de fluxo** e **feedbacks imediatos**, reduzindo a sobrecarga cognitiva de usuários inexperientes.  

---

## 🔧 9. Limitações e Trabalhos Futuros  

- **Base sintética:** validar em dados reais (coletas de logs e questionários SUS/UEQ).  
- **Novos atributos:** profundidade de clique, taxa de abandono, comportamento de scroll.  
- **Modelos:** testar Random Forest e Regressão Logística para comparar robustez e explicabilidade.  

---

## 📎 10. Materiais Entregues  

- `usabilidade_site_educacional.arff`  
- `relatorio.md` (este documento)  
- Pasta `prints/` contendo:  
  - Capturas de visualização do Weka (`eficiencia × classe`, `erros × classe`, etc.);  
  - Árvore J48 exportada (`J48_tree.png`);  
  - Sumários e matrizes de confusão de cada classificador.  

---

*(Relatório conforme estrutura oficial do TP2 — IHC e ML.)*


