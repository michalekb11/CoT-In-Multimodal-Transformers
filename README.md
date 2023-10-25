# Chain of Thought Reasoning in Multimodal Transformers

## Overview
**tldr:** The authors show that chain of thought reasoning is possible in small multimodal transformers using a two-stage framework (1. Rationale generation | 2. Answer inference). Additionally, it outperforms humans and models like GPT 3.5 on a ScienceQA multiple choice benchmark by providing more reliable rationales. 

### **What is chain of thought reasoning (CoT)?**
* Chain of Thought reasoning is an approach to elicit better responses by language models by forcing the model to explain its thought process and logical reasoning before answering a question or drawing a conclusion
<div align="center">
  <img width="832" alt="Screenshot 2023-10-25 at 1 26 50 PM" src="https://github.com/michalekb11/CoT-In-Multimodal-Transformers/assets/109704770/5eb6d04b-1282-4bf1-bbfe-a61ba0366495">
</div>

### **What are the limitations of CoT prompting in text-only models?**
* Imagine trying to read a textbook without any figures or tables. Our ability to answer complex or detailed questions about the content would be greatly hindered. This is because we learn how to reason better when we are taught the information via a variety of modalities. For example, we read about the architechture of transformers and constantly reference the corresponding diagram. Without this, we would have a more difficult time answering questions about transformers.
* It turns out that small language models (~1B parameters) are very similar. Text-only models of this size tend to hallucinate aspects of the rationale in chain of thought prompting, leading the model to the wrong final answer. We will see an example shortly.
* The authors hypothesize that providing these models with another data modality that they can reference (in this case, images) will improve their performance on answering multiple choice questions.

### **How can we implement CoT prompting in multimodal models?**
* Two stage framework
* Enables small 1B models to perform effective reasoning

## Architecture
* Stage 1: Encode text and image, fuse features, generate rationale
* Stage 2: Encode text + rationale from stage 1, fuse with image, predict answer
* Shared encoder-decoder architecture using Transformers
* Gated fusion to incorporate multimodal context
<img width="567" alt="Screenshot 2023-10-25 at 11 11 37 AM" src="https://github.com/michalekb11/CoT-In-Multimodal-Transformers/assets/109704770/8f5298ba-603d-4af8-8ac6-e46b4ed8e79b">

<img width="509" alt="Screenshot 2023-10-25 at 11 11 54 AM" src="https://github.com/michalekb11/CoT-In-Multimodal-Transformers/assets/109704770/11821cbd-e10b-4c44-a888-9e5f7f010b59">

## Example
<img width="1192" alt="Screenshot 2023-10-25 at 11 07 27 AM" src="https://github.com/michalekb11/CoT-In-Multimodal-Transformers/assets/109704770/4c6eff97-fb39-4641-bd8d-ac362690511e">


----
### Question 1
What is one critique of this example, keeping in mind the baseline two-stage architecture (text-only) and the two-stage framework with image features?

----

## Results
* Improves over GPT-3.5 by 16% and exceeds human performance on ScienceQA
* Outperforms methods using just image captions

<img width="1157" alt="Screenshot 2023-10-25 at 11 23 12 AM" src="https://github.com/michalekb11/CoT-In-Multimodal-Transformers/assets/109704770/a961f28f-9c68-4a5d-843e-7863746b2913">

* Visual features help mitigate hallucinated rationales
* Two-stage framework and multimodality are keys to performance gains

  <img width="566" alt="Screenshot 2023-10-25 at 11 09 35 AM" src="https://github.com/michalekb11/CoT-In-Multimodal-Transformers/assets/109704770/1e47cafb-8c18-4d82-8364-666835c0af52">

* Two stage framework boosts convergence (fewer epochs required to reach maximum accuracy)
* Multimodalily boosts accuracy

  <img width="580" alt="Screenshot 2023-10-25 at 11 24 40 AM" src="https://github.com/michalekb11/CoT-In-Multimodal-Transformers/assets/109704770/251abe44-fcfa-4dfb-a9c6-d6c9a33b894c">

## Critical Analysis
* Evaluation limited to visual questions, should include some text-only
* Lack of implementation details for training the rationale generator
* Consider semantic similarity metrics, not just text overlap loss
* Weakly supervise rationale generation instead of full annotation
* Optimize for end task accuracy directly, not just rationale quality

## Conclusion
* Summarize key ideas and contributions
* Multimodal-CoT enables small models to perform complex reasoning
* Visual grounding improves rationale quality and answer accuracy
* Promising direction for developing stronger reasoning abilities in models

## Questions
Can mix this into rest of presentation

## Additional Resources
Chain of Thought Prompting - Prompt engineering guide
https://www.promptingguide.ai/techniques/cot

DETR Vision Feature Extractor
* https://huggingface.co/docs/transformers/model_doc/detr


