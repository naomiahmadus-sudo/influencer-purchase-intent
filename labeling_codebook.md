# Labeling Codebook: Buy-Intent Classification

The rule set used to hand-label comments as buy-intent (1) or not (0). Built to keep labeling consistent across sessions and to document the decision logic.

## Core Decision Rule

Does the comment show the person moving toward a purchase, asking how, where, how much, or signaling concrete desire to own the product? Yes is 1, no is 0. Emotion is irrelevant. Intent is not a feeling.

## Buy-Intent (1)

- Buying logistics: where to get it, link?, how to order, does it ship, in stock?
- Price and money: how much, price?, discount code?, any sale
- Buyer's product questions: does it come in [color], what sizes, true to size?
- Concrete ownership desire: need this, buying now, take my money
- Availability and urgency: restock when?, sold out, still available?
- Source-seeking dressed as praise: where did you get this?!, where is that dress from?

## Not-Intent (0)

- Pure compliment: you look stunning, gorgeous, so pretty
- Aspirational or vibes: goals, obsessed, iconic
- Tag with no intent: tagging a friend with no buy language
- Generic engagement: first, love this, clean transition
- Praise of the influencer: queen, you ate

## Edge Cases (decided in advance)

| Case | Example | Call |
|---|---|---|
| Tag plus buy language | "let's order these" | 1 |
| Tag alone | a friend tagged, no buy language | 0 |
| Wanting plus stated barrier | "wish I could afford this" | 0 |
| Product compliment, no buy language | "this is gorgeous" | 0 |
| Asking the source | "where did you get this?!" | 1 |
| Giveaway entry | "how do I enter the giveaway" | 0 |

## Discipline Rules

1. When genuinely 50/50, label 0. A false positive pollutes the signal more than a miss.
2. Log every new edge call as it comes up, to stay consistent and to document reasoning.
3. When resuming a session, re-read the last ten labels before continuing, to prevent drift.
