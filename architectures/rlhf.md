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

diagram: |
  flowchart TD
    BASE["Pretrained base model"] --> SFT["Supervised fine-tuning on demos"]
    SFT --> GEN["Sample several responses per prompt"]
    GEN --> HUM["Humans rank responses"]
    HUM --> RM["Train reward model"]
    SFT --> POL["Policy (model being trained)"]
    RM -->|"reward score"| PPO["PPO update"]
    POL --> PPO
    SFT -->|"KL penalty (leash)"| PPO
    PPO --> POL
    POL --> ALIGNED["Aligned assistant"]
diagram_caption: "Three stages: fine-tune on demonstrations, train a reward model from human rankings, then use PPO to raise the reward while a KL penalty to the reference model keeps outputs fluent and honest."

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
