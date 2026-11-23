---
id: architecture-era/rlhf
type: architecture-era
slug: rlhf
schema_version: 2.5
title: "RLHF & Instruction Alignment"
name: "Reinforcement Learning from Human Feedback"
era: post-transformer
era_range: "2022–2024"
year: 2022
family: llm
core_innovation: "Train a reward model on human preference comparisons, then fine-tune the language model with reinforcement learning plus a KL penalty to align it to human intent."
description: "A way to fine-tune a language model with people's judgements: people compare the model's answers, a reward model learns to predict which answer they prefer, and reinforcement learning then trains the language model to earn a higher predicted reward."
key_features: ["Supervised fine-tuning on human demonstrations", "Reward model trained on human preference comparisons", "Reinforcement learning against the reward model (PPO in InstructGPT)", "KL penalty to the supervised reference model"]
key_models: ["InstructGPT (2022)", "ChatGPT (2022)", "Llama-2-Chat (2023)"]
paper_title: "Training Language Models to Follow Instructions with Human Feedback (InstructGPT)"
paper_url: "https://arxiv.org/abs/2203.02155"
influenced_by: [architecture-era/gpt]
led_to: [architecture-era/reasoning-models]

analogy:
  title: "A coach who learns your taste from comparisons"
  body: "This is an analogy, not the mechanism. Picture a well-read trainee who can imitate any writing but does not know what you want. Writing the perfect answer to every request yourself is impossible, but looking at two of the trainee's drafts and saying which is better is easy. A coach watches many such comparisons and learns to predict your choice: that is the reward model. The trainee then practises, writing drafts and receiving the coach's score, while a rule keeps their writing close to how they wrote before practice began, because the coach is only reliable on drafts like the ones it has seen: that is the KL penalty. The exact rule is the key equation below."

narrative:
  motivation: "**Bigger language models were not better at doing what users asked.** The InstructGPT paper is by **Ouyang et al. (2022)** at OpenAI.\n\n- **The problem:** the authors write that making language models bigger does not inherently make them better at following a user's intent; large models can produce **untruthful, toxic or unhelpful** outputs.\n- **The cause they name:** the pre-training objective, predicting the next token of web pages, is different from **following the user's instructions helpfully and safely**.\n- **Why not write a reward by hand:** **Christiano et al. (2017)** note that for many goals it is unclear how to write a reward function, and that a simple one tends to be satisfied without satisfying what people actually want."
  approach: "**Learn a reward from people's comparisons, then optimise it.**\n\n- **Christiano et al. (2017):** people compared pairs of **1 to 2 second** video clips of an agent; a reward model was fitted to their choices and the agent trained against it, with feedback on **less than 1%** of its interactions. A simulated robot learned a **backflip** from **900** comparisons in under an hour.\n- **Ziegler et al. (2019):** applied this reward learning to language models, for text continuation and summarisation.\n- **Stiennon et al. (2020):** summaries of Reddit posts; their **1.3B** model trained with human feedback beat a supervised model **10 times** its size (**61%** against **43%** preferred to the reference summaries)."
  solution: "**InstructGPT's three steps** (the figure above).\n\n- **① Supervised fine-tuning (SFT):** GPT-3 is fine-tuned on answers written by labelers, about **13k** training prompts.\n- **② Reward model:** labelers rank **4 to 9** answers per prompt; a **6B** model learns to give the preferred answer the higher score, on about **33k** prompts.\n- **③ Reinforcement learning:** the SFT model is trained with **PPO** (proximal policy optimization) to earn a higher reward, on about **31k** prompts, with a per-token **KL penalty** (Kullback-Leibler divergence) that keeps it close to the SFT model.\n- **PPO-ptx:** a variant that also mixes in pre-training updates to limit losses on public benchmarks; in the paper, \"InstructGPT\" means this variant."
  outcome: "**Results the authors report:**\n\n- **Preference:** labelers preferred outputs of the **1.3B** InstructGPT to those of the **175B** GPT-3, which has over **100 times** more parameters; the 175B InstructGPT was preferred to the 175B GPT-3 **85 ± 3%** of the time.\n- **Truthfulness:** made-up information on closed-domain tasks fell from **41%** to **21%** of outputs.\n- **Cost:** the 175B PPO-ptx model took **60** petaflop/s-days to train, against **3,640** for GPT-3's pre-training.\n\n**What came next:**\n\n- **ChatGPT (2022):** OpenAI writes that it was trained with RLHF using the same methods as InstructGPT, with slight differences in data collection.\n- **Llama 2-Chat (2023):** Meta trained **two** reward models, one for helpfulness and one for safety, on over **1 million** binary comparisons."

key_equation:
  latex: "\\mathrm{objective}(\\phi)=\\mathbb{E}_{(x,y)\\sim D_{\\pi_{\\phi}^{\\mathrm{RL}}}}\\!\\left[r_{\\theta}(x,y)-\\beta\\log\\frac{\\pi_{\\phi}^{\\mathrm{RL}}(y\\mid x)}{\\pi^{\\mathrm{SFT}}(y\\mid x)}\\right]+\\gamma\\,\\mathbb{E}_{x\\sim D_{\\mathrm{pretrain}}}\\!\\left[\\log\\pi_{\\phi}^{\\mathrm{RL}}(x)\\right]"
  plain: "In words: take a prompt x, let the model being trained write an answer y, and score it with the reward model; subtract β times the log of how much more likely the trained model makes that answer than the frozen SFT model does (summed over the answer's tokens, this is the per-token KL penalty); then add γ times the log-likelihood of ordinary pre-training text. Training raises the average of all this. InstructGPT uses β = 0.02 and γ = 27.8; with γ = 0 the paper calls the model \"PPO\". This is equation 2 of the InstructGPT paper; the worked example computes its first two parts for one answer."
  terms:
    - { symbol: "\\phi", meaning: "the parameters of the model being trained" }
    - { symbol: "\\pi_{\\phi}^{RL}", meaning: "the model being trained with reinforcement learning (the policy); it starts as a copy of the SFT model" }
    - { symbol: "\\pi^{SFT}", meaning: "the supervised fine-tuned model, kept frozen as the reference" }
    - { symbol: "r_\\theta(x,y)", meaning: "the reward model's score for answer y to prompt x; a 6B model in InstructGPT" }
    - { symbol: "\\beta", meaning: "the strength of the KL penalty; 0.02 in InstructGPT" }
    - { symbol: "\\gamma", meaning: "the weight of the pre-training term; 27.8 in InstructGPT, 0 for plain PPO" }
    - { symbol: "D_{pretrain}", meaning: "the data GPT-3 was pre-trained on" }

strengths:
  - "**Comparisons, not scores:** Christiano et al. ask for comparisons because people gave them **more consistently** than absolute scores; their reward model needed feedback on **less than 1%** of the agent's interactions."
  - "**Better than 100 times the size:** labelers preferred outputs of the **1.3B** InstructGPT to those of the **175B** GPT-3, the authors report."
  - "**Cheap next to pre-training:** **4.9** petaflop/s-days for the 175B SFT model and **60** for the 175B PPO-ptx model, against **3,640** for GPT-3's pre-training."
  - "**Not only the training labelers:** labelers who wrote no training data preferred InstructGPT to GPT-3 at about the **same rate** as the training labelers."
  - "**Beyond its data:** the authors observed InstructGPT following instructions in **other languages** and answering questions about **code**, both rare in its fine-tuning data."

weaknesses:
  - "**The reward model is a stand-in:** Stiennon et al. found that optimising too hard against it makes summaries **worse**, until its scores become **anti-correlated** with human preference; InstructGPT adds the KL penalty against this."
  - "**An alignment tax:** the PPO model lost ground on public datasets such as **SQuAD, DROP, HellaSwag** and **WMT 2015 French to English**; mixing in pre-training (PPO-ptx) reduced the losses, but the model still lags GPT-3 on DROP, SQuADv2 and translation."
  - "**Whose preferences:** InstructGPT is aligned to about **40** contractors and to the researchers' instructions, not to human values at large; the labelers agreed with each other about **73%** of the time."
  - "**Still makes simple mistakes:** InstructGPT can accept **false premises**, **hedge** too much and lose track of several explicit constraints; OpenAI's ChatGPT announcement links its verbosity partly to **over-optimisation**."
  - "**Complex to run:** the DPO authors describe RLHF as **complex and often unstable**: a reward model is fitted first, then the language model is trained with reinforcement learning while kept close to the original."

visual:
  kind: distribution
  tokens: ["Answer A", "Answer B"]
  logits: [2.0, 0.5]
  temperatures: [1]
  panel_titles: ["Predicted chance that the labeler prefers each answer"]
  caption: "The reward model's predicted chance that a labeler prefers each of two answers, from its example scores 2.0 for answer A and 0.5 for answer B. A softmax over the two scores equals σ(2.0 − 0.5) ≈ 0.818 for A: the form of the reward-model loss in InstructGPT and of equation 1 in Christiano et al. (2017). The scores are an example, not from a trained model."

comparison:
  before: "GPT-3"
  after: "InstructGPT (PPO-ptx)"
  rows:
    - { aspect: "Training after pre-training", before: "None", after: "Supervised fine-tuning on labeler-written answers, then reinforcement learning against a reward model trained on labeler rankings", source: "https://arxiv.org/abs/2203.02155" }
    - { aspect: "Labelers' choice in a direct comparison of the 175B models", before: "Baseline", after: "Preferred 85 ± 3% of the time", source: "https://arxiv.org/abs/2203.02155" }
    - { aspect: "Made-up information on closed-domain tasks (summaries, closed-domain questions)", before: "41% of outputs", after: "21% of outputs", source: "https://arxiv.org/abs/2203.02155" }
    - { aspect: "Truthful and informative answers on TruthfulQA", before: "Baseline", after: "About twice as often", source: "https://arxiv.org/abs/2203.02155" }
    - { aspect: "Toxic outputs when asked to be respectful (RealToxicityPrompts)", before: "Baseline", after: "About 25% fewer", source: "https://arxiv.org/abs/2203.02155" }
    - { aspect: "Bias (Winogender, CrowS-Pairs)", before: "Baseline", after: "No significant improvement", source: "https://arxiv.org/abs/2203.02155" }
    - { aspect: "Public NLP datasets", before: "Baseline", after: "Ahead on HellaSwag; still behind on DROP, SQuADv2 and translation", source: "https://arxiv.org/abs/2203.02155" }
    - { aspect: "Training compute, 175B models", before: "3,640 petaflop/s-days of pre-training", after: "4.9 to train the SFT model; 60 to train the PPO-ptx model", source: "https://arxiv.org/abs/2203.02155" }

key_numbers:
  columns: ["Stage", "Setting", "Value in InstructGPT"]
  rows:
    - { cells: ["People", "Contractors labelling data", "about 40"], source: "https://arxiv.org/abs/2203.02155" }
    - { cells: ["Models", "Sizes of the model trained", "1.3B, 6B and 175B parameters"], source: "https://arxiv.org/abs/2203.02155" }
    - { cells: ["① Supervised fine-tuning", "Training prompts", "about 13k"], source: "https://arxiv.org/abs/2203.02155" }
    - { cells: ["① Supervised fine-tuning", "Epochs; residual dropout", "16; 0.2"], source: "https://arxiv.org/abs/2203.02155" }
    - { cells: ["② Reward model", "Training prompts", "about 33k"], source: "https://arxiv.org/abs/2203.02155" }
    - { cells: ["② Reward model", "Answers ranked per prompt (K)", "4 to 9, giving K(K−1)/2 comparisons"], source: "https://arxiv.org/abs/2203.02155" }
    - { cells: ["② Reward model", "Size; passes over the data", "6B parameters; 1 epoch"], source: "https://arxiv.org/abs/2203.02155" }
    - { cells: ["③ PPO", "Training prompts; episodes", "about 31k; 256k"], source: "https://arxiv.org/abs/2203.02155" }
    - { cells: ["③ PPO", "KL coefficient β; pre-training coefficient γ", "0.02; 27.8"], source: "https://arxiv.org/abs/2203.02155" }
    - { cells: ["③ PPO", "Clip ratio; batch size", "0.2; 512"], source: "https://arxiv.org/abs/2203.02155" }

misunderstandings:
  - misunderstanding: "RLHF aligns a model with human values in general."
    truth: "The InstructGPT authors write that they align the model to the stated preferences of a **specific group**, mostly their labelers and the researchers who wrote the labelling instructions, rather than any broader notion of human values. About **40** contractors produced the data, and they agreed with each other about **73%** of the time."
    source: "https://arxiv.org/abs/2203.02155"
  - misunderstanding: "The higher the reward model's score, the better the answer."
    truth: "The reward model is learned from limited data and is not a perfect copy of people's preferences. Stiennon et al. trained summarisers with weaker and stronger optimisation against it: under light optimisation summaries improved, but with more optimisation **real preferences fell** below the reward model's prediction, until its scores became **anti-correlated** with what people preferred."
    source: "https://arxiv.org/abs/2009.01325"
  - misunderstanding: "The reward model is trained on scores that people give to each answer."
    truth: "It is trained on **comparisons**. In Christiano et al., people say which of two clips is better, and the reward model is fitted so that the **difference** in reward predicts the chance one is chosen, the way a difference in Elo points predicts a chess result. The authors found comparisons **easier to give consistently** than absolute scores, and on continuous-control tasks predicting comparisons worked much better than predicting scores."
    source: "https://arxiv.org/abs/1706.03741"
  - misunderstanding: "RLHF makes a model better at everything."
    truth: "The PPO model lost performance on public datasets such as **SQuAD, DROP, HellaSwag** and **WMT 2015 French to English**, which the authors call an **alignment tax**. Mixing in pre-training reduced it, but InstructGPT still lags GPT-3 on DROP, SQuADv2 and translation, and it can still accept **false premises** and **hedge** on simple questions."
    source: "https://arxiv.org/abs/2203.02155"

diagram_svg: |
  <svg class="atlas-figure" viewBox="0 0 870 520" role="img" aria-label="The three stages of RLHF as in InstructGPT: supervised fine-tuning of GPT-3 on labeler-written answers; a reward model trained on labeler rankings of several sampled answers; and a PPO loop in which the policy answers prompts, the reward model scores each answer, a KL penalty to the frozen supervised model is subtracted, and PPO updates the policy.">
  <defs><marker id="rlhf-arrow" viewBox="0 0 8 8" refX="7" refY="4" markerWidth="7" markerHeight="7" orient="auto"><path class="d-arrow-strong" d="M0 0 L8 4 L0 8 Z"/></marker><marker id="rlhf-arrow-light" viewBox="0 0 8 8" refX="7" refY="4" markerWidth="7" markerHeight="7" orient="auto"><path class="d-arrow" d="M0 0 L8 4 L0 8 Z"/></marker><marker id="rlhf-arrow-accent" viewBox="0 0 8 8" refX="7" refY="4" markerWidth="7" markerHeight="7" orient="auto"><path class="d-arrow-accent" d="M0 0 L8 4 L0 8 Z"/></marker></defs>
  <rect class="d-frame" x="12" y="34" width="274" height="404" rx="8"/>
  <text class="d-title" x="149" y="24" text-anchor="middle">① Supervised fine-tuning</text>
  <rect class="d-frame" x="298" y="34" width="274" height="404" rx="8"/>
  <text class="d-title" x="435" y="24" text-anchor="middle">② Reward model from rankings</text>
  <rect class="d-frame" x="584" y="34" width="274" height="404" rx="8"/>
  <text class="d-title" x="721" y="24" text-anchor="middle">③ RL with PPO against the RM</text>
  <rect class="d-box d-role-input" x="32" y="52" width="234" height="26" rx="5"/>
  <text class="d-note" x="149" y="69" text-anchor="middle">prompt x: “Summarise this email in one line”</text>
  <path class="d-line-strong" d="M149 80 V96" marker-end="url(#rlhf-arrow)"/>
  <circle class="d-node d-role-input" cx="149" cy="105" r="6"/>
  <path class="d-line-strong" d="M138 122 Q139 112 149 112 Q159 112 160 122"/>
  <text class="d-note" x="169" y="110" text-anchor="start">a labeler writes</text>
  <text class="d-note" x="169" y="123" text-anchor="start">the ideal answer</text>
  <path class="d-line-strong" d="M149 126 V142" marker-end="url(#rlhf-arrow)"/>
  <rect class="d-box d-role-input" x="32" y="146" width="234" height="26" rx="5"/>
  <text class="d-note" x="149" y="163" text-anchor="middle">demonstration y*  (prompt + answer pair)</text>
  <rect class="d-box d-box" x="30" y="224" width="92" height="40" rx="5"/>
  <text class="d-label" x="76" y="241" text-anchor="middle">GPT-3</text>
  <text class="d-note" x="76" y="255" text-anchor="middle">pretrained</text>
  <rect class="d-box d-role-feedforward" x="176" y="224" width="92" height="40" rx="5"/>
  <text class="d-label" x="222" y="241" text-anchor="middle">SFT model</text>
  <text class="d-note" x="222" y="255" text-anchor="middle">π<tspan class="d-note" dy="-4">SFT</tspan><tspan dy="4">​</tspan></text>
  <path class="d-line-strong" d="M124 244 H172" marker-end="url(#rlhf-arrow)"/>
  <path class="d-line" d="M149 174 V240" marker-end="url(#rlhf-arrow-light)"/>
  <text class="d-note" x="157" y="196" text-anchor="start">trains on (x, y*)</text>
  <text class="d-math" x="149" y="296" text-anchor="middle">maximise log π<tspan class="d-note" dy="-4">SFT</tspan><tspan dy="4">​</tspan>(y* | x)</text>
  <text class="d-note" x="149" y="312" text-anchor="middle">ordinary supervised learning</text>
  <text class="d-note" x="149" y="356" text-anchor="middle">π<tspan class="d-note" dy="-4">SFT</tspan><tspan dy="4">​</tspan> becomes the starting point of</text>
  <text class="d-note" x="149" y="370" text-anchor="middle">the policy (③) and its frozen reference</text>
  <text class="d-label" x="149" y="424" text-anchor="middle">≈13k training prompts</text>
  <rect class="d-box d-role-input" x="375" y="52" width="120" height="26" rx="5"/>
  <text class="d-note" x="435" y="69" text-anchor="middle">prompt x</text>
  <path class="d-line-strong" d="M435 80 V92" marker-end="url(#rlhf-arrow)"/>
  <rect class="d-box d-role-feedforward" x="355" y="96" width="160" height="40" rx="5"/>
  <text class="d-label" x="435" y="113" text-anchor="middle">π<tspan class="d-note" dy="-4">SFT</tspan><tspan dy="4">​</tspan></text>
  <text class="d-note" x="435" y="127" text-anchor="middle">samples K answers</text>
  <path class="d-line" d="M435 138 L357 156" marker-end="url(#rlhf-arrow-light)"/>
  <rect class="d-box d-role-input" x="339" y="160" width="36" height="24" rx="5"/>
  <text class="d-label" x="357" y="176" text-anchor="middle">A</text>
  <path class="d-line" d="M435 138 L409 156" marker-end="url(#rlhf-arrow-light)"/>
  <rect class="d-box d-role-input" x="391" y="160" width="36" height="24" rx="5"/>
  <text class="d-label" x="409" y="176" text-anchor="middle">B</text>
  <path class="d-line" d="M435 138 L461 156" marker-end="url(#rlhf-arrow-light)"/>
  <rect class="d-box d-role-input" x="443" y="160" width="36" height="24" rx="5"/>
  <text class="d-label" x="461" y="176" text-anchor="middle">C</text>
  <path class="d-line" d="M435 138 L513 156" marker-end="url(#rlhf-arrow-light)"/>
  <rect class="d-box d-role-input" x="495" y="160" width="36" height="24" rx="5"/>
  <text class="d-label" x="513" y="176" text-anchor="middle">D</text>
  <circle class="d-node d-role-input" cx="338" cy="207" r="6"/>
  <path class="d-line-strong" d="M327 224 Q328 214 338 214 Q348 214 349 224"/>
  <text class="d-note" x="358" y="212" text-anchor="start">a labeler ranks them,</text>
  <text class="d-note" x="358" y="225" text-anchor="start">best to worst</text>
  <rect class="d-box d-role-input" x="339" y="240" width="36" height="24" rx="5"/>
  <text class="d-label" x="357" y="256" text-anchor="middle">D</text>
  <rect class="d-box d-role-input" x="391" y="240" width="36" height="24" rx="5"/>
  <text class="d-label" x="409" y="256" text-anchor="middle">C</text>
  <text class="d-label" x="383" y="256" text-anchor="middle">></text>
  <rect class="d-box d-role-input" x="443" y="240" width="36" height="24" rx="5"/>
  <text class="d-label" x="461" y="256" text-anchor="middle">A</text>
  <text class="d-label" x="435" y="256" text-anchor="middle">></text>
  <rect class="d-box d-role-input" x="495" y="240" width="36" height="24" rx="5"/>
  <text class="d-label" x="513" y="256" text-anchor="middle">B</text>
  <text class="d-label" x="487" y="256" text-anchor="middle">></text>
  <path class="d-line" d="M435 266 V302" marker-end="url(#rlhf-arrow-light)"/>
  <text class="d-note" x="445" y="279" text-anchor="start">every pair of answers:</text>
  <text class="d-note" x="445" y="292" text-anchor="start">y<tspan class="d-note" dy="3">w</tspan><tspan dy="-3">​</tspan> ranked above y<tspan class="d-note" dy="3">l</tspan><tspan dy="-3">​</tspan></text>
  <rect class="d-box d-role-router" x="335" y="306" width="200" height="40" rx="5"/>
  <text class="d-label" x="435" y="323" text-anchor="middle">reward model r<tspan class="d-note" dy="3">θ</tspan><tspan dy="-3">​</tspan>(x, y)</text>
  <text class="d-note" x="435" y="337" text-anchor="middle">6B parameters · one scalar score</text>
  <text class="d-math" x="435" y="372" text-anchor="middle">loss = −log σ(r<tspan class="d-note" dy="3">θ</tspan><tspan dy="-3">​</tspan>(x, y<tspan class="d-note" dy="3">w</tspan><tspan dy="-3">​</tspan>) − r<tspan class="d-note" dy="3">θ</tspan><tspan dy="-3">​</tspan>(x, y<tspan class="d-note" dy="3">l</tspan><tspan dy="-3">​</tspan>))</text>
  <text class="d-note" x="435" y="390" text-anchor="middle">K = 4 to 9 answers per prompt → every pair</text>
  <text class="d-label" x="435" y="424" text-anchor="middle">≈33k training prompts</text>
  <rect class="d-box d-role-input" x="630" y="52" width="152" height="26" rx="5"/>
  <text class="d-note" x="706" y="69" text-anchor="middle">new prompt x (no labels)</text>
  <path class="d-line-strong" d="M706 80 V96" marker-end="url(#rlhf-arrow)"/>
  <rect class="d-box d-role-feedforward" x="638" y="100" width="136" height="40" rx="5"/>
  <text class="d-label" x="706" y="117" text-anchor="middle">policy π<tspan class="d-note" dy="-4">RL</tspan><tspan dy="4">​</tspan><tspan class="d-note" dy="3">φ</tspan><tspan dy="-3">​</tspan></text>
  <text class="d-note" x="706" y="131" text-anchor="middle">starts as a copy of π<tspan class="d-note" dy="-4">SFT</tspan><tspan dy="4">​</tspan></text>
  <path class="d-line-strong" d="M706 142 V160" marker-end="url(#rlhf-arrow)"/>
  <rect class="d-box d-role-input" x="656" y="164" width="100" height="26" rx="5"/>
  <text class="d-note" x="706" y="181" text-anchor="middle">answer y</text>
  <path class="d-line-strong" d="M706 192 V210" marker-end="url(#rlhf-arrow)"/>
  <rect class="d-box d-role-router" x="638" y="214" width="136" height="40" rx="5"/>
  <text class="d-label" x="706" y="231" text-anchor="middle">reward model r<tspan class="d-note" dy="3">θ</tspan><tspan dy="-3">​</tspan></text>
  <text class="d-note" x="706" y="245" text-anchor="middle">frozen, from ②</text>
  <path class="d-line-strong" d="M706 256 V274" marker-end="url(#rlhf-arrow)"/>
  <rect class="d-box d-role-attention" x="626" y="278" width="160" height="44" rx="5"/>
  <text class="d-label" x="706" y="296" text-anchor="middle">reward</text>
  <text class="d-math" x="706" y="313" text-anchor="middle">r<tspan class="d-note" dy="3">θ</tspan><tspan dy="-3">​</tspan>(x, y) − β·log(π<tspan class="d-note" dy="-4">RL</tspan><tspan dy="4">​</tspan> / π<tspan class="d-note" dy="-4">SFT</tspan><tspan dy="4">​</tspan>)</text>
  <rect class="d-box d-role-feedforward" x="798" y="160" width="52" height="34" rx="5"/>
  <text class="d-label" x="824" y="175" text-anchor="middle">π<tspan class="d-note" dy="-4">SFT</tspan><tspan dy="4">​</tspan></text>
  <text class="d-note" x="824" y="188" text-anchor="middle">frozen</text>
  <path class="d-line" d="M758 177 H794" marker-end="url(#rlhf-arrow-light)"/>
  <path class="d-line-back" d="M824 196 V300 H790" marker-end="url(#rlhf-arrow-light)"/>
  <text class="d-note" x="0" y="0" text-anchor="middle" transform="translate(838 250) rotate(90)">KL penalty</text>
  <path class="d-line-accent" d="M624 300 H604 V120 H634" marker-end="url(#rlhf-arrow-accent)"/>
  <text class="d-label" x="0" y="0" text-anchor="middle" transform="translate(598 210) rotate(-90)">PPO update of φ</text>
  <text class="d-note" x="706" y="348" text-anchor="middle">β sets how tightly π<tspan class="d-note" dy="-4">RL</tspan><tspan dy="4">​</tspan> is held</text>
  <text class="d-note" x="706" y="362" text-anchor="middle">near π<tspan class="d-note" dy="-4">SFT</tspan><tspan dy="4">​</tspan> (limits reward hacking)</text>
  <text class="d-label" x="721" y="424" text-anchor="middle">≈31k training prompts</text>
  <path class="d-line" d="M16 454 H854" opacity="0.6"/>
  <text class="d-math" x="435" y="474" text-anchor="middle">objective(φ) = E<tspan class="d-note" dy="3">(x, y) ~ π<tspan class="d-note" dy="-4">RL</tspan><tspan dy="4">​</tspan></tspan><tspan dy="-3">​</tspan>[ r<tspan class="d-note" dy="3">θ</tspan><tspan dy="-3">​</tspan>(x, y) − β·log(π<tspan class="d-note" dy="-4">RL</tspan><tspan dy="4">​</tspan><tspan class="d-note" dy="3">φ</tspan><tspan dy="-3">​</tspan>(y | x) / π<tspan class="d-note" dy="-4">SFT</tspan><tspan dy="4">​</tspan>(y | x)) ] + γ·E<tspan class="d-note" dy="3">x ~ pretraining</tspan><tspan dy="-3">​</tspan>[ log π<tspan class="d-note" dy="-4">RL</tspan><tspan dy="4">​</tspan><tspan class="d-note" dy="3">φ</tspan><tspan dy="-3">​</tspan>(x) ]</text>
  <text class="d-note" x="435" y="494" text-anchor="middle">InstructGPT (Ouyang et al. 2022) keeps γ > 0 (“PPO-ptx”) to limit regressions on public NLP tasks; plain “PPO” sets γ = 0.</text>
  <text class="d-note" x="435" y="508" text-anchor="middle">Learning a reward model from human comparisons follows Christiano et al. 2017.</text>
  </svg>
diagram_caption: "RLHF as in InstructGPT: fine-tune GPT-3 on labeler-written answers, train a reward model on labeler rankings of sampled answers, then improve the policy with PPO against that reward minus a KL penalty to the frozen SFT model."
notable_models:
  - { name: "InstructGPT", year: 2022, note: "Applied supervised fine-tuning, a reward model and PPO to instruction following; labelers preferred its 1.3B model to 175B GPT-3." }
  - { name: "ChatGPT", year: 2022, note: "Trained with RLHF using the same methods as InstructGPT, for dialogue." }
  - { name: "Llama-2-Chat", year: 2023, note: "Open weights tuned with supervised fine-tuning and RLHF, using two separate reward models for helpfulness and safety." }
  - { name: "DPO-tuned models", year: 2023, note: "Direct Preference Optimization solves the standard RLHF problem with a simple classification loss, without sampling from the model during fine-tuning; its authors report that it exceeds PPO-based RLHF at controlling sentiment and matches or improves response quality in summarisation and single-turn dialogue." }

resources:
  - { kind: paper, title: "Training Language Models to Follow Instructions with Human Feedback (Ouyang et al., 2022)", url: "https://arxiv.org/abs/2203.02155" }
  - { kind: blog, title: "Illustrating Reinforcement Learning from Human Feedback (Hugging Face)", url: "https://huggingface.co/blog/rlhf" }
  - { kind: blog, title: "Learning to Summarize with Human Feedback (OpenAI)", url: "https://openai.com/index/learning-to-summarize-with-human-feedback/" }
  - { kind: paper, title: "Direct Preference Optimization: Your Language Model is Secretly a Reward Model (Rafailov et al., 2023)", url: "https://arxiv.org/abs/2305.18290" }

learn_first:
  - { label: "GPT & autoregressive pretraining", href: "/models/architecture/gpt", tag: "Architecture" }
  - { label: "Policy-gradient RL & PPO basics", href: "/learn/concepts/policy-gradient", tag: "Concept" }
  - { label: "KL divergence — measuring distribution drift", href: "/learn/concepts/kl-divergence", tag: "Math" }

tags: [rlhf, alignment, instruction-tuning, ppo, llm]
updated: '2026-11-23'
sources:
  - { url: "https://arxiv.org/abs/2203.02155", title: "Training Language Models to Follow Instructions with Human Feedback (Ouyang et al., 2022)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1706.03741", title: "Deep reinforcement learning from human preferences (Christiano et al., 2017)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1707.06347", title: "Proximal Policy Optimization Algorithms (Schulman et al., 2017)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/1909.08593", title: "Fine-Tuning Language Models from Human Preferences (Ziegler et al., 2019)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2009.01325", title: "Learning to summarize from human feedback (Stiennon et al., 2020)", trust: 0.95 }
  - { url: "https://openai.com/index/chatgpt/", title: "Introducing ChatGPT (OpenAI, 30 November 2022)", trust: 0.85 }
  - { url: "https://arxiv.org/abs/2307.09288", title: "Llama 2: Open Foundation and Fine-Tuned Chat Models (Touvron et al., 2023)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2305.18290", title: "Direct Preference Optimization (Rafailov et al., 2023)", trust: 0.9 }
---

## What Changed

GPT-3 and InstructGPT have the **same architecture**; the authors write that they differ only in
that InstructGPT is fine-tuned on their human data. Every row of the table above comes from that
fine-tuning, measured by the InstructGPT authors.

How to read the table above:

- **Baseline** means GPT-3 is the reference the InstructGPT result is measured against.
- **Labelers' choice:** labelers saw two answers to the same prompt and picked the better one;
  85% means InstructGPT's answer was picked in 85 of 100 comparisons.
- **Closed-domain tasks** are tasks whose answer must use only the input text, such as a summary;
  a made-up fact there is called a hallucination.
- **Petaflop/s-days** measure training compute: a computer doing 10¹⁵ operations per second for a
  day. Training the 175B PPO-ptx model took 60 of them, under 2% of GPT-3's 3,640.
- **Not every row improved:** bias did not change significantly, and some benchmark scores fell.

## Key Ideas

- **Preferences as data:** labelers rank answers, and the rankings train a reward model.
- **A learned reward:** the reward model stands in for a person during reinforcement learning.
- **A leash:** the KL penalty keeps the trained model close to the supervised one, where the
  reward model is reliable.
- **Direct alternatives:** DPO solves the same objective with a classification loss, with no
  separate reinforcement-learning loop.

## How It Works

The figure above has **three panels**, the three steps of InstructGPT. Each step starts from the
model the step before produced. Follow the numbers ① to ③.

A **prompt** is the text a user sends; an **answer** is what the model writes. In
reinforcement-learning terms the model being trained is the **policy**.

1. **Prompt x (panel ①).** Prompts come from the OpenAI API Playground and from labelers; about
   **13k** are used for this step.
2. **A labeler writes the ideal answer.** The prompt and its answer form a **demonstration**.
3. **SFT model.** GPT-3 is fine-tuned on the demonstrations with ordinary supervised learning:
   make the labeler's answer more likely. This model, $\pi^{SFT}$, is the starting point of step
   ③ and stays frozen there as the reference.
4. **Sample K answers (panel ②).** For a new prompt, **4 to 9** answers are sampled, mostly from
   the SFT models (A to D in the figure).
5. **A labeler ranks them,** best to worst. K answers give K(K−1)/2 pairs: 4 answers give 6
   pairs, 9 give 36.
6. **Reward model.** A **6B** model reads a prompt and one answer and outputs **one number**. For
   each pair it learns to score the preferred answer $y_w$ above the other $y_l$, with the loss
   $-\log \sigma(r(x, y_w) - r(x, y_l))$; σ, the sigmoid, squashes any number into 0 to 1. All
   pairs of one prompt go into one batch, which the authors found stopped the model overfitting.
7. **The policy answers (panel ③).** The policy starts as a copy of the SFT model and answers new
   prompts that carry no labels.
8. **Score it.** The frozen reward model scores the answer.
9. **KL penalty.** The frozen SFT model reads the same answer. At every token
   $\beta \cdot \log(\pi^{RL}/\pi^{SFT})$ is subtracted, so moving far from the SFT model costs
   reward.
10. **PPO update.** PPO changes the policy to raise the penalised reward, but clips each step: the
    ratio of new to old probability of an answer earns nothing beyond **1 ± 0.2**. A value
    function, initialised from the reward model, estimates how much reward to expect.
11. **Pre-training mix (PPO-ptx).** Updates from ordinary next-token prediction on GPT-3's
    pre-training data are added, weighted by γ = **27.8**.

## Worked Example

**One comparison, one penalised reward, one clipped update.** All numbers are an example, not from
a trained model, except β = 0.02 and ε = 0.2, which are the paper's settings.

**Part 1: the reward model on one pair** (what the figure draws). For one prompt the reward model
scores answer A **2.0** and answer B **0.5**; the labeler preferred A.

1. **Difference:** 2.0 − 0.5 = **1.5**.
2. **Sigmoid:** σ(1.5) = 1 / (1 + $e^{-1.5}$) = 1 / (1 + 0.2231) ≈ **0.818**, the predicted chance
   that the labeler prefers A.
3. **The same as a softmax over the two scores:** $e^{2.0} \approx 7.389$ and $e^{0.5} \approx 1.649$;
   7.389 / (7.389 + 1.649) ≈ **0.818**.
4. **Loss:** −ln 0.818 ≈ **0.201**. Had the labeler preferred B, the loss would be
   −ln(1 − σ(1.5)) = −ln 0.18243 ≈ **1.701**, and training would push the two scores the other way.

**Part 2: the KL penalty on one answer.** The policy writes a three-token answer:

| Token | Policy probability | SFT model probability | ln(policy ÷ SFT) |
| --- | --- | --- | --- |
| 1 | 0.5 | 0.4 | ln 1.25 ≈ 0.22314 |
| 2 | 0.6 | 0.5 | ln 1.2 ≈ 0.18232 |
| 3 | 0.9 | 0.9 | ln 1 = 0 |

1. **Add the log ratios:** 0.22314 + 0.18232 + 0 = 0.40546 ≈ **0.4055**. This equals
   ln(0.27 / 0.18), the ratio of the whole answer's probabilities: 0.5 × 0.6 × 0.9 = 0.27 and
   0.4 × 0.5 × 0.9 = 0.18.
2. **Penalty with β = 0.02:** 0.02 × 0.4055 ≈ **0.0081**.
3. **Penalised reward**, with the reward model's score 2.0: 2.0 − 0.0081 ≈ **1.992**.
4. **With β = 2.0**, 100 times the default, which the authors also tested: the penalty is
   2.0 × 0.4055 ≈ 0.811 and the reward ≈ **1.189**. In their test this β lowered the validation
   reward a lot and still did not remove the losses on public datasets.

**Part 3: PPO's clip.** Suppose this answer did better than expected, an **advantage** of 1.0, and
an update would raise its probability ratio, new over old, to 1.5.

1. **Unclipped:** 1.5 × 1.0 = 1.5.
2. **Clipped** to 1 + ε = 1.2: 1.2 × 1.0 = 1.2.
3. **PPO keeps the smaller:** **1.2**. Pushing the ratio past 1.2 earns nothing more, so one batch
   cannot move the policy far.

**What this means:** the reward model turns rankings into a number; the KL penalty charges the
policy a small price for drifting from the SFT model, a price set by β; and the clip limits how
far each update can go.
