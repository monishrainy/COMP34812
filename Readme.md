# Natural Language Inference – COMP34812

This project implements two models for Natural Language Inference (NLI):  
- A **non-transformer model** using recurrent networks with soft attention and local inference modeling  
- A **transformer-based model** (Category C – placeholder)

Each approach is located in its own directory:
- `category_B/` – ESIM-inspired BiLSTM model with FastText embeddings for binary entailment classification  
- `category_C/` – Placeholder for transformer-based model (not part of this submission)

---

## Model Architectures

### Classical Model (Category B)
- Based on an ESIM-inspired architecture [1]
- Uses pretrained FastText embeddings (Wiki News, 300d) [2]
- The model includes:
  - Shared embedding layer with pretrained FastText vectors
  - Shared BiLSTM encoder for contextual encoding of premise and hypothesis
  - Soft attention alignment between the two sequences
  - Local inference modelling using concatenation, difference, and element-wise product
  - Fully connected layers with dropout for classification
- Sentence representations are obtained using global average and max pooling
- Outputs a binary prediction (Entailment / Contradiction) using a sigmoid layer

Link to trained model is [here.](ADD_DRIVE_LINK_HERE)

---

### Transformer Model (Category C)
- Placeholder for transformer-based approach (not included in this submission)
- This section is reserved for a potential transformer model (e.g., RoBERTa/BERT)

Link to trained model is not applicable.

---

## Development Setup

### 1. Clone the repository
```bash
git clone https://github.com/monishrainy/COMP34812.git
cd COMP34812
```

### 2. Create a virtual environment
```bash
python -m venv venv
source venv/bin/activate
```

### 3. Install dependencies
```bash
pip install -r requirements.txt
```

---

## Running the Code

### Training
Train the models using the provided Jupyter Notebooks:
- `category_B/train.ipynb`  
- `category_C/train.ipynb`

### Evaluation & Testing
- `category_B/` – contains notebooks for evaluation on the development set and generating test predictions  
- `category_C/` – contains notebooks for evaluation and test prediction generation

---

## Model Cards

| Model | Link |
|-------|------|
| Classical NLI (Category B) | [View Model Card](nli-rnn-model-card.md) |
| Transformer NLI (Category C) | [View Model Card](nli-transformer-model-card.md) |

---

## References

- [1] Chen, Q., Zhu, X., Ling, Z., Wei, S., Jiang, H., & Inkpen, D. (2017). *Enhanced LSTM for Natural Language Inference*. [ArXiv](https://arxiv.org/abs/1609.06038) 

- [2] Bojanowski, P., Grave, E., Joulin, A., & Mikolov, T. (2017). *Enriching Word Vectors with Subword Information*. [ArXiv](https://arxiv.org/abs/1607.04606)
