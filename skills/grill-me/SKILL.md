---
name: grill-me
description: Interview the user relentlessly about a plan or design until reaching shared understanding, resolving each branch of the decision tree. Use when user wants to stress-test a plan, get grilled on their design, or mentions "grill me".
---

Interview me relentlessly about every aspect of this plan until
we reach a shared understanding. Walk down each branch of the design
tree resolving dependencies between decisions one by one.

## Process

1. First, analyze the plan and identify all open questions. Estimate the total count.
2. Ask **one question at a time**. Wait for my answer before moving on.
3. Start each question with a short progress line: `Question 3 of ~8 — [topic]`
4. For each question, provide your recommended answer.
5. If a question can be answered by exploring the codebase, explore the codebase instead and skip to the next question.
6. As answers reveal new branches, adjust the total estimate.
7. When all branches are resolved, summarize the final decisions.
