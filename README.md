# DREU 2026 Research Log

**Student:** Shaashvat Shetty  
**Mentor:** Prof. Bahar Asgari  
**Institution:** University of Maryland, College Park  
**Project Title:** Profiling Unstructured KV Cache Sparsity in Multimodal LLMs  

## Project Description

My DREU project focuses on studying unstructured sparsity in the key-value cache of multimodal large language models. The goal is to understand how visual and text tokens contribute differently to KV cache usage during inference, and whether visual tokens can tolerate more aggressive pruning than text tokens without significantly reducing model accuracy.

During the first stage of the project, I focused on understanding the research problem, reading related papers, and completing an initial project proposal in Overleaf. The next stages of the project involve studying KV cache behavior in open-source vision-language models, comparing sparsity patterns between visual and text tokens, and evaluating modality-aware KV cache pruning strategies on multimodal benchmarks.

---

# Weekly Entries

## Week 1

### Goals
Understand the overall motivation of the project and build background knowledge on multimodal large language models, KV cache memory usage, and sparsity. Begin reading related work and identifying the main research questions for the project.

### Approach and Implementation
I spent this week getting familiar with the project direction and reading papers related to KV cache compression, multimodal inference, and sparsity in transformer models. I focused on understanding what the KV cache is, why it becomes a memory bottleneck during inference, and how prior work attempts to reduce KV cache size while preserving model performance. I also began organizing notes from the papers and connecting them to the project’s main goal.

### Results
By the end of the week, I had a better understanding of the project’s motivation and the key technical concepts involved, including KV cache structure, visual and text tokens, sparsity, and pruning. I also identified several related papers that are important for the project, including work on KV cache compression and multimodal model efficiency.

---

## Week 2

### Goals
Continue reading and summarizing related papers, clarify the project scope, and complete the initial project proposal in Overleaf.

### Approach and Implementation
I continued reviewing papers related to KV cache sparsity, sparse attention, and efficient inference for vision-language models. I focused on understanding how different methods reduce KV cache memory usage and how these ideas may apply differently to visual tokens compared with text tokens. I also worked on finalizing the Overleaf project proposal by organizing the background, motivation, related work, proposed solution, experimental setup, evaluation plan, and project milestones.

### Results
By the end of the week, I completed the initial Overleaf project proposal. The proposal helped define the project direction around profiling KV cache behavior in multimodal models and exploring whether modality-aware pruning can reduce memory usage while maintaining accuracy. The first two weeks were mainly focused on building background knowledge, reading relevant papers, clarifying the research plan, and producing the written project proposal.
https://www.overleaf.com/read/kxqpnrwrjgtc#fa325e
