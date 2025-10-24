# 🧠 Classificação da Usabilidade de um Site Educacional

## 🧩 1. Definição do Problema  
O projeto tem como objetivo aplicar **classificação supervisionada** para prever o **nível de usabilidade de um site educacional** (`baixa`, `média` ou `alta`).  
A base foi construída com atributos relacionados à experiência e desempenho do usuário:

- **tempo_tarefa_seg** → tempo médio para concluir tarefas;  
- **erros** → número de erros cometidos;  
- **satisfacao** → grau de satisfação (1–5);  
- **experiencia_previa** → familiaridade com plataformas digitais (`baixa`, `média`, `alta`);  
- **navegacao_intuitiva** → facilidade de navegação (1–5);  
- **eficiencia** → relação entre desempenho e tempo gasto.  

A **classe-alvo** é `nivel_usabilidade`, indicando a qualidade geral da experiência.

---

## ⚙️ 2. Regras para Gerar a Classe-Alvo  
A classe foi gerada com base em **regras lógicas** aplicadas aos atributos:

- **Alta:** se o usuário tiver poucos erros, tempo curto, alta satisfação, boa navegação e eficiência ≥ 0.60;  
- **Média:** se atender parcialmente às condições;  
- **Baixa:** se apresentar muitos erros, tempo longo e baixa satisfação.  

Essas regras simulam a avaliação real de usabilidade feita por especialistas em IHC, permitindo que o modelo aprenda padrões realistas de interação.

---

## 🧮 3. Descrição da Base Sintética  
A base contém **300 instâncias** (usuários simulados) e **6 atributos preditivos + 1 classe-alvo**.  
Os dados foram gerados de forma **sintética** com auxílio de um LLM (ChatGPT), em formato `.arff` compatível com o **Weka**, sob o nome:

