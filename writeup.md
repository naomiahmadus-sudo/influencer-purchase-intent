# Predicting Purchase Intent from Influencer Comments

**A natural language processing project comparing sentiment analysis against a fine-tuned transformer for detecting buyer intent in social media comments.**

## The Problem

Brands spend heavily on influencer marketing, but the relationship between engagement and sales is weak and hard to measure. A post can attract thousands of comments and still convert almost nothing, because much of that engagement is aspirational rather than transactional. 
A creator’s following is not simply filled with those looking for a recommendation, we have aspirational followers, who simply engage for the aesthetics. For a small business weighing the opportunity cost of a campaign, this gap is the central risk: there is no fast, reliable way to tell whether an influencer's audience is made of buyers or admirers.


## Research Question

Can comment sentiment patterns reliably predict purchase intent in influencer marketing, and does a model trained to detect intent outperform standard sentiment analysis?

## Data

Comments were collected from YouTube using the YouTube Data API v3, drawn from fourteen videos selected for high commercial relevance (hauls, product reviews, and get-ready-with-me content, where buyer questions concentrate). This produced a raw pool of roughly 1,800 comments.

Because purchase intent is rare, comments were hand-labeled against a custom codebook with explicit decision rules. Each comment was labeled as buy-intent (1) or not (0). Buy-intent covers transactional signals: asking where to buy, requesting prices or discount codes, asking about sizing or availability, or stating a concrete intention to purchase. Not-intent covers everything else, including compliments, aspirational comments, tags, and general praise, even when emotionally positive.

The rarity of intent is itself a finding: fewer than thirty obvious buy-intent comments surfaced on the first keyword pass through 1,800 comments. This scarcity is precisely why brands struggle to identify intent manually, and why an automated tool has value.

The final labeled dataset contained 129 comments: 71 buy-intent and 58 not-intent, kept deliberately balanced so the model could not score well by simply guessing the majority class. The set was split 80/20 into 103 training and 26 test comments, stratified to preserve the class ratio in both.

## Method

The project followed a baseline-then-improve structure.

**Baseline: VADER.** VADER, a rule-based sentiment analyzer, scored each comment, and its sentiment polarity was converted into a naive intent prediction. This establishes how far standard sentiment analysis gets on the task.

**Model: fine-tuned DistilBERT.** DistilBERT, a transformer language model pre-trained on a large corpus of English, was fine-tuned on the labeled comments with a binary classification head. Because the model already understands language broadly, it can learn the specific intent distinction from a small labeled set. Comments were tokenized at a maximum length of 140 tokens (chosen after measuring that the longest comment was 131 tokens), and the model was trained for eight epochs on a T4 GPU.

## Results

The contrast between the two approaches is the core finding.

| Approach | Accuracy | Buy-intent precision | Buy-intent recall |
|---|---|---|---|
| VADER (sentiment baseline) | 0.49 | 0.53 | 0.66 |
| DistilBERT (fine-tuned) | 0.92 | 0.93 | 0.93 |

VADER achieved 49 percent accuracy, equivalent to random guessing on a two-class problem. Its errors went in both directions: it misclassified the majority of non-intent comments as intent, misreading emotional but non-transactional language (such as compliments and admiration) as buying signals, while also missing genuine intent expressed in emotionally flat, transactional language (such as "where to buy" or "restock?"). This is the central evidence that sentiment polarity is not a valid proxy for purchase intent. The two are different signals, and the most intent-loaded comments are often the least emotional.

The fine-tuned DistilBERT model reached 92 percent accuracy, with 0.93 precision and 0.93 recall on the buy-intent class. It improved on both error types at once, rather than trading one for the other as the baseline was forced to. The training curve showed clean convergence, with validation loss falling alongside training loss across all eight epochs and no sign of overfitting.

## Cross-Platform Validation

To test whether the model generalizes beyond its training platform, it was evaluated on a separate set of 49 Instagram comments, hand-collected and labeled with the same codebook.

Accuracy fell to 71 percent, with the degradation concentrated in the buy-intent class (0.58 precision, 0.65 recall). Error analysis points to stylistic domain shift: Instagram intent is expressed through more emoji-dense, colloquial, and code-mixed language than the YouTube training data, and the model, having learned intent from YouTube's flatter phrasing, recognized it less reliably in the Instagram style.

This result strengthens rather than weakens the project. It shows the approach generalizes partially, and it identifies precisely what robust cross-platform deployment requires: platform-diverse training data. A model evaluated only in-domain would have looked stronger on paper while hiding this limitation.

## Limitations

The dataset is small. A 26-comment in-domain test set means each comment carries significant weight, so the 92 percent figure should be read as promising and indicative rather than precise. The cross-platform set is likewise small. The labeling, while codebook-driven, was performed by a single annotator, so inter-annotator agreement was not measured. These constraints are typical of an early-stage project and define the next steps rather than undermining the result.

## Future Work

Three directions follow directly from the findings. First, expand the labeled dataset and incorporate platform-diverse training data (YouTube, Instagram, and TikTok) to close the cross-platform gap measured here. Second, move from binary classification toward distinguishing aspirational engagement from genuine recommendation-seeking, which is the deeper commercial question of why an audience follows an influencer. Third, develop the model into a usable tool that helps brands evaluate influencer audiences by buying signal rather than by reach, with a free version for smaller businesses.

## Summary

The project demonstrates, with collected and hand-labeled data, that sentiment analysis is not a reliable predictor of purchase intent (49 percent, no better than chance), that a fine-tuned transformer trained specifically for intent performs substantially better (92 percent in-domain), and that cross-platform deployment introduces measurable domain shift that points to clear next steps (71 percent on Instagram). Together these form a complete arc: a baseline that fails for an understandable reason, a model that fixes it, and an honest test that reveals where the work goes next.
