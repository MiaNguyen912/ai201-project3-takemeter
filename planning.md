# Makeup Posts Classification Planning

## Community ##
The makeup enthusiasts community is constantly sharing makeup looks, discussing products, troubleshooting beauty concerns, and exchanging tips across platforms such as Reddit, Discord, and beauty forums.

**Why this community?** the rich, naturally-occurring discourse with high variance in intent and communication style within the makeup community is an ideal test case for few-shot classification because:

1. **Discourse variance:** Posts mix multiple functions (showcase + question, advice + personal opinion, product rec + review). Unlike technical documentation, where intent is often explicit, makeup posts require understanding implicit intent—what the author primarily wants to communicate beneath the surface words.

2. **Pragmatic value:** Beauty communities influence purchasing decisions ($13B+ annual beauty spend). A classifier that distinguishes "I'm showing off this product because I loved it" (opinion/showcase) from "This product works for acne-prone skin—try it" (advice) would help curators surface the right content type: inspirational content for discovery feeds vs. practical guidance for troubleshooting threads.

3. **Label ambiguity is real:** The boundary between "opinion" and "advice" is genuinely blurry in user speech. Handling this ambiguity—not by avoiding it, but by defining decision rules—teaches the model how real communities actually communicate.

The taxonomy distinguishes between posts that primarily **showcase** a look or result, **request** information or help, provide **advice** or guidance, and express **opinion** through reviews, reactions, or personal experiences. These distinctions matter because makeup discussions often mix multiple discourse functions in a single post, and accurately identifying the author's primary intent helps capture how community members contribute, seek support, influence purchasing decisions, and share expertise.

## Labels ##
- `showcase` (sharing a look/product discovery/experience)
    - The author is sharing/displaying a makeup look, result, product discovery, or personal experience they found or created. The focus is on their own discovery, creation, or experience—centering what *they* found or did, not on evaluating for the reader’s benefit.
    - **Key signal:** The post is primarily saying *”look at what I found/did/experienced”* rather than *”here’s my judgment about X”* or *”here’s how to solve your problem.”* The author’s personal story or discovery is the focal point; any recommendation (“you should try this”) serves to express their excitement about the discovery, not to prescribe an action.
    - **Examples:**
        - “Matched my makeup with my hair.” (displaying their look)
        - “My makeup after 10 hours of wear.” (showing their result)
        - “Tried a different makeup technique today.” (sharing their experiment)
        - “Recently picked up the Patrick Ta cream and I’m honestly shocked that it blends super easily and it stays on all day. Anyone else with acne prone skin found blushes that work for them?” → Label `showcase` (The author is showing off a product they discovered—the story centers on their finding)
        - “This foundation is amazing. You should definitely try it.” → Label `showcase` (The author is sharing a product discovery they’re excited about; the “you should try it” advice expresses their enthusiasm, not a directive)

- `request` (seeking information/help/recommendations)
    - The author is seeking information, recommendations, troubleshooting help, product identification, or guidance from others. The author does not already have the answer and needs input from the community.
    - **Key signal:** The post is primarily asking/seeking. The author wants others to provide information, suggestions, or answers. The tone can be a direct question (“How do I...?”) or an implicit request (“Setting spray, pleaseee”).
    - **Examples:**
        - “How do I fix under-eye creasing?” (seeking troubleshooting help)
        - “What setting spray are you using?” (seeking product recommendations)
        - “Can anyone identify this lipstick?” (seeking product identification)
        - “Does anyone have recommendations for a cool-toned blush?” (seeking recommendations)
        - “Setting spray, pleaseeee” → label `request` (seeking a product recommendation through an emphatic request, not stating a fact)


- `advice` (providing guidance to help solve a problem or achieve a result)
    - The author provides recommendations, instructions, techniques, tutorials, or practical guidance intended to help someone else solve a problem, achieve a specific result, or improve their technique. The author’s primary goal is to prescribe an action that will help the reader.
    - **Key signal:** The post is telling someone *”you should do X to achieve Y”* or *”try this to solve your problem.”* The author is positioned as a helper/teacher, diagnosing a reader’s need and prescribing a solution.
    - **Distinguishing from showcase:** Advice is *prescriptive* and *problem-solving focused*, not sharing a personal discovery. “Use tubing mascara if yours smudges” (advice) vs. “I use tubing mascara and love it” (potential showcase or opinion).
    - **Examples:**
        - “Apply powder before foundation.” (prescribing a technique)
        - “Use a tubing mascara if your mascara smudges.” (solving a problem with a recommendation)
        - “Blend the edges with your finger for a softer look.” (instructing how to achieve a result)
        - “Try the Fwee Blurry Pudding Pot for this look.” (prescribing a product to achieve the look the reader is asking about)
        - “Are you using all of those moisturizers/sunscreens/serums at once? it might just be caused by too many products. i would just do one serum, one moisturizer, and one primer...” → label `advice` (diagnosing the problem and prescribing a solution)

- `opinion` (evaluating, judging, comparing)
    - The author expresses a judgment, evaluation, reaction, or assessment *about* a product, look, or technique—with the primary goal of sharing that judgment with others. The focus is on the author’s *verdict* or *assessment*, not on their personal discovery or experience.
    - This includes: product reviews, product comparisons, critical reactions, aesthetic judgments, preferences, and subjective assessments.
    - **Key signal:** The post is primarily saying *"here’s what I think about X"* or *"here’s how X compares to Y"*—with the intent to inform the reader’s judgment. The post centers on a claim/judgment, not a discovery.
    - **Examples:**
        - "Liquid blush lasts longer for me than cream blush." (comparing two products)
        - "This foundation oxidizes on my skin." (evaluating a product property)
        - "The Fwee Blurry Pudding Pot is worth the hype." (making a judgment claim)
        - "Gorgeous look!" (reacting to someone’s result)
        - "I prefer satin finishes over matte." (expressing a preference/judgment)
        - “This foundation is amazing. It works better than [competitor].” → `opinion` (making a comparative judgmen although this can look like a product showcase)


## How to Handle Ambiguous Cases ##

**Core principle:** Use the author’s **primary intent** rather than the topic of the post. When in doubt, ask these questions in order:

1. Is the author **showing/sharing** something they found or did? → `showcase`
2. Is the author **asking/seeking** information or help? → `request`
3. Is the author **telling someone what to do** to solve a problem or achieve a result? → `advice`
4. Is the author **expressing a judgment/evaluation** about something? → `opinion`

**Choose the first rule that clearly applies.**

---

### Examples by pairs

- **showcase vs opinion (the hardest distinction):**
    - **Rule:** Is the author’s story *”I found/did something amazing”* (showcase) or *”here’s my judgment about X”* (opinion)?
    - “I just tried this blush and wow, the color is so perfect for my skin tone.” → `showcase` (story centers on **personal** discovery)
    - “This blush color is perfect for warm undertones.” → `opinion` (making a judgment claim)
    - “This foundation is amazing. You should definitely try it.” → `showcase` (author excited about **personal** discovery; recommendation expresses enthusiasm)
    - “This foundation is amazing. It works better than [competitor].” → `opinion` (making a comparative judgment)
    - **Gut check:** Remove the recommendation from the post. Does it still work as a personal story? If yes → `showcase`. If the recommendation is essential to complete a judgment → `opinion`.

- **showcase vs advice (product recommendations):**
    - **Rule:** Is the author sharing a *personal discovery* they’re excited about (showcase) or *prescribing an action to solve someone’s problem* (advice)?
    - “I use tubing mascara and I love it.” → `showcase` (sharing a personal discovery)
    - “Use a tubing mascara if your mascara smudges.” → `advice` (solving a problem with a prescription)
    - “Recently picked up the Patrick Ta cream and I’m honestly shocked it blends so easily and stays all day.” → `showcase` (sharing a discovery)
    - “Try the Patrick Ta cream for better blending.” → `advice` (prescribing for a specific result)

- **advice vs request:**
    - “What setting spray should I use?” → `request` (author lacks information)
    - “Use a silicone-based setting spray.” → `advice` (author prescribing a solution)
    - “Setting spray, pleaseeee” → `request` (author is seeking/asking, not prescribing)

- **opinion vs request:**
    - These should not overlap. `opinion` expresses judgment; `request` seeks information.

- **advice mixed with question:**
    - “Are you using all those moisturizers? I’d suggest just one serum, one moisturizer, and one primer, spaced 10-15 minutes apart.” → `advice` (The post’s purpose is to prescribe a solution, not to seek information about the reader’s routine.)
    - “Have you tried looking at Douyin makeup? They have great tutorials for achieving this look.” → `advice` (prescribing a resource to help achieve a result)

- **showcase with a question:**
    - “Here’s today’s makeup look. Any suggestions for improvement?” → `showcase` (primary purpose is presenting the look; feedback request is secondary)
    - “Recently picked up the Patrick Ta cream and I’m shocked it blends so easily. Anyone else with acne-prone skin find blushes that work for them?” → `showcase` (primary purpose is sharing a discovery)

- **Compliments and reactions:**
    - “You look amazing.” or “This look is stunning.” → `opinion` (expressing a judgment/emotional reaction)


## Data collection plan ##

**Data sources:** Realistic Reddit posts and comments from:
- r/MakeupAddiction, r/Makeup, and subreddits focused on makeup reviews/tutorials
- Discord makeup/beauty communities
- Beauty forums (e.g., MakeupAlley)

**Target:** 200 total instances, aiming for ~50 examples per label (showcase, request, advice, opinion).

**Sampling strategy:** Collect posts randomly from threads to ensure varied community conversations.

### Handling Label Imbalance

After collecting ~200 examples, check label distribution:

| Label | Target | Acceptable min | Action threshold |
|-------|--------|----------------|------------------|
| showcase | 50 | 40 | If <40, trigger targeted collection |
| request | 50 | 40 | If <40, trigger targeted collection |
| advice | 50 | 40 | If <40, trigger targeted collection |
| opinion | 50 | 40 | If <40, trigger targeted collection |

If a label falls below 40 examples: Targeted collection:
- For `request`: Collect from "Help" threads, "Troubleshooting" posts, and Q&A-focused subreddits
- For `advice`: Collect from tutorial posts, "Tips & Tricks" threads, and response comments
- For `showcase`: Collect from "Look of the Day" threads and photo megathreads
- For `opinion`: Collect from product review threads and "What's your favorite...?" posts
- Aim to collect 20–30 additional examples from these targeted sources to reach ~50 per label

Total may exceed 200 to achieve balance; this is acceptable. A small imbalance (e.g., 45, 48, 52, 55) is also acceptable and natural to real-world discourse. `Showcase` posts are likely more common in makeup communities than `request` posts, and that's OK. The goal is to avoid *severe* imbalance which would cause the model to ignore rare labels.


## Evaluation metrics ##

**Chosen metrics (in order of importance):**

1. **Overall accuracy — sanity check**
   - % of all predictions that are correct, regardless of label
   - **What it does:** Quick gut check. At minimum, accuracy should be >50% (better than random guessing).
   - **What it does NOT catch:** Accuracy can hide catastrophic failures on minority labels. E.g., 80% accuracy could mean 95% correct on `showcase` but 10% on `request`. This is why accuracy alone is insufficient—it can be high even when the model ignores entire label types.
   - **Interpretation:** If accuracy ≈ macro F1, the distribution is likely balanced or the model's performance is consistent across labels. If accuracy >> macro F1, the model is biased toward majority labels and ignoring minority ones.
   - **Example:** 
     - Accuracy = 75%, Macro F1 = 68% → Probably OK (slight imbalance but learnable)
     - Accuracy = 80%, Macro F1 = 45% → **Red flag.** Model is succeeding on majority labels but failing on minorities

2. **Macro-averaged F1 — measures balanced learning across all labels**
   - Computes F1 for each label independently, then averages them equally
   - **Why for this task:** Ensures the model learns all four discourse types, not just the high-frequency ones. In a makeup community, some labels may naturally be less common (e.g., `request` might be <20% of posts), but the classifier must still recognize them. Macro F1 prevents the model from achieving high overall scores by ignoring minority labels.
   - **What it catches:** A model that's terrible at classifying `request` (20% F1) can't hide behind strong performance on `showcase` (90% F1)—the macro F1 would be ~60%, signaling a problem.

3. **Per-label precision and recall — catches asymmetric failure modes (replaces per-class accuracy)**
   - **Precision (per-label):** Of all posts labeled `request`, how many were actually requests? (Prevents false positives—users won't get answers if the classifier misroutes them to non-question threads.)
   - **Recall (per-label):** Of all actual requests in the test set, how many did the classifier find? (Prevents missing real requests—if users post questions but the classifier ignores them, the tool is broken.)
   - **Note:** Per-label recall = per-class accuracy. We report both metrics (F1 includes both precision and recall, but per-label breakdown helps identify specific failure modes).
   - **Why for this task:** Different label pairs have different costs for a community tool:
     - **High precision, low recall on `request`:** The classifier rarely tags posts as requests, but when it does, it's right. **Problem:** User questions go unseen. A user asking "How do I fix creasing?" gets ignored even though the answer exists. **Unacceptable for deployment.**
     - **Low precision, high recall on `request`:** The classifier tags many posts as requests, including false positives (e.g., `opinion` posts like "Foundations cause creasing in my skin type"). **Problem:** Users follow the classifier to irrelevant threads, but at least real questions aren't missed. **Acceptable but noisy.**
     - The asymmetry matters. Per-label metrics expose these trade-offs.


## Definition of success ##

**Success criteria:**

**1. Overall accuracy ≥65%** (sanity check—confirms learning signal exists)
- Quick sanity check. If accuracy is <50%, the model didn't learn.
- If accuracy >> macro F1 (e.g., 80% accuracy but 50% macro F1), the model is biased toward majority labels. **This is a red flag.**
- Expected pattern: accuracy should be 3–7% higher than macro F1 due to label imbalance

**2. Macro F1 ≥70% (primary metric)**  (ensures all 4 labels are learned)
- The model identifies intent correctly 7 out of 10 times
- Real-world impact: In a 1,000-post subreddit with 200 requests, the model finds ~140 questions (vs. ~20 by random). Users are **7× more likely to find help**.
- Validates that the taxonomy is learnable and data is sufficient

**3. Per-label F1 ≥60% (minimum for all labels)** (all discourse types are recognized)
- Ensures no discourse type becomes invisible. If any label drops below 60%, that content type doesn't surface in the tool.
- Example failure: F1 scores of 85%, 80%, 75%, 45% → macro F1 = 71% (passes), but `advice` is broken

**4. Per-label precision & recall ≥55% minimum** (no label is completely broken)
- Precision: Prevents false positives that frustrate users ("This isn't a question, why was it routed here?")
- Recall: Prevents false negatives that make the tool useless ("I asked a question but it wasn't found")
- The asymmetry matters: low recall on `request` is worse than low precision (users need help found, not routed)