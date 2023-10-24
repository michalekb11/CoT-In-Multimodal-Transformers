# Chain of Thought Reasoning in Multimodal Transformers

## Overview
**tldr:** The authors show that chain of thought reasoning is possible in small multimodal transformers using a two-stage framework (1. Rationale generation | 2. Answer inference). Additionally, it outperforms humans and models like GPT 3.5 on a ScienceQA multiple choice benchmark by providing more reliable rationales. 

**What is chain of thought reasoning (CoT)?**
* Example or description

**What are the limitations of CoT prompting in text only models?**
* Hallucinating rationale
* Maybe using multimodal CoT is better

**How can we implement CoT promping in multimodal models?**
* Two stage framework
* Enables small 1B models to perform effective reasoning

## Architecture
* Stage 1: Encode text and image, fuse features, generate rationale
* Stage 2: Encode text + rationale from stage 1, fuse with image, predict answer
* Shared encoder-decoder architecture using Transformers
* Gated fusion to incorporate multimodal context

## Example
Magnet example.

----
### Question 1
What is one critique of this example, keeping in mind the baseline two-stage architecture (text-only) and the two-stage framework with image features?

----

## Results
* Improves over GPT-3.5 by 16% and exceeds human performance on ScienceQA
* Visual features help mitigate hallucinated rationales
* Outperforms methods using just image captions
* Two-stage framework and multimodality are keys to performance gains

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
DETR Vision Feature Extractor
* https://huggingface.co/docs/transformers/model_doc/detr


