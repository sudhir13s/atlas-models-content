---
id: architecture-era/reasoning-models
type: architecture-era
slug: reasoning-models
schema_version: 2.7
title: "Reasoning Models (Inference-Time Scaling)"
name: "Reasoning Models"
era: post-transformer
era_range: "2024–2026"
year: 2024
family: llm
core_innovation: "A long chain of thought, trained with large-scale reinforcement learning, generated before the answer, so that, as OpenAI and DeepSeek report, spending more computation at inference time raises accuracy."
description: "A language model trained, with reinforcement learning, to write out a long chain of reasoning before its final answer, so that it can spend more computation on harder problems; OpenAI o1 and DeepSeek-R1 are examples."
key_features: ["Long chain of thought before the answer", "Trained with large-scale reinforcement learning", "Accuracy improves with more test-time compute", "Self-correction within the chain of thought"]
key_models: ["OpenAI o1 (2024)", "DeepSeek-R1 (2025)", "o3 (2025)"]
paper_title: "DeepSeek-R1: Incentivizing Reasoning Capability in LLMs via Reinforcement Learning"
paper_url: "https://arxiv.org/abs/2501.12948"
influenced_by: [architecture-era/rlhf, architecture-era/mixture-of-experts]
led_to: []

analogy:
  title: "The student who shows their work"
  body: "This is an analogy, not the mechanism. Two students take a hard maths exam. The first writes an answer straight away. The second fills pages with working, spots a mistake, crosses it out, tries another way and only then circles an answer. A reasoning model is the second student. DeepSeek-R1-Zero was coached like a strict marker who checks only the circled answer and whether the page follows the required layout, never the working; the habit of long working grew from that alone. Unlike a student's working, the model's reasoning is text it generates one token at a time, and every token costs computation."

narrative:
  motivation: "**Showing worked steps helped, but only in big models, and only with written examples.**\n\n- **Chain-of-thought prompting (Wei et al., 2022):** putting **eight** worked examples with intermediate steps into the prompt raised PaLM 540B's solve rate on the GSM8K maths word problems from **18%** to **57%**.\n- **The limits:** the gains appeared only in models of about **100B** parameters; the DeepSeek-R1 authors add that this kind of success depends heavily on **human-annotated demonstrations** and is still insufficient for more complex problems."
  approach: "**Reward only the final answer, and let reinforcement learning find the reasoning.**\n\n- **DeepSeek-R1-Zero (DeepSeek-AI, 2025):** reinforcement learning applied directly to the **DeepSeek-V3-Base** model, with **no** supervised fine-tuning first and **no** human-written reasoning.\n- **The reward:** rule-based checks only: is the final answer **correct** (a maths answer in a set format, code run against test cases), and is the reasoning inside the required tags. The authors avoid neural reward models here because they are susceptible to **reward hacking**.\n- **The algorithm:** GRPO (Group Relative Policy Optimization), which scores each sampled answer against the other answers to the same question instead of training a separate value model.\n- **OpenAI o1 (2024):** OpenAI states that o1 is trained with a large-scale reinforcement-learning algorithm to use its chain of thought; its announcement does not describe the algorithm or the reward."
  solution: "**Longer reasoning grew by itself; DeepSeek-R1 then made it readable.**\n\n- **What appeared:** R1-Zero's responses grew steadily longer through training, to **hundreds to thousands** of tokens, with reflection, verification and trying other approaches, including a sudden rise in the word \"wait\".\n- **Its problems:** poor readability and **language mixing**, English and Chinese in one chain of thought.\n- **DeepSeek-R1's pipeline:** **thousands** of cold-start examples of readable reasoning → reinforcement learning with a **language-consistency** reward → rejection sampling and fine-tuning on about **800,000** samples → a second reinforcement-learning stage that adds reward models for helpfulness and harmlessness.\n- **OpenAI's description of o1:** it learns to recognise and correct its mistakes, to break tricky steps into simpler ones and to try a different approach when one is not working."
  outcome: "**Results the makers report:**\n\n- **DeepSeek-R1-Zero:** pass@1 on AIME 2024 (a US maths competition) rose from **15.6%** to **77.9%** during training, and to **86.7%** with self-consistency, a majority vote over several answers. The paper's first version (January 2025) gave **71.0%** in place of 77.9%; the figures here are from the current version.\n- **DeepSeek-R1:** **79.8%** pass@1 on AIME 2024; its reasoning was distilled into **six** smaller Qwen and Llama models of **1.5B** to **70B** parameters.\n- **OpenAI o1:** on AIME 2024, **74%** with one sample per problem and **83%** with a consensus of 64 samples; OpenAI reports performance improving with both train-time and test-time compute.\n- **OpenAI o3 (2025):** OpenAI states that it pushed an **additional order of magnitude** in both training compute and inference-time reasoning beyond o1 and still saw clear gains."

key_equation:
  latex: "A_i=\\frac{r_i-\\mathrm{mean}(\\{r_1,r_2,\\dots,r_G\\})}{\\mathrm{std}(\\{r_1,r_2,\\dots,r_G\\})}"
  plain: "In words: for one question, sample G answers and score each one with the reward; an answer's advantage is how far its reward sits above or below the group's average, measured in standard deviations. Answers that beat their siblings get a positive advantage and are made more likely; worse ones are made less likely. This is equation 3 of the DeepSeek-R1 paper, the part of GRPO that replaces a separate value model; DeepSeek-R1-Zero samples G = 16 answers per question. The worked example computes it for four answers."
  terms:
    - { symbol: "A_i", meaning: "the advantage of answer i: how much better it scored than the group" }
    - { symbol: "r_i", meaning: "the reward of answer i; for reasoning tasks, the accuracy reward plus the format reward" }
    - { symbol: "G", meaning: "the number of answers sampled for one question; 16 in DeepSeek-R1-Zero" }
    - { symbol: "mean", meaning: "the average reward of the G answers" }
    - { symbol: "std", meaning: "the standard deviation of the G rewards, how spread out they are" }

strengths:
  - "**No human-written reasoning:** DeepSeek-R1-Zero's rewards are rule-based checks of the final answer and the format, so its reasoning trajectories need **no human labels**, the authors report."
  - "**More thinking, better results:** OpenAI reports that o1's performance improves with **more time spent thinking**, and for o3 that performance keeps climbing if the model is allowed to think longer."
  - "**Effort follows difficulty:** DeepSeek reports R1 using fewer than **7,000** thinking tokens on simple 2024 competition maths problems and more than **18,000** on the hardest, **8,793** on average."
  - "**Small models can learn it:** the DeepSeek-R1 authors report **28.9%** pass@1 on AIME 2024 for their **1.5B** distilled model, against **9.3%** for GPT-4o."
  - "**A modest training bill, as reported:** DeepSeek states **147K** H800 GPU hours for R1-Zero, the fine-tuning data and R1 together, about **$294K** at an assumed **$2** per GPU hour, on top of the already trained DeepSeek-V3-Base."

weaknesses:
  - "**Every thought is paid for:** in the OpenAI API, reasoning tokens are **not visible** but take space in the context window and are **billed as output tokens**; OpenAI writes that a response may use from a few hundred to **tens of thousands** of them."
  - "**Needs a checkable answer:** the DeepSeek-R1 authors write that reliable rule-based rewards are hard to build for tasks such as writing, and that a reward given by a model is easier to **exploit** as training goes on."
  - "**Rough edges in DeepSeek-R1:** its authors list **language mixing**, sensitivity to prompts (few-shot prompting **degrades** it), **overthinking** on simple questions, and no use of tools such as search engines or calculators."
  - "**Not for every task:** OpenAI reports that o1-preview was **not preferred** to GPT-4o on some natural-language tasks."
  - "**Little is published about o1:** users see only a **model-generated summary** of its chain of thought, and OpenAI's announcement does not state o1's size, architecture, training data, reward or reinforcement-learning algorithm."

visual:
  kind: group_advantages
  answers: ["Answer 1: 12", "Answer 2: 12", "Answer 3: 15", "Answer 4: 9"]
  rewards: [2, 2, 1, 1]
  caption: "Parts 1 and 2 of the worked example: the reward of each of the four answers (accuracy point plus format point; the correct answer is 12), then each answer's group-relative advantage computed from them. The rewards are an example, not from a trained model."

comparison:
  before: "Non-reasoning model (GPT-4o, May 2024)"
  after: "Reasoning model (DeepSeek-R1)"
  rows:
    - { aspect: "How a solution is written", before: "Typically directly, without intermediate thinking steps, in the DeepSeek authors' description", after: "A long chain of thought with self-reflection, backtracking and trying other approaches", source: "https://arxiv.org/abs/2501.12948" }
    - { aspect: "Solve rate on a collection of 2024 competition maths problems", before: "24.7%", after: "61.8%", source: "https://arxiv.org/abs/2501.12948" }
    - { aspect: "Tokens written per problem on that collection", before: "711 output tokens on average", after: "8,793 thinking tokens on average", source: "https://arxiv.org/abs/2501.12948" }
    - { aspect: "AIME 2024, one sample (pass@1)", before: "9.3%", after: "79.8%", source: "https://arxiv.org/abs/2501.12948" }
    - { aspect: "AIME 2024 with a majority vote", before: "13.4% over 64 samples", after: "86.7%", source: "https://arxiv.org/abs/2501.12948" }
    - { aspect: "Spending more tokens by sampling many answers", before: "Minimal improvement on the 2024 collection with 16 samples, despite more total tokens than DeepSeek-R1", after: "Pass@64 on AIME 2024 is 90.0%, against 79.8% for one sample", source: "https://arxiv.org/abs/2501.12948" }

key_numbers:
  columns: ["Setting", "DeepSeek-R1-Zero", "DeepSeek-R1"]
  rows:
    - { cells: ["Starting model", "DeepSeek-V3-Base: 671B parameters, 37B active per token", "DeepSeek-V3-Base, first fine-tuned on cold-start data"], source: "https://arxiv.org/abs/2501.12948" }
    - { cells: ["Human-written reasoning before reinforcement learning", "None", "Thousands of cold-start examples"], source: "https://arxiv.org/abs/2501.12948" }
    - { cells: ["Answers sampled per question (G)", "16", "16 (first RL stage)"], source: "https://arxiv.org/abs/2501.12948" }
    - { cells: ["Maximum answer length, tokens", "32,768, then 65,536 after step 8,200", "32,768 (first RL stage)"], source: "https://arxiv.org/abs/2501.12948" }
    - { cells: ["Questions per step; answers per batch", "32; 512", "32; 512 (first RL stage)"], source: "https://arxiv.org/abs/2501.12948" }
    - { cells: ["Learning rate; KL coefficient", "3 × 10⁻⁶; 0.001", "3 × 10⁻⁶; 0.001 (first RL stage)"], source: "https://arxiv.org/abs/2501.12948" }
    - { cells: ["Sampling temperature", "1", "1, then 0.7 in the second RL stage"], source: "https://arxiv.org/abs/2501.12948" }
    - { cells: ["Training steps", "10,400 (1.6 epochs)", "1,700 in the second RL stage; the training-details section does not give the first stage's count"], source: "https://arxiv.org/abs/2501.12948" }
    - { cells: ["Rewards", "Accuracy and format, rule-based", "Rule-based and language consistency; reward models added in the second stage"], source: "https://arxiv.org/abs/2501.12948" }
    - { cells: ["H800 GPU hours", "101K", "41K, plus 5K to create the fine-tuning data"], source: "https://arxiv.org/abs/2501.12948" }

misunderstandings:
  - misunderstanding: "A reasoning model is a new kind of neural network."
    truth: "DeepSeek-R1 and DeepSeek-R1-Zero are trained on top of **DeepSeek-V3-Base**, a mixture-of-experts language model with **671B** parameters, **37B** of them active per token. What changes is the **training**: reinforcement learning with rule-based rewards. The reasoning itself is ordinary generated text, written between `<think>` and `</think>`."
    source: "https://arxiv.org/abs/2501.12948"
  - misunderstanding: "The reasoning that o1 shows its users is its raw chain of thought."
    truth: "OpenAI decided **not to show** the raw chains of thought to users, citing user experience, competitive advantage and the option of monitoring the chain of thought. For the o1 series it shows a **model-generated summary** of the chain of thought instead."
    source: "https://openai.com/index/learning-to-reason-with-llms/"
  - misunderstanding: "If a model writes out its reasoning, its answer must be right."
    truth: "Wei et al. state that there is **no guarantee of correct reasoning paths**, which can lead to both correct and incorrect answers, and they leave open, as a question for further work, whether the network is actually \"reasoning\"."
    source: "https://arxiv.org/abs/2201.11903"
  - misunderstanding: "A reasoning model thinks for the same length on every question."
    truth: "DeepSeek reports that R1 adjusts its effort to the problem: fewer than **7,000** thinking tokens on simple 2024 competition maths problems, more than **18,000** on the hardest. Its authors also report **overthinking**: excessive reasoning on simpler questions."
    source: "https://arxiv.org/abs/2501.12948"

diagram_svg: |
  <svg class="atlas-figure" viewBox="0 0 860 462" role="img" aria-label="A reasoning model's response as a token timeline: after the prompt it generates a long span of reasoning tokens, often hidden from the user but billed and counted as output, then a short final answer; more reasoning tokens cost latency and money and raise accuracy on multi-step problems, and the behaviour is trained with reinforcement learning on problems whose answers can be checked automatically.">
  <defs><marker id="reasoning-models-arrow" viewBox="0 0 8 8" refX="7" refY="4" markerWidth="7" markerHeight="7" orient="auto"><path class="d-arrow-strong" d="M0 0 L8 4 L0 8 Z"/></marker><marker id="reasoning-models-arrow-accent" viewBox="0 0 8 8" refX="7" refY="4" markerWidth="7" markerHeight="7" orient="auto"><path class="d-arrow-accent" d="M0 0 L8 4 L0 8 Z"/></marker></defs>
  <rect class="d-box d-role-input" x="74.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-input" x="90.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-input" x="106.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-input" x="122.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-input" x="138.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-input" x="154.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-input" x="170.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-frame" x="195.5" y="65" width="501" height="27" rx="4"/>
  <rect class="d-box d-role-attention" x="201.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-attention" x="217.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-attention" x="233.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-attention" x="249.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-attention" x="265.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-attention" x="281.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-attention" x="297.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-attention" x="313.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-attention" x="329.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-attention" x="345.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-attention" x="361.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-attention" x="377.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-attention" x="393.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-attention" x="409.5" y="72" width="13" height="13" rx="2"/>
  <text class="d-label" x="446" y="83" text-anchor="middle">⋯</text>
  <rect class="d-box d-role-attention" x="469.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-attention" x="485.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-attention" x="501.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-attention" x="517.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-attention" x="533.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-attention" x="549.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-attention" x="565.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-attention" x="581.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-attention" x="597.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-attention" x="613.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-attention" x="629.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-attention" x="645.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-attention" x="661.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-attention" x="677.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-output" x="708.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-output" x="724.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-output" x="740.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-output" x="756.5" y="72" width="13" height="13" rx="2"/>
  <rect class="d-box d-role-output" x="772.5" y="72" width="13" height="13" rx="2"/>
  <path class="d-line-strong" d="M74.5 64 V58 H183.5 V64"/>
  <text class="d-title" x="129" y="40" text-anchor="middle">Prompt</text>
  <text class="d-note" x="129" y="53" text-anchor="middle">input tokens</text>
  <path class="d-line-strong" d="M195.5 64 V58 H696.5 V64"/>
  <text class="d-title" x="446" y="40" text-anchor="middle">Reasoning span: a long chain of thought</text>
  <text class="d-note" x="446" y="53" text-anchor="middle">in DeepSeek-R1 written between &lt;think&gt; and &lt;/think&gt;</text>
  <path class="d-line-strong" d="M708.5 64 V58 H785.5 V64"/>
  <text class="d-title" x="747" y="40" text-anchor="middle">Answer</text>
  <text class="d-note" x="747" y="53" text-anchor="middle">short, visible</text>
  <path class="d-line" d="M262.625 93 V101"/>
  <text class="d-note" x="262.625" y="113" text-anchor="middle">works through steps</text>
  <path class="d-line" d="M384.875 93 V101"/>
  <text class="d-note" x="384.875" y="113" text-anchor="middle">re-checks a step</text>
  <path class="d-line" d="M507.125 93 V101"/>
  <text class="d-note" x="507.125" y="113" text-anchor="middle">“wait…” re-evaluates</text>
  <path class="d-line" d="M629.375 93 V101"/>
  <text class="d-note" x="629.375" y="113" text-anchor="middle">tries another approach</text>
  <path class="d-line-strong" d="M195.5 125 V131 H785.5 V125"/>
  <text class="d-label" x="490.5" y="147" text-anchor="middle">Reasoning + answer are both output: billed as output tokens, counted against the output limit (OpenAI API)</text>
  <text class="d-note" x="490.5" y="161" text-anchor="middle">the raw reasoning can be hidden: o1 shows the user only a summary; DeepSeek-R1 writes it out between the tags</text>
  <path class="d-line-strong" d="M74.5 192 H785.5" marker-end="url(#reasoning-models-arrow)"/>
  <text class="d-note" x="74.5" y="208" text-anchor="start">generated one token after another: every reasoning token adds decoding time before the answer starts</text>
  <path class="d-line" d="M16 226 H844" opacity="0.6"/>
  <text class="d-title" x="224" y="248" text-anchor="middle">More thinking buys accuracy (schematic)</text>
  <path class="d-line-strong" d="M74 264 V394 H374"/>
  <text class="d-label" x="0" y="0" text-anchor="middle" transform="translate(60 329) rotate(-90)">accuracy</text>
  <text class="d-label" x="224" y="412" text-anchor="middle">reasoning tokens spent per problem (log scale)</text>
  <path class="d-line-accent" d="M82.0 378.4 L91.5 370.2 L100.9 362.7 L110.4 355.8 L119.9 349.5 L129.3 343.7 L138.8 338.3 L148.3 333.5 L157.7 329.0 L167.2 324.9 L176.7 321.1 L186.1 317.7 L195.6 314.5 L205.1 311.6 L214.5 309.0 L224.0 306.5 L233.5 304.3 L242.9 302.2 L252.4 300.4 L261.9 298.6 L271.3 297.1 L280.8 295.6 L290.3 294.3 L299.7 293.1 L309.2 291.9 L318.7 290.9 L328.1 290.0 L337.6 289.1 L347.1 288.3 L356.5 287.6 L366.0 286.9"/>
  <text class="d-note" x="74" y="430" text-anchor="start">cost rises too: more latency, more billed tokens</text>
  <path class="d-line-strong" d="M310 426 H374" marker-end="url(#reasoning-models-arrow)"/>
  <text class="d-note" x="224" y="356" text-anchor="start">reported on multi-step maths</text>
  <text class="d-note" x="224" y="369" text-anchor="start">and coding benchmarks</text>
  <text class="d-title" x="625" y="248" text-anchor="middle">Trained by RL on checkable answers (DeepSeek-R1)</text>
  <path class="d-line-accent" d="M727.8 294.7 A160 80 0 0 1 774.4 327.3" marker-end="url(#reasoning-models-arrow-accent)"/>
  <path class="d-line-accent" d="M774.4 384.7 A160 80 0 0 1 732.1 415.5" marker-end="url(#reasoning-models-arrow-accent)"/>
  <path class="d-line-accent" d="M517.9 415.5 A160 80 0 0 1 475.6 384.7" marker-end="url(#reasoning-models-arrow-accent)"/>
  <path class="d-line-accent" d="M475.6 327.3 A160 80 0 0 1 522.2 294.7" marker-end="url(#reasoning-models-arrow-accent)"/>
  <rect class="d-box d-role-input" x="533" y="259" width="184" height="34" rx="5"/>
  <text class="d-label" x="625" y="273" text-anchor="middle">problem with a checkable answer</text>
  <text class="d-note" x="625" y="287" text-anchor="middle">maths result · code with tests</text>
  <rect class="d-box d-role-attention" x="717" y="339" width="136" height="34" rx="5"/>
  <text class="d-label" x="785" y="353" text-anchor="middle">model answers</text>
  <text class="d-note" x="785" y="367" text-anchor="middle">&lt;think&gt;…&lt;/think&gt; answer</text>
  <rect class="d-box d-role-router" x="532" y="419" width="186" height="34" rx="5"/>
  <text class="d-label" x="625" y="433" text-anchor="middle">rule-based check</text>
  <text class="d-note" x="625" y="447" text-anchor="middle">answer correct? format followed?</text>
  <rect class="d-box d-role-feedforward" x="409" y="339" width="112" height="34" rx="5"/>
  <text class="d-label" x="465" y="353" text-anchor="middle">reward → RL update</text>
  <text class="d-note" x="465" y="367" text-anchor="middle">of the model (GRPO)</text>
  <text class="d-note" x="625" y="352" text-anchor="middle">the reward looks only at the</text>
  <text class="d-note" x="625" y="366" text-anchor="middle">final answer and the format</text>
  </svg>
diagram_caption: "A reasoning model writes a long, often hidden reasoning span before a short answer; all of it is billed output, so more thinking costs latency and money for accuracy (schematic). RL on automatically checkable answers trains it."
underlying_model_note: "DeepSeek-R1 is a language model underneath: its makers took DeepSeek-V3-Base and trained it further with reinforcement learning, so the reasoning comes from the training, not from a different network."
notable_models:
  - { name: "OpenAI o1", year: 2024, note: "Trained with large-scale reinforcement learning; OpenAI reports performance improving with both train-time and test-time compute." }
  - { name: "DeepSeek-R1", year: 2025, note: "Cold-start fine-tuning on thousands of readable reasoning examples, then reinforcement learning with rule-based rewards, fine-tuning on about 800,000 samples and a last reinforcement-learning stage that adds reward models; six smaller distilled models were released." }
  - { name: "DeepSeek-R1-Zero", year: 2025, note: "Reinforcement learning applied directly to the DeepSeek-V3 base model, without human-labelled reasoning trajectories." }
  - { name: "o3", year: 2025, note: "OpenAI reports an additional order of magnitude in training compute and inference-time reasoning over o1." }

resources:
  - { kind: paper, title: "DeepSeek-R1: Incentivizing Reasoning Capability in LLMs via Reinforcement Learning (DeepSeek-AI, 2025)", url: "https://arxiv.org/abs/2501.12948" }
  - { kind: blog, title: "Learning to Reason with LLMs (OpenAI o1)", url: "https://openai.com/index/learning-to-reason-with-llms/" }
  - { kind: paper, title: "Chain-of-Thought Prompting Elicits Reasoning in Large Language Models (Wei et al., 2022)", url: "https://arxiv.org/abs/2201.11903" }
  - { kind: blog, title: "Why We Think — reasoning and test-time compute (Lilian Weng)", url: "https://lilianweng.github.io/posts/2025-05-01-thinking/" }

learn_first:
  - { label: "RLHF & preference-optimization machinery", href: "/models/architecture/rlhf", tag: "Concept" }
  - { label: "Mixture of Experts (DeepSeek-R1's base model)", href: "/models/architecture/mixture-of-experts", tag: "Architecture" }
  - { label: "Chain-of-thought prompting", href: "/learn/concepts/chain-of-thought", tag: "Concept" }

tags: [reasoning, chain-of-thought, rl, test-time-compute, llm]
updated: '2026-11-23'
sources:
  - { url: "https://arxiv.org/abs/2501.12948", title: "DeepSeek-R1: Incentivizing Reasoning Capability in LLMs via Reinforcement Learning (DeepSeek-AI, 2025)", trust: 0.95 }
  - { url: "https://openai.com/index/introducing-o3-and-o4-mini/", title: "Introducing OpenAI o3 and o4-mini (OpenAI, 16 April 2025)", trust: 0.85 }
  - { url: "https://arxiv.org/abs/2201.11903", title: "Chain-of-Thought Prompting Elicits Reasoning in Large Language Models (Wei et al., 2022)", trust: 0.9 }
  - { url: "https://openai.com/index/learning-to-reason-with-llms/", title: "Learning to reason with LLMs (OpenAI, 2024): accuracy against test-time compute; the raw chain of thought is shown only as a summary", trust: 0.85 }
  - { url: "https://developers.openai.com/api/docs/guides/reasoning", title: "Reasoning models guide (OpenAI API): reasoning tokens are not visible, are billed as output and count against the output limit", trust: 0.85 }
---

## What Changed

A reasoning model spends its effort **while answering**: it writes many tokens of reasoning first.
The table above is DeepSeek's own comparison of DeepSeek-R1 with GPT-4o on the same problems.

How to read the table above:

- **pass@1** is the share of single attempts that are right. DeepSeek samples k answers per
  question (k = 64 for AIME) and averages their correctness.
- **Majority vote** (written cons@64 for 64 samples) takes the answer given most often.
- **Thinking tokens** are the tokens of the reasoning span, before the answer.
- **The catch:** DeepSeek-R1 used about **12 times** as many tokens per problem as GPT-4o
  (8,793 against 711), and those tokens cost time and money.
- **Not only length:** the DeepSeek authors report that GPT-4o, given more tokens through
  16 independent samples, improved very little; they attribute this to samples not building on
  each other.

## Key Ideas

- **Reason first, then answer:** the model generates a long chain of thought before a short
  answer.
- **Reward the outcome:** in DeepSeek-R1-Zero only the final answer and the format are checked,
  with no human-written reasoning.
- **Compare within a group:** GRPO scores each answer against the others for the same question.
- **Inference-time scaling:** OpenAI and DeepSeek report accuracy rising with the computation
  spent while answering, a lever separate from model size and training data.

## How It Works

The figure above has **three parts**: the top row is one response, token by token; the lower left
sketches what more thinking buys; the lower right is the training loop of DeepSeek-R1-Zero.

A **token** is a piece of text, a word or part of a word. **Reinforcement learning** trains a model
by rewarding the outputs it produces, rather than by showing it correct outputs to copy.

1. **Prompt (top left).** The question arrives as input tokens.
2. **Reasoning span.** The model generates a long chain of thought, one token at a time.
   DeepSeek-R1 writes it between `<think>` and `</think>`.
3. **Answer.** A short final answer follows; DeepSeek-R1-Zero's training template puts it between
   `<answer>` and `</answer>`.
4. **What the user sees.** OpenAI shows o1's users a model-generated summary, not the raw chain.
   In the OpenAI API, reasoning tokens are not returned but are **billed as output tokens** and
   count toward the `max_output_tokens` limit.
5. **More thinking (lower left).** OpenAI reports o1's accuracy rising with test-time compute. The
   curve in the figure is a schematic of that claim, not OpenAI's data.
6. **The training loop (lower right),** as DeepSeek-R1-Zero runs it:
   - **A problem with a checkable answer:** a maths question with a known result, or a coding
     problem with test cases.
   - **Sample a group:** the model writes **16** answers to the same question.
   - **Rule-based check:** an accuracy reward (is the final answer right; does the code pass) and
     a format reward (is the reasoning inside the tags), added with the same weight.
   - **Group-relative advantage:** each answer's reward minus the group's mean, divided by the
     group's standard deviation (the key equation).
   - **GRPO update:** like PPO, a clipped probability ratio and a KL penalty to a reference model
     (coefficient 0.001), but with **no value model**.
7. **DeepSeek-R1's extra stages.** Cold-start fine-tuning on thousands of examples; a
   language-consistency reward, the share of words in the target language; rejection sampling
   and fine-tuning on about **800,000** samples; and a second reinforcement-learning stage with
   reward models for helpfulness and harmlessness.
8. **Distillation.** Six smaller Qwen and Llama models are fine-tuned on the 800,000 samples,
   with no reinforcement learning of their own.

## Worked Example

**One group of answers, one score, one bill.** All numbers are an example, not from a trained
model; the formulas are DeepSeek-R1's and the billing rule is OpenAI's.

**Part 1: rewards for one group** (G = 4 here; DeepSeek-R1-Zero samples 16). The question is a
maths problem whose answer is 12. Suppose each check gives 1 point when passed and 0 when not:
the paper adds the accuracy and format rewards with the same weight but does not print their
point values.

All four answers put their reasoning inside the tags, so each earns the format point.

| Answer | Final | Accuracy | Format | Reward r |
| --- | --- | --- | --- | --- |
| 1 | 12 | 1 | 1 | 2 |
| 2 | 12 | 1 | 1 | 2 |
| 3 | 15 | 0 | 1 | 1 |
| 4 | 9 | 0 | 1 | 1 |

**Part 2: group-relative advantages** (the key equation).

1. **Mean:** (2 + 2 + 1 + 1) / 4 = **1.5**.
2. **Deviations from the mean:** +0.5, +0.5, −0.5, −0.5.
3. **Standard deviation:** √((0.25 + 0.25 + 0.25 + 0.25) / 4) = √0.25 = **0.5**. The paper does not
   say whether it divides by G or by G − 1; dividing by G − 1 = 3 gives 0.577 and advantages of
   ±0.866.
4. **Advantages:** 0.5 / 0.5 = **+1** for answers 1 and 2, and −0.5 / 0.5 = **−1** for answers 3
   and 4.
5. **The update** makes the two correct answers more likely and the two wrong ones less likely. No
   person judged the reasoning inside any of them.

**Part 3: scoring the model on this question.** Use the four final answers 12, 12, 15 and 9.

1. **pass@1:** the average correctness, (1 + 1 + 0 + 0) / 4 = **0.5**.
2. **Majority vote:** 12 appears twice, 15 and 9 once each, so the vote is 12, which is right.
   With 64 samples instead of 4 this is the cons@64 score.

**Part 4: the bill for one response in the OpenAI API.** Suppose a 500-token prompt produces
3,000 reasoning tokens and a 200-token visible answer.

1. **Output tokens billed:** 3,000 + 200 = **3,200**.
2. **Share the user never sees:** 3,000 / 3,200 ≈ **94%** of the billed output is reasoning.
3. **The limit:** `max_output_tokens` must cover all 3,200. OpenAI's guide warns that if the
   limit is reached during reasoning, the response can end before any visible answer, and the
   input and reasoning tokens are still charged.

**What this means:** training needs only a way to check the final answer, and the reasoning is
learned from that check alone. The price is paid at inference: most of what the model writes is
reasoning the user may never see.
