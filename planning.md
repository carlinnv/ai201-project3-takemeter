## Labels

### Insight
Posts that fall under this label provide a specific claim about the game (whether it is about agent balance, mechanics, or strategy) and back it up with evidence, statistics, or observation.
* Example 1: [Valorant rank distribution is kind of wild when you actually look at it:](https://www.reddit.com/r/VALORANT/comments/1ud90uy/valorant_rank_distribution_is_kind_of_wild_when/) This user notes the percentages of players in the different ranks of Valorant. They note that while most people feel frustrated staying in the same rank, being in the "Gold" rank alone makes you better than average. 
* Example 2: [If Killjoy becomes Meta, Veto is the perfect counter:](https://www.reddit.com/r/VALORANT/comments/1udivdo/if_killjoy_becomes_meta_veto_is_the_perfect/) This user provides three distinct points as to why the character Veto would be a great counter to the character Killjoy, citing their specific abilities and characteristics.  
* Uncertain example: [Add Chamber Headhunter to Deathmatch!](https://www.reddit.com/r/VALORANT/comments/1ucwfjr/add_chamber_headhunter_to_deathmatch/): This user suggests adding one of the character's (Chamber) weapons (Headhunter) as a permanent weapon in the Deathmatch gamemode. I labeled this as an uncertain example because while they do mention a specific reason, a lot of it is based on their personal experience as someone who plays Chamber a lot. 

### Vent
Posts that fall under this label express frustration or anger, typically as a complaint about teammates, matchmaking, or the game mechanics in general, without providing a specific argument.
* Example 1: [Miks seems like the most lazy agent ever, Riot is truly running out of ideas:](https://www.reddit.com/r/VALORANT/comments/1uasnm6/miks_seems_like_the_most_lazy_agent_ever_riot_is/) This user vents about a new agent (Miks) having abilities that are essentially repeats of previous agents' abilities, accusing the developers of putting in minimal effort. 
* Example 2: [The Whiplash in Diamond elo is insane:](https://www.reddit.com/r/VALORANT/comments/1uaiqd3/the_whiplash_in_diamond_elo_is_insane/) This user expresses frustrations about the Valorant MMR system, sayingn that games are not consistent at all. 
* Uncertain example: [Just to air frustrations, look at my W/L gains and losses. These past 2-3 acts have been incredibly annoying to climb](https://www.reddit.com/r/VALORANT/comments/1ucv3n1/just_to_air_frustrations_look_at_my_wl_gains_and/): In this post, the user vents about having difficulty climbing ranks due to the fact that losses are punished more than wins are celebrated. They give photo evidence of their match history, however, this post could still be classified a vent due to their frustrated language. 



## Community
r/VALORANT is a general discussion subreddit for Riot's tactical shooter, where posts range from competitive analysis and patch reactions to gameplay clips and ranked frustrations. The two labels, "Insight" and "Vent", capture the dominant modes of discourse: whether a post is making a reasoned argument or expressing emotional frustration. These distinctions matter because the community itself frequently debates what belongs on the subreddit versus r/ValorantCompetitive, making discourse quality a live, contested issue that players already have strong intuitions about.

## Hard edge cases
The most ambiguous posts will be the ones that mix both labels in the same text. For example, a post may begin with a frustrated complaint about matchmaking but then include concrete evidence, ranked data, or a specific gameplay argument. Another tricky case is a strongly emotional post that still contains a valid claim, or an "Insight" post that is mostly a disguised rant.

When I encounter one of these during annotation, I will label the post based on its dominant intent rather than isolated sentences. Primarily, if the user is moreso concerned with improving their own performance, I would label the post "Vent". If they are making an observation or suggestion that affects players other than them and thus pushing a statement of the game as a whole, I would label it "Insight". If the post is still genuinely split, I will use a consistent rule: choose the label that best matches the primary purpose of the post, and mark the example as uncertain in my notes so it can be reviewed later. I would rather preserve consistency than force a shaky decision on a borderline case.

## Data Collection Plan
I will collect examples from r/VALORANT posts that cover the range of competitive discussion, patch reactions, rank complaints, and gameplay opinion threads. To avoid sampling only easy examples, I will deliberately include posts with different titles, lengths, and levels of evidence. A practical target is roughly 100 examples per label to start, for a total of about 200 labeled posts.

If a label is still underrepresented after 200 examples, I will keep collecting until the classes are closer to balanced rather than training on a skewed set. I would also actively search for posts that are likely to contain the minority label, such as evidence-heavy balance discussions for Insight or complaint-heavy rank posts for Vent, so the dataset improves instead of just growing randomly.

## Evaluation Metrics
Accuracy alone is not enough because it can hide whether the model is failing on one label more than the other. I will use precision, recall, and F1 score for each label, plus macro-averaged F1 so both classes count equally even if the dataset is slightly imbalanced. That matters here because a community moderation-style classifier needs to avoid both false positives and false negatives: misclassifying a Vent as Insight or an Insight as Vent are both meaningful errors.

I would also inspect the confusion matrix to see which mistakes are most common, since that will show whether the model is confusing emotionally worded evidence posts with true rants, or missing argumentative posts that lack explicit statistics. If the classes are imbalanced, balanced accuracy will be another useful sanity check.

## Definition of Success
This classifier is genuinely useful if it is consistently better than a naive baseline and can support lightweight community triage without constant manual correction. A good target would be strong macro-F1, with precision and recall both high enough that most predictions are trustworthy in practice rather than only impressive on paper.

For deployment in a real community tool, I would accept performance that is good enough to reduce manual sorting work, not necessarily perfect. In concrete terms, I would want the model to be reliable on clear examples, reasonably calibrated on borderline ones, and accurate enough that moderators or community managers could use it as a first-pass filter without having to second-guess most outputs. If it still makes frequent confusion errors on the edge cases, I would treat it as a drafting aid rather than a production classifier.

## AI Tool Plan
### Label stress-testing
I will give an AI tool my label definitions and my edge-case rules, then ask it to generate 5 to 10 synthetic posts that sit right on the boundary between Insight and Vent. The goal is to see whether the definitions are actually sharp enough to separate borderline examples. If the AI generates posts that I cannot classify cleanly, I will tighten the label descriptions before I annotate the full set of 200 examples.

### Annotation assistance
I may use an LLM to pre-label a batch of examples before I review them myself, but only as a draft aid. If I do, I will record which examples were pre-labeled and which tool produced the draft labels so I can disclose that clearly in the AI usage section. I will still make the final annotation decision manually and will not treat the pre-label as ground truth.

### Failure analysis
After evaluation, I will give the AI tool my list of incorrect predictions and ask it to identify recurring patterns in the mistakes, such as confusion between emotional evidence-based posts and true vents. I will use those suggestions as hypotheses, then verify them myself by checking the original posts and the confusion matrix before I write the final analysis. That way, the AI can help surface patterns, but I am still responsible for confirming them.