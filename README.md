## Community

r/VALORANT is a general discussion subreddit for Riot's tactical shooter, where posts range from competitive analysis and patch reactions to gameplay clips and ranked frustrations. The community spans casual players, competitive climbers, and amateur analysts, which means its discourse is highly varied in tone and depth. This made it a productive source for distinguishing between emotional reactions and reasoned arguments — both types of posts appear frequently, and the community itself already debates which belongs on the main subreddit versus more competitive-focused communities like r/ValorantCompetitive. That live, contested quality means the two labels reflect a distinction players already have strong intuitions about, which made annotation more principled and less arbitrary.

---

## Label Taxonomy

### Insight
A post qualifies as Insight if it makes a specific claim about the game — about agent balance, mechanics, map design, or strategy — and supports that claim with evidence, statistics, observation, or structured reasoning. The claim must be directed at the game or community broadly, not just an expression of the poster's personal experience.

**Example 1 — "Valorant rank distribution is kind of wild when you actually look at it"**
This post cites actual rank distribution percentages and argues that Gold rank players are already statistically better than average, even if it does not feel that way. The specific data and the broader claim directed at the playerbase make this a clear Insight.

**Example 2 — "If Killjoy becomes Meta, Veto is the perfect counter"**
This post presents three distinct reasons why one agent counters another, citing their specific abilities and the conditions under which they interact. The structured multi-point argument grounded in game mechanics is a textbook Insight.

---

### Vent
A post qualifies as Vent if it expresses frustration or anger — typically a complaint about teammates, matchmaking, or game mechanics — without making a specific argument that could be evaluated as true or false. The primary purpose of a Vent post is emotional expression, not persuasion or analysis.

**Example 1 — "Miks seems like the most lazy agent ever, Riot is truly running out of ideas"**
This post expresses frustration that a new agent's abilities repeat existing ones and accuses the developers of minimal effort. While it makes an implicit observation about game design, the post does not support that claim with ability comparisons or evidence — it asserts the conclusion without building toward it. The primary purpose is to vent indignation, not persuade.

**Example 2 — "The Whiplash in Diamond elo is insane"**
This post expresses frustration about inconsistent matchmaking in the Diamond rank. It describes the personal experience of volatile wins and losses but does not make a specific testable argument about the matchmaking system that would apply to other players.

---

## Data Collection

**Source:** All examples were collected from r/VALORANT. Posts were selected to cover a range of competitive discussion, patch reactions, rank complaints, and gameplay opinion threads. To avoid sampling only easy examples, collection deliberately included posts with varied titles, lengths, and levels of evidence.

**Labeling process:** Each post was labeled based on its dominant intent: Vent for posts whose primary purpose was emotional expression, Insight for posts whose primary purpose was to make a game-relevant argument with evidence. For edge cases where a post contained both emotional language and an argument, the label was assigned based on whichever purpose was dominant. Posts that remained genuinely split were marked as uncertain for later review.

**Label distribution:**

| Split | Vent | Insight | Total |
|---|---|---|---|
| Training | 82 | 58 | 140 |
| Test | 18 | 12 | 30 |
| **Total** | **100** | **70** | **170** |

---

### Difficult-to-Label Examples

**Example 1 — "Add Chamber Headhunter to Deathmatch!"**
*Label assigned: Insight (uncertain)*

This post suggests adding a specific weapon to a game mode and gives a reason grounded in personal experience playing Chamber. It sits at the boundary because it does make a specific game-design claim — that the weapon should be available to all players in Deathmatch — but the evidence is entirely personal rather than community-directed, and the first-person framing ("I play Chamber a lot") pattern-matches to Vent. I labeled it Insight because the claim is outward-facing (a suggestion about how the game should work, not just about the poster's experience) rather than expressing frustration, but I flagged it as uncertain because the argument is thin and the framing is ambiguous.

**Example 2 — "Just to air frustrations, look at my W/L gains and losses"**
*Label assigned: Vent (uncertain)*

This post vents about the difficulty of climbing ranks because losses are penalized more heavily than wins. The poster includes a screenshot of their match history as evidence, which blurs the line — using data to support a point is a feature of Insight. I labeled it Vent because the primary purpose is expressing frustration about personal rank experience, not making a broader claim about the ranking system that would apply to other players. The title's "just to air frustrations" phrasing signals that the intent is emotional expression, even though evidence is present.

**Example 3 — "Miks seems like the most lazy agent ever, Riot is truly running out of ideas"**
*Label assigned: Vent*

This post is difficult because it contains an implicit specific claim — that Miks has abilities that duplicate existing agents — which is falsifiable and could read as an Insight about game design. A strict reading might justify that label. I assigned Vent because the post does not attempt to support the observation: it asserts the conclusion without naming which abilities are duplicates or comparing them to prior agents. The difference from Insight is that the poster is not trying to persuade — they are expressing indignation. The claim is incidental to the frustration, not the point of the post.

---

## Fine-Tuning Approach

**Base model:** `distilbert-base-uncased` — a distilled version of BERT that retains approximately 97% of its language understanding at 40% fewer parameters. It was chosen because the classification task is binary and the inputs are short text posts, which does not require a full BERT-scale model, and because a smaller model is less prone to overfitting on a dataset of around 200 examples.

**Training setup:**

| Parameter | Value |
|---|---|
| Base model | distilbert-base-uncased |
| Task | Sequence classification (binary) |
| Epochs | 3 |
| Batch size | 16 |
| Learning rate | 2e-5 |
| Max sequence length | 256 |
| Optimizer | AdamW |

---

## Baseline

**Prompt used:**

You are classifying posts from r/VALORANT, a subreddit for Riot's tactical shooter game.
Assign each post to exactly one of the following categories.

Insight: The post makes a specific claim about the game — balance, mechanics, or strategy — and backs it up with evidence, statistics, or reasoned observation.
Example: "Valorant rank distribution is kind of wild — being in Gold already puts you above average. Most players think they're stuck in the middle, but the data says otherwise."

Vent: The post expresses frustration or anger about teammates, matchmaking, or game mechanics without making a reasoned argument — the dominant intent is emotional, not analytical.
Example: "Miks is the laziest agent Riot has ever released. Every single ability is just a reskin of something we already have. They are completely out of ideas."

If a post mixes both, label it by its dominant intent. If the user is primarily concerned with their own performance or rank, label it Vent. If the post makes a broader observation about the game that would affect players generally, label it Insight.

Your response must be one of these two strings, exactly as written, with no punctuation, no explanation, and no additional text:
Insight
Vent

**How results were collected:** The baseline classifier was implemented using the Groq API with the llama-3.3-70b-versatile model. Predictions were generated by providing a detailed SYSTEM_PROMPT that defined the 'Insight' and 'Vent' labels, including examples and explicit instructions for the model to output only the label name. The classify_with_groq function then sent each test post to the Groq API, processed the model's raw text response (converting it to lowercase), and mapped it to the LABEL_MAP by checking for exact matches or substrings. Finally, accuracy was calculated by comparing these predicted labels against the true labels of the test set using sklearn.metrics.accuracy_score, providing an overall baseline accuracy score and per-class metrics through a classification report.

The baseline achieved **90% accuracy** on the 30-example test set, correctly classifying approximately 27 of 30 examples. Per-class metrics were not recorded for the baseline run.

---

## Evaluation Report

### Overall Accuracy

| Model | Accuracy | Test Set Size |
|---|---|---|
| Baseline | 90% | 30 |
| Fine-Tuned (DistilBERT) | 60% | 30 |

Fine-tuning reduced accuracy by 30 percentage points. The fine-tuned model underperformed because it converged to always predicting the majority class (Vent), which constitutes 60% of the test set.

---

### Per-Class Metrics

**Baseline Model:** Per-class metrics were not recorded. Given 90% accuracy on a 30-example test set (18 Vent, 12 Insight), approximately 27 of 30 examples were correctly classified. Without a per-class breakdown, it is not possible to determine whether errors were distributed across both classes or concentrated in one.

**Fine-Tuned Model:**

| Class | Precision | Recall | F1-Score | Support |
|---|---|---|---|---|
| Vent | 0.60 | 1.00 | 0.75 | 18 |
| Insight | 0.00 | 0.00 | 0.00 | 12 |
| **Macro avg** | 0.30 | 0.50 | 0.38 | 30 |
| **Weighted avg** | 0.36 | 0.60 | 0.45 | 30 |

The fine-tuned model predicted Vent for every example. This produced perfect recall for Vent (18/18) but zero recall for Insight (0/12). The 0.60 precision for Vent reflects the class ratio in the test set, not genuine discriminative ability.

---

### Confusion Matrix

|  | **Predicted: Vent** | **Predicted: Insight** |
|---|---|---|
| **True: Vent** | 18 | 0 |
| **True: Insight** | 12 | 0 |

Every Insight post was classified as Vent. No Vent post was misclassified. The model did not learn the Insight class at all.

---

### Wrong Predictions

All 12 errors are Insight posts predicted as Vent — the confusion is entirely one-directional. Many of these posts opened with informal or emotionally reactive language ("kind of wild", "is insane") that overlaps heavily with Vent surface patterns, but the model also failed on structurally unambiguous Insight posts, indicating collapse rather than boundary confusion.

---

**Example 1 — "Valorant rank distribution is kind of wild when you actually look at it"**

*True label:* Insight | *Predicted:* Vent

This post cites actual rank distribution percentages and makes a specific factual argument: that players in Gold rank are already better than average, even though it does not feel that way. The claim is data-backed and addressed to the community broadly rather than expressing personal frustration.

*Why it failed:* The framing — "kind of wild" — reads as informal and emotionally reactive. DistilBERT fine-tuned on a small dataset learns surface-level patterns, and phrases that signal surprise or disbelief overlap heavily with frustrated Vent language. The model likely never learned that citing rank statistics distinguishes this post structurally, because it did not see enough Insight examples to internalize that distinction.

*What would fix it:* More training examples of Insight posts that use informal or surprised language but still contain data. The problem is not annotation inconsistency — this is a clear Insight — it is that the training set underrepresented this style of evidence-backed post.

---

**Example 2 — "If Killjoy becomes Meta, Veto is the perfect counter"**

*True label:* Insight | *Predicted:* Vent

This post presents three distinct reasons why one agent counters another, citing their specific abilities. It is a structured argument with no complaint about matchmaking, teammates, or Riot. The post is nearly a textbook Insight example.

*Why it failed:* This is the clearest evidence that the model collapsed entirely rather than failing at the boundary. A post this unambiguously analytical should not be misclassified unless the model stopped making content-based predictions altogether. Training pushed its output distribution so far toward one class that no surface feature of the text could pull it back.

*What would fix it:* Fixing the cause of collapse — class-weighted loss or balanced sampling during training — would resolve this case without any changes to labeling or data.

---

**Example 3 — "Add Chamber Headhunter to Deathmatch!"**

*True label:* Insight (uncertain) | *Predicted:* Vent

This post suggests adding a specific weapon to a game mode and gives a reason grounded in personal experience playing Chamber. The annotation marked this uncertain because the argument is thin and the framing is first-person, but the post still makes a game-design claim directed at players beyond the poster.

*Why it failed:* This case sits at the exact boundary the label definitions flag as hard: a specific claim, but personal rather than statistical evidence, and a tone closer to a request than an analysis. Even a well-trained model might fail here. However, since the fine-tuned model failed on unambiguous Insight posts too, collapse is the primary issue rather than genuine label difficulty. The case also highlights a real annotation challenge: first-person framing ("I play Chamber a lot") pattern-matches to Vent even when the underlying intent is a game-design argument.

*What would fix it:* Both a training fix and a labeling clarification. The collapse needs addressing first. Once the model makes real predictions, posts like this should be used as deliberate training examples to teach the distinction between personal-framing Insight and true Vent.

---

### Sample Classifications

| Post Text | True Label | Predicted | Confidence |
|---|---|---|---|
| Hello. For reference, I am a fairly new player and I started in about the middle of February with about 70 hours in CS2. I am not very good at the game as my peak rank was Iron 2 while playing against... | Vent | Vent | 0.53 |
| Riot missed a massive opportunity by making "My Card" website-only. It should be in-game! The new "My Card" website feature that came out is super cool for sharing stats and favorite agents on social... | Insight | Vent | 0.54 |
| alr ill be the first to say i suck at this game thats why im in bronze but being toxic in bronze and iron lobbies is crazy ngl i would get it in higher lobbies but not the lowest just keep it to yours... | Vent | Vent | 0.52 |

The third example (bronze lobbies post) is a correct Vent prediction. The post uses explicitly negative language throughout ("i suck", "crazy", "toxic") with no underlying argument — it expresses frustration about player behavior without making a claim that could be evaluated. The model's prediction of Vent is well-supported even at low confidence, since the surface features the model overfit to happen to be genuinely present here.

---

## Reflection

The label definitions draw a distinction based on **intent**: a Vent post is one where the primary purpose is to express frustration, and an Insight post is one where the primary purpose is to make an argument about the game. That is a meaningful distinction, but it is an intent-based one — and intent does not live on the surface of text the way tone does.

What the model captured was **tone**. Frustrated language, informal phrasing, first-person complaints, and reactive expressions like "is crazy" or "is insane" are statistically reliable markers — they appear in nearly every Vent post. The model learned to weight those signals heavily, because in the training data they were the most consistent feature correlated with the Vent label. The problem is that the same signals appear in a large subset of Insight posts too. Many Insight posts open with a frustrated observation before pivoting to an argument. The model never learned the pivot.

What the model missed was **structure**. The feature that actually separates Insight from Vent is not how the post sounds but what it does: does it introduce a specific claim, then support it? That structure — claim, then evidence or reasoning — is what the label definition was trying to capture. But structural features like the presence of a numbered list, a cited statistic, a comparative argument, or a conditional claim ("if X happens, Y follows") are much harder for a small fine-tuned model to learn. They require reading the post as a whole rather than pattern-matching on individual tokens.

The gap, then, is this: the intended decision boundary runs between *purpose* (expressing frustration vs. making an argument), but the learned decision boundary ran between *tone* (emotionally charged language vs. neutral language). Those two boundaries overlap on easy examples and diverge on hard ones. A post like "Riot missed a massive opportunity by making 'My Card' website-only" has a clear argument embedded in an excited, opinionated frame — exactly the kind of post that falls on the right side of the intent boundary but the wrong side of the tone boundary. The model called it Vent.

This also points to a design tension worth naming. The Vent label inherently selects for posts with strong emotional language. The Insight label selects for posts with specific argumentative content. But those are not mutually exclusive properties — a post can have both. The label definitions resolve that tension by prioritizing intent, but the model was never given enough examples of the hard case (strong emotion plus real argument) to learn that resolution. The result is a classifier that captured the easier half of what the labels were meant to encode.

---

## Spec Reflection

**One way the spec helped:** The spec's requirement to define evaluation metrics upfront — specifically the decision to use per-class precision, recall, and F1 rather than accuracy alone — was essential for interpreting the results. Accuracy alone would have made the fine-tuned model look like a partial success (60%) when it had in fact completely failed to learn the Insight class. The spec pushed toward a richer evaluation framework before data collection began, which meant the tools to diagnose the collapse were already in place when it happened.

**One way implementation diverged:** The spec called for roughly 100 examples per label and explicitly flagged that if a class became underrepresented, collection should continue until both classes were close to balanced. In practice, the training data ended up with more Vent than Insight examples. Vent posts are more common on r/VALORANT and easier to identify quickly, so the natural collection process skewed toward them. The divergence mattered: class imbalance during training is one of the factors that allowed the model to collapse to the majority class. In retrospect, the spec's warning was correct — it just was not acted on aggressively enough during collection.

---

## AI Usage

**Instance 1 — Asked Claude to identify patterns**

I gave Claude my csv file of labeled examples. Then, I gave it the 12 misclassified ones and asked it to identify patterns. 

**Instance 2 — Asked Claude to stress-test label definitions**

Before finalizing the label definitions, I gave Claude the Vent and Insight definitions and asked it to generate five synthetic posts that sat right on the boundary between the two labels. Several of the generated posts combined frustrated language with a specific game claim — for example, a post that opened with "this matchmaking is broken" but then cited win-rate data to make an argument about MMR calculation. This confirmed that the hard case was emotional framing combined with real evidence, and that the definitions needed an explicit tiebreaker rule for it. I used those examples as a prompt to write the dominant-intent rule into the labeling process. I did not use any of the synthetic posts as training data.
