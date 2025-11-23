# Model Card for Dutch Emotion Classifier

## Model Overview

### Architecture
**Model Name:** Dutch Emotion Classifier  
**Model Type:** Transformer-based model (BERT)  
**Base Model:** `wietsedv/bert-base-dutch-cased` (BERTje)  
**Model Architecture:** BertForSequenceClassification with 7 output classes  
**Task:** Multi-class Emotion Classification in Dutch

### Purpose
This model is developed to be used for automated emotion labeling of audio in Dutch-language media to enhance media analysis and content evaluation for editorial purposes.

### Development Context

**Key Assumptions:**
- Sentence-level classification sufficient for media analysis.
- Training data (reality TV, legal shows) representative of target deployment content.
- Seven-class emotion model captures key emotions needed for editorial work.

**Constraints:**
- Limited training data (10,402 samples) due to annotation resource constraints.
- Natural class imbalance reflects real-world emotion distribution.
- **Computational budget:** Local/university GPU infrastructure only

**Development Conditions:**
- **Hardware**: NVIDIA T4 GPU (16GB VRAM).
- **Training time:**~3 hours for 3 epochs.
- **Annotation:** Student team + CIA pipeline for initial labels.
- **Quality control:** Manual verification of all emotion labels.

---

## Intended Use

### Primary Use Case
**Automated labeling of emotional tone in Dutch-language media.**

This model provides valuable insights for media professionals by:
- **Media Intelligence Workflows:** Automatically detecting emotional tone across scenes.
- **Content Analysis:** Analyzing emotional patterns in Dutch television content.
- **Editorial Support:** Tagging emotionally charged segments for creative teams.
- **Scene Classification:** Identifying emotionally significant moments.
### Secondary Use Cases
- Emotion detection in Dutch social media content.
- Sentiment analysis in Dutch customer feedback.
- Emotional tone analysis for conversational AI systems.

### Where NOT to Use

**The model should NOT be used for:**
- Clinical, legal, or critical decision-making without human oversight.
- Content in languages other than Dutch.
- Autonomous editorial decisions (high false negatives).
- Dialectal Dutch or content mixed with Dutch and another language.

### Suitability Assessment

**Suitable For:**
- Dutch television content (reality TV, talk shows, legal shows).
- Sentence-level emotion classification.
- Detecting explicit emotional expressions.
- Content with conversational/argumentative style.

**Not Suitable For:**
- Multi-lingual content
- Highly formal written text (academic, legal documents).
- Implicit emotions, sarcasm, irony or figurative language.
- Real-time conversational discourse analysis.

---

## Dataset Details

### Training and Evaluation Data

**Training Dataset:**
- **Size:** 10,402 lines from Dutch reality TV and legal shows.
- **Sources:** Temptation Island, Expeditie Robinson, Big Brother, Kees van der Spek; Oplichters Aangepakt.
- **Split:** 80% training (8,322) / 20% validation (2,081).
- **Annotation:** CIA pipeline with manual student verification.

**Test Dataset:**
- **Size:** 827 lines.
- **Source:** Meester Frank Visser doet uitspraak.
- **Purpose:** Tests cross-domain generalization.
- **Distribution:** Natural class imbalance (444 neutral, 170 anger, 117 happiness, etc.).

### Data Preprocessing Pipeline

#### 1. Emotion Normalization
50+ complex emotions mapped to 7 standard classes:

| Primary Emotion | Mapped Sub-emotions |
|----------------|---------------------|
| Happiness | joy, optimism, approval, pride, gratitude, confidence, satisfaction, hope, love, excitement, caring, relief, admiration, amusement, anticipation, encouragement, desire |
| Sadness | disappointment, nostalgia, remorse, pain, stress, regret, resignation, despair, confusion, uncertainty |
| Anger | annoyance, disapproval, frustration, disbelief, warning, rejection |
| Disgust | disgust |
| Fear | nervousness, worry, anxiety, doubt, insecurity, urgency, panic |
| Surprise | realization, shock, amazement, wonder |
| Neutral | mixed, trust, agreement, instruction, suggestion, request, confirmation, acceptance, reassurance, clarification, understanding, certainty, curiosity |

#### 2. Manual Verification
Students verified emotion labels for consistency and accuracy across the dataset.

#### 3. Train/Test Split Strategy

- **Training Set (10,402 lines):** Includes transcriptions from multiple reality TV shows (*Temptation Island*, *Expeditie Robinson*) and legal shows, providing diverse emotional contexts and speaker patterns
  - Further split into 80% training (8,322 lines) and 20% validation (2,081 lines) using stratified sampling to maintain class distribution.
- **Test Set (827 lines - full dataset):** Exclusively drawn from *Meester Frank Visser doet uitspraak*, a legal show **not represented in the training data**.
  - Contains natural class distribution: 444 neutral, 170 anger, 117 happiness, 39 sadness, 28 surprise, 15 disgust, 14 fear.
  - Emotions were classified using the CIA's pipeline from a 15 minute part of the video.
- **Rationale:** This source-based split tests the model's ability to generalize to unseen content domains rather than merely memorizing training examples.
- **Temporal Independence:** Test data was collected separately to simulate real-world deployment where the model encounters new episodes and speakers.


#### 4. Data Cleaning
Both training and test datasets underwent quality control:
   - Filtered for duplicate entries to prevent data leakage
   - Standardized formatting across datasets for consistent processing
   - Removed inconsistent or ambiguous annotations flagged during manual review
   - Validated emotion label distributions to identify potential annotation errors
   - Converted all text to string type to handle potential null values

#### 5. Synthetic Augmentation
Added synthetic examples for severely underrepresented class, Disgust.

#### 6. Tokenization & Input Preprocessing
- **Tokenizer:** BERTje tokenizer (`wietsedv/bert-base-dutch-cased`)
- **Max Length:** 128 tokens
- **Padding:** Applied to all sequences.
- **Special Tokens:** `[CLS]` and `[SEP]`
- **Case Preservation:** Keep original capitalization to help the model correctly identify names, places, and other entities in Dutch.

### Dataset Strengths
- Authentic Dutch television transcripts.
- Manual verification ensuring label quality.
- Consistent emotion labeling via CIA pipeline.
- Diverse reality TV and legal shows.

### Dataset Limitations & Mitigations

1. **Class Imbalance**
   - **Issue:** Neutral class dominates; Disgust, Fear, Surprise underrepresented.
   - **Justification:** Mirrors real-world emotion frequency.
   - **Mitigation:** Synthetic examples added for minority classes.
   - **Acknowledgment:** Performance on minority classes remains challenging.

2. **Dialect Coverage**
   - **Issue:** Limited representation of Dutch dialects.
   - **Justification:** Dataset from national broadcast media using standard Dutch.
   - **Limitation:** May not generalize to dialectal or informal speech.

3. **Transcription Errors**
   - **Issue:** Occasional inaccuracies in transcripts.
   - **Justification:** Mixed-quality audio and varied speaker clarity.
   - **Mitigation:** Manual student feedback.
   - **Limitation:** Potential distortion of emotion cues.

### Language Diversity Considerations
- **Scope:** Standard Dutch only.
- **Bias:** Towards nationally broadcast media language.
- **Limitation:** No coverage of Flemish Dutch, Frisian, or Dutch dialects.
- **Multilingual Challenges:** Not ideal for transcription that contain Dutch and other languages.

---

## Performance Metrics and Evaluation

### Training Progress

| Epoch | Train Loss | Train Accuracy | Validation Accuracy | Validation F1 (Weighted) |
|-------|-----------|---------------|-------------------|------------------------|
| 1 | 1.1072 | 61.13% | 64.92% | 60.74% |
| 2 | 0.7688 | 72.92% | 67.56% | 65.30% |
| 3 | 0.4765 | 84.05% | 64.34% | 64.84% |

### Final Test Performance
- **Test Accuracy:** 62.15%
- **Test F1 (Weighted):** 61.67%

### Per-Class Performance (Final test set)

| Emotion | Precision | Recall | F1-Score | Support |
|---------|----------|--------|----------|---------|
| Anger | 0.58 | 0.50 | 0.54 | 170 |
| Disgust | 0.00 | 0.00 | 0.00 | 15 |
| Fear | 0.18 | 0.29 | 0.22 | 14 |
| Happiness | 0.55 | 0.48 | 0.51 | 117 |
| Neutral | 0.74 | 0.79 | 0.77 | 444 |
| Sadness | 0.20 | 0.28 | 0.24 | 39 |
| Surprise | 0.21 | 0.21 | 0.21 | 28 |

### Performance Analysis

**Model Strengths:**

1. **Strong Majority Class Performance:** Achieves 74% precision and 79% recall on neutral class, reliably identifying the most common emotion in media content.
2. **Moderate Performance on Common Emotions:** Anger (58% precision) and Happiness (55% precision) show usable performance for well-represented classes.
3. **Efficient Processing:** 128-token input length enables fast inference suitable for real-time or batch processing of large datasets.
4. **Cross-Domain Generalization:** Generalizes from reality TV to legal dispute content with 62% accuracy, showing robustness within Dutch television genres.

**Model Challenges:**

1. **Critical Minority Class Failures:** Complete failure on Disgust (0% precision/recall), and poor performance on fear (18%) and surprise (21%), meaning the model cannot detect these emotions.
2. **Significant Overfitting:** 22-point gap between training (84%) and test accuracy (62%) indicates memorization rather than generalization.
3. **Conservative Predictions:** Lower recall across most classes means the model frequently misses emotion instances that actually occur.

### Implications for Deployment:

**Recommended Use:**
- **Neutral content filtering** with 79% recall for workflow efficiency.
- **Preliminary emotion detection** for anger/happiness requiring human verification.
- **Weak label generation** for active learning workflows.

**Not Recommended:**
- Automated content moderation (misses minority emotions).
- Autonomous editorial decisions (no confidence scores, high false negatives).
- Trend analysis requiring comprehensive emotion coverage (skewed toward neutral/common emotions).

**Required Operational Constraints:**
- Mandatory human review for all minority emotion predictions (Fear, Disgust, Surprise).
- Quality assurance sampling (10-20%) of all predictions.
- Class-specific confidence thresholds when implemented (Neutral >0.7, Anger/Happiness >0.8).
- Domain validation before deployment on new content types.

### Error Analysis

**Identified Failure Patterns:**
- **Neutral overbias:** Subtle or sarcastic inputs misclassified as Neutral.
- **Poor minority recognition:** Especially Disgust, Fear, Surprise.
- **Sentence length sensitivity:** Context across long sentences often lost.

**Example Error:**
> "Als ze van de trap af valt, zou ik het niet erg vinden."  
> (If she falls down the stairs, I wouldn't mind.)  
> **True Label:** Anger — **Predicted:** Neutral

More detailed analysis found on this [page](https://github.com/BredaUniversityADSAI/2024-25c-fai2-adsai-group-team_4_y2c/blob/main/Datalabtasks/Task8/Error_Analysis.pdf).

### Bias Assessment

**Identified Biases:**

1. **Domain Bias**
   - Reality TV (confrontational, emotional).
   - Legal disputes (formal, argumentative).
   - May not generalize to news, documentaries and/or entertainment.

2. **Emotional Context Bias**
   - Favors explicit expressions over subtle cues.
   - Struggles with sarcasm and passive-aggressive language figurative language (e.g., metaphors or idioms).

3. **Speaker Demographics**
   - Reality TV skews toward younger adults (25-45).
   - Potential bias against children, elderly, or specific cultural groups.
4. **Language Variation Bias**
   - Bias toward contemporary spoken Dutch.
   - Limited understanding of regional dialects.

---

## Explainability and Transparency
We conducted an XAI analysis on a fine-tuned Dutch BERT model using three methods:

- Gradient × Input
- Layer-wise Relevance Propagation (LRP)
- Input Perturbation 

Findings:

- Emotionally loaded verbs (e.g., "haten", "houden van") influence output.
- LRP yielded highest clarity in token attributions.
- Insightful for debugging misclassifications.


**Key recommendations:** Include using a Dutch-optimized tokenizer and applying threshold-based filtering to improve interpretability. 

A detailed XAI analysis with graphs explaining the model's decision-making is included in [this link](https://github.com/BredaUniversityADSAI/2024-25c-fai2-adsai-group-team_4_y2c/blob/main/Datalabtasks/Task9/XAI_Analysis.md) and in [this link](https://github.com/BredaUniversityADSAI/2024-25c-fai2-adsai-group-team_4_y2c/blob/main/Datalabtasks/Task9/Explainable_AI.pdf).

---

## Recommendations for Use

### Deployment Guidelines

**Recommended Use Cases:**
- Filtering neutral content (79% recall) to save time in workflows.
- Quick emotion detection for anger or happiness, but have humans double-check.
- Creating weak labels to support active learning.

**Not Recommended For:**
- Fully automated content moderation (can miss less common emotions).
- Making editorial decisions without human oversight (no confidence scores, many false negatives).
- Trend analysis that needs all emotions to be captured.

### Required Operational Constraints

**Quality Checks:**
- Humans must review all predictions for less common emotions (Fear, Disgust, Surprise).
- Quality sampling (10-20%) of all predictions.
- Class-specific confidence thresholds:
  - **Neutral:** >0.7
  - **Anger/Happiness:** >0.8
  - **Minority classes:** Always require human verification.

**Domain Validation:**
- Test the model on a sample of the new content type before full use.
- Monitor on performance over time.
- Retrain when accuracy drops below acceptable thresholds.

### Integration Guidelines for Media Companies

**Workflow Integration:**
1. **Batch Processing:** Process transcripts in batches during off-peak hours.
2. **Human-in-the-Loop:** Flag low-confidence predictions for manual review.
3. **Active Learning:** Use predictions to prioritize human annotation efforts.
4. **Performance Monitoring:** Track per-class accuracy monthly.

---

## Sustainability Considerations

### Training Phase Environmental Impact

**Energy Consumption Analysis:**

| Component | Value | Calculation |
|-----------|-------|-------------|
| Hardware | NVIDIA T4 GPU (70W TDP) | Manufacturer specs |
| Training Duration | ~3 hours (3 epochs) | Measured |
| Energy Consumption | 0.21 kWh | 70W × 3h |
| CO₂ Emissions | 0.073 kg CO₂e | 0.21 kWh × 0.485 kg/kWh |
| Equivalent Impact | ~0.4 km by car | EPA factors |

**Context:**
- Equivalent to charging a smartphone 18 times.
- 0.02% of average EU citizen's daily carbon footprint.

### Inference Phase Impact

**Per-Prediction Cost:**
- **Energy per prediction:** ~0.0005 kWh (batch size 16)
- **CO₂ per 1000 predictions:** ~0.017 g CO₂e
- **Annual estimate (100K predictions):** ~1.7 kg CO₂e

**Scalability:**
For 1 million segments annually:
- **Annual inference energy:** ~50 kWh
- **Annual inference CO₂:** ~17 kg CO₂e
- **Equivalent to:** ~90 km driven

### Sustainability Practices Implemented

**During Training:**
1. **Efficient Architecture:** 128-token length (75% reduction vs 512).
2. **Transfer Learning:** Fine-tuning vs training from scratch (95% energy savings).
3. **Batch Optimization:** Balanced throughput with memory efficiency.

**Not Yet Implemented:**
- **Gradient checkpointing:** Memory optimization.
- **Early Stopping:** To prevent overfitting. 

### Recommendations for Minimizing Impact

**For Training:**
1. **Use Green Cloud Infrastructure:**
   - Google Cloud (Europe-west1): 60% lower carbon intensity.
   - AWS (eu-north-1): 95% renewable energy.
   - Azure (West Europe): 100% renewable by 2025.

2. **Implement Carbon Tracking:**
   - Use `codecarbon` library for monitoring.
   - Track per-epoch energy for early stopping decisions.

3. **Optimize Hyperparameters:**
   - Learning rate scheduling and early stopping.
   - Consider smaller batch sizes for marginal savings.

4. **Strategic Scheduling:**
   - Train during off-peak renewable energy hours.
   - Netherlands wind energy peaks at night.

---

## Limitations

### Performance Limitations
- **Critical Minority Class Failures:** Complete failure on Disgust (0%), poor on Fear (18%) and Surprise (21%).
- **Severe Overfitting:** 22-point gap between training (84%) and test accuracy (62%).
- **Low Recall:** Misses 50%+ of actual Anger and Happiness instances.

### Technical Limitations
- **No Confidence Calibration:** Cannot filter unreliable predictions.
- **Single-Sentence Context:** No discourse or conversational understanding.

### Linguistic Limitations
- **Sarcasm Blindness:** Does not understand sarcasm.
- **Lexical Ambiguity:** Defaults to most frequent word sense from training.
- **No Intensity Detection:** Cannot distinguish mild vs. intense emotions.

### Domain & Language Limitations
- **Dutch Only:** No multilingual or mixed-language capability.
- **Standard Dutch Bias:** Limited dialect coverage.
- **Domain-Specific:** Reality TV/legal training may not generalize to news or formal content.

### Deployment Limitations
- **GPU Required:** CPU too slow for production use.
- **Unsuitable for High-Stakes:** Requires mandatory human oversight.
- **Small Dataset:** 10,402 samples insufficient for robust NLP.

---

## Future Work

- Introduce multilingual and dialectal variation.
- Improve sarcasm/irony detection.
- Add post-processing rules and human-in-the-loop fallbacks.
- Enable energy tracking during deployment.
- Develop confidence calibration for production use.

---

## Versioning & Contact

**Model Version:** 1.0  
**Authors:** Jonas Vos, Ayumi Chotoe  
**Institution:** Breda University of Applied Sciences  
**Last Updated:** October 2025  

---

## Citation & Acknowledgments

**Data Source:** Student-generated transcription dataset from Breda University of Applied Sciences, collected in 2024
**Pretrained Model:** BERTje transformer model (de Vries et al.)