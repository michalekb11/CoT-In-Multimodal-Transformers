# Chain of Thought Reasoning in Multimodal Transformers

## Overview
**tldr:** The authors show that chain of thought reasoning is possible in small multimodal transformers using a two-stage framework (1. Rationale generation | 2. Answer inference). Additionally, it outperforms humans and models like GPT 3.5 on a ScienceQA multiple choice benchmark by providing more reliable rationales. 

### What is chain of thought reasoning (CoT)?
* Chain of Thought reasoning is an approach to elicit better responses by language models by forcing the model to explain its thought process and logical reasoning before answering a question or drawing a conclusion.
<div align="center">
  <img width="832" alt="Screenshot 2023-10-25 at 1 26 50 PM" src="https://github.com/michalekb11/CoT-In-Multimodal-Transformers/assets/109704770/5eb6d04b-1282-4bf1-bbfe-a61ba0366495">
</div>

* Typically, this helps model performance by providing the model with a richer context before giving the final answer.
* However, the vast majority of CoT research has focused on the language modality. If we were to try a multimodal task such as asking questions about images, can we still elicit effective CoT reasoning?

### Challenges of CoT reasoning in a multimodal setting
* For situations in which arriving at the correct answer requires a visual context, text-only models struggle. The models tend to hallucinate the rationale aspect of CoT reasoning, leading them to the wrong final answer. This happens because they lack the required visual context for multimodal reasoning.
   - For example, imagine trying to read the first few chapters of the transformer textbook without any of the diagrams. Then, you will be asked detailed questions about transformer architecture. 
* Additionally, providing the text model with corresponding image captions results in very little performance gain. Figure captions are like a really bad dimension reduction technique for images. Lots of information is lost!
* Finally, small models (~1B parameters) struggle to produce effective CoT reasoning at all. The authors of this paper attempt to show that they can elicit reliable and effective CoT reasoning in small models, even in a multimodal setting.

### A two-stage approach to effective multimodal CoT prompting
* The authors propose the breakdown of CoT into two steps:
   1. Rationale Generation (QCM --> R)
   2. Answer Inference (QCMR --> A)
  
 <div>
   <img width="1160" alt="Screenshot 2023-10-25 at 3 03 31 PM" src="https://github.com/michalekb11/CoT-In-Multimodal-Transformers/assets/109704770/6e076fff-9e56-4360-80f9-bdac226c4897">
 </div>

## Model Architecture
* Both the rationale generation and the answer inference use the same transformer architecture (T5 - developed by Google researchers).
* To enable multimodal CoT reasoning, the authors incorporate visual features directly into the context rather than using text summarizations such as image captions. This is accomplished via a vision feature extractor.


* Stage 1: Encode text and image, fuse features, generate rationale
* Stage 2: Encode text + rationale from stage 1, fuse with image, predict answer
* Shared encoder-decoder architecture using Transformers
* Gated fusion to incorporate multimodal context


<img width="500" alt="Screenshot 2023-10-25 at 11 11 37 AM" src="https://github.com/michalekb11/CoT-In-Multimodal-Transformers/assets/109704770/8f5298ba-603d-4af8-8ac6-e46b4ed8e79b"> <img width="400" alt="Screenshot 2023-10-25 at 11 11 54 AM" src="https://github.com/michalekb11/CoT-In-Multimodal-Transformers/assets/109704770/11821cbd-e10b-4c44-a888-9e5f7f010b59">



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


