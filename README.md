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

---

## Week 3

### Goals
Begin moving from background reading into implementation. Set up the first KV-cache capture workflow for Qwen2.5-VL and create a small image benchmark that could be used for initial testing.

### Approach and Implementation
I started by preparing a small set of images and prompts for testing the cache capture pipeline. I used COCO images as an initial source because they are easy to download, open-source, and contain different types of scenes such as animals, people, vehicles, indoor objects, and street scenes. I also worked on understanding how Qwen2.5-VL represents an image as visual tokens and how those visual tokens appear inside the language decoder input.

I set up and used a KV-cache capture script for Qwen2.5-VL-3B-Instruct. The script takes an image and a short text prompt, runs the model during the prefill stage, and saves the Key and Value caches along with metadata. The metadata records important information such as the number of layers, attention heads, sequence length, visual-token positions, and text-token positions. I also learned more about the model architecture, including the role of the vision encoder, visual tokens, text tokens, transformer layers, attention heads, and cache channels.

### Results
By the end of the week, I had a working first version of the cache capture pipeline. I was able to run the model on a small image set, save `.pt` cache files, and verify that the outputs contained separate visual-token and text-token positions. This gave me the initial data needed for the next stage of the project, which was analyzing whether visual-token KV caches show outlier-channel structure.

---

## Week 4

### Goals
Build the Milestone 2 analysis pipeline for studying saved KV caches. The main goal was to check whether visual-token Key and Value caches concentrate magnitude in a small number of channels, and whether that pattern changes across layers and attention heads.

### Approach and Implementation
I wrote and updated a profiling script that loads saved KV-cache files and computes statistics separately for visual-token and text-token positions. The analysis focused on Key and Value caches, layer by layer and attention head by attention head. I added metrics for top-1, top-5, and top-10 channel concentration, which measure how much of the total cache magnitude is carried by the largest channels. I also generated channel-rank curves, where channels are sorted by magnitude and plotted by cumulative contribution.

I created several plots to make the results easier to interpret, including magnitude distributions, layer-wise concentration plots, visual-token channel-rank curves, and layer/head heatmaps. I also updated the plotting code so heatmaps use integer attention-head labels instead of misleading decimal axis values. During this stage, I also spent time learning the meaning of the main architecture terms used in the analysis, including layers, attention heads, channels, visual-token positions, text-token positions, prefill, Key cache, Value cache, and outlier-channel concentration.

### Results
The first profiling results showed that visual-token Key caches have stronger outlier-channel behavior than visual-token Value caches. In the Key cache, a small number of channels account for a noticeably larger share of the total magnitude, especially in certain layer/head locations. In contrast, the Value cache is more evenly distributed across channels, and its channel-rank curve is closer to a straight line. This suggested that the Key cache may be more structured and more concentrated than the Value cache for visual tokens.

---

## Week 5

### Goals
Scale the analysis beyond a small test set and check whether the same visual-token cache patterns appear across larger and different image datasets.

### Approach and Implementation
I expanded the experiments to larger datasets. I first ran the workflow on 500 COCO val2017 images. I used the same short prompt for every image so the inputs stayed consistent, and I saved the exact list of images I used so I could rerun the same experiment later.

After COCO, I repeated the same process with ImageNet21k images on Nexus. I used GPU nodes to capture the Qwen2.5-VL KV caches and then ran the same profiling script on the saved caches. Throughout this step, I kept the visual-token measurements separate from the small text prompt measurements, since the main research question is about visual-token cache behavior. I also worked through practical Nexus issues, including choosing the right GPU nodes, handling storage limits, using scratch space, and restarting runs without losing progress.

### Results
The larger COCO500 and ImageNet21k-500 experiments showed the same main pattern as the smaller tests. In both datasets, visual-token Key caches showed stronger outlier-channel structure than visual-token Value caches. The visual Key channel-rank curve rose much faster than the visual Value curve, meaning that fewer Key channels carried a larger fraction of the total magnitude. The visual Value cache was much closer to evenly distributed across channels.

The results so far suggest that visual-token Key caches may have a consistent outlier-channel pattern across datasets, while visual-token Value caches appear less concentrated. This supports the idea that Key and Value caches may need to be treated differently when studying KV-cache sparsity or pruning for multimodal models.

---

## Week 6 (Still in Progress)

### Goals
Slow down and focus on understanding the model architecture, the datasets, and the meaning of the current results before choosing the next experiment direction.

### Approach and Implementation
This week has been focused more on studying and interpretation than running new experiments. I spent time reviewing how Qwen2.5-VL handles image and text inputs, how images become visual tokens, how the transformer decoder forms Key and Value caches during the prefill stage, and how layers, attention heads, token positions, and channels fit into the saved cache tensors.

I also spent time comparing the datasets used so far. COCO contains more scene-style images, while ImageNet21k is more focused on object/category images. Since the same Key-versus-Value pattern appears in both datasets, I am trying to understand why that makes the result more convincing and what follow-up experiments would best test the idea.

### Results
The main takeaway I studied this week is that visual-token Key caches consistently show stronger outlier-channel structure than visual-token Value caches. This means that, for the Key cache, a small number of channels often carry a larger share of the total magnitude, while the Value cache is more evenly spread across channels.

I am now thinking about how this connects to the Mustafar paper and related work on outlier channels in KV caches. A possible next direction is to compare whether the outlier-channel behavior discussed for language-model KV caches also appears for visual tokens in multimodal models, and whether Key and Value caches should be analyzed or compressed differently.

---

## Current Status and Next Steps

### What I Have Done So Far
So far, I have completed the initial proposal, built a Qwen2.5-VL KV-cache capture workflow, created profiling tools for visual-token and text-token cache analysis, and tested the pipeline on multiple datasets. I have run experiments on small COCO examples, TextVQA-50, COCO500, and ImageNet21k-500. Across the larger COCO and ImageNet21k experiments, the main finding is that visual-token Key caches show stronger outlier-channel behavior than visual-token Value caches. I am currently spending Week 6 interpreting these results and connecting them to the Mustafar paper.

### Next Goals
The next step is to turn the current observations into a clearer written summary, compare them more directly with the Mustafar paper, and decide which follow-up experiment would best test whether the Key-cache concentration can be used for cache pruning. After that, the project can move toward comparing how pruning Key and Value channels affects model output quality.
