# Análise do Impacto da IA nos Alunos — Predição de Risco de Burnout

Projeto Final da disciplina de Aprendizado de Máquina. O objetivo é treinar um modelo de **classificação** capaz de predizer o **nível de risco de burnout** (baixo, médio ou alto) de estudantes universitários com base em hábitos de uso de ferramentas de IA generativa, desempenho acadêmico e outros fatores comportamentais.

## Integrantes

- Gabriel Angel dos Santos Sousa
- Jorge Batista Santos Júnior

## Fonte dos Dados

Dataset **AI Impact on Students** disponível no Kaggle:
[https://www.kaggle.com/datasets/laveshjadon/ai-impact-on-students/data](https://www.kaggle.com/datasets/laveshjadon/ai-impact-on-students/data)

O dataset é carregado diretamente no notebook via `kagglehub`, sem necessidade de upload manual de arquivos.

## Tipo da Tarefa

**Classificação** — o atributo-alvo `Burnout_Risk_Level` representa categorias (`High`, `Medium`, `Low`).

- **Atributo-alvo:** `Burnout_Risk_Level` (nível de risco de burnout do aluno)
- **Atributos preditivos:** `Major_Category`, `Year_of_Study`, `Pre_Semester_GPA`, `Weekly_GenAI_Hours`, `Primary_Use_Case`, `Prompt_Engineering_Skill`, `Tool_Diversity`, `Paid_Subscription`, `Traditional_Study_Hours`, `Perceived_AI_Dependency`, `Institutional_Policy`, `Anxiety_Level_During_Exams`
- **Atributos descartados por data leakage:** `Post_Semester_GPA` e `Skill_Retention_Score` (medidos após o período avaliado, não podem ser usados como preditores)
- **Atributo descartado por irrelevância:** `Student_ID` (identificador único, sem valor preditivo)

## Organização dos Arquivos

```
.
├── README.md
├── notebook 
|   ├── ai_student_impact_classification.ipynb   
├── .gitignore 
├── LICENSE
├── requirements.txt                 

```

## Como Executar no Google Colab

1. Acesse o repositório no GitHub e abra o arquivo `ai_student_impact_classification.ipynb` diretamente pelo Colab (Abrir com > Google Colaboratory), ou importe-o pelo menu *Arquivo > Abrir notebook > GitHub* dentro do próprio Colab.
2. Execute as células em ordem, de cima para baixo.
3. A primeira célula de código baixa o dataset automaticamente do Kaggle via `kagglehub` — não é necessário nenhum arquivo local.
4. Bibliotecas utilizadas: `pandas`, `matplotlib`, `scikit-learn`, `optuna` e `kagglehub`. Caso alguma não esteja disponível no ambiente, instale com `!pip install <biblioteca>` em uma célula do Colab.

## Metodologia

### Compreensão e Pré-processamento dos Dados

- 50.000 registros e 16 atributos, sem valores ausentes ou duplicados.
- Variáveis categóricas (`Major_Category`, `Year_of_Study`, `Primary_Use_Case`, `Prompt_Engineering_Skill`, `Institutional_Policy`) foram transformadas com **One-Hot Encoding**.
- Variáveis numéricas foram mantidas via `passthrough` no `ColumnTransformer`.
- Atributo-alvo com desbalanceamento moderado: Medium (42,3%), Low (32,7%) e High (25,0%).
- Separação treino/teste em proporção 80/20, com estratificação (`stratify=y`) para preservar a proporção das classes.

### Modelos Utilizados

| Modelo | Descrição |
|---|---|
| Árvore de Decisão (baseline) | `DecisionTreeClassifier`, profundidade máxima 5 |
| Random Forest | `RandomForestClassifier`, com posterior otimização de hiperparâmetros via **Optuna** |
| SGDClassifier | Regressão logística treinada por gradiente descendente estocástico |

## Principais Resultados

| Modelo | Acurácia (teste) |
|---|---|
| Árvore de Decisão | ~50% |
| SGDClassifier | próxima aos demais modelos |
| Random Forest (otimizado) | **~53%** (melhor modelo) |

O **Random Forest** apresentou o melhor desempenho entre os modelos avaliados e foi escolhido como modelo final, com hiperparâmetros ajustados via busca com Optuna (`n_estimators=313`, `max_depth=11`, `min_samples_split=2`, `min_samples_leaf=2`, `max_features="sqrt"`).

**Limitações:** o ganho de desempenho com a otimização de hiperparâmetros foi pequeno, limitado também pelo tempo de processamento disponível. Além disso, a distribuição muito equilibrada do atributo-alvo e a ausência de inconsistências ou valores ausentes sugerem que o dataset pode ser sintético, o que reduz o teto de acurácia alcançável e a representatividade de um cenário real.

## Divisão das Contribuições

> Angel: Desenvolvimento dos códigos relacionados ao pandas e tabelas, aos gráficos, treinamento e modelagem.
---
> Jorge: Análise das tabelas, gráficos, dos atributos e decisões sobre o desenvolvimento e escolha do modelo.

## Vídeo de Apresentação

> _[Inserir aqui o link do vídeo explicativo do grupo]_

## Uso de Ferramentas de Inteligência Artificial

- **Ferramenta utilizada:** Claude (Anthropic)
- **Finalidade:** apoio na escrita de código e na interpretação de resultados
- **Partes do trabalho em que foi utilizada:**
  - Modelagem com `scikit-learn`, na instância dos diferentes tipos de modelos (Árvore de Decisão, Random Forest e SGDClassifier)
  - Construção dos gráficos com `matplotlib` (histogramas, boxplots e gráfico de distribuição do atributo-alvo)
  - Análise do desempenho dos modelos (leitura de métricas, matriz de confusão e comparação entre os resultados)
- **Forma de verificação do conteúdo/código produzido:** o código gerado foi executado e revisado pelo grupo, conferindo se as saídas (gráficos, métricas e previsões) faziam sentido com os dados reais do dataset antes de serem incorporados ao notebook.