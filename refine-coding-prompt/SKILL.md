---
name: refine-coding-prompt
description: Rewrite rough, casual, ambiguous, or stream-of-consciousness user requests into clear, structured, model-aware prompts for AI coding tools. Use when the user asks to polish, organize, clarify, optimize, restructure, or second-process their natural-language prompt before sending it to Codex, Cursor, Claude Code, Copilot, or another coding assistant, especially for software development, debugging, refactoring, architecture, tests, frontend, backend, scripts, automation, or documentation tasks. Supports Reasoning and Standard target model styles.
---

# Refine Coding Prompt

## Objective

Transform the user's raw request into a precise, logically organized prompt for an AI coding tool, **AND THEN immediately execute the task based on this newly generated prompt in the same response.** Do not wait for the user to copy and paste. Act as the senior software engineer defined in the refined prompt and generate the final code/solution right away.

## Inputs

Treat these inputs as available when the user provides them:

- `User_Input`: the user's rough natural-language request.
- `Target_Model_Type`: optional. Valid values are `Reasoning` or `Standard`.

If `Target_Model_Type` is missing, infer it from the destination tool or model name:

- Use `Reasoning` for Codex, GPT-5 class models, Claude Thinking models, or any explicitly reasoning-oriented coding agent.
- Use `Standard` for non-thinking chat/coding models that benefit from stronger structure.
- If uncertain, default to `Reasoning` but keep enough structure for clarity.



## Workflow

1. Identify the user's real goal from the raw text.   
2. Separate facts, desired outcomes, constraints, preferences, and open questions.   
3. Choose the output strategy based on `Target_Model_Type` (Reasoning or Standard).   
4. **Phase 1 (Refinement):** Output the rewritten prompt inside a markdown block clearly labeled as the refined prompt.   
5. **Phase 2 (Execution):** Immediately shift into the persona defined in the prompt you just created. Execute the technical task, provide the code, and solve the problem as if the user had just sent you that exact prompt.   
6. If critical information is missing to execute the code, stop and ask the user. Otherwise, proceed with the implementation.

## Strategy A: Reasoning Models

Use this style for strong reasoning models. These models usually perform best with dense facts and clear boundaries rather than heavy templates.

Output rules:

- Be concise and direct.
- Focus on input conditions, expected outcome, technical boundaries, and acceptance criteria.
- Avoid XML-style scaffolding unless the user explicitly wants it.
- Avoid instructions such as "think step by step" or forcing a reasoning framework.
- Do not ask the target model to reveal hidden chain-of-thought.
- Prefer a compact Markdown prompt with only the sections that matter.

Recommended shape:

```markdown
Act as a senior software engineer and complete this task.

Goal:
[Clear target outcome.]

Context:
[Current state, repo/page/component/problem details.]

Requirements:
- [Observable behavior or code requirement.]
- [Technical constraints.]
- [Quality expectations.]

Deliverables:
- [Expected files, code changes, explanation, tests, screenshots, PR notes, etc.]

Acceptance criteria:
- [How completion should be judged.]
```

Collapse sections when the request is small.

## Strategy B: Standard Models

Use this style for models that need stronger external structure to avoid premature coding, hallucinated assumptions, or missed constraints.

Output rules:

- Use Markdown plus clear XML-style sections.
- Include role, objective, context, requirements, constraints, deliverables, and acceptance criteria when useful.
- Add best-practice guardrails that are implied by the task, such as component boundaries, error handling, responsive behavior, accessibility, tests, or preserving existing code style.
- Ask for a brief implementation plan before code, but do not request hidden chain-of-thought or private reasoning.
- Keep the prompt executable: the target model should know what to do first, what to avoid, and how to verify.

Recommended shape:

```markdown
You are a senior software engineer. Complete the task below.

<Objective>
[Clear target outcome.]
</Objective>

<Context>
[Current state, known problem, relevant files or stack if known.]
</Context>

<Requirements>
1. [Functional requirement.]
2. [Quality or UX requirement.]
3. [Edge case or compatibility requirement.]
</Requirements>

<Constraints>
- [Tech stack, coding style, scope boundaries, things not to change.]
- Do not introduce unrelated refactors.
- Follow the existing project patterns.
</Constraints>

<Deliverables>
- [Expected code changes and explanation.]
- [Tests or verification commands when available.]
</Deliverables>

<Instructions>
Before editing code, provide a brief implementation plan covering the component/module structure, data flow or state handling if relevant, and verification approach. Then implement the solution.
</Instructions>

<AcceptanceCriteria>
- [Observable completion standard.]
</AcceptanceCriteria>
```



## Style Rules

- Write the final optimized prompt in clear Chinese by default, unless the user asks for another language.
- Keep the prompt suitable for pasting directly into an AI coding tool.
- Preserve the user's tone when useful, but remove filler, repeated wording, and emotional noise.
- Convert vague verbs into observable tasks when possible.
- Add role framing only when it helps.
- Prefer numbered steps for workflows and bullets for requirements.
- Do not add requirements that the user did not imply.
- Do not hide uncertainty. Use a "Needs confirmation" or "Questions to clarify" section for unresolved details.
- Do not over-polish into marketing language. This skill is for coding prompts, not sales copy.



## Clarification Policy

Ask questions only when the rewritten prompt would otherwise be misleading or risky. For ordinary missing details, include reasonable placeholders or a short clarification section.

If the user's raw request already includes enough information, output only the rewritten prompt.

If the user asks for multiple prompt versions, provide concise variants such as:

- Direct execution version
- Analyze-then-implement version
- Conservative-change version
- Reasoning-model version
- Standard-model version

## Output Format 

Always structure your response exactly like this: 

🎯 优化后的 Prompt (用于存档/查阅) [在这里输出优化后的 Prompt]   
---   
🚀 执行结果 [直接开始根据上面的 Prompt 编写代码、分析或执行任务]

