# Multimodal Chain of Thought Reasoning in Language Transformers
.....insert citation here.....


**Summary:** The authors show that multimodal chain of thought reasoning is possible in small language transformers using a two-stage framework (1. Rationale generation | 2. Answer inference). Additionally, by incorporating vision features directly into the context, the model outperforms humans and models like GPT 3.5 on a ScienceQA multiple choice benchmark.

## Overview
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
Here, the authors attempt to use CoT reasoning to answer certain multiple choice questions from ScienceQA, most of which require a visual context to answer. In order to accomplish this, the authors propose that the following two actions be taken.
1.  Separate the CoT into 2 steps:
    - **Rationale Generation (QCM --> R):** Encode text and image, fuse features (gated fusion mechanism), generate rationale
    - **Answer Inference (QCMR --> A):** Encode text + rationale and image, fuse features (gated fusion mechanism), predict answer
2.  Encode vision features directly into the input context rather than using figure captions.

<div align="center">
  <img width="1160" alt="Screenshot 2023-10-25 at 3 03 31 PM" src="https://github.com/michalekb11/CoT-In-Multimodal-Transformers/assets/109704770/6e076fff-9e56-4360-80f9-bdac226c4897">
</div>

---
## Two-stage CoT is necessary in small models
The table below demonstrates that using a one-stage framework performs worse than no CoT at all in small models. Here, "one-stage" refers to strategies that generate both the rationale and the final answer in the same model run.
* **QCM --> RA** - The answer is conditioned on the rationale. The authors do not provide a great reason that this strategy does not perform well. The best hypothesis is that generating rationale and the final answer in the same output may be too complicated of a task for small models to achieve. In cases where the context length is extremely short, it is possible that the model runs out of tokens before being able to generate the final answer.
* **QCM --> AR** - The rationale is conditioned on the answer. In this case, the rationale is more or less an "explanation" for the chosen answer rather than the reason that the answer was chosen.

<div align="center">
  <img width="553" alt="Screenshot 2023-10-30 at 7 35 09 PM" src="https://github.com/michalekb11/Multimodal-CoT-Reasoning-in-Transformers/assets/109704770/ef2e4b4d-76da-4bdf-bcca-c20452d910c8">
</div>

These results suggest that generating the rationale and answer in the same output is not a good strategy for eliciting CoT reasoning in small models. Perhaps a two-stage framework works better in which the only the rationale is generated in the first model run. Then, the rationale is appended to the end of the original context to output the final answer in a second model run. After the authors attempted this, there was slight improvement in the accuracy score. However, the only method to achieve better accuracy than no CoT was the two-stage framework with vision features (table 3). Notice that incorporating image captions provided very little improvement from the two-stage baseline model.

<div align="center">
  <img width="566" alt="Screenshot 2023-10-25 at 11 09 35 AM" src="https://github.com/michalekb11/CoT-In-Multimodal-Transformers/assets/109704770/1e47cafb-8c18-4d82-8364-666835c0af52">
</div>

Let's dive deeper into how the direct incorporation of vision features improves performance in the model.

---

## Encoding raw vision features boosts accuracy by preventing rationale hallucination
The authors note in the study that in both the two-stage baseline and two-stage with captions models, the primary reason for wrong answers was the hallucination of the rationale. These models do not have access to the original image. Because most of the ScienceQA questions require a visual context to correctly answer the question, models that do not see the image produce unreliable rationale with wrong information. During the second inference, the model reads the incorrect rationale and generates the wrong answer. 

Fortunately, there are ways to directly incorporate information from the image, despite using a language model. The authors have converted the langauge model to be "multimodal" via some changes in architecture. 

### Model architecture
To enable multimodal CoT reasoning, the authors ***incorporate visual features directly*** into the context rather than using text summarizations such as image captions. This is accomplished via a vision feature extractor (DETR) that generates embeddings for each patch in the image. These features are then fused with the text embeddings gathered from the T5 architecture (developed by Google researchers). Similarly, the T5 decoder is used to generate the desired output Y.

<div align="center">
  <img width="1285" alt="Screenshot 2023-10-25 at 3 34 59 PM" src="https://github.com/michalekb11/CoT-In-Multimodal-Transformers/assets/109704770/d8dd014c-1183-4f1b-9add-55989d92a5f7">
</div>
<br>
<br>
<div align="center">
  <img width="555" alt="Screenshot 2023-10-30 at 2 37 57 PM" src="https://github.com/michalekb11/CoT-In-Multimodal-Transformers/assets/109704770/9deae835-04c4-4079-b439-c589927b9592">
</div>

### Example - Correcting hallucinated rationales
The following example demonstrates that incorporating visual features directly into the context of the model can prevent the hallucination of the rationale. Here, the question, context, multiple choice options, and the image are fed into the model, which is expected to generate the "gold rationale". However, a basline text-only model cannot generate the expected rationale since it does not see the corresponding image. Therefore, the baseline model hallucinates its rationale, leading it to the wrong final answer.

<div align="center">
  <img width="1192" alt="Screenshot 2023-10-25 at 11 07 27 AM" src="https://github.com/michalekb11/CoT-In-Multimodal-Transformers/assets/109704770/4c6eff97-fb39-4641-bd8d-ac362690511e">
</div>

----
### Question 2
<details close>
<summary>The authors use this example to emphasize that incorporating vision features mitigates the issue of hallucinated rationale. What is one critique of this example, keeping in mind that the baseline two-stage architecture is text only and does not include figure captions?</summary>
<br>
  
**Answer:** The baseline text-only model has no access to the image, and thus cannot answer this question better than random chance. In fact, EVEN HUMANS cannot answer this question correctly more than 50% of the time. Therefore, this appears to inflate the authors' results. A much better baseline would be the two-stage model with figure captions since this model at least in theory may have access to the correct answer. This way, the authors would be able to more strongly highlight the performance augmentation via visual features.
</details>

----

# Multimodal CoT Results
The following table shows the performance of the authors' multimodal CoT reasoning on the ScienceQA benchmark in comparison to a variety of other models. Here are the most important results to be aware of:
* The multimodal strategy with vision features exibitsa 16.5% increase in accuracy compared to GPT-3.5
* This model also outperforms humans on average
* The model achieves the greatest increase in performance over other models in the question category of "image context"

<div align="center">
  <img width="1157" alt="Screenshot 2023-10-25 at 11 23 12 AM" src="https://github.com/michalekb11/CoT-In-Multimodal-Transformers/assets/109704770/a961f28f-9c68-4a5d-843e-7863746b2913">
</div>

---

## Critical Analysis
* The evaluation of this model could be improved. Some of the models for comparison are text-only and do not incorporate image captions. It is unfair to compare the multimodal CoT against baseline models that do not have enough information to answer the question, and this slightly inflates the results.
* The authors grade the rationale generation using the RougeL score, which finds the length of the longest common subsequence between the predicted rationale and the gold-standard rationael. The authors should consider using a semantic similarity score (perhaps using embeddings from a transformer) to grade the predicted rationale. This may be more accurate/useful.

---

## Conclusion
* A two-stage CoT framework in which the rationale is produced separate from the answer improves complex reasoning in small models (<1B parameters).
* Multimodal CoT using vision features outperforms methods using just image captions.
* Visual features help mitigate hallucinated rationales.
* The study demonstrates a promising direction for developing stronger reasoning ability in small language transformers.
 
---

## Additional Resources
Chain of Thought Prompting - Prompt engineering guide
https://www.promptingguide.ai/techniques/cot

DETR Vision Feature Extractor
* https://huggingface.co/docs/transformers/model_doc/detr


