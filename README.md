# Natural Language Inference – COMP34812

This project implements two models for Natural Language Inference (NLI):  
- A **non-transformer model** using recurrent networks with soft attention and local inference modeling  
- A **transformer-based hybrid ensemble model**

Each approach is located in its own directory:
- `category_B/` – ESIM-inspired BiLSTM model with FastText embeddings for binary entailment classification  
- `category_C/` – Hybrid transformer ensemble model for binary NLI classification

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

Link to trained model is [here.](https://livemanchesterac-my.sharepoint.com/:f:/r/personal/mohd_-_student_manchester_ac_uk/Documents/NLI_Category_B_Artifacts?csf=1&web=1&e=bNBKeO)

- Download the model artifacts from the link above and place them in:

  - `outputs/category_B/`
      
        Required files:
        - `best_model.keras`
        - `tokenizer.pkl`
        - `config.json`

  - Then run `category_B/demo.ipynb`.

---

### Transformer Model (Category C)
- Based on a hybrid transformer architecture using RoBERTa [3]
- Combines two complementary models:
  - A cross-encoder for joint reasoning over premise and hypothesis  
  - A bi-encoder for semantic similarity using sentence embeddings  
- The model includes:
  - A pretrained RoBERTa encoder (roberta-base)
  - A cross-encoder that processes concatenated premise–hypothesis pairs
  - A bi-encoder that encodes each sentence independently
  - Mean pooling to obtain fixed-size sentence representations
  - Feature combination using concatenation, absolute difference, and element-wise operations
  - Fully connected layers with dropout for classification
- Final predictions are obtained using a weighted ensemble of both models
- Outputs a binary prediction (Support / Contradiction)

Link to trained model is [here.](https://livemanchesterac-my.sharepoint.com/:f:/r/personal/mohd_-_student_manchester_ac_uk/Documents/NLI_Category_C_Artifacts?csf=1&web=1&e=G1dreV)

- Download the model artifacts from the link above and place them in:

  - `outputs/category_C/`
      
        Required structure:
        - `cross_encoder/`
            - `cross_encoder_model.pt`
            - `cross_encoder_metadata.json`
        - `bi_encoder/`
            - `bi_encoder_model.pt`
        - `ensemble/`
            - `ensemble_metadata.json`

  - Then run `category_C/demo.ipynb`.

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
| Classical NLI (Category B) | [View Model Card](https://github.com/monishrainy/COMP34812/blob/main/category_B/nli-rnn-model-card.md) |
| Transformer NLI (Category C) | [View Model Card](https://github.com/monishrainy/COMP34812/blob/main/category_C/nli-transformer-model-card.md) |

---

## References

- [1] Chen, Q., Zhu, X., Ling, Z., Wei, S., Jiang, H., & Inkpen, D. (2017). *Enhanced LSTM for Natural Language Inference*. [ArXiv](https://arxiv.org/abs/1609.06038) 

- [2] Bojanowski, P., Grave, E., Joulin, A., & Mikolov, T. (2017). *Enriching Word Vectors with Subword Information*. [ArXiv](https://arxiv.org/abs/1607.04606)

- [3] Liu, Y., Ott, M., Goyal, N., et al. (2019). *RoBERTa: A Robustly Optimized BERT Pretraining Approach*. [ArXiv](https://arxiv.org/abs/1907.11692)
