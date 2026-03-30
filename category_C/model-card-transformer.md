---
language: en
license: MIT
tags:
  - sequence-classification
  - pairwise-classification
  - natural-language-inference
  - ensemble
  - nlu
repo: https://github.com/monishrainy/COMP34812
---

# Model Card for r04271dc-b13305ax-s79336mx-NLI

---

## Model Overview

This model performs Natural Language Inference (NLI), classifying whether a hypothesis is supported or contradicted by a given premise.

The approach uses a hybrid ensemble of two transformer-based models:

- A cross-encoder RoBERTa model, which processes the premise and hypothesis jointly  
- A bi-encoder RoBERTa model, which encodes each sentence separately and compares their representations  

The final prediction is obtained by combining both models using a weighted average.

---

## Model Architecture

The system combines two complementary approaches:

- The cross-encoder captures detailed token-level interactions between the premise and hypothesis  
- The bi-encoder captures overall semantic similarity by comparing sentence embeddings  

The final prediction is computed as:

\[
\text{final\_logits} = \alpha \cdot \text{cross\_logits} + (1 - \alpha) \cdot \text{bi\_logits}
\]

The weight \( \alpha \) is selected based on development set performance.

---

## Data Preprocessing

Basic preprocessing steps were applied to ensure data quality:

- Missing values in premise and hypothesis were replaced with empty strings  
- Leading and trailing whitespace was removed  
- Examples with empty premise or hypothesis were discarded  
- Labels were mapped to numerical IDs for training  

---

## Training Details

### Dataset
- Approximately 24,000 premise-hypothesis pairs from the NLI shared task  
- Binary labels:
  - 0: Contradiction  
  - 1: Support  

### Hyperparameters
- Batch size: 16  
- Epochs: 3  
- Learning rate: 2e-5  
- Weight decay: 0.01  
- Warmup ratio: 0.06  
- Maximum sequence length: 256  
- Random seed: 42  

---

## Evaluation

### Metrics
- Accuracy  
- Macro Precision  
- Macro Recall  
- Macro F1-score  
- Matthews Correlation Coefficient (MCC)  

### Results (Development Set)

| Metric | Score |
|------|------|
| Accuracy | 0.886 |
| Macro Precision | 0.886 |
| Macro Recall | 0.886 |
| Macro F1-score | 0.886 |
| MCC | 0.772 |

### Baseline Comparison

| Model | Accuracy | Macro F1 |
|------|--------|---------|
| SVM | ~0.586 | ~0.584 |
| LSTM | ~0.660 | ~0.660 |
| BERT (baseline) | ~0.820 | ~0.820 |
| Proposed Ensemble Model | 0.886 | 0.886 |

The proposed model shows a clear improvement over standard baselines.

---

## Key Contributions

- Combines cross-encoder and bi-encoder transformer architectures  
- Uses a weighted ensemble to integrate complementary representations  
- Improves performance over standard fine-tuned transformer models  

---

## Implementation Details

- Framework: PyTorch  
- Transformer library: HuggingFace Transformers  
- Optimizer: AdamW  
- Learning rate scheduling: linear warmup  

---

## Limitations

- Inputs longer than 256 tokens are truncated  
- The ensemble increases inference time compared to a single model  
- Performance depends on the choice of ensemble weight  

---

## References

- Devlin et al. (2019) — BERT: Pre-training of Deep Bidirectional Transformers  
  https://arxiv.org/abs/1810.04805  

- Liu et al. (2019) — RoBERTa: A Robustly Optimized BERT Pretraining Approach  
  https://arxiv.org/abs/1907.11692  

---

## Summary

This shows that combining interaction-based and representation-based transformer models can improve performance on NLI. The hybrid ensemble captures both detailed reasoning and overall semantic similarity, leading to more robust predictions.