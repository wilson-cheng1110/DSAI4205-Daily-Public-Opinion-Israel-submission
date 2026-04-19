# Presentation Script
## DSAI4205 — Daily Public Opinion on Israel-Palestine War
### 12 Slides | 12 Minutes | 5 Speakers

---

## SLIDE 1: Title (no speaking — wait for audience)

---

## SLIDE 2: Dataset Overview & Class Distribution
**Member A (~2 min)**

> "Our dataset has 3,125 Reddit comments about the Israel-Palestine conflict, each labeled Positive, Neutral, or Negative. As the bar chart shows, it's imbalanced — Positive dominates at 44.5%, Negative is the minority at 27%. This 1.64-to-1 ratio directly causes the Negative class failure we see across every model.
>
> We found 641 encoding artifacts, 8 duplicates, 34 near-duplicates, and 277 text-length outliers. The Kruskal-Wallis test confirmed text length differs significantly across classes — Neutral comments average only 77 characters versus 327 for Positive. We retained outliers since they're genuine extended opinions."

---

## SLIDE 3: EDA Visuals
**Member A (~1 min)**

> "Three quick EDA highlights. Text lengths are right-skewed — most comments are short but some exceed 7,000 characters. Word clouds show heavy vocabulary overlap — 'Israel' and 'Palestinian' appear in all classes. But at the bigram level we see class-specific patterns: 'war crime' is strongly Negative, 'free palestine' is Positive. This overlap is why classification is inherently difficult."

*Hand over:* "Member B will cover cleaning and baseline."

---

## SLIDE 4: Cleaning & Baseline
**Member B (~2 min)**

> "Our cleaning pipeline, shown in the flowchart, takes 3,125 rows down to 2,809 through 6 enhancements — contraction expansion, Reddit pattern handling, negation preservation, and near-duplicate removal.
>
> Crucially, we created 3 text variants: fully lemmatized for TF-IDF, light-cleaned for embedding models, and minimally cleaned for BERT.
>
> The baseline uses TF-IDF with 11,857 features plus Logistic Regression. F1 of 0.66 — Neutral and Positive around 0.70, but Negative only 0.57. The confusion matrix shows 105 cases of Positive-Negative confusion — these are mixed-sentiment political arguments that even humans struggle with."

*Hand over:* "Member C will present the advanced models."

---

## SLIDE 5: Embedding-Classifier Connection
**Member C (~1.5 min)**

> "Every advanced model follows the same pattern: tokenize, embed, aggregate into a fixed-size vector, then classify. The key difference is the aggregation strategy.
>
> Mean pooling — used by Word2Vec and GloVe — averages all token vectors but loses word order and dilutes discriminative signals. BERT's [CLS] token captures context through self-attention. BiLSTM processes the full sequence preserving order. And Doc2Vec learns document vectors directly without word-level pooling."

---

## SLIDE 6: 5 Advanced Models Results
**Member C (~1.5 min)**

> "Here are the results. Word2Vec plus SVM was weakest at F1 0.38 — 39% of test words had no embedding. GloVe plus XGBoost reached 0.55 thanks to its 400K pretrained vocabulary. BERT frozen gave 0.49, fine-tuned 0.59.
>
> FastText plus BiLSTM: despite zero out-of-vocabulary words through subword handling, it reached 0.41 but still with very low Negative recall. Doc2Vec plus CNN got 0.40.
>
> The critical finding: ALL five models underperform the TF-IDF baseline of 0.66, but our ensemble stacking with 4 models reaches 0.6708."

*Hand over:* "Member D will explain why and cover imbalance handling."

---

## SLIDE 7: Why Advanced Models Underperform
**Member D (~2 min)**

> "We diagnosed 5 root causes. First, out-of-vocabulary: Word2Vec misses 39% of test vocabulary — those become zero vectors that dilute the document representation. TF-IDF misses zero by definition.
>
> Second, class separability: in TF-IDF space, the ratio is 0.734 — in Word2Vec space, only 0.015. That's 50 times worse.
>
> Third, mean pooling destroys discriminative information. A word like 'genocide' gets averaged with 30 other words, losing its strong Negative signal. TF-IDF preserves each word as its own feature.
>
> Fourth, dimensionality: 11,857 features versus 300. Fifth, data efficiency: TF-IDF works on 1,000 samples; BERT needs 10,000 or more. We have 2,247. Bottom line: on small data, sparse beats dense."

---

## SLIDE 8: Imbalance Handling
**Member D (~1 min)**

> "Five strategies for class imbalance. Class weights gave the biggest single gain — SVM jumped 0.17 F1. SMOTE improved SVM by 0.15. But focal loss didn't help the BiLSTM — you can't fix data insufficiency with loss reweighting. Text augmentation expanded Negative from 648 to 1,944 samples using synonym replacement and random deletion."

*Hand over:* "Member E for refinement and creativity."

---

## SLIDE 9: Refinement & Ensemble
**Member E (~1.5 min)**

> "GridSearchCV improved SVM the most — moving C from 1 to 10 gave a 0.13 F1 boost. LR and XGBoost were already near-optimal.
>
> For the baseline, we built a voting ensemble: LR, LinearSVC, and Ridge Classifier on the same TF-IDF features. Hard voting achieved F1 0.6560 and accuracy 0.6797 — a slight improvement over standalone LR.
>
> We also tried stacking all 6 models, but weak models added noise. The standalone baseline outperforms the stacked ensemble."

---

## SLIDE 10: Creativity
**Member E (~1 min)**

> "Three creativity elements. SHAP explains which embedding dimensions drive predictions. t-SNE and UMAP show that BERT produces the best class separation while Word2Vec forms one amorphous blob — confirming the diagnostic analysis. Error analysis found only 25% full model agreement and 16% potential label noise, setting a hard performance ceiling of about 84%."

---

## SLIDE 11: Conclusion
**Member E (~0.5 min)**

> "Five key findings. One: data quality matters more than model complexity — TF-IDF beats all deep learning. Two: small corpus kills from-scratch embeddings. Three: class weights are the best imbalance fix. Four: mean pooling destroys information — TF-IDF's 11,857 features preserve it. Five: 16% label noise caps what any model can achieve.
>
> Our recommendation: deploy TF-IDF plus LR. For future work, a domain-specific transformer on 10K+ data. Thank you."

---

## SLIDE 12: Thank You & Q&A

**Prepared answers:**

- **Q: Why not GPT/LLM?** "Project brief requires traditional ML + embedding approaches. Zero-shot LLM would be interesting future work."
- **Q: How to improve Negative recall?** "More Negative samples, better annotations, domain-specific pretrained model."
- **Q: Why doesn't ensemble beat baseline?** "4 of 5 base models are weaker. Correlated errors on Negative class add noise."
- **Q: Is 0.66 F1 good?** "For 3-class political Reddit sentiment with 16% label noise, published benchmarks are 0.55-0.70."

---

## Timing Summary

| Slides | Speaker | Time |
|--------|---------|------|
| 2-3 | Member A | 3:00 |
| 4 | Member B | 2:00 |
| 5-6 | Member C | 3:00 |
| 7-8 | Member D | 3:00 |
| 9-11 | Member E | 3:00 |
| **Total** | | **~12:00** |
