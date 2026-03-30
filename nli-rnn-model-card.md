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
- **Finetuned from model [optional]:** None


### Model Resources

<!-- Provide links where applicable. -->

- **Repository:** None
- **Paper or documentation:**  
  - https://arxiv.org/pdf/1606.01933
  - https://arxiv.org/pdf/1609.06038

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
    - BiLSTM hidden units: 200 per direction  
    - batch size: 32  
    - number of epochs: 15  
    - optimizer: Adam  
    - learning rate: 8e-4  
    - gradient clipping: clipnorm = 1.0  
    - dropout:
      - BiLSTM dropout: 0.20
      - comparison layer dropout: 0.25
      - classifier dropout: 0.40 and 0.30
    - loss function: binary cross entropy
    - random seed: 42

Training also used the following callbacks:

    - EarlyStopping on validation loss
    - ReduceLROnPlateau to reduce learning rate when validation loss plateaus
    - ModelCheckpoint to save the best-performing model

#### Speeds, Sizes, Times

    - training environment: TensorFlow / Keras notebook environment
    - training epochs: up to 15 with early stopping
    - saved artifacts:
      - trained model (`best_model.keras`)
      - tokenizer (`tokenizer.pkl`)
      - configuration file (`config.json`)
    - training time depends on hardware but typically ranges from several minutes to under an hour on GPU-enabled environments

## Evaluation

### Testing Data & Metrics

#### Testing Data

Model evaluation was performed on the **development dataset (`dev.csv`)**, which contains **6,736 premise–hypothesis pairs**. This dataset was used to measure model performance and guide model selection and hyperparameter tuning.

#### Metrics

The following metrics were computed during evaluation:

    - Accuracy
    - Precision
    - Recall
    - F1-score

Predicted probabilities were converted to binary labels using a **classification threshold of 0.5**.

### Results

Development-set results from `evaluate.ipynb`:

- **Accuracy:** 0.7300  
- **Precision:** 0.7168
- **Recall:** 0.7887 
- **F1-score:** 0.7510 

These results show that the model achieves balanced overall performance on the development set, with somewhat stronger recall for the positive entailment class than for the negative class.

### Baseline Comparison

| Model | Accuracy | Macro F1 |
|------|--------|---------|
| SVM | ~0.586 | ~0.584 |
| LSTM | ~0.660 | ~0.660 |
| BERT (baseline) | ~0.820 | ~0.820 |
| Proposed ESIM Model | 0.733 | 0.731 |

## Technical Specifications

### Hardware

The model can be trained and evaluated in a standard machine learning environment.

Recommended hardware:

- RAM: 16 GB RAM
- Storage: at least 2GB (to store pretrained embeddings and saved model artifacts)
- GPU: Google Colab with NVIDIA Tesla T4 GPU

### Software

The model was implemented using the following software stack:

- Python
- TensorFlow / Keras
- NumPy
- Pandas
- Scikit-learn
- Gensim
- tqdm
- pickle and json for saving model configuration and tokenizer

## Bias, Risks, and Limitations

This model inherits potential biases present in the provided dataset, including linguistic bias, topic bias, and annotation bias. Since the dataset was supplied as part of a shared task, its data collection and labeling process are outside the control of the developers.

The model also has several technical limitations:

- sequence truncation may remove information from longer sentences
- performance depends on the coverage and quality of the pretrained FastText embeddings
- the model performs **binary entailment prediction rather than full three-class NLI classification**
- non-transformer architectures such as BiLSTM may struggle with long-range dependencies and complex reasoning tasks compared to modern transformer-based models
- the model shows slightly lower recall on class 0 than on class 1, suggesting some asymmetry in class-level behavior

Additionally, the model relies on a fixed probability threshold for classification, which may not always be optimal under different class balances or evaluation settings.

## Additional Information

This model was developed as part of the **COMP34812 Natural Language Understanding coursework shared task**. It corresponds to **Category B: deep learning-based approaches that do not use transformer architectures**.

The hyperparameters were determined by experimentation with different values.

The full implementation is organised into three notebooks:

- **`train.ipynb`** – trains the ESIM-inspired BiLSTM model and saves the best model
- **`evaluate.ipynb`** – loads the saved model and evaluates it on the development set
- **`demo.ipynb`** – runs the trained model on unseen input data and generates prediction outputs in the required submission format

The goal of this solution is to demonstrate how **attention-based recurrent architectures can model sentence interactions in NLI without relying on transformer models**.
