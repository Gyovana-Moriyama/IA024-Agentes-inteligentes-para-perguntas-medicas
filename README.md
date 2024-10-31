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
 
## Resultados

### Resultados esperados

ReAct > CoT: ReAct com desempenho superior ao CoT, uma vez que ele tem acesso ao conhecimento necessário para responder às perguntas

### Resultados preliminares - entrega 2

#### Acurácia

![gráfico de acurácia](entrega2/acuracia.png)

Gráfico comparando a acurácia da resposta aleatória, pergunta direta, CoT e os dois benchmarks.

#### Exemplos de respostas CoT

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
