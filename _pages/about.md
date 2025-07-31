---
permalink: /
title: "Academic Pages is a ready-to-fork GitHub Pages template for academic personal websites"
author_profile: true
date: 2025-07-31
redirect_from: 
  - /about/
  - /about.html
---

<figure>
    <img src="{{site.baseurl}}/images/haystack.png"
         alt="Evolution of context window size">
</figure>

*This article is part of the 'Recent applications of machine learning' seminar at the University of Stuttgart, Germany. it covers the general advantages and potential challenges associated with large context models, cover three different approaches to large context benchmarking and sheds some light on one of the original large context evaluation methods, the now legendary "Needle in a Haystack" test.*

Background - Evolution of ultra long context models
======
The release of Llamma 4 Scout this April marked yet another milestone in the rapid expansion of transformer LLM's capability to support larger and larger context sizes. The model claims an impressive 10M+ token context window, overshadowing even Googles Gemini series that made waves back in 2024 with the first model to break the 1M+ token boundary. Compare this to GPT1's rather modest context limit of a mere 520 tokens and the trend over the last half a decade becomes pretty clear.

<figure>
    <img src="{{site.baseurl}}/images/llm_context_window_evolution.png"
         alt="Evolution of context window size">
    <figcaption style="text-align: center; max-width: 50%; display: block; margin: auto; width: 50%;">Figure 1: Increasing lengths of context windows over time.</figcaption>
</figure>

So why are leading LLM developers chasing these ultra large context models? Token limits on a models attention act as one of the key performance indicator relevant for many task modern LLMs are geared towards. A models prompt, system prompt, conversation history, output and any additional documents relevant for a given task all have to fit into a models context window. A large context window comes with significant advantages, but also presents several challenges for both development and deployment of the model.

## Applications for large context models: 

- **Code completion**: 
  Coding assistant's like GitHub copilot benefit significantly from the ability to ingest large chunks of an existing codebase to better fit a generated code segment to the project it is deployed in. Small context sizes limit a model to the generation of individual functions that then have to manually be patched in, while sufficiently large context sizes would allow for the generation of entire modules or more interconnected class structures.

- **Chain of thought**:
   Models explaining their reasoning process can significantly increase the outputs token consumption. Models such as Googles o3 go into significant detail when explaining their approach to problem solving. More complex task may include code segments, scraped text or math equations used to come to a particular conclusion. As such it is not only important to fit these elements, but also to maintain a high degree of accuracy in long context scenarios to achieve a result that's both accurate and easy to follow. 

- **RAG / CAG**:
   Many modern inferencing pipelines include retrieval augmented generation (RAG) to preselect chunks of relevant tokens from a knowledge base to include as context to a given prompt. RAG allows for the efficient usage of limited context space by only including the most relevant information pertaining to a given query, however, the inclusion of a separate rag pipeline produces additional overhead. In contrast, models with very large context windows allow not only for much broader inclusion of documents, but enable the usage of cache augmented generation (CAG). With CAG, the entire knowledge base could be cashed and injected into each prompt, ensuring the model always has all relevant information at its disposal. As with the previous use cases, effective use of CAG requires the model to be able to effectively query and retrieve information across a much larger token space.

- **Chat Applications**:
   With the ubiquity of LLM chat models, it is also worth mentioning, that a models ability to track past exchanges is likewise dependent on the size of its context window. A model can only recall information that still fits within its context limit and may otherwise hallucinate or outright 'forget' about older parts of the conversation.

## Challenges of large context models
 
- **Computational costs**:
   Transformer models, which make up the vast majority of modern LLM's incur significant performance cost when scaling up context lengths. Computational costs generally scale quadratically, meaning a model with a context size of 4094 tokens requires roughly sixteen times the computational resources of a similar models with a context size of only 1024 tokens. This effect becomes increasingly significant when talking about models with context windows of several million tokens. Computational costs are one of the main factors to consider when deciding if a ultra large context model is fit for a given task, especially in resource constraint environments.

- **Safety Concerns**:
   While the primary focus often lays on ever increasing performance, researchers have warned about potential safety concerns when operating ultra large context models. Over fifty percent of responses from LLMs tested in the [LongSafetyBench framework](https://arxiv.org/html/2411.06899v1) contained elements the researchers deemed unsafe. Furthermore, models seem to struggle with identifying harmful content within their provided context when the total context size increases. As a result, the point towards the fact, that a models safety behavior can vary significantly between small and large context scenarios. 

- **Performance**:
   Finally, the issue of performance. While many models claim large theoretical context windows of 128k+ tokens, the question remains, whether their performance stays consistent when actually utilizing that space. The last couple of years have seen the development of various benchmarking tools and frameworks to assess whether or not these models can keep up when confronted with large context scenarios. Generally, a models acceptable performance cutoff seems to fall significantly short of its claimed maximal context size. 

With the general overview out of the way, lets now dive deeper into the performance assessment. This article covers three long context evaluation benchmarks, as well as one of the original long context evaluations tests, the widely known Needle in a Haystack test. 

Related Work - A needle in the Haystack
======
 The Needle in a Haystack test, or NIAH was developed by Greg Kamradt in 2023, specifically to evaluate model performance at large context sizes. Kamradt published his methodology and findings from performing the test on OpenAIs ChatGPT-4 and Claude-2.1 on [Twitter](https://x.com/gregkamradt/status/1722386725635580292) and [GitHub](https://github.com/gkamradt/LLMTest_NeedleInAHaystack). Since then, NIAH has has been incorporated and build upon by various authors and researchers and it made its way into a large number of long context bench marking frameworks, including [NeedleBench](https://arxiv.org/abs/2407.11963), [Longbench](https://arxiv.org/abs/2308.14508) and [RULER](https://arxiv.org/abs/2404.06654). Before we take a closer look at these benchmarks, lets briefly cover the original NIHA.

**How the NIAH test works**:
1. Construct the haystack, a distractor text that will make up the majority of the prompt. Kamradt used snippets of the collection of [Paul Graham essays](https://www.paulgraham.com/articles.html) to construct the stack for the original NIAH.
2. Insert a needle, an out of place fact or statement into the haystack. in his testing, Kamradt used the statement "The best thing to do in San Francisco is eat a sandwich and sit in Dolores Park on a sunny day".
3. Formulate a prompt containing the haystack with the inserted needle and task the model with answering a question who's answer is contained within the needle. In this case "What the best thing to do in San Francisco?"
4. Vary the size of the haystack and the point the needle is inserted.
5. Evaluate the models answers on accuracy for each configuration. 

<figure>
    <img src="{{site.baseurl}}/images/NIAH_results.jpg"
         alt="Results of the initial NIAH test on ChatGPT-4">
    <figcaption style="text-align: center; max-width: 50%; display: block; margin: auto; width: 50%;">Figure 2: Results of the initial NIAH test on ChatGPT-4.</figcaption>
</figure>

As can be seen in Figure 2, ChatGPT-4 generally performed well in testing, achieving flawless results for context sizes below 73k. Needles inserted at the beginnign and end of the document were also always retrieved correctly, even at the maximum of 128K tokens. Notably, there is a significant performance loss observed for needles inserted between 10-50% document depth. This was dubbed the ["lost in the middle" phenomenon](https://arxiv.org/pdf/2307.03172).

Kamradt's [test on Claud-2.1](https://x.com/GregKamradt/status/1727018183608193393) showed a similar pattern, with performance taking a significant hit in the outlined area, however the overall reults were much spottier.

<figure>
    <img src="{{site.baseurl}}/images/Claude_2_1_testing.png"
         alt="Results of the initial NIAH test on Claud-2.1">
    <figcaption style="text-align: center; max-width: 50%; display: block; margin: auto; width: 50%;">Figure 3: Results of the initial NIAH test on Claud-2.1.</figcaption>
</figure>

These results were later [challenged by Anthropic](https://www.anthropic.com/news/claude-2-1-prompting), claiming that the models comparatively poor performance was due to non-ideal prompting and the models tendency to "not [answer] a question based on a document if it doesn’t contain enough information to justify that answer". Anthropics revised testing boosted the NIAH performance from 27% to 98% accuracy, indicating that both model architecture as well as prompt construction can have a significant impact on NIAH performance. A hypothesis that would later be re-affirmed in other bench marking results building on the original NIAH.

***Fun fact**: Greg Kamradt's testing on ChatGPT-4 cost roughly 200$ in API calls. A singular run at 128k tokens was billed at 1.28$, outlining one of the issues of repeatedly testing closed source models at high context lenghts.*

Related Work - Long Context Benchmarking
======
Following the publication of NIAH, several long-context benchmarking frameworks, including NeedleBench, have implemented variations of the original NIAH test into their suit of evaluation tasks. NeedleBench in turn, seeks to address some of the shortcomings identified in these related benchmarks. We will Therefore briefly cover two of these, RULER and LongBench, as they are emblematic of two different approaches to long-context benchmarking, identified in the paper.  

## RULER
Published by Hsieh et al. in 2024, the english language synthetic RULER benchmark builds on the original NIAH, incorporating variation on the base test, RULER further tests on variable tracking (VT), common word extraction (CWE), frequent word extraction (FWE) and question answering (QA). 

**NIAH Tasks in the RULER Benchmark**:
-  **Single NIAH(S-NIAH)**: This is the original NIAH test where a single needle needs
    to be retrieved from the haystack.
- **Multi-keys NIAH (MK-NIAH)**: Multiple needles are inserted into the haystack, and
 only one of them needs to be retrieved. The additional needles are hard distractors. The
 most challenging setting is a version where the haystack is filled with distractor needles.
- **Multi-values NIAH (MV-NIAH)**: Multiple needles sharing the same key are inserted
 into the haystack. All values associated with the same key need to be retrieved.
- **Multi-queries NIAH (MQ-NIAH)**: Multiple needles are inserted into the haystack.
 All needles with distinct keys need to be retrieved.

Like in Kamradt, RULER uses synthetic data to construct and scale their haystack. They tested 17 models, 2 closed source and 15 open source, with context lengths ranging between 3-128k tokens.  

<figure>
    <img src="{{site.baseurl}}/images/RULER.png"
         alt="RULER experimental results">
    <figcaption style="text-align: center; max-width: 50%; display: block; margin: auto; width: 50%;">Figure 4: Long Context performers of models in RULER</figcaption>
</figure>

***NOTE**: Effective length refers to the reference value of 85.7 the Llama2-7B model achieved at 4k context length. A models effective length is the context length at which it still surpasses the reference value.*

**Main Findings**:
- As in Kamradt, passkey retrival and base NIAH achieve near perfect results for most models.
- Model performance in advanced tasks drops at higher context lenghts
- Models effective context lengths are noticably shorter then their claimed maximum. 

**Limitations**:
- **Lacking correlation to realistic long-context tasks**: Distractor text as well as keywords such as needles for NIAH or targets for FWE / CWE are synthetic ("aaaa", "cccc"), limiting their corellation to real world natural language tasks.
- **Lack of evaluation on short context**: Models were not evaluated at short contexts below 4k tokens. RULER is purely focused on the drop off in performance at higher context lengths.
- **Lack of verification of prompt robustness**: As seen with the original NIAH test on Claude-2.1 prompt robustness can significantly impact performance. RULER did not verify prompt robustness beyond preliminary, early stage testing.

## LongBench
Comparaded to RULER,  Bai et al.'s LongBench takes a different approach. The LongBench Framework utilizes human annotated, real world text's from both english and chinese alongside synthetic sources. It boast a robust and diverse number of evaluation tasks ranging from QA, summarization and few-shot-learning to code completion.

<figure>
    <img src="{{site.baseurl}}/images/LongBench_sources.png"
         alt="Breakdown of text used in LongBench by source.">
    <figcaption style="text-align: center; max-width: 50%; display: block; margin: auto; width: 50%;">Figure 5: Breakdown of text sources used in LongBench.</figcaption>
</figure>

An interesting quirk identified by the authors was the models tendency to perform supprisingly well on QA tasks, even when not provided with any context. They attribute this to a likely overlap of test-data, partially sourced from wikipedia, and the models parameterized knowledge, highlighting a potencial dissadvantage of real world text in testing.   

<figure>
    <img src="{{site.baseurl}}/images/LongBench_memory.png"
         alt="Context understanding vs memorization.">
    <figcaption style="text-align: center; max-width: 50%; display: block; margin: auto; width: 50%;">Figure 6: Assessing context understanding vs memorization.</figcaption>
</figure>

**Main Findings**:
- Open-source models still lag behind closed-source in longer context tasks.
- Parametrizes knowledge can play a significant role in performance.
- Real world data appears less discerning then sythethic tests, which tend toward high or very low scores, making averaged scores across various tasks less reliable.

**Limitations**:
- **Instruction-following ability of models influences test results**: Due to the variety of tasks evaluated in LongBench, a models ability to adapt to different tasks as well as the particularities of individual prompts can bias test results.
- **Real world adjacency can lead to memorization**: Usage of non-synthetic texts enables models to utilize parameterized knowledge to solve certain tasks, even without context.
- **Synthetic tests are more flexible**: Scalability of real text tasks is limited by the availability of appropriate data, and is harder to scale to very large context sizes. 