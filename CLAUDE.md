# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Purpose

This is a **learning repository** for LLM post-training (大模型后训练), not a software project. It contains study plans, notes, and experimental code for becoming an LLM algorithm engineer, covering:

- **Phase 1 (Weeks 1-6):** Transformer source code reading + SFT fine-tuning (nanoGPT, Qwen2/LLaMA source reading, LoRA, LLaMA-Factory)
- **Phase 2 (Weeks 7-12):** RLHF/DPO/GRPO reinforcement learning alignment
- **Phase 3 (Weeks 13-16):** Inference optimization + deployment (vLLM, quantization, serving)
- **Phase 4 (Weeks 17-20):** Evaluation systems + end-to-end projects

## Key Context

- Target role: LLM Algorithm Engineer (post-training / inference / data)
- Starting point: Familiar with Python + PyTorch, understands Attention math, RL knowledge is early-stage
- Key tools/frameworks referenced: LLaMA-Factory, trl, transformers, vLLM, wandb
- Key models referenced: Qwen2.5 (1.5B/7B), LLaMA

## How to Help

When assisting in this repo:
- Explain concepts with code-level intuition, connecting math formulas to PyTorch implementations
- Provide runnable code examples for training concepts (SFT, LoRA, RLHF, DPO, GRPO)
- Help debug training issues (OOM, loss anomalies, gradient problems)
- Assist with experiment design and hyperparameter comparisons
- Use Chinese when the user writes in Chinese, as the study materials are in Chinese

## Sync Rule: MD → HTML

Whenever you modify `LLM算法工程师完整学习计划_20周 (1).md`, you **must** also regenerate the corresponding HTML file `LLM算法工程师完整学习计划_20周.html` by running:

```bash
npx --yes marked < "LLM算法工程师完整学习计划_20周 (1).md" > body_temp.html && cat header_template.html body_temp.html footer_template.html > "LLM算法工程师完整学习计划_20周.html" && rm body_temp.html
```

If the template files don't exist, regenerate the full HTML: wrap the `marked` output with the `<!DOCTYPE html>...<body>` header (including the CSS styles already in the HTML file) and `</body></html>` footer.

## Learning Plan Editing Guidelines

When modifying the learning plan (`LLM算法工程师完整学习计划_20周 (1).md`), follow this structure for **every phase**:

### Phase Header Section (insert between phase title and first week)
Each phase must include four resource summary tables:
1. **📚 必读论文清单** — Table columns: `#`, `论文`, `链接`, `安排周次`, `为什么要读`. All papers must have arxiv links.
2. **🎬 推荐课程/讲座** — Table columns: `资源`, `链接`, `时长`, `何时看`. Include YouTube/website URLs.
3. **🔧 核心代码资源** — Table columns: `资源`, `链接`, `用途`. Include GitHub repo URLs.
4. **🗂️ 核心数据集** — Table columns: `数据集`, `链接`, `用途`. Include HuggingFace/GitHub URLs.

### Weekly Content Rules
- **No vague references**: Every paper, course, tool, or dataset mentioned in daily tasks must have an inline URL (arxiv, GitHub, YouTube, etc.). Never write "重读之前精读文章" or "Karpathy 视频" without a link.
- **Each week must have**: `### 本周目标` (clear goal statement), `### 每日任务` (with time estimates per day), `### ✅ 验收` (checkboxes).
- **Daily tasks format**: `**周X（Xh）：标题**` followed by bullet points with specific actions and links.
- **Papers inline**: When a daily task involves reading a paper, include the arxiv link inline, specify which sections to read, and explain what to focus on.
