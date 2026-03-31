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

## Model Details

### Model Description

This model is designed for Natural Language Inference (NLI), where the goal is to determine whether a hypothesis is supported or contradicted by a premise.

It uses a hybrid ensemble of two RoBERTa-based models: a cross-encoder that processes the premise and hypothesis jointly, and a bi-encoder that encodes them separately and compares their representations. Final predictions are made using a weighted average of both models.

- Developed by: Diya Chutani, Affan Bin Imran and Mohd Monish Rainy  
- Language(s): English  
- Model type: Supervised  
- Model architecture: Transformer
- Finetuned from model: roberta-base  

---

### Model Architecture

The ensemble combines two complementary modeling strategies:

- The **cross-encoder** captures fine-grained interactions between the premise and hypothesis by processing them jointly.
- The **bi-encoder** captures sentence-level semantic similarity by encoding each text separately and comparing the resulting embeddings.

The final prediction is computed as:

final_logits = α · cross_logits + (1 - α) · bi_logits

where \(alpha) is chosen based on development set performance.

---

### Model Resources

- Repository: https://huggingface.co/FacebookAI/roberta-base
- Paper or documentation: https://arxiv.org/abs/1907.11692 

---

## Training Details

### Training Dataset
- Approximately 24,000 premise-hypothesis pairs from the NLI shared task  
- Binary labels:
  - 0: Contradiction  
  - 1: Support  

### Hyperparameters

    - Batch size: 16  
    - Epochs: 2
    - Learning rate: 2e-5  
    - Weight decay: 0.01  
    - Warmup ratio: 0.06  
    - Maximum sequence length: 256  
    - Random seed: 42

---
### Speeds, Sizes, Times

    - overall training time: 30 minutes  
    - cross-encoder training time: 14 minutes  
    - bi-encoder training time: 16 minutes  
    - cross-encoder model size: 476 MB
    - bi-encoder model size: 478 MB  

---

### Evaluation Results (Development Set)

The hybrid ensemble model was evaluated on the NLI development set using the official local scoring script.

    - Accuracy: 0.8861  
    - Macro Precision: 0.8863  
    - Macro Recall: 0.8857  
    - Macro F1 Score: 0.8859  

    - Weighted Precision: 0.8862  
    - Weighted Recall: 0.8861  
    - Weighted F1 Score: 0.8861  

    - Matthews Correlation Coefficient (MCC): 0.7720

### Baseline Comparison

| Model | Accuracy | Macro F1 |
|------|--------|---------|
| SVM | ~0.586 | ~0.584 |
| LSTM | ~0.660 | ~0.660 |
| BERT (baseline) | ~0.820 | ~0.820 |
| Proposed Ensemble Model | 0.886 | 0.886 |

---

## Technical Specifications

### Hardware

- RAM: 10–16 GB recommended for training
- Storage: 3–5 GB
- GPU: NVIDIA Tesla T4
- Compute: CUDA enabled GPU acceleration  

---

### Software

    - Python 3.10  
    - PyTorch 2.1  
    - HuggingFace Transformers 4.36  
    - NumPy 1.26  
    - Pandas 2.1  
    - Scikit-learn 1.3  
    - TQDM 4.66  

---

## Limitations

- Inputs longer than 256 tokens are truncated  
- The ensemble increases inference time compared to a single model  
- Performance depends on the choice of ensemble weight  

---

## References

- Liu et al. (2019) — RoBERTa: A Robustly Optimized BERT Pretraining Approach  
  https://arxiv.org/abs/1907.11692  

---

## Additional Information

The hyperparameters were determined by experimentation with different values.
