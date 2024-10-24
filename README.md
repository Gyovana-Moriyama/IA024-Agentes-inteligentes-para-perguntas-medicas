# Sistemas inteligentes para respostas a perguntas médicas

## Pergunta

LLMs que utilizam ReAct são mais precisas do que modelos com apenas Chain-of-Thought (CoT) ao responder perguntas do domínio médico?

## Objetivo

Avaliar o desempenho de dois sistemas, um que gera raciocínios passo a passo, e outro que combina raciocínio e ações interativas, na tarefa de question answering (QA) em perguntas do exame USMLE a fim de comparar a acurácia de ambos.

## Metodologia

Modelo pré-treinado: GPT-4

Cada sistema será avaliado nas questões do exame USMLE (US Medical Licensing Examination). Esse exame é composto por três etapas de questôes de múltipla escolha.

CoT e ReAct serão utilizados para geração de passos de raciocínio para responder às perguntas; _Retrieval-augmented generation_ (RAG) como ferramenta para enriquecimento dos contextos.

## Datasets

### MedQA

Utilizado para recuperação de contextos. É composto por 18 arquivos de texto, em inglês, separados por temas da prova USMLE.

### MedQA-USMLE-4-options

Utilizado para avaliação dos sistemas. O dataset é composto por 12,723 perguntas de múltipla escolha. Além da questão, são fornecidas quatro alternativas, seguida da resposta.

## Métrica de avaliação

A métrica a ser utilizada é a acurácia, a qual corresponde à porcentagem de questões corretamente respondidas.

## Resultados

### Resultados esperados

ReAct > CoT: ReAct com desempenho superior ao CoT, uma vez que ele tem acesso ao conhecimento necessário para responder às perguntas

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
