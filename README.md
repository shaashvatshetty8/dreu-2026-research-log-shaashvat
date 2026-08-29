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

## Week 6

### Goals
Slow down and focus on understanding the model architecture, the datasets, and the meaning of the current results before choosing the next experiment direction.

### Approach and Implementation
This week has been focused more on studying and interpretation than running new experiments. I spent time reviewing how Qwen2.5-VL handles image and text inputs, how images become visual tokens, how the transformer decoder forms Key and Value caches during the prefill stage, and how layers, attention heads, token positions, and channels fit into the saved cache tensors.

I also spent time comparing the datasets used so far. COCO contains more scene-style images, while ImageNet21k is more focused on object/category images. Since the same Key-versus-Value pattern appears in both datasets, I am trying to understand why that makes the result more convincing and what follow-up experiments would best test the idea.

### Results
The main takeaway I studied this week is that visual-token Key caches consistently show stronger outlier-channel structure than visual-token Value caches. This means that, for the Key cache, a small number of channels often carry a larger share of the total magnitude, while the Value cache is more evenly spread across channels.

I am now thinking about how this connects to the Mustafar paper and related work on outlier channels in KV caches. A possible next direction is to compare whether the outlier-channel behavior discussed for language-model KV caches also appears for visual tokens in multimodal models, and whether Key and Value caches should be analyzed or compressed differently.

---

## Week 7

### Goals
Turn the Week 6 reading on the Mustafar paper into an actual implementation, and test whether the channel-concentration pattern found in Weeks 4-5 can be used to prune the visual-token Key cache without hurting model output.

### Approach and Implementation
I implemented Mustafar's pruning rule directly rather than approximating it: I ported the actual pruning function from Mustafar's public repository so my implementation matches theirs exactly, and wrote a test that checks the two produce identical tensors. This is closest to Mustafar's `Kt_Mag` setting: Key cache, token-wise (each token's cache vector is pruned on its own), magnitude-based (keep the largest values, zero the rest).

I first ran this offline against the saved cache files from Weeks 4-5 to measure how much magnitude survives pruning at different sparsity levels. Then I moved to a live version that actually prunes the KV cache during real generation on 50 COCO images with ground-truth captions, comparing pruned captions against the dense (unpruned) baseline using several metrics: token overlap, ROUGE, BLEU, CIDEr, and KL divergence between the pruned and dense next-token probability distributions. I learned an important detail of Mustafar's design along the way: it keeps the prefill attention computation dense and only prunes the tensor that gets stored afterward, so the very first generated token is always identical between pruned and dense runs. I also added a second "structured" pruning mode (dropping whole channels instead of individual token entries) so I could directly test Mustafar's central claim that unstructured pruning beats structured pruning at the same sparsity level.

### Results
The offline results supported the Week 4-5 finding: even after zeroing a large fraction of the visual-token Key cache, most of the original magnitude was retained, consistent with a small number of channels dominating the cache. The live generation results were the more important payoff: pruning visual-token Keys by 30-70% left the generated captions and next-token distributions close to the dense baseline, and the structured-vs-unstructured comparison showed the same pattern Mustafar reports in their paper - unstructured (token-wise) pruning holds up noticeably better than structured (channel-wise) pruning at matched sparsity.

---

## Week 8

### Goals
Move past caption-similarity metrics, which only measure whether two pieces of text read alike, and evaluate visual-token pruning with metrics that have an objective right or wrong answer. Also scale the pruning evaluation up to a real sweep across sparsity levels and multiple benchmarks.

### Approach and Implementation
I built a full evaluation pipeline around three benchmarks that each stress a different kind of question: TextVQA (reading text embedded in natural images), DocVQA (dense document pages, which are almost entirely visual tokens), and MMMU (broader visual reasoning). Each has a fixed ground-truth answer, so scoring is right/wrong rather than a similarity judgment. For every example, I ran the dense baseline and every pruning condition back-to-back on the identical prompt, sweeping sparsity from 50% up to 90% and pruning only the cache entries belonging to visual tokens, leaving every text-token entry fully dense so that any change in accuracy can be attributed to the visual tokens specifically.

To make sure the pruning was actually doing something meaningful and not just getting lucky, I added two control conditions: a "random" control that zeros out the same number of cache entries per token but picks them randomly instead of by magnitude, and a "uniform" control that prunes text-token Keys too instead of only visual ones. I also caught and fixed a bug where different runs were capping image resolution differently, which made some of the DocVQA comparisons unfair, and re-ran the affected experiments once everything used the same visual-token count.

### Results
Accuracy from magnitude-based visual-token pruning stayed close to the dense baseline all the way out to 90% sparsity on both TextVQA and DocVQA. The random control, by contrast, collapsed almost immediately, losing large amounts of accuracy even at 50% sparsity. This was the clearest evidence yet that the benefit comes specifically from keeping the largest-magnitude cache entries, not simply from having a sparser cache. The uniform control (pruning text tokens as well) tracked the visual-only result closely through about 70% sparsity but then fell sharply between 80% and 90%, suggesting visual tokens tolerate aggressive pruning better than text tokens do, though the biggest danger zone turned out to be very high sparsity in general rather than text tokens specifically.

---

## Week 9

### Goals
Find the best combination of Key-cache and Value-cache sparsity, and turn the pruning from a simulated "zero out and measure" experiment into a real, physically smaller cache with a measured memory savings.

### Approach and Implementation
I ran a grid search over different Key-sparsity and Value-sparsity combinations to find the setting that saves the most memory while staying within a small accuracy budget. I also built the actual compressed storage format that Mustafar's paper describes, rather than just zeroing entries inside a full-size tensor: pruned cache tensors are packed into small tiles, each with a compact bitmap marking which entries survived plus an offset table, so the cache is genuinely smaller in memory rather than just sparse-looking. I wrote tests confirming this compressed format produces bit-for-bit identical outputs to the earlier "zero out the dense tensor" approach, then measured the real GPU memory used by the compressed cache during generation on both TextVQA and DocVQA, and compared it against what an analytical model predicted the savings should be.

### Results
The grid search identified a Key/Value sparsity combination that kept the accuracy drop on both benchmarks within a small margin while cutting cache memory substantially. The compressed cache format worked correctly, generating identical text to the uncompressed version, and the measured GPU memory savings came out very close to what the analytical model had predicted, confirming that the compression scheme behaves the way the math says it should and that the savings are real rather than theoretical.

---

## Week 10

### Goals
Test whether the compressed, pruned cache also makes generation faster, not just smaller, and begin identifying a stronger version of the pruning method for the next phase of the project.

### Approach and Implementation
I integrated Mustafar's own GPU kernel, which reads the compressed cache format directly without ever decompressing it back to a dense tensor, and verified it against the dense baseline: correctness tests on GPU passed, and running the full 500-example evaluation on both TextVQA and DocVQA produced zero mismatched predictions between the pruned/compressed path and the dense path. I then benchmarked its speed and found it is currently slower than a standard dense attention implementation at the batch size I tested, which matches what the Mustafar paper itself reports: their own kernel is also slower than dense at small batch sizes and only wins once many sequences are processed together.

With the implementation now correctness-verified and honestly benchmarked, I spent the rest of the week reading papers to figure out how to improve on straightforward magnitude-based pruning, which prunes every visual token to the same fixed sparsity regardless of how important that token actually is to the model's answer. I read LSH-E, which uses locality-sensitive hashing to quickly estimate, before attention is even computed, which cached tokens are least likely to matter for the current query, and evicts those rather than applying a fixed sparsity rate everywhere. I also read IVTP, which scores how important each visual token is using attention information from the model itself and prunes visual tokens in two stages, one based on the vision encoder and one guided by the actual text instruction, so that pruning aggressiveness adapts per token rather than being applied uniformly.

### Results
Both papers point toward the same idea I want to try next: instead of pruning every visual token's cache entries to the same sparsity level, the sparsity level itself could vary per token based on an importance score, similar to how LSH-E decides relevance before attention and how IVTP scores each visual token's importance to the instruction. The next step is to adapt the Mustafar-style pruning I already built and verified so that it applies a per-token, importance-weighted sparsity to visual patches instead of one fixed sparsity across the board, which should let genuinely unimportant patches be pruned harder while protecting the patches that matter most.

---

## Current Status and Next Steps

### What I Have Done So Far
Over the first ten weeks, I completed the initial project proposal, built a Qwen2.5-VL KV-cache capture and profiling workflow, and used it to show that visual-token Key caches concentrate magnitude in a small number of channels while visual-token Value caches do not. I then implemented Mustafar-style magnitude-based pruning of the visual-token Key cache, verified it against the reference implementation, and evaluated it with objective right/wrong metrics across TextVQA, DocVQA, and MMMU, finding that visual-token pruning holds up to very high sparsity (90%) while a random-selection control collapses almost immediately, showing that magnitude-based selection is what matters. I found a strong Key/Value sparsity combination through a grid search, built and verified a real compressed cache format with measured (not just modeled) GPU memory savings, and integrated Mustafar's own GPU kernel, confirming it produces correct output but is currently slower than dense attention at low batch size, consistent with the original paper. This past week I read LSH-E and IVTP to look into importance-based, rather than uniform, token pruning.

### Next Goals
The next step is to extend the pruning method so that sparsity is chosen per visual token based on an importance score, rather than applying the same fixed sparsity to every visual patch, drawing on the ideas from LSH-E (relevance estimated before attention) and IVTP (instruction-aware importance scoring). Alongside that, I want to add a text-only pruning control to more directly test whether visual tokens are intrinsically more prunable than text tokens, and benchmark the kernel at larger batch sizes to see whether the speed gap with dense attention closes as the paper's own results suggest it should.
