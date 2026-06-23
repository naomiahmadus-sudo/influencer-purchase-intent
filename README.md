
# Predicting Purchase Intent from Influencer Comments

Detecting buyer intent in social media comments, comparing rule-based sentiment analysis against a fine-tuned transformer.

## TL;DR

Brands spend heavily on influencer marketing but cannot easily tell whether an audience is made of buyers or admirers. The common shortcut is to read comment sentiment. This project shows that shortcut fails, and that a model trained specifically for purchase intent does far better.

| Approach | Accuracy | Buy-intent precision | Buy-intent recall |
|---|---|---|---|
| VADER (sentiment baseline) | 0.49 | 0.53 | 0.66 |
| DistilBERT (fine-tuned) | 0.92 | 0.93 | 0.93 |
| DistilBERT on Instagram (cross-platform) | 0.71 | 0.58 | 0.65 |

Sentiment analysis performs no better than chance (0.49). A fine-tuned DistilBERT reaches 0.92 in-domain. Tested across platforms on Instagram, accuracy drops to 0.71, revealing measurable domain shift and pointing to clear next steps.

## The Idea

Purchase intent is not the same as positive sentiment. The most intent-loaded comments are often the least emotional: "where can I buy this", "what's the price", "restock?". Sentiment tools score these as neutral while flagging emotional but non-transactional comments ("you look stunning") as positive. This project builds a model that detects intent rather than emotion.

## Data

Comments were collected from YouTube via the YouTube Data API v3, from fourteen videos chosen for commercial relevance (hauls, reviews, get-ready-with-me). From a raw pool of roughly 1,800 comments, a balanced labeled set of 129 was hand-built (71 buy-intent, 58 not-intent) using a custom labeling codebook with explicit decision rules.

Purchase intent proved rare, fewer than thirty obvious cases on the first pass through 1,800 comments, which is itself why brands struggle to spot it manually.

A representative anonymized sample is in [`sample_labeled_comments.csv`](sample_labeled_comments.csv). The full dataset is withheld for privacy and in line with platform data policies. All names and handles in the sample have been removed.

## Method

1. **Baseline (VADER):** rule-based sentiment scores converted to intent predictions, to measure how far standard sentiment analysis gets.
2. **Model (DistilBERT):** a pre-trained transformer fine-tuned with a binary classification head, tokenized at 140 tokens, trained eight epochs on a T4 GPU.

## Results in Brief

VADER misreads the task in both directions: it flags emotional non-intent comments as buyers and misses flat transactional intent. DistilBERT improves both error types at once, with clean training convergence and no overfitting.

## Cross-Platform Validation

The YouTube-trained model was tested on 49 hand-labeled Instagram comments. Accuracy fell to 0.71, concentrated in the buy-intent class. The cause appears to be stylistic domain shift: Instagram intent is more emoji-dense, colloquial, and code-mixed than YouTube's. This identifies what robust deployment needs, platform-diverse training data, and is reported here as a finding rather than hidden.

## Limitations

Small dataset (26-comment in-domain test set), single annotator, small cross-platform set. The 0.92 figure is indicative rather than precise. These define the next steps.

## Future Work

- Expand and diversify training data across YouTube, Instagram, and TikTok to close the cross-platform gap.
- Move beyond binary classification toward separating aspirational engagement from genuine recommendation-seeking.
- Develop into a usable tool that ranks influencer audiences by buying signal rather than reach, with a free tier for small businesses.

## Files

- [`writeup.md`](writeup.md) full project write-up
- [`sample_labeled_comments.csv`](sample_labeled_comments.csv) anonymized labeled sample
- [`labeling_codebook.md`](labeling_codebook.md) labeling rules and edge cases
- [`notebook.ipynb`](buyintent.ipynb) Colab Notebook
