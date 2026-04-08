---
language: en
license: MIT
tags:
  - sequence-classification
  - pairwise-classification
  - natural-language-inference
  - ESIM
  - nlu
repo: https://github.com/monishrainy/COMP34812
---

# Model Card for r04271dc-b13305ax-s79336mx-NLI

---


<!-- Provide a quick summary of what the model is/does. -->

This model performs **Natural Language Inference (NLI)** by predicting whether a **hypothesis is entailed by a premise**.  
The system was developed as part of the **COMP34812 Natural Language Understanding shared task** for pairwise sequence classification.

## Model Details

### Model Description

The architecture is inspired by the **Enhanced Sequential Inference Model (ESIM)**. The model encodes both the premise and hypothesis using a **shared Bidirectional LSTM encoder** and applies **soft attention alignment** between the two sequences. The aligned representations are then combined using **local inference modelling operations**, including concatenation, difference, and element-wise multiplication. These representations are pooled and passed through a **multi-layer perceptron classifier** to produce the final entailment prediction.

The model uses **pretrained FastText Wiki News Subwords 300-dimensional embeddings**, which help represent rare and unseen words through subword information.

- **Developed by:** Diya Chutani, Affan Bin Imran, and Mohd Monish Rainy
- **Language(s):** English
- **Model type:** Supervised
- **Model architecture:** Shared BiLSTM encoder (ESIM-inspired architecture with FastText embeddings, attention alignment, local inference composition, pooling, and MLP classifier)
- **Finetuned from:** None


### Model Resources

<!-- Provide links where applicable. -->

- **Repository:** None 

- **References:**

  - Chen, Q., Zhu, X., Ling, Z., Wei, S., Jiang, H., & Inkpen, D. (2017).  
    *Enhanced LSTM for Natural Language Inference.*  
    https://arxiv.org/abs/1609.06038  

  - Bojanowski, P., Grave, E., Joulin, A., & Mikolov, T. (2017).  
    *Enriching Word Vectors with Subword Information.*  
    https://arxiv.org/abs/1607.04606  

  - FastText Wiki News Subword 300d embeddings (accessed via `gensim.downloader`)

## Training Details

### Training Data

The model was trained using the **dataset provided for the COMP34812 Natural Language Inference shared task**. 

The dataset contains:

- approximately **24,000 premise–hypothesis pairs for training**
- approximately **6,736 premise–hypothesis pairs for development/validation**
- Binary labels:
  - 0: Contradiction
  - 1: Support

Each instance contains:

- a **premise sentence**
- a **hypothesis sentence**
- a **binary label indicating whether the hypothesis is entailed by the premise**

Preprocessing steps included:

- converting premise and hypothesis text to strings
- tokenizing text using a **Keras Tokenizer**
- limiting vocabulary size to **30,000 tokens**
- introducing an **`<OOV>` token** for unseen words
- converting sentences to integer sequences
- padding sequences to a fixed length determined using the **95th percentile of sentence lengths**
- constructing an **embedding matrix from pretrained FastText embeddings**

### Training Procedure

The model was trained as a supervised classifier using the training set and evaluated on the development set.

#### Training Hyperparameters

    - vocabulary size: 30,000
    - embedding dimension: 300
    - pretrained embeddings: FastText Wiki News Subwords 300d
    - embeddings trainable: False
    - encoder: shared BiLSTM
    - BiLSTM hidden units: 200 per direction
    - batch size: 32
    - maximum epochs: 15
    - optimizer: Adam
    - initial learning rate: 8e-4
    - learning rate scheduler: ReduceLROnPlateau (factor = 0.5, patience = 1, min_lr = 1e-5)
    - gradient clipping: clipnorm = 1.0
    - dropout:
      - BiLSTM dropout: 0.20
      - comparison layer dropout: 0.25
      - classifier dropout: 0.40 and 0.30
    - loss function: binary cross-entropy
    - output activation: sigmoid
    - early stopping: validation loss, patience = 3, restore best weights = True
    - model checkpoint: best model saved by validation loss
    - random seed: 42
    - sequence length: determined from the 95th percentile of training sentence lengths, rounded to a multiple of 5

Training also used the following callbacks:

    - EarlyStopping on validation loss
    - ReduceLROnPlateau to reduce learning rate when validation loss plateaus
    - ModelCheckpoint to save the best-performing model

#### Speeds, Sizes, Times

    - overall training time: 5 minutes 10 seconds  
    - duration per training epoch: 20.42 seconds  
    - model size: 50.02 MB  
    - tokenizer size: 1.35 MB  

## Evaluation

### Testing Data & Metrics

#### Testing Data

Model evaluation was performed on the **development dataset (`dev.csv`)**, which contains **6,736 premise–hypothesis pairs**. This dataset was used to measure model performance and guide model selection and hyperparameter tuning.

#### Metrics

The following metrics were computed during evaluation:

    - Accuracy
    - Macro Precision
    - Macro Recall
    - Macro F1-score
    - Weighted Precision
    - Weighted Recall
    - Weighted F1-score

Predicted probabilities were converted to binary labels using a **classification threshold of 0.5**.

### Results

Development-set results from `evaluate.ipynb`:

- **Accuracy:** 0.7213
- **Macro Precision:** 0.7211
- **Macro Recall:** 0.7206
- **Macro F1-score:** 0.7208
- **Weighted Precision:** 0.7213
- **Weighted Recall:** 0.7213
- **Weighted F1-score:** 0.7212

These results indicate that the model achieves balanced overall performance on the development set. The macro F1-score demonstrates consistent performance across both classes, while the weighted metrics closely align with overall accuracy, reflecting a relatively balanced class distribution.

### Baseline Comparison

| Model | Accuracy | Macro F1 |
|------|--------|---------|
| SVM (baseline)| ~0.586 | ~0.584 |
| LSTM (baseline)| ~0.660 | ~0.660 |
| BERT (baseline) | ~0.820 | ~0.820 |
| Proposed ESIM-lite Model | 0.721 | 0.721 |

The proposed ESIM-lite model significantly outperforms traditional baselines such as SVM and vanilla LSTM, while remaining competitive given the constraint of not using transformer-based architectures.

## Technical Specifications

### Hardware

The model can be trained and evaluated in a standard machine learning environment.

Recommended hardware:

    - RAM: 16 GB RAM
    - Storage: at least 2GB (to store pretrained embeddings and saved model artifacts)
    - GPU: Google Colab with NVIDIA Tesla T4 GPU

### Software

The model was implemented using the following software stack:

    - Python: 3.12.13
    - TensorFlow: 2.19.0
    - Keras: 3.13.2
    - NumPy: 2.0.2
    - Pandas: 2.2.2
    - Scikit-learn: 1.6.1
    - Gensim: 4.4.0
    - tqdm: 4.67.3
    - pickle and json for saving model configuration and tokenizer


## Bias, Risks, and Limitations

This model inherits potential biases present in the provided dataset, including linguistic, topical, and annotation biases. Since the dataset was supplied as part of a shared task, its data collection and labeling process are outside the control of the developers.

The model also has several limitations. The maximum sequence length is determined from the 95th percentile of the training data, so longer inputs are truncated and may lose important information. The vocabulary is limited to the most frequent 30,000 tokens, meaning rare or unseen words may be mapped to an `<OOV>` token. Performance also depends on the coverage and quality of the pretrained FastText embeddings.

Additionally, the model performs binary entailment classification and does not address full three-class NLI. As a non-transformer architecture, it may be less effective than modern transformer-based models at capturing long-range dependencies and complex reasoning patterns. Finally, the model relies on a fixed probability threshold for classification, which may not be optimal under different class distributions.

## Additional Information

This model was developed as part of the COMP34812 Natural Language Understanding coursework shared task (Category B: non-transformer approaches). The hyperparameters were selected through empirical experimentation.

The implementation is organised into three notebooks:
- **train.ipynb –** trains the ESIM-inspired BiLSTM model and saves the best model  
- **evaluate.ipynb –** evaluates the model on the development set  
- **demo.ipynb –** generates predictions on unseen data in the required submission format  

The goal of this work is to demonstrate how attention-based recurrent architectures can model sentence interactions in NLI without relying on transformer-based methods.
