---
id: architecture-era/rlhf
type: architecture-era
slug: rlhf
schema_version: 1.0
title: "RLHF & Instruction Alignment"
name: "Reinforcement Learning from Human Feedback"
era: post-transformer
era_range: "2022–2024"
year: 2022
family: llm
core_innovation: "Train a reward model on human preference comparisons, then fine-tune the language model with reinforcement learning plus a KL penalty to align it to human intent."
description: "The alignment recipe (InstructGPT) that turned raw next-token predictors into helpful, instruction-following assistants."
key_features: ["Supervised instruction fine-tuning", "Preference-trained reward model", "PPO policy optimization", "KL penalty to the reference model", "Helpfulness/harmlessness alignment"]
key_models: ["InstructGPT (2022)", "ChatGPT (2022)", "Llama-2-Chat (2023)"]
paper_title: "Training Language Models to Follow Instructions with Human Feedback (InstructGPT)"
paper_url: "https://arxiv.org/abs/2203.02155"
influenced_by: [architecture-era/gpt]
led_to: [architecture-era/reasoning-models]

eli5: "A raw language model is great at guessing the next word but doesn't actually try to help you. RLHF fixes that by coaching: people compare the model's answers and say which is better, a scorer learns those preferences, and the model is trained to earn a higher score — so it learns to be helpful instead of just plausible."

analogy:
  title: "Coaching a talented but unfocused writer"
  body: "Picture a hugely well-read intern who can imitate any text but has no idea what you actually want — ask a question and they might reply with more questions, because that's a plausible way text continues. You can't hand-write the perfect answer for every situation, but you can look at two of their drafts and say 'this one's better.' A coach (the reward model) watches enough of these comparisons to internalize your taste, then gives the intern a score on every draft. The intern practices to raise their score — while a leash keeps them writing like themselves so they don't start gaming the coach with weird gibberish. The comparisons are human feedback; the coach is the reward model; the leash is the KL penalty."

narrative:
  motivation: "A base GPT model is trained to predict likely next tokens — which is not the same as being helpful. Ask it a question and it might continue with more questions, or drift off-topic, because those are plausible text continuations. The gap between 'predict text' and 'do what the user wants' is the alignment problem, and next-token training alone doesn't close it."
  approach: "Optimize for human preference directly. But humans can't write a perfect answer for every prompt, and can't put an absolute number on quality. What they can do reliably is compare: shown two responses, say which is better. So the recipe learns from comparisons, distills them into an automatic scorer, and then trains the model to score well."
  solution: "InstructGPT runs three stages. First, supervised fine-tuning on human-written demonstrations teaches the base format of a good answer. Second, a reward model is trained on human comparisons to predict which response people prefer. Third, reinforcement learning (PPO) updates the language model to maximize that reward — with a KL-divergence penalty anchoring it to the original model so it improves preference without drifting into degenerate or off-distribution text."
  outcome: "The jump in perceived helpfulness was striking — even smaller aligned models were preferred over much larger unaligned ones. This is what made ChatGPT and every modern assistant feel usable rather than merely fluent. RLHF became the standard finishing step for chat models, and its preference-optimization machinery is the exact foundation the later RL-trained reasoning models build on (swapping human preference for verifiable correctness)."

build_steps:
  - { title: "1. Start from a pretrained base model", detail: "Begin with a raw next-token predictor — knowledgeable and fluent, but not steered toward being helpful or following instructions." }
  - { title: "2. Supervised fine-tuning (SFT)", detail: "Fine-tune on human-written demonstrations of good responses to prompts. This teaches the model the shape of a helpful, instruction-following answer." }
  - { title: "3. Collect human preference comparisons", detail: "Sample several responses per prompt and have people rank them best-to-worst. Comparisons are cheaper and more consistent than asking humans to score answers absolutely." }
  - { title: "4. Train the reward model", detail: "Train a model to predict which response humans prefer, turning the rankings into an automatic scalar reward that stands in for human judgment." }
  - { title: "5. Optimize the policy with PPO", detail: "Use reinforcement learning (PPO) to update the language model so its responses earn higher reward, generating fresh samples and improving against the reward model." }
  - { title: "6. Leash it with a KL penalty", detail: "Penalize drift from the SFT reference model via a KL-divergence term. This keeps generations fluent and on-distribution and blocks reward hacking — exploiting quirks in the reward model." }

key_equation:
  latex: "\\max_{\\pi_\\theta}\\; \\mathbb{E}_{x \\sim D,\\, y \\sim \\pi_\\theta}\\big[\\, r_\\phi(x, y) \\,\\big] \\;-\\; \\beta\\, \\mathrm{KL}\\big(\\pi_\\theta(y\\mid x)\\,\\|\\,\\pi_{\\mathrm{ref}}(y\\mid x)\\big)"
  plain: "Adjust the model so its answers earn a high reward from the learned reward model, but subtract a penalty for straying too far from the original reference model. The first term chases human preference; the second term is the leash that keeps the model fluent and stops it from gaming the reward with degenerate text."
  terms:
    - { symbol: "π_θ", meaning: "the policy — the language model being fine-tuned, with parameters θ" }
    - { symbol: "π_ref", meaning: "the frozen reference model (the SFT model) the policy must stay close to" }
    - { symbol: "r_φ(x, y)", meaning: "the reward model's score for response y to prompt x, trained on human preferences" }
    - { symbol: "KL(·‖·)", meaning: "the Kullback–Leibler divergence measuring how far the policy has drifted from the reference" }
    - { symbol: "β", meaning: "the penalty strength — how tight the leash is; higher β keeps the model closer to the reference" }

strengths:
  - "Learns from cheap, reliable human comparisons instead of impossible-to-write perfect answers."
  - "Dramatically improves perceived helpfulness and instruction-following, even at smaller model sizes."
  - "The KL leash keeps outputs fluent and on-distribution, curbing reward hacking and mode collapse."
  - "General finishing step: the same recipe steers helpfulness, harmlessness, tone, and format."

weaknesses:
  - "The reward model is an imperfect proxy — optimize it too hard and the policy games it (reward hacking)."
  - "Human preference data is expensive, slow to collect, and can bake in annotator bias."
  - "The full PPO loop is complex and finicky, motivating simpler alternatives like DPO."
  - "Mostly elicits and directs existing abilities rather than adding new capability; it can also over-align into sycophancy or excessive refusals."

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
  <text class="d-note" x="149" y="370" text-anchor="middle">the reward model (②) and the policy (③)</text>
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
  <text class="d-note" x="435" y="337" text-anchor="middle">6B, starts from π<tspan class="d-note" dy="-4">SFT</tspan><tspan dy="4">​</tspan> · scalar score</text>
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
  - { name: "InstructGPT", year: 2022, note: "Introduced the three-stage RLHF recipe and showed a 1.3B aligned model beating 175B GPT-3 on human preference." }
  - { name: "ChatGPT", year: 2022, note: "Applied RLHF at scale in a chat interface — the product that made aligned assistants mainstream." }
  - { name: "Llama-2-Chat", year: 2023, note: "Detailed an open RLHF pipeline with separate helpfulness and safety reward models." }
  - { name: "DPO-tuned models", year: 2023, note: "Direct Preference Optimization — proved you can hit RLHF-like alignment by optimizing preferences directly, skipping the explicit reward model and PPO loop." }

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
updated: 2026-07-20
sources:
  - { url: "https://arxiv.org/abs/2203.02155", title: "Training Language Models to Follow Instructions with Human Feedback (Ouyang et al., 2022)", trust: 0.95 }
  - { url: "https://arxiv.org/abs/2305.18290", title: "Direct Preference Optimization (Rafailov et al., 2023)", trust: 0.9 }
---

## What Changed
A base GPT model predicts likely next tokens — which is not the same as being *helpful*. Ask it a
question and it might continue with more questions, because that is a plausible continuation of the
text. Reinforcement learning from human feedback (RLHF) closes the gap between "predict text" and "do
what the user wants." The InstructGPT recipe has three stages: (1) **supervised fine-tuning** on
human-written demonstrations of good responses; (2) train a **reward model** on human *comparisons* —
people rank several model outputs, and the reward model learns to score responses the way humans would;
(3) **reinforcement learning** (PPO) that updates the language model to maximise that reward, with a
**KL-divergence penalty** anchoring it to the original model so it improves preference without drifting
into degenerate or off-distribution text. The result — a striking jump in perceived helpfulness even at
smaller sizes — is what made ChatGPT and every modern assistant feel usable. RLHF descends from the GPT
line and its preference-optimization machinery is the foundation the later RL-trained reasoning models
build on.

## Key Ideas
- **Preference learning** — humans compare outputs rather than writing perfect answers; ranking is
  cheaper and more reliable than absolute scoring, and trains the reward model.
- **Reward model as proxy** — a learned scorer stands in for expensive human judgement so RL can
  optimise against it at scale.
- **PPO with a KL leash** — policy-gradient RL maximises reward while a KL penalty to the frozen
  reference model prevents reward hacking and keeps generations fluent.
- **Alignment ≠ capability** — RLHF mostly elicits and directs abilities already present from
  pretraining; it makes models follow intent, not fundamentally smarter.
