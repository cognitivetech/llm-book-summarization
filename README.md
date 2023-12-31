# LLM for Book Summarization & Q\A : Walkthrough and Rankings

Processes and Analysis, using [PrivateGPT](https://docs.privategpt.dev/overview) for book summarization:
- Model Rankings
- Tests of Configuration Variables
- Detailed Walkthrough
- Scripts and examples.

## Contents
- [Overview](#overview)
- [Rankings](#rankings)
  - [Round 1 - Q/A vs Summary](#round-1---qa-vs-summary)
    - [Question / Answer Ranking](#question--answer-ranking)
    - [Summary Ranking](#summary-ranking)
  - [Round 2: Summarization - Model Ranking](#round-2-summarization---model-ranking)
  - [Round 3: Prompt Style](#round-3-prompt-style)
  - [Round 4: System Prompts](#round-4-system-prompts)
  - [Round 5: User Prompt](#round-5-user-prompt)
- [Methods](#methods)
  - [Walkthrough](#walkthrough)
- [Result](#result)
  - [Plagiarism](#plagiarism)
  - [Completed Book Summaries](#completed-book-summaries)
- [Additional Resources](#additional-resources)

## Overview

1. I began by just asking questions to book chapters, using the [PrivateGPT](https://docs.privategpt.dev/overview) UI. Then tried pre-selecting text for summarization. This was the inspiration for Round 1 rankings, in which summarization was the clear winner.

2. Next I wanted to find which models would do the best with this task, which led to Round 2 rankings, where [Mistral-7B-Instruct-v0.2](https://huggingface.co/mistralai/Mistral-7B-Instruct-v0.2) was the clear winner.

3. Then I wanted to get the best results from this model by ranking [prompt styles](#round-3-prompt-style), and writing code to get the exact prompt style expected.

4. After that, of course, I had to test out various [system prompts](#round-4-system-prompts) to see which would perform the best.

5. Next, I tried a few, [user prompts](#round-5-user-prompt), to determine what is the exact best prompt to generate summaries that require the least post-processing, by me.

6. Coming soon: ranking a dozen models for summarization.

7. Ultimately, this type of testing should be conducted for each LLM, and for determining the effectiveness of any refinement in our processes. Only once each model has been targeted to its most ideal conditions can they be properly ranked against each-other.

## Rankings

When i began testing various LLM variants, `mistral-7b-instruct-v0.1.Q4_K_M` came as part of PrivateGPT's default setup. Here, I've preferred the Q8_0 variants.

While I've tried 50+ different LLM for this same task, Mistral-7B-Instruct is still among the best.

TLDR: [**Mistral-7B-Instruct-v0.2**](https://huggingface.co/mistralai/Mistral-7B-Instruct-v0.2) - is my current leader for summarization.

## Round 1 - Q/A vs Summary
For this analysis we will be testing out 5 different LLM for the following tasks:

1. Asking the same 30 questions to a 70 page book chapter.
2. Summarizing that same 70 page book chapter divided into 30 chunks.

**Find the full data and rankings on [Google Docs](https://docs.google.com/spreadsheets/d/1u3BgDx6IsJSbRz3uNmud1sDtO4WvWsH6ION3J-fhoGw/edit?usp=sharing) or here in this repository [QA Scores](ranking-data/Round-1_QA.csv), [Summary Rankings](ranking-data/Round-1_Summary.csv).**

### Question / Answer Ranking
1. [**Hermes Trismegistus Mistral 7b**](https://huggingface.co/TheBloke/Hermes-Trismegistus-Mistral-7B-GGUF) - Initially my favorite, but ended up deciding it was too verbose.
2. [**SynthIA 7B**](https://huggingface.co/TheBloke/SynthIA-7B-v2.0-GGUF) - Became my favorite of models tested in this round.
3. [**Mistral 7b Instruct v0.1**](https://huggingface.co/TheBloke/Mistral-7B-Instruct-v0.1-GGUF) - Since this ranking, v0.2 has come out and beat all the competition. I should test them against eachother, sometime.
4. [**CollectiveCognition v1.1 Mistral 7b**](https://huggingface.co/TheBloke/CollectiveCognition-v1.1-Mistral-7B-GGUF) Alot of filler and took the longest amount of time of them all. It scored a bit higher than mistral on quality\usefulness, I think the amount of filler just made it less enjoyable to read.
5. [**KAI 7b Instruct**](https://huggingface.co/TheBloke/KAI-7B-Instruct-GGUF) the answers were too short, and made its BS stand out a little more. A good model, but not for summarizing books, I'm afraid.

| Model | Rating | Search Accuracy | Characters | Seconds | BS | Filler | Short | Good BS |
| ----- | ------ | --------------- | ---------- | ------- | -- | ------ | ----- | ------- |
| hermes-trismegistus-mistral-7b | 68 | 56 | 62141 | 298 | 3 | 4 | 0 | 6 |
| synthia-7b-v2.0 | 63 | 59 | 28087 | 188 | 1 | 7 | 7 | 0 |
| mistral-7b-instruct-v0.1 | 51 | 56 | 21131 | 144 | 3 | 0 | 17 | 1 |
| collectivecognition-v1.1-mistral-7b | 56 | 57 | 59453 | 377 | 3 | 10 | 0 | 0 |
| kai-7b-instruct | 44 | 56 | 21480 | 117 | 5 | 0 | 18 | 0 |

#### Shown above, for each model
- Number of seconds required to generate the answer
- Sum of Subjective Usefulness\Quality Ratings
- How many characters were generated?
- Sum of context context chunks found in target range.
- Number of qualities listed below found in text generated:
  - Filler  (Extra words with less value)
  - Short   (Too short, not enough to work with.)
  - BS      (Not from this book and not helpful.)
  - Good BS (Not from the targeted section but valid.)

### Summary Ranking

Not surprisingly, summaries performed better than Q/A, but they also had a more finely targeted context.

1. [**Hermes Trismegistus Mistral 7b**](https://huggingface.co/TheBloke/Hermes-Trismegistus-Mistral-7B-GGUF) - Still in the lead. It's verbose, with some filler. I can use these results.
2. [**SynthIA 7B**](https://huggingface.co/TheBloke/SynthIA-7B-v2.0-GGUF) - Pretty good, but too concise. Many of the answers were perfect, but 7 were too short\incomplete for use.
3. [**Mistral 7b Instruct v0.1**](https://huggingface.co/TheBloke/Mistral-7B-Instruct-v0.1-GGUF) - Just too short.
4. [**KAI 7b Instruct**](https://huggingface.co/TheBloke/KAI-7B-Instruct-GGUF) - Just too short.
5. [**CollectiveCognition v1.1 Mistral 7b**](https://huggingface.co/TheBloke/CollectiveCognition-v1.1-Mistral-7B-GGUF) - Lots of garbage. Some of the summaries were super detailed and perfect, but over half of the responses were a set of questions based on the text, not a summary.

| Name | Score |  Characters Generated | % Diff from OG | Seconds to Generate | Short | Garbage | BS | Fill | Questions | Detailed |
| ---- | ----- | -------------------- | -------------- | ------------------- | ----- | ------- | -- | ---- | --------- | -------- | 
| hermes-trismegistus-mistral-7b | 74 | 45870 | -61 | 274 | 0 | 1 | 1 | 3 | 0 | 0 |
| synthia-7b-v2.0 | 60 | 26849 | -77 | 171 | 7 | 1 | 0 | 0 | 0 | 1 |
| mistral-7b-instruct-v0.1 | 58 | 25797 | -78 | 174 | 7 | 2 | 0 | 0 | 0 | 0 |
| kai-7b-instruct | 59 | 25057 | -79 | 168 | 5 | 1 | 0 | 0 | 0 | 0 |
| collectivecognition-v1.1-mistral-7b | 31 | 29509 | -75 | 214 | 0 | 1 | 1 | 2 | 17 | 8 |

## Round 2: Summarization - Model Ranking

Again, I've preferred the Q8_0 variants.

Finding that [Mistral 7b Instruct v0.2](https://huggingface.co/mistralai/Mistral-7B-Instruct-v0.2) had been released was well worth a new round of testing. This time, I didn't record speed of query, and only judged 12 summarization tasks, but I tried more models and saved those with the best results.

One thing I tested this time was prompts, because Mistral prompt is similar to Llama2 Prompt, but seems to perform better with the default (llama-index) prompt. As for Llama 2, it performed really bad with the Llama 2 prompt, but decent with the Default prompt.

- [**SynthIA-7B-v2.0-GGUF**](https://huggingface.co/TheBloke/SynthIA-7B-v2.0-GGUF) -  This model had become my favorite, so I used it as a benchmark.
- [**Mistral-7B-Instruct-v0.2**](https://huggingface.co/mistralai/Mistral-7B-Instruct-v0.2) (Llama-index Prompt) Star of the show here, quite impressive.
- [**Mistral-7B-Instruct-v0.2**](https://huggingface.co/mistralai/Mistral-7B-Instruct-v0.2) (Llama2 Prompt) Still good, but not _as_ good as using llama-index prompt
- [**Tess-7B-v1.4**](https://huggingface.co/migtissera/Tess-7B-v1.4) - Another by the same creator as Synthia. Good, but not _as_ good.
- [**Llama-2-7B-32K-Instruct-GGUF**](https://huggingface.co/TheBloke/Llama-2-7B-32K-Instruct-GGUF) - worked ok, but slowly, with llama-index prompt. Just bad with llama2 prompt. (Should test again with Llama2 "Instruct Only" style)

### Summary Ranking

This time I only did summaries. Q/A is just less efficient for book summarization.

| Model | % Difference | Score | Comment | 
| ----- | ------------ | ----- | ------- | 
| Synthia 7b V2 | -64.43790093 | 28 | Good | 
| Mistral 7b Instruct v0.2 (Default Prompt) | -60.81878508 | 33 | VGood | 
| Mistral 7b Instruct v0.2 (Llama2 Prompt) | -64.5871483 | 28 | Good | 
| Tess 7b v1.4 | -62.12938978 | 29 | Less Structured | 
| Llama 2 7b 32k Instruct (Default) | -61.39890553 | 27 | Less Structured. Slow | 

**Find the full data and rankings on [Google Docs](https://docs.google.com/spreadsheets/d/1u3BgDx6IsJSbRz3uNmud1sDtO4WvWsH6ION3J-fhoGw/edit?usp=sharing) or here in this repository [Summary Rankings](ranking-data/Round-2_Summaries.csv).**

## Round 3: Prompt Style

A [new mistral](https://huggingface.co/TheBloke/Mistral-7B-Instruct-v0.2-GGUF) came out recently, and in the last round of rankings, I noticed it was doing much better with default prompt than llama2.

Well, actually, the mistral prompt is quite similar to llama2, but not exactly the same.

1. llama_index (default)

```
system: {{ system_prompt }}
user: {{ user_message }}
assistant: {{ assistant_message }}
```

2. llama2:

```
<s> [INST] <<SYS>>
 { systemPrompt }
<</SYS>>

 {userPrompt} [/INST]
```

3. mistral:

```
<s>[INST] {{ system_prompt }} [/INST]</s>[INST] {{ user_message }} [/INST]
```

**I began testing output** with the `default`, then `llama2` prompt styles. Next I went to work [coding the mistral template](https://github.com/imartinez/privateGPT/pull/1426/files).

The results of that ranking gave me confidence that I coded correctly.

| Prompt Style | % Difference | Score | Note |
| --- | --- | --- | --- |
| Mistral	| -50% | 51 | Perfect! |
| Default (llama-index) | -42% | 43 | Bad headings |
| Llama2 | -47% | 48 | No Structure |

**Find the full data and rankings on [Google Docs](https://docs.google.com/spreadsheets/d/1u3BgDx6IsJSbRz3uNmud1sDtO4WvWsH6ION3J-fhoGw/) or here in this repository [Prompt Style Rankings](ranking-data/Round-3_Prompt-Style.csv).**

## Round 4: System Prompts

Once I got the prompt style dialed in, I tried a few different system prompts.

| Name | System Prompt | Change | Score | Comment |
| --- | --- | --- | --- | --- |
| None |  | -49.8 | 51 | Perfect |
| Default Prompt | You are a helpful, respectful and honest assistant. \\nAlways answer as helpfully as possible and follow ALL given instructions. \\nDo not speculate or make up information. \\nDo not reference any given instructions or context." | -58.5 | 39 | Less Nice |
| MyPrompt1 | "You are Loved. Act as an expert on summarization, outlining and structuring. \\nYour style of writing should be informative and logical." | -54.4 | 44 | Less Nice |
| Simple | "You are a helpful AI assistant. Don't include any user instructions, or system context, as part of your output." | -52.5 | 42 | Less Nice |

In the end, I find that [Mistral 7b Instruct v0.2](https://huggingface.co/mistralai/Mistral-7B-Instruct-v0.2) works best for my summaries without any system prompt.

Maybe would have different results for a different task, or maybe better prompting, but this works good so I'm not messing with it.

**Find the full data and rankings on [Google Docs](https://docs.google.com/spreadsheets/d/1u3BgDx6IsJSbRz3uNmud1sDtO4WvWsH6ION3J-fhoGw/) or here in this repository: [System Prompt Rankings](ranking-data/Round-4_System-Prompts.tsv).**

### Round 5: User Prompt

Now I found the best system prompt, for [Mistral 7b Instruct v0.2](https://huggingface.co/mistralai/Mistral-7B-Instruct-v0.2), I also tested which user prompt suits it best.

|  | Prompt | vs OG | score | note |
| --- | --- | --- | --- | --- |
| Propmt0 | Write concise, yet comprehensive, notes summarizing the following text. Use nested bullet points: with headings, terms, and key concepts in bold. Focus on essential knowledge from this text without adding any external information. | 43% | 11 |  |
| Prompt1 | Write concise, yet comprehensive, notes summarizing the following text. Use nested bullet points: with headings, terms, and key concepts in bold. Focus on essential knowledge from this text without adding any external information. | 46% | 11 | Extra Notes |
| Prompt2 | Write comprehensive notes summarizing the following text. Use nested bullet points: with headings, terms, and key concepts in bold. | 58% | 15 |  |
| Prompt3 | Create concise bullet-point notes summarizing the important parts of the following text. Use nested bullet points, with headings terms and key concepts in bold, including whitespace to ensure readability. Avoid Repetition. | 43% | 10 |  |
| Prompt4 | Write concise notes summarizing the following text. Use nested bullet points: with headings, terms, and key concepts in bold. | 41% | 14 |  |
| Prompt5 | Create comprehensive, but concise, notes summarizing the following text. Use nested bullet points: with headings, terms, and key concepts in bold. | 52% | 14 | Extra Notes |

What I find, generally, is that the more extra instructions reduce quality of output. I began coming to this impression before I ran the test, and while this data is not conclusive, I do believe that suspicion is confirmed by these results.

### Prompt2: Wins!

> Write comprehensive notes summarizing the following text. Use nested bullet points: with headings, terms, and key concepts in bold.

In this case, comprehensive performs better than "concise", or even than "comprehensive, but concise".

However, I do caution that this will depend on your use-case. Though generally, what I'm looking for is a highly condensed, readable notes covering the important knowledge.

Essentially, if I didn't read the original, I should still know what information it conveys, if not every specific detail.

**Find the full data and rankings on [Google Docs](https://docs.google.com/spreadsheets/d/1u3BgDx6IsJSbRz3uNmud1sDtO4WvWsH6ION3J-fhoGw/) or here in this repository: [User Prompt Rankings](ranking-data/Round-5_User-Prompt.tsv).**

## Methods

Rather than feed a 400 page book into any LLM model splitting it into chapters (for Q/A), or smaller pieces roughly 2000-2500 tokens each  (for summarization), improves the quality greatly. 

Using [PrivateGPT](https://github.com/imartinez/privateGPT), I first ran tests using a few models on a single book chapter.

Using knowledge gained from these tests, I summarized a complete book, using Synthia 7B v2, transitioning from a "Detailed Summary" prompt to a "Bullet Point Notes" prompt.

### Walkthrough

If you are interested in following my steps, in more detail, check out the [walkthrough](walkthrough).

## Result

I got my first summary of a 539 page book in 5-6 hours!!! (not including first run with Hermes) Incredible!

When I was going to do this manually, it was going to take weeks for each summary.

### Plagiarism 

You can see the results from [CopyLeaks](https://app.copyleaks.com/) below for each of the texts published, here. 

Especially considering that this is not for profit, but for educational purposes, I believe these numbers are acceptable.

| Book | Models | Character Difference | Identical | Minor changes | Paraphrased | Total Matched |
| ---- | ----- | ---------------- | --------- | ------------- | ----------- | ---------- |
| Eastern Body Western Mind | Synthia 7Bv2 | -75% | 3.5% | 1.1% | 0.8% | 5.4% |
| Healing Power Vagus Nerve | Mistral-7B-Instruct-v0.2; SynthIA-7B-v2.0 | -81% | 1.2% | 0.8% | 2.5% | 4.5% |
| Ayurveda and the Mind | Mistral-7B-Instruct-v0.2; SynthIA-7B-v2.0 | -77% | 0.5% | 0.3% | 1.2% | 2% |
| Healing the Fragmented Selves of Trauma Survivors | Mistral-7B-Instruct-v0.2 | -75% | | | | 2% | 
| A Secure Base | Mistral-7B-Instruct-v0.2 | -84% | 0.3% | 0.1% | 0.3% | 0.7% |
| The Body Keeps the Score | Mistral-7B-Instruct-v0.2 | -74% | 0.1% | 0.2% | 0.3% | 0.5% |
| Complete Book of Chakras | Mistral-7B-Instruct-v0.2 | -70% | 0.3% | 0.3% | 0.4% | 1.1% |
| 50 Years of Attachment Theory | Mistral-7B-Instruct-v0.2 | -70% | 1.1% | 0.4% | 2.1% | 3.7% |
| Attachment Disturbances in Adults | Mistral-7B-Instruct-v0.2 | -62% | 1.1% | 1.2% | 0.7% | 3.1% |
| Psychology Major's Companion | Mistral-7B-Instruct-v0.2 | -62% | 1.3% | 1.2% | 0.4% | 2.9% |
| Psychology in Your Life | Mistral-7B-Instruct-v0.2 | -74% | 0.6% | 0.4% | 0.5% | 1.6% |

### Completed Book Summaries

1. [**Summary of Anodea Judith's Eastern Body Western Mind**](summaries/Eastern-Body_Western-Mind.md) (436 pages)
   > **Adult Development**
   >  
   > The process of individuation involves becoming a single, homogeneous being and embracing one's innermost uniqueness. This journey begins in early adulthood when individuals leave home and start living independently.
   > 
   > 1. Chakra One: Survival is the primary issue during this stage, which includes getting a place to live, learning self-care, and finding an independent income source. The time spent on this stage varies from person to person, with some spending their entire lives struggling for survival.
   > 2. Chakra Two: Once basic independence is achieved, individuals form sexual relationships. This stage involves satisfying emotional needs, often projected onto a partner. Emotional frustration may arise due to unresolved childhood conflicts or the inability to develop personal will and responsibility.
   > 3. Chakra Three: The individuation process allows individuals to become true individuals operating under their own power and will. This stage is marked by the liberation from conforming to parental, cultural, or societal expectations. It may be triggered by meaningless jobs or enslavement in relationships where one's needs are defined by others.
   > 4. Chakra Four:  Relationships become more empathetic and altruistic during this stage, focusing on maintaining lasting partnerships. This may involve reevaluating behavior towards others, examining family dynamics, and building connections with colleagues, friends, and the community.
   > 5. Chakra Five: Creative and personal expression is emphasized in this stage, where individuals make their personal contribution to the community. It often occurs around midlife and may precede or dominate other activities for more creative personalities.
   > 6. Chakra Six: This introverted stage involves reflection and study of patterns through exploration of mythology, religion, and philosophy. It is a time of searching and spiritual interest, which intensifies when children are grown and adults have more freedom for contemplation and spiritual practice.
   > 7. Chakra Seven: The final stage is characterized by wisdom, spiritual understanding, knowledge, and teaching. Individuals bring together information gathered throughout life to pass it on to others or pursue a spiritual path.
2. [**Summary of Stanley Rosenberg's Healing Power of the Vagus Nerve**](summaries/Healing-Power-Vagus-Nerve_Stanley-Rosenberg.md) (335 Pages)
   > The ANS is an integral part of the human nervous system, monitoring and regulating the activity of visceral organs such as heart, lungs, liver, gall bladder, stomach, intestines, kidneys, and sexual organs. Problems with any of these organs can arise from dysfunction of the ANS.
   > 
   > - **Old understanding of the ANS**: The ANS functioned in two states - stress and relaxation. 
   >   - Stress response is a survival mechanism activated when we feel threatened; it mobilizes our body to prepare for fight or flight. 
   >   - Relaxation response kicks in after threat has passed, keeping us in a relaxed state until the next threat appears.
   >   - Fails to explain why people continue to feel stressed even when they are not under any threat.
   > - **Polyvagal Theory (PVT)**: introduced the concept of three states
   >   - **Ventral Vagal State**: This state is characterized by social engagement and safety. It is associated with feelings of calmness, connection, and trust.
   >   - **Dorsal Vagal State**: This state is characterized by immobilization or shutdown. It is associated with feelings of fear, helplessness, and hopelessness.
   >   - **Sympathetic Nervous System (SNS)**: This system is responsible for the fight-or-flight response. It is associated with feelings of anxiety, stress, and tension.
3. [**Summary of Dr. David Frawley's Ayurveda and the Mind**](summaries/Ayurveda-and-the-Mind_David-Frawley.md) (181 Pages)
   > 1. *Section 1*: Explores Ayurvedic view of mind-body relationship, including gunas (Sattva, Rajas, Tamas), doshas (Vata, Pitta, Kapha), and five elements.
   > 2. *Section 2*: In-depth examination of functions of awareness through consciousness, intelligence, mind, ego, and self.
   > 3. *Section 3*: Ayurvedic therapies for the mind: outer (diet, herbs, massage) and inner (color, aroma, mantra).
   > 4. *Section 4*: Spiritual and yogic practices from an Ayurvedic perspective, integrating all therapies.
   > 5. *Appendix*: Contains tables on functions of the mind and their correspondences.
   > 6. *Goals*: To provide sufficient knowledge for personal use and relevant to psychologists/therapists.
4. [**Summary of Janina Fisher's Healing the Fragmented Selves of Trauma Survivors**](summaries/Healing-Fragmented-Selves-Trauma-Survivors_Janina-Fisher.md) (367 Pages)
   > 1. **Fragmentation and Internal Struggles**
   >    - Ten years ago, I observed a common pattern among traumatized clients: internal fragmentation.
   >    - Clients appeared integrated but showed signs of conflict between trauma-related perceptions and impulses versus present assessments.
   >    - They experienced paradoxical symptoms, such as the desire for kindness and spirituality alongside intense rage or violence.
   > 2. **Observable Patterns**
   >    - Describing these conflicts made them more observable and meaningful.
   >    - Each side represented a different way of surviving traumatic experiences.
   > 3. **Theoretical Model: Structural Dissociation**
   >    - The [Structural Dissociation model by Onno van der Hart, Ellert Nijenhuis, and Kathy Steele](https://link.springer.com/article/10.1007/BF03379560) explained these phenomena.
   >    - Rooted in neuroscience, it was accepted in Europe as a trauma model.
   > 4. **Brain's Innate Structure**
   >    - The brain's innate physical structure facilitates left-right brain disconnection under threat.
   >    - The left brain stays focused on daily tasks, while the right brain fosters an implicit self in survival mode.
   > 5. **Identifying and Owning Parts**
   >    - Some parts were easier to identify with or "own," while others were dismissed as "not me.
   >    - The internal relationships between fragmented aspects of self reflected traumatic environments.
   >    - The left-brain-dominant present-oriented self avoided or judged right-brain-dominant survival-oriented parts.
   >    - Both sides felt alienated from each other.
   > 6. **Functioning Self**
   >    - The functioning self carried on, trying to be "normal," but at the cost of feeling alienated or invaded by intrusive communications from parts.
5. [**Summary of John Bowlby's "A Secure Base"**](summaries/a-secure-base_john-bowlby.md) (133 Pages)
   > - **Attachment behavioral response** essential for protection from predation and foundation for psychological health.
   > - **Sensitive caregiving** crucial for secure attachment throughout the life cycle.
   > - **Real-life adversity** origin of subsequent psychopathology, opposed to endo-psychic entities.
   > - **Systematic scientific observation** important for understanding attachment phenomena.
   > - **Secure base for patients** therapists providing a safe space for emotional exploration.
6. [**Summary of Bessel van der Kolk's The Body Keeps the Score**](summaries/Body-Keeps-Score_Bessel-van-der-Kolk.md) (454 Pages)
   > **The Importance of Trauma:**
   > * Trauma reveals our fragility and man's inhumanity but also our resilience.
   > * Visionaries and societies have made profound advances from dealing with trauma.
   > * **Trauma is now the most urgent public health issue, and we have the knowledge to respond effectively.**
7. [**Summary of Yoga and Polyvagal Theory, from Steven Porges' Polyvagal Safety**](summaries/Yoga-Therapy+PolyVagal-Theory.md) (37 pages)
   > * **Top-down processes**: regulation of attention, intention, decreasing psychological stress, HPA axis and SNS activity, modulating immune function and inflammation
   > * **Bottom-up processes**: breathing techniques, movement practices, influencing musculoskeletal, cardiovascular, nervous system function, affecting HPA and SNS activity, emotional well-being
   > * **Self-regulation**: conscious ability to manage responses to threat or adversity, reducing symptoms of various conditions, mitigating allostatic load, shifting autonomic state
8. [**Summary of Llewellyn's Complete Book of Chakras: Your Definitive Source of Energy Center Knowledge for Health, Happiness, and Spiritual Evolution**](summaries/llewen-complete-book-chakras_1.md) (999 pages)
   * SECTION 1. CHAKRA FUNDAMENTALS AND BASIC PRACTICES
     > * Chakras are metaphysical energy centers that organize subtle energy for human use 
     > * **Three main systems of subtle energy in the body**: chakras, meridians/nadis, and auric fields
     > * Chakras convert physical energy into subtle energy and vice versa
     > * Seven main chakras regulate vital physical, psychological, and spiritual concerns
     > * Each chakra assists in embracing physical needs while attaining wisdom for enlightenment
     > * Section 1 is a pocket guide to understanding all aspects of chakras
   * [SECTION 2: CHAKRAS IN DEPTH. HISTORICAL, SCIENTIFIC, AND CROSS-CULTURAL UNDERSTANDINGS](summaries/llewen-complete-book-chakras_2.md)
     > * Recap of chakras as pulsing points of light and key to health, connection, and spiritual enlightenment
     > * Origin and history of chakra knowledge primarily from ancient India
     > * Chakras and their companions (subtle energies, body, energetic anatomies, kundalini) are explainable through scientific perspectives
9. [**Summary of Fifty Years of Attachment Theory: The Donald Winnicott Memorial Lecture**](summaries/50-years-attachment-theory.md) (54 pages)
   > **Background of Attachment Theory**
   > - Developed by Sir Richard Bowlby's father, John Bowlby
   > - Based on research into parent-child attachment relationship
   > - Originally called "research into bonding"
   > - Motivated by his own experience of losing a surrogate mother figure, Minnie, at age 4
   > 
   > **Early Influences and Education**
   > - Father was a successful surgeon with six children
   > - Children had twenty-three hour care from nannies and nursemaids
   > - Father grew attached to his nursemaid, Minnie
   > - When Minnie left, father felt pain but not traumatized
   > - Met John Alford at age 21, who inspired interest in maternal deprivation
10. [**Summary of Attachment Disturbances in Adults**](notes/Attachment-Disturbances-Adults2.md) (477 Pages)
    > - Part I: Foundational Concepts
    > - Part II: Assessment
    > - Part III: Treatment
    > - Part IV: Type-Specific Treatment
    > - Part V: A Treatment Guide and Expected Outcomes
11. [**Summary of The Psychology Major's Companion**](summaries/Psychology_Majors_Companion.md) (308 Pages)
    > **Questions Addressed:**
    > 1. **What do psychologists do? Where do they work?**
    > 2. **Is majoring in psychology the right choice for me?**
    > 3. **Skills and benefits of an undergraduate degree in psychology**
    >    * High-performing student
    >    * Organizational memberships, summer jobs, internships
    > 4. **Career opportunities after graduation**
    >    * Bachelor's level psychology majors
    >    * Job search right out of college
    > 5. **Graduate study in psychology**
    >    * Application process
    >    * Choosing an area of psychology
    >    * Making a compelling application
    > 6. **Benefits of psychology beyond education and career**
12. [**Summary of The Myth of Redemptive Violence**](summaries/myth-redemptive-violence.md) (5 Pages)
    > - Walter Wink's analysis of the pervasiveness of the belief that violence saves and brings peace in Western culture.
    > - Discussion on how this belief is rooted in ancient mythology and perpetuated through modern media, particularly cartoons.
13. [**Psychology In Your Life**](summaries/Psychology-In-Your-Life.md) (1072 Pages)
    > 1. **Biological Domain**: This domain focuses on the study of biological factors that influence behavior and mental processes. It includes research on genetics, neuroanatomy, physiology, and pharmacology.
    > 2. **Cognitive Domain**: The cognitive domain investigates mental processes such as perception, attention, memory, language, problem-solving, and decision-making.
    > 3. **Developmental Domain**: This domain examines the psychological and physical changes that occur throughout the human lifespan, from infancy to old age. It includes research on cognitive, social, emotional, and moral development.
    > 4. **Social and Personality Domain**: The social and personality domain explores how individuals think about themselves and others, as well as their interactions with other people. It includes research on personality traits, interpersonal relationships, group dynamics, and social influence.
    > 5. **Mental and Physical Health Domain**: This domain focuses on understanding mental health disorders and promoting positive mental and physical health. It includes research on diagnosis, treatment, and prevention of various mental and physical health conditions.


## Additional Resources

* [🐺🐦‍⬛ LLM Prompt Format Comparison/Test: Mixtral 8x7B Instruct with **17** different instruct templates](https://www.reddit.com/r/LocalLLaMA/comments/18ljvxb/llm_prompt_format_comparisontest_mixtral_8x7b/) WolframRavenwolf
* [🐺🐦‍⬛ LLM Comparison/Test: Ranking updated with 10 new models (the best 7Bs)!](https://www.reddit.com/r/LocalLLaMA/comments/18u122l/llm_comparisontest_ranking_updated_with_10_new/) WolframRavenwolf
* [Hallucination leaderboard](https://github.com/vectara/hallucination-leaderboard/) Vectara