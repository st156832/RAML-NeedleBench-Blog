---
permalink: /
title: "Academic Pages is a ready-to-fork GitHub Pages template for academic personal websites"
author_profile: true
date: 2025-07-31
redirect_from: 
  - /about/
  - /about.html
---

*This article was written in the context of the 'Recent applications of machine learning' seminar at the University of Stuttgart, Germany. In it, I will be going over the general advantages and potential challenges associated with large context models, cover three different approaches to large context benchmarking and shed some light on one of the original large context evaluation methods, the now legendary Needle in a Haystack' test.*

Background:
The release of Llamma 4 Scout this April marked yet another milestone in the rapid expansion of transformer LLM's capability to support larger and larger context sizes. The model claims an impressive 10M+ token context window, overshadowing even Googles Gemini series that made waves back in 2024 with the first model to break the 1M+ token boundary. Compare this to GPT1's rather modest context limit of a mere 520 tokens and the trend over the last half a decade becomes pretty clear.

<figure>
    <img src="{{site.baseurl}}/images/llm_context_window_evolution.png"
         alt="Albuquerque, New Mexico">
    <figcaption style="text-align: center; max-width: 50%; display: block; margin: auto; width: 50%;">A single track trail outside of Albuquerque, New Mexico.</figcaption>
</figure>

So why are leading LLM developers chasing these ultra large context models? Token limits on a models attention act as one of the key performance indicator relevant for many task modern LLMs are geared towards. A models prompt, system prompt, conversation history, output and any additional documents relevant for a given task all have to fit into a models context window. A large context window comes with significant advantages, but also presents several challenges for both development and deployment of the model.

Advantages of large context models: 

Code completion: Coding assistant's like GitHub copilot benefit significantly from the ability to ingest large chunks of an existing codebase to better fit a generated code segment to the project it is deployed in. Small context sizes limit a model to the generation of individual functions that then have to manually be patched in, while sufficiently large context sizes would allow for the generation of entire modules or more interconnected class structures.

Chain of thought: Models explaining their reasoning process can significantly increase the outputs token consumption. Models such as Googles o3 go into significant detail when explaining their approach to problem solving. More complex task may include code segments, scraped text or math equations used to come to a particular conclusion. As such it is not only important to fit these elements, but also to maintain a high degree of accuracy in long context scenarios to achieve a result that's both accurate and easy to follow. 

RAG and CAG: Many modern inferencing pipelines include retrieval augmented generation (RAG) to preselect chunks of relevant tokens from a knowledge base to include as context to a given prompt. RAG allows for the efficient usage of limited context space by only including the most relevant information pertaining to a given query, however, the inclusion of a separate rag pipeline produces additional overhead. In contrast, models with very large context windows allow not only for much broader inclusion of documents, but enable the usage of cache augmented generation (CAG). With CAG, the entire knowledge base could be cashed and injected into each prompt, ensuring the model always has all relevant information at its disposal. As with the previous use cases, effective use of CAG requires the model to be able to effectively query and retrieve information across a much larger token space.

Chat Applications: With the ubiquity of LLM chat models, it is also worth mentioning, that a models ability to track past exchanges is likewise dependent on the size of its context window. A model can only recall information that still fits within its context limit and may otherwise hallucinate or outright 'forget' about older parts of the conversation.

Disadvantages of large context models
 
Computational costs: Transformer models, which make up the vast majority of modern LLM's incur significant performance cost when scaling up context lengths. Computational costs generally scale quadratically, meaning a model with a context size of 4094 tokens requires roughly sixteen times the computational resources of a similar models with a context size of only 1024 tokens. This effect becomes increasingly significant when talking about models with context windows of several million tokens. Computational costs are one of the main factors to consider when deciding if a ultra large context model is fit for a given task, especially in resource constraint environments.

Safety Concerns: While the primary focus often lays on ever increasing performance, researchers have warned about potential safety concerns when operating ultra large context models. Over fifty percent of responses from LLMs tested in the LongSafetyBench framework contained elements the researchers deemed unsafe. Furthermore, models seem to struggle with identifying harmful content within their provided context when the total context size increases. As a result, the point towards the fact, that a models safety behavior can vary significantly between small and large context scenarios. 

Performance: Finally, the issue of performance. While many models claim large theoretical context windows of 128k+ tokens, the question remains, whether their performance stays consistent when actually utilizing that space. The last couple of years have seen the development of various benchmarking tools and frameworks to assess whether or not these models can keep up when confronted with large context scenarios. Generally, a models acceptable performance cutoff seems to fall significantly short of its claimed maximal context size. 

With the general overview out of the way, lets now dive deeper into the performance assessment. This article covers three long context evaluation benchmarks, as well as one of the original long context evaluations tests, the widely known Needle in a Haystack test. 

A data-driven personal website
======
Like many other Jekyll-based GitHub Pages templates, Academic Pages makes you separate the website's content from its form. The content & metadata of your website are in structured Markdown files, while various other files constitute the theme, specifying how to transform that content & metadata into HTML pages. You keep these various Markdown (.md), YAML (.yml), HTML, and CSS files in a public GitHub repository. Each time you commit and push an update to the repository, the [GitHub pages](https://pages.github.com/) service creates static HTML pages based on these files, which are hosted on GitHub's servers free of charge.

Many of the features of dynamic content management systems (like Wordpress) can be achieved in this fashion, using a fraction of the computational resources and with far less vulnerability to hacking and DDoSing. You can also modify the theme to your heart's content without touching the content of your site. If you get to a point where you've broken something in Jekyll/HTML/CSS beyond repair, your Markdown files describing your talks, publications, etc. are safe. You can rollback the changes or even delete the repository and start over - just be sure to save the Markdown files! You can also write scripts that process the structured data on the site, such as [this one](https://github.com/academicpages/academicpages.github.io/blob/master/talkmap.ipynb) that analyzes metadata in pages about talks to display [a map of every location you've given a talk](https://academicpages.github.io/talkmap.html).

For those users that need more advanced functionality, the template also supports the following popular tools:
- [MathJax](https://www.mathjax.org/) for mathematical equations
- [Mermaid](https://mermaid.js.org/) for diagraming
- [Plotly](https://plotly.com/javascript/) for plotting

Getting started
======
1. Register a GitHub account if you don't have one and confirm your e-mail (required!)
1. Fork [this template](https://github.com/academicpages/academicpages.github.io) by clicking the "Use this template" button in the top right. 
1. Go to the repository's settings (rightmost item in the tabs that start with "Code", should be below "Unwatch"). Rename the repository "[your GitHub username].github.io", which will also be your website's URL.
1. Set site-wide configuration and create content & metadata (see below -- also see [this set of diffs](http://archive.is/3TPas) showing what files were changed to set up [an example site](https://getorg-testacct.github.io) for a user with the username "getorg-testacct")
1. Upload any files (like PDFs, .zip files, etc.) to the files/ directory. They will appear at https://[your GitHub username].github.io/files/example.pdf.  
1. Check status by going to the repository settings, in the "GitHub pages" section

Site-wide configuration
------
The main configuration file for the site is in the base directory in [_config.yml](https://github.com/academicpages/academicpages.github.io/blob/master/_config.yml), which defines the content in the sidebars and other site-wide features. You will need to replace the default variables with ones about yourself and your site's github repository. The configuration file for the top menu is in [_data/navigation.yml](https://github.com/academicpages/academicpages.github.io/blob/master/_data/navigation.yml). For example, if you don't have a portfolio or blog posts, you can remove those items from that navigation.yml file to remove them from the header. 

Create content & metadata
------
For site content, there is one Markdown file for each type of content, which are stored in directories like _publications, _talks, _posts, _teaching, or _pages. For example, each talk is a Markdown file in the [_talks directory](https://github.com/academicpages/academicpages.github.io/tree/master/_talks). At the top of each Markdown file is structured data in YAML about the talk, which the theme will parse to do lots of cool stuff. The same structured data about a talk is used to generate the list of talks on the [Talks page](https://academicpages.github.io/talks), each [individual page](https://academicpages.github.io/talks/2012-03-01-talk-1) for specific talks, the talks section for the [CV page](https://academicpages.github.io/cv), and the [map of places you've given a talk](https://academicpages.github.io/talkmap.html) (if you run this [python file](https://github.com/academicpages/academicpages.github.io/blob/master/talkmap.py) or [Jupyter notebook](https://github.com/academicpages/academicpages.github.io/blob/master/talkmap.ipynb), which creates the HTML for the map based on the contents of the _talks directory).

**Markdown generator**

The repository includes [a set of Jupyter notebooks](https://github.com/academicpages/academicpages.github.io/tree/master/markdown_generator
) that converts a CSV containing structured data about talks or presentations into individual Markdown files that will be properly formatted for the Academic Pages template. The sample CSVs in that directory are the ones I used to create my own personal website at stuartgeiger.com. My usual workflow is that I keep a spreadsheet of my publications and talks, then run the code in these notebooks to generate the Markdown files, then commit and push them to the GitHub repository.

How to edit your site's GitHub repository
------
Many people use a git client to create files on their local computer and then push them to GitHub's servers. If you are not familiar with git, you can directly edit these configuration and Markdown files directly in the github.com interface. Navigate to a file (like [this one](https://github.com/academicpages/academicpages.github.io/blob/master/_talks/2012-03-01-talk-1.md) and click the pencil icon in the top right of the content preview (to the right of the "Raw | Blame | History" buttons). You can delete a file by clicking the trashcan icon to the right of the pencil icon. You can also create new files or upload files by navigating to a directory and clicking the "Create new file" or "Upload files" buttons. 

<figure>
    <img src="{{site.url}}/images/llm_context_window_evolution.png"
         alt="Albuquerque, New Mexico" style="max-width: 50%; display: block; margin: auto; width: 50%;">
    <figcaption style="text-align: center;">A single track trail outside of Albuquerque, New Mexico.</figcaption>
</figure>

For more info
------
More info about configuring Academic Pages can be found in [the guide](https://academicpages.github.io/markdown/), the [growing wiki](https://github.com/academicpages/academicpages.github.io/wiki), and you can always [ask a question on GitHub](https://github.com/academicpages/academicpages.github.io/discussions). The [guides for the Minimal Mistakes theme](https://mmistakes.github.io/minimal-mistakes/docs/configuration/) (which this theme was forked from) might also be helpful.
