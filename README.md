# ThreatDataLink

# SemanticLink

**SemanticLink** is a Siamese RoBERTa-large neural network for automated mapping of CVE (Common Vulnerabilities and Exposures) descriptions to MITRE ATT&CK techniques. It combines Semantic Role Labeling (SRL) structural markup with cross-attention and contrastive learning to produce calibrated, interpretable similarity scores between vulnerability descriptions and threat technique definitions.

---

## Architecture Overview

SemanticLink is built around three core design contributions:

**1. SRL-Augmented Encoding**
CVE and ATT&CK technique texts are preprocessed with Semantic Role Labeling markup before encoding. This provides structural scaffolding: annotating predicate-argument structure (agents, actions, targets), so the encoder learns role-aware representations rather than treating input as flat token sequences.

**2. Cross-Attention (ESIM-style)**
A cross-attention module (inspired by ESIM) allows the CVE and technique representations to attend to each other before the final similarity scoring step, capturing fine-grained alignment between role spans.

**3. Contrastive Loss + Platt Calibration**
The model is trained with contrastive loss to push semantically similar (CVE, technique) pairs together and dissimilar pairs apart in embedding space. Platt scaling is applied post-training to calibrate raw similarity scores into reliable probabilities.

```
CVE Description ──► SRL Markup ──► RoBERTa-large ──┐
                                                      ├──► Cross-Attention ──► Similarity Score ──► Platt Calibration
ATT&CK Technique ──► SRL Markup ──► RoBERTa-large ──┘
```

---

## Key Features

- **SRL structural scaffolding** as the core contribution: improves both mean performance and training stability (variance reduction across seeds)
- **ESIM-style cross-attention** for role-span aligned comparison
- **Contrastive loss** for discriminative embedding training
- **Platt calibration** for well-calibrated output probabilities
- **Multi-seed evaluation** with Wilcoxon significance testing against fine-tuned baselines (SBERT, SecBERT)
- **Full ablation suite**: Full model vs. without cross-attention vs. without contrastive loss
- **Interpretability**: Role-span aggregated Integrated Gradients panels for explainability and attention visulization

---

## Repository Structure

```
semanticlink/
├── model/                  # Siamese RoBERTa-large model definition
│   ├── semanticlink.ipynb     # Core model: encoder, cross-attention, contrastive loss
│   
│
├── data/
│   ├── preprocessing/      # SRL markup pipeline for CVE and ATT&CK texts
│   └── negative_mining/    # TF-IDF hard negative mining for contrastive training
│
├── baselines/              # Fine-tuned encoder baselines
│   ├── sbert_baseline.ipynb   # SBERT: encoder + mean pool + linear head
│   ├── secbert_baseline.ipynb # SecBERT: encoder + mean pool + linear head
│   └── secRoBERTa_baseline.ipynb  # AttackBERT: encoder + mean pool + linear head
```

---

## Installation

```bash
git clone https://github.com/<your-username>/semanticlink.git
cd semanticlink
```

**Requirements include:**
- Python 3.9+
- PyTorch ≥ 2.0
- Hugging Face Transformers
- scikit-learn
- scipy (Wilcoxon testing)


## License

[MIT License](LICENSE)

---

## Acknowledgements

- [MITRE ATT&CK](https://attack.mitre.org/) for the threat technique taxonomy
- [Hugging Face](https://huggingface.co/) for RoBERTa-large and SecBERT/RoBERTa model weights
