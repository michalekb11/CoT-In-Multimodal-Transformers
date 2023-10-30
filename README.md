# Multimodal Chain of Thought Reasoning in Language Transformers

## Overview
**tldr:** The authors show that multimodal chain of thought reasoning is possible in small language transformers using a two-stage framework (1. Rationale generation | 2. Answer inference). Additionally, it outperforms humans and models like GPT 3.5 on a ScienceQA multiple choice benchmark by providing more reliable rationales prior to the answer inference step. 

### What is chain of thought reasoning (CoT)?
* Chain of Thought reasoning is an approach to elicit better responses from language models by forcing the model to explain its thought process and logical reasoning before answering a question or drawing a conclusion.
* Typically, this helps model performance by providing the model with a richer context before giving the final answer.
* However, the vast majority of CoT research has focused on the language modality. If we were to try a multimodal task such as asking questions about images, can we still elicit effective CoT reasoning?

<div align="center">
  <img width="832" alt="Screenshot 2023-10-25 at 1 26 50 PM" src="https://github.com/michalekb11/CoT-In-Multimodal-Transformers/assets/109704770/5eb6d04b-1282-4bf1-bbfe-a61ba0366495">
</div>

### Challenges of CoT reasoning in a multimodal setting
* For situations in which arriving at the correct answer requires a visual context, text-only models struggle. The models tend to hallucinate the rationale aspect of CoT reasoning, leading them to the wrong final answer. This happens because they lack the required visual context for multimodal reasoning.
   - For example, imagine trying to read the first few chapters of the transformer textbook without any of the diagrams. Then, you will be asked detailed questions about transformer architecture. 
* Additionally, providing the text model with corresponding image captions results in very little performance gain. Figure captions are like a really bad dimension reduction technique for images. Lots of information is lost!
* Finally, small models (~1B parameters) struggle to produce effective CoT reasoning at all. The authors of this paper attempt to show that they can elicit reliable and effective CoT reasoning in small models, even in a multimodal setting.

### Proposed solution
The authors propose the following two actions to make multimodal CoT reasoning effective in answering multiple choice questions.
1.  Encode vision features directly into the input context rather than using figure captions.
2.  Separate the CoT into 2 steps:
    - **Rationale Generation (QCM --> R):** Encode text and image, fuse features (gated fusion mechanism), generate rationale
    - **Answer Inference (QCMR --> A):** Encode text + rationale and image, fuse features (gated fusion mechanism), predict answer

<div align="center">
  <img width="1160" alt="Screenshot 2023-10-25 at 3 03 31 PM" src="https://github.com/michalekb11/CoT-In-Multimodal-Transformers/assets/109704770/6e076fff-9e56-4360-80f9-bdac226c4897">
</div>

# Encoding raw vision features prevents hallucination of rationale, boosting accuracy
### Model architecture
To enable multimodal CoT reasoning, the authors ***incorporate visual features directly*** into the context rather than using text summarizations such as image captions. This is accomplished via a vision feature extractor (DETR) that generates embeddings for each patch in the image. These features are then fused with the text embeddings gathered from the T5 architecture (developed by Google researchers). Similarly, the T5 decoder is used to generate the desired output Y.

### Formal algorithm and architecture visual
<div align="center">
  <img width="1285" alt="Screenshot 2023-10-25 at 3 34 59 PM" src="https://github.com/michalekb11/CoT-In-Multimodal-Transformers/assets/109704770/d8dd014c-1183-4f1b-9add-55989d92a5f7">
</div>
<br>
<br>
<div align="center">
  <img width="800" alt="Screenshot 2023-10-30 at 2 37 57 PM" src="https://github.com/michalekb11/CoT-In-Multimodal-Transformers/assets/109704770/9deae835-04c4-4079-b439-c589927b9592">
</div>

### Example - Correcting hallucinated rationales
The following example demonstrates that incorporating visual features directly into the context of the model can prevent the hallucination of the rationale. Here, the question, context, multiple choice options, and the image are fed into the model, which is expected to generate the "gold rationale". However, a basline text-only model cannot generate the expected rationale since it does not see the corresponding image. Therefore, the baseline model hallucinates its rationale, leading it to the wrong final answer.
<div align="center">
  <img width="1192" alt="Screenshot 2023-10-25 at 11 07 27 AM" src="https://github.com/michalekb11/CoT-In-Multimodal-Transformers/assets/109704770/4c6eff97-fb39-4641-bd8d-ac362690511e">
</div>

----
### Question 1
The authors use this example to emphasize that incorporating vision features mitigates the issue of hallucinated rationale. What is one critique of this example, keeping in mind that the baseline two-stage architecture is text only?

----

# Two-stage CoT is necessary in small models
The authors' two-step breakdown of CoT involves the following:
1. **Rationale Generation (QCM --> R):** Encode text and image, fuse features (gated fusion mechanism), generate rationale
2. **Answer Inference (QCMR --> A):** Encode text + rationale and image, fuse features (gated fusion mechanism), predict answer

You can see that using a one-stage framework performs worse than no CoT at all in small models.

<div align="center">
  <img width="566" alt="Screenshot 2023-10-25 at 11 09 35 AM" src="https://github.com/michalekb11/CoT-In-Multimodal-Transformers/assets/109704770/1e47cafb-8c18-4d82-8364-666835c0af52">
</div>
 
# Multimodal CoT Results
* Improves over GPT-3.5 by 16% and exceeds human performance on ScienceQA
* Outperforms methods using just image captions
  
<div align="center">
  <img width="1157" alt="Screenshot 2023-10-25 at 11 23 12 AM" src="https://github.com/michalekb11/CoT-In-Multimodal-Transformers/assets/109704770/a961f28f-9c68-4a5d-843e-7863746b2913">
</div>

* Visual features help mitigate hallucinated rationales
* Two stage framework boosts convergence (fewer epochs required to reach maximum accuracy)
* Multimodalily boosts accuracy

<div align="center">
  <img width="580" alt="Screenshot 2023-10-25 at 11 24 40 AM" src="https://github.com/michalekb11/CoT-In-Multimodal-Transformers/assets/109704770/251abe44-fcfa-4dfb-a9c6-d6c9a33b894c">
</div>

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


