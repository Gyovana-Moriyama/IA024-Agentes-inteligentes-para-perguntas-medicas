# Sistemas inteligentes para respostas a perguntas médicas

## Hipótese

LLMs com acesso a informações extras são mais precisas do que modelos que dependem apenas de sua memória ao responder perguntas do domínio médico.

## Objetivo

Avaliar o desempenho de dois sistemas, um que gera raciocínios passo a passo, e outro que combina raciocínio e ações interativas, na tarefa de question answering (QA) em perguntas do exame USMLE a fim de comparar a acurácia de ambos.

A métrica de avaliação a ser utilizada é a acurácia, a qual corresponde à porcentagem de questões corretamente respondidas.

## Datasets

### MedQA

Utilizado para recuperação de contextos. É composto por 18 arquivos de texto, em inglês, separados por temas da prova USMLE.

### MedQA-USMLE-4-options

Utilizado para avaliação dos sistemas. O dataset é composto por 12,723 perguntas de múltipla escolha. Além da questão, são fornecidas quatro alternativas, seguida da resposta.

## Metodologia

Modelo pré-treinado: GPT-4o-mini
Hardware: Google Colab

Cada sistema será avaliado nas questões do exame USMLE (US Medical Licensing Examination). Esse exame é composto por três etapas de questôes de múltipla escolha.

CoT: Uso de prompts para instruir o modelo a gerar passos de raciocínio para responder às perguntas.

ReAct: CoT + _Retrival-augmented generation_ (RAG). Instrução ao modelo para repetir ciclos de raciocínio, ação e observação para tomada de decisão.

### Exploração dos datasets

**MedQA-USMLE-4-options:**

* Utilizado dataset disponível no HuggingFace.
* Dataset de teste composto por 1273 perguntas, sendo 679 da step 1 e 594 das steps 2 e 3.
* Perguntas + opções possuem tamanho médio de 888.60 caracteres.

**MedQA:** 

* Utilizado dataset disponível no drive do artigo.
* Dataset composto por 18 livros texto.
* Tamanho médio de 4,951,794.67 caracteres e 11,851.67 parágrafos por livro texto. 

### Implementação

GPT-4o-mini com temperatura de 0.5, no Google Colab

**Baselines:**

Implementação de duas baselines para comparação:

1. Respostas aleatórias;
2. Pergunta direta, sem aplicação de técnicas de engenharia de prompt.

Prompt:

```
question: {question}
options: {options}
Among A through D, the answer is: <answer>

```

**Chain-of-thought:**

Raciocínio passo-a-passo com chain-of-thought utilizando um prompt adaptado de [10].

Configuração zero-shot.

Prompt:

```
Answer the question below.

Question: {question}
Options: {options}

Answer: Let's think step-by-step...
```

**ReAct**

O ReAct é um método que combina raciocínio (CoT) com recuperação de informações (RAG). O modelo realiza ciclos de raciocínio e consulta ao banco de dados (MedQA) antes de tomar uma decisão final, o que permite respostas mais contextualizadas.

A parte de _reasoning_, ou seja, a parte do racicínio é baseada no CoT. Já a parte de _acting_, é a busca na base de livros-textos com RAG. 

O RAG implementado utiliza o _RecursiveCharacterTextSplitter_ do _Langchain_ como separador de texto e Similarity Search do FAISS como recuperador de informação.

Para a escolha do recuperador de informação de da melhor configuração do separador, foram feitas comparações utilizando 50 perguntas aleaórias da amostra do dataset de treino. Foi comparado combinações de _chunk_size_ e _overlap_size_ do separador de texto com dois recuperadores, o Similarity Search do FAISS e o BM25 Okapi. Para avaliar essas combinações, foi utlizada a acurácia nas questões e o _context relevance_ com RAGAS nos contextos recuperados.

Prompt ReAct:

```
Solve the question answering task alternating between Question, Thought, Action, Input and Observation steps.
You only have access to the following tools: {tools}

Use the following format:
Question: the input question you must answer
Thought: you should always think about what to do. Break the problem down into subproblems and smaller steps and decide which action to take.
Action: the action to take, must be one of [{tool_names}]. If no action is needed, return your Final answer instead.
Action Input: the input to the action
Observation: the output of the action.
... (this Thought/Action/Action Input/Observation can repeat any number of times)
Final Answer: A/B/C/D

Begin!
Question: {question}
Options: {options}
Thought: {agent_scratchpad}

```

Prompt para RAGAS, _Context Relevance_

```
Please extract relevant sentences from the provided context that can potentially help answer the following question.
If no relevant sentences are found, or if you believe the question cannot be answered from the given context, return the phrase "Insufficient Information".

While extracting candidate sentences you’re not allowed to make any changes to sentences from given context.

Here is the context: {context}
And here is the question: {question}

```
 
## Resultados

### Resultados esperados

ReAct > CoT: ReAct com desempenho superior ao CoT, uma vez que ele tem acesso ao conhecimento necessário para responder às perguntas

### Resultados preliminares - entrega 2

#### Acurácia

![gráfico de acurácia](entrega2/acuracia.png)

Gráfico comparando a acurácia da resposta aleatória, pergunta direta, CoT e os dois benchmarks.

#### Exemplos de respostas CoT

```
Q: A 40-year-old man is referred to an optometrist. He complains of mild vision impairment over the last 6 months. His vision has continued to slowly deteriorate and his condition is now affecting his night driving. Past medical history is significant for well-controlled schizophrenia. He takes a low-potency typical antipsychotics and a multivitamin every day. He has been compliant with his medication and has regular follow-up visits. What is the best first step in the management of this patient’s symptoms?

A) Decrease medication dosage

B) Reassurance

C) Ocular examination under anesthesia

**D) Slit-lamp examination**

A:
1. The patient is experiencing mild vision impairment that has been progressively worsening, particularly affecting his night driving. This suggests a potential ocular issue that needs to be evaluated.
2. Given the patient's age and the nature of his symptoms, it is important to conduct a thorough ocular examination to identify any underlying conditions, such as cataracts or other retinal issues.
3. A slit-lamp examination is a non-invasive procedure that allows for detailed visualization of the anterior segment of the eye, which can help diagnose various ocular conditions.
4. Decreasing the medication dosage (Option A) might not address the underlying cause of the vision impairment and could worsen his psychiatric condition. 
5. Reassurance (Option B) is not sufficient without a proper evaluation of the cause of vision changes.
6. Ocular examination under anesthesia (Option C) is typically reserved for more complex cases or when a detailed examination cannot be performed awake, which is not indicated initially in this scenario.
7. **Therefore, the best first step is to conduct a slit-lamp examination (Option D) to assess the patient's ocular health.**

```
### Resultados preliminares - entrega 3

#### Acurácia por separador 

Acurácia em 50 amostras com agentes utilizando diferentes separadores de texto.

|                 | **300/30** | **500/50** | **600/60** | **900/90** |
|-----------------|------------|------------|------------|------------|
| **BM25**        | 0.74       | 0.68       | 0.70       | 0.70       |
| **Sim. Search** | 0.66       | 0.74       | 0.72       | 0.72       |


#### Relecância dos contextos

Relevância dos contextos recuperados por cada agente.

|                 | **300/30** | **500/50** | **600/60** | **900/90** |
|-----------------|------------|------------|------------|------------|
| **BM25**        | 0.38       | 0.30       | 0.47       | 0.30       |
| **Sim. Search** | 0.62       | 0.42       | 0.32       | 0.33       |

#### Comparação inicial das abordagens

Geração das respostas no dataset de teste e comparação com resultados obtidos no sistema sem acesso às bases de dados.

![gráfico de acurácia](entrega3/results/results.png)

#### Exemplo de respost ReAct

```

Q: A one-day-old male is evaluated in the hospital nursery for bilious vomiting. The patient has urinated multiple times since birth but has not yet passed meconium. He was born at 38 weeks gestation to a 36-year-old gravida 3 via vaginal delivery. The pregnancy was complicated by gestational diabetes. The patient’s mother received routine prenatal care and declined first trimester screening. She subsequently underwent a quadruple screen at 15 weeks gestation that demonstrated decreased maternal serum alpha-fetoprotein (AFP), increased beta-hCG, decreased unconjugated estriol, and increased inhibin A. Her last ultrasound prior to onset of labor demonstrated an amniotic fluid index (AFI) of 28 cm. The patient’s two older siblings are both healthy. The patient’s temperature is 97.8°F (36.6°C), blood pressure is 58/37 mmHg, pulse is 166/min, and respirations are 38/min. On physical exam, the patient is in moderate distress. His abdomen is firm and distended with hypoactive bowel sounds.
Which of the following is the most likely etiology of this fetus’s presentation?
Duodenal atresia
Meconium ileus
Necrotizing enterocolitis
Pyloric stenosis

A:

To determine the most likely etiology of the one-day-old male's presentation, I need to analyze the symptoms and the clinical context provided. The key points from the case are:

1. The patient is experiencing bilious vomiting and has not passed meconium.
2. The abdomen is firm and distended with hypoactive bowel sounds.
3. The mother had a quadruple screen that showed abnormal results, which could suggest a risk for certain congenital anomalies.

A:

Now, let's break down the options:

- A: Duodenal atresia - This condition often presents with bilious vomiting and is associated with a lack of meconium passage. It is also linked with Down syndrome, which could correlate with the abnormal quad screen results.  
- B: Meconium ileus - Typically associated with cystic fibrosis, it can cause bilious vomiting and delayed passage of meconium, but it is less common in newborns without a known history of cystic fibrosis.  
- C: Necrotizing enterocolitis - This condition usually occurs in premature infants and presents with abdominal distension and feeding intolerance, but it is less likely in a healthy term infant.  
- D: Pyloric stenosis - This condition usually presents with non-bilious vomiting and typically occurs in infants older than a few weeks.

Given the symptoms of bilious vomiting and the absence of meconium, along with the mother's abnormal screening results, duodenal atresia seems to be the most consistent with the clinical picture.

Action: search_docs  
Action Input: "duodenal atresia presentation in newborns" 

Duodenal atresia is found in approximately 1 in 10,000 births (Best, 2012; Dolk, 2010). It is characterized by the sonographic double-bubble sin, which represents distention of the stomach and the irst part of the duodenum (Fig. 10-38). This finding is usually not present before 22 to 24 weeks' gestation. Demonstrating continuity between the stomach and proximal duodenum confirms that the second "bubble" is the proximal duodenum. Approximately 30 percent of afected fetuses have an associated
in the stomach and duodenum) proximal to the site of the atresia. Treatment: Surgical repair.
I. DUODENAL ATRESIA
A. Congenital failure of duodenum to canalize; associated with Down syndrome
B. Clinical features 1.
2.
Distension of stomach and blind loop of duodenum ('double bubble' sign, Fig. 10.15) 3.
II. MECKEL DIVERTICULUM
A. Outpouching of all three layers of the bowel wall (true diverticulum, Fig. 10.16)
B. Arises due to failure of the vitelline duct to involute
C. 'Rule of 2s' 1.
Seen in 2% of the population (most common congenital anomaly of the GI tract) 2.
Available @ StudentConsult.com
Congenital partial or complete blockage of the intestine is a developmental defect that occurs in about 1 in 1500 live births. Atresia occurs in several forms (Fig. 129-3). One ormore segments of bowel may be missing completely, theremay be varying degrees of obstruction caused by webs orstenosis, or there may be obliteration of the lumen in cord-like bowel remnants. The end result is obstruction withThe information gathered from the observation confirms the link between duodenal atresia and the clinical presentation of the one-day-old male. The key points reiterate that duodenal atresia is associated with bilious vomiting and failure to pass meconium, which aligns with the patient's symptoms. Additionally, the mention of the "double-bubble sign" further supports the diagnosis, as it indicates a blockage at the duodenum.

Given the context of the mother's abnormal quad screen results, which can point towards congenital anomalies such as Down syndrome, and the clinical presentation of the infant, it strengthens the case for duodenal atresia as the most likely etiology.

Now I will finalize the answer.

Final Answer: A

```

#### Problemas e limitações

Agentes não chegam a uma conclusão dentro do limite de iterações em parte das perguntas.

Mesmo com acesso às informações, os contextos recuperados são de baixa relevância e, em muitos casos, não respondem à questão.


## Referências

[1] Wu, Chaoyi, et al. "PMC-LLaMA: toward building open-source language models for medicine." Journal of the American Medical Informatics Association (2024): ocae045.

[2] Wang, Yubo, Xueguang Ma, and Wenhu Chen. "Augmenting black-box llms with medical textbooks for clinical question answering." arXiv preprint arXiv:2309.02233 (2023).

[3] Jin, Di, et al. "What disease does this patient have? a large-scale open domain question answering dataset from medical exams." Applied Sciences 11.14 (2021): 6421.

[4] Kung, Tiffany H., et al. "Performance of ChatGPT on USMLE: potential for AI-assisted medical education using large language models." PLoS digital health 2.2 (2023): e0000198.

[5] Xiong, Guangzhi, et al. "Improving Retrieval-Augmented Generation in Medicine with Iterative Follow-up Questions." arXiv preprint arXiv:2408.00727 (2024).

[6] Jeong, Minbyul, et al. "Improving medical reasoning through retrieval and self-reflection with retrieval-augmented large language models." Bioinformatics 40.Supplement_1 (2024): i119-i129.

[7] Z. Hammane, F. -E. Ben-Bouazza and A. Fennan, "SelfRewardRAG: Enhancing Medical Reasoning with Retrieval-Augmented Generation and Self-Evaluation in Large Language Models," 2024 International Conference on Intelligent Systems and Computer Vision (ISCV), Fez, Morocco, 2024, pp. 1-8

[8] Saab, Khaled, et al. "Capabilities of gemini models in medicine." arXiv preprint arXiv:2404.18416 (2024).

[9] Nori, Harsha, et al. "Can generalist foundation models outcompete special-purpose tuning? case study in medicine." arXiv preprint arXiv:2311.16452 (2023).

[10] Liévin, Valentin, et al. "Can large language models reason about medical questions?." Patterns 5.3 (2024).

[11] https://www.usmle.org/scores-transcripts 

[12] https://paperswithcode.com/sota/question-answering-on-medqa-usmle

[13] https://huggingface.co/datasets/GBaker/MedQA-USMLE-4-options
