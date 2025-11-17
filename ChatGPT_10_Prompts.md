# 10 Prompts to Use and Improve Your Work at Ferrellgas

*Admin Guide for IT Engineers & Architects*

---

## How to Use This Guide

Think of these 10 prompts as your **core tools** (like `ping`, `traceroute`, `top`, `git status`… but for your brain + ChatGPT).

For each prompt you’ll get:

* A **prompt template** you can save and reuse (fill in the [brackets]).
* A **strategy**: how to get the best results.
* **3 concrete use cases** for an IT engineer/architect.

Try this workflow:

1. **Copy the prompt** into ChatGPT.
2. **Fill in the brackets** with your real project/problem.
3. **Iterate**: ask “show me option B”, “go deeper on step 3”, “now write the email/doc/script for this”.

You don’t have to memorize them—just save this as a reference and use like a playbook.

---

## Prompt 1 – System Architect Co-Pilot

### Prompt Template

```text
You are my System Architect Co-Pilot.

Context:
- I am an IT engineer/architect working on: [brief description of the project].
- Main tech stack / platforms: [cloud, OS, tools, languages].
- Constraints: [security, budget, legacy systems, SLAs, compliance, uptime, etc.].

Task:
1. Help me design a clear, high-level architecture for this system.
2. Show it as:
   - a simple text diagram,
   - a list of main components,
   - how data flows between them.
3. Call out:
   - security risks,
   - scalability concerns,
   - single points of failure,
   - monitoring/observability needs.
4. Suggest 2–3 design variants with pros/cons.

Explain everything so a smart 10th grader who knows basic IT could follow it.
```

### Strategy

* Always **fill in constraints** (security, budget, legacy) – that’s what makes the answer useful.
* Ask follow-ups:

  * “Turn design #2 into a migration plan.”
  * “Now give me Terraform module boundaries for this.”
* Use this before you start building; don’t wait until you’re halfway in.

### 3 Example Use Cases

1. **New internal web app**:
   Designing an internal ticketing tool hosted on Azure with SSO, logging, and role-based access.

2. **Hybrid network refresh**:
   You need a design for connecting on-prem data center to AWS with VPN + Zero Trust access.

3. **Monitoring redesign**:
   You want a unified observability approach for logs, metrics, and traces across microservices.

---

## Prompt 2 – Problem → Step-By-Step Battle Plan

### Prompt Template

```text
You are my IT Project Planner.

I need a step-by-step plan for:

[Describe the goal or problem in 3–5 sentences.]

Constraints:
- Timeframe: [e.g., 3 months, 2 weeks, weekend project].
- Team: [just me, small team, cross-functional].
- Risk level: [low risk, medium, high].
- Change window: [maintenance window details if relevant].

Task:
1. Break this into clear phases and steps.
2. For each step, include:
   - what to do,
   - why it matters,
   - what to verify (checklist).
3. Highlight:
   - dependencies,
   - risks,
   - rollback points.

Format the answer as a numbered plan that I can paste into a project tracker.
```

### Strategy

* Use this when your brain says “I don’t know where to start.”
* Turn the output straight into **Jira tickets** or a **Trello board**.
* Ask: “Now tag each step as S (Small), M (Medium), or L (Large) effort.”

### 3 Example Use Cases

1. **Migrating a legacy app to the cloud**:
   Get a phase-by-phase plan: assessment → PoC → pilot → full cutover → decommission.

2. **Rolling out a new endpoint security tool**:
   Plan for pilot group, policies, testing, global deployment, and user communication.

3. **Building a home lab for certifications**:
   Step-by-step for building a lab for CCNA/Azure/AWS study, including hardware, VMs, and scenarios.

---

## Prompt 3 – Code Review & Refactor Partner

### Prompt Template

```text
You are a senior engineer reviewing my code.

Language/stack: [Rust, Python, TypeScript, etc.]
Context: [what this code is supposed to do, where it runs, performance/security constraints].

Task:
1. Review the following code for:
   - correctness,
   - security issues,
   - performance problems,
   - readability and maintainability.
2. Suggest concrete improvements with short explanations.
3. Then show a "before vs after" version of the key parts.

Here is the code:
[PASTE CODE]
```

### Strategy

* Always tell it **where the code runs** (backend API, CLI tool, agent, etc.).
* Use on **critical paths**: auth, RBAC, data access, crypto, network code.
* Ask: “Now explain your changes like I’m a 10th grader learning this language.”

### 3 Example Use Cases

1. **API endpoint in Rust/TS**:
   Review a new `/login` or `/webhook` handler for security problems and better structure.

2. **Automation script**:
   Improve a Bash/PowerShell script that deploys services or rotates logs.

3. **Small internal library**:
   Clean up a reused helper library (logging, metrics, config) before sharing with your team.

---

## Prompt 4 – Debugging & Root Cause Analyst

### Prompt Template

```text
You are my Debugging & Root Cause Analyst.

Context:
- System: [app/service name, tech stack, environment].
- What I expected to happen: [short description].
- What actually happens: [error messages, screenshots described in text, logs].
- Recent changes: [code deploys, config changes, infra changes].

Task:
1. Help me form 3–5 possible root cause hypotheses.
2. For each hypothesis, give:
   - why it might be true,
   - what I should check (specific logs/commands),
   - a quick test or experiment to confirm or deny it.
3. Suggest a minimal, safe workaround while we investigate.

Use clear steps and keep the language simple.
```

### Strategy

* Include **logs and error messages** – copy and paste them.
* Treat it like pair-debugging: run the checks it suggests, then paste back results.
* Ask: “Now compress this into a short Root Cause Analysis summary.”

### 3 Example Use Cases

1. **Service keeps crashing after deploy**:
   You paste stack traces and logs from a new microservice that keeps restarting in Docker/Kubernetes.

2. **Performance regression**:
   Users report your API got slower after enabling a new feature; you share metrics and code snippets.

3. **Network weirdness**:
   A service in one subnet can’t reach a database; you paste ping/traceroute logs and firewall rules (sanitized).

---

## Prompt 5 – Documentation & Runbook Generator

### Prompt Template

```text
You are my Technical Writer.

Context:
- System/process to document: [name and short description].
- Audience: [help desk, junior engineers, senior engineers, mixed].
- Goal: [quick troubleshooting, onboarding, compliance, audit, etc.].

Task:
Create a clear document with:
1. Overview (what this is and why it matters).
2. Prerequisites (access, tools, permissions).
3. Step-by-step instructions:
   - for normal operations,
   - for common issues and quick fixes.
4. "When to escalate" section.
5. Glossary of key terms.

Write for a smart 10th grader who knows basic IT concepts.
Format as markdown.
```

### Strategy

* Use after you finish a project or solve a pain-in-the-neck issue.
* Paste in messy notes / chat logs and say:
  “Use this raw info to build the document”.
* Reuse docs as **runbooks**, **KB articles**, or internal wikis.

### 3 Example Use Cases

1. **Password reset + MFA troubleshooting runbook**:
   For help desk and on-call engineers.

2. **VM build process**:
   Document how to build a standard Windows or Linux server with company tools.

3. **Incident response checklist**:
   A step-by-step document for responding to high CPU, disk full, or suspicious login alerts.

---

## Prompt 6 – Learning Coach & Explainer

### Prompt Template

```text
You are my Learning Coach.

Topic I want to learn: [e.g., Kubernetes, OAuth2, Zero Trust, Rust, etc.].
My current level: [beginner / intermediate / advanced].
Time I can spend per week: [e.g., 3 hours].
My goal: [e.g., pass a cert, design a system, read code, run a PoC].

Task:
1. Explain this topic to me in simple language first.
2. Then create a 4-week learning plan with:
   - weekly focus,
   - key concepts,
   - small hands-on exercises.
3. Give me 5 "interview-style" questions per week to test myself.

Use plain language suitable for a 10th grader who likes tech.
```

### Strategy

* Use this whenever you touch **new tech** in a project.
* Ask follow-ups like:

  * “Compare this to X that I already know.”
  * “Draw a simple mental model / analogy.”
* Copy the plan into a note-taking app and check items off.

### 3 Example Use Cases

1. **Learning Kubernetes basics**:
   Understanding pods, services, deployments, and writing your first manifest.

2. **Grasping OAuth2 and OpenID Connect**:
   For designing secure login flows across web and mobile apps.

3. **Picking up Rust/Go**:
   To build faster, safer backend services or CLIs.

---

## Prompt 7 – Meeting, Email & Communication Assistant

### Prompt Template

```text
You are my Communication Assistant.

Context:
- Audience: [manager, execs, security team, developers, vendors, non-technical users].
- Goal: [status update, incident report, change request, design proposal, escalation].
- Tone: [professional, direct, calm, friendly, urgent but not panicked].

Task:
1. Create:
   - (A) a short summary (3–5 bullet points),
   - (B) a more detailed explanation (1–3 paragraphs),
   - (C) 3–5 talking points I can use in a meeting.
2. Use simple, non-buzzwordy language.
3. Make sure risk, impact, and next steps are crystal clear.

Here are my rough notes:
[PASTE BULLETS / NOTES / LOGS]
```

### Strategy

* Use before sending emails to **managers, leadership, or customers**.
* Paste rough notes; let ChatGPT do the polishing, not the thinking.
* Ask: “Now shorten this to fit in a Teams/Slack message.”

### 3 Example Use Cases

1. **Incident summary email**:
   Summarize a 2-hour outage for leadership, with impact and next steps.

2. **Change request proposal**:
   Explain why you need to change firewall rules or upgrade a database.

3. **Design walkthrough prep**:
   Talking points for a design review meeting for a new architecture.

---

## Prompt 8 – Threat Modeling & Risk Review

### Prompt Template

```text
You are my Security & Threat Modeling Assistant.

Context:
- System: [web app, API, VPN, Zero Trust setup, internal tool, etc.].
- Tech stack: [languages, frameworks, cloud provider, SSO, etc.].
- Data sensitivity: [public, internal, confidential, regulated (PCI/PHI/PII)].

Task:
1. Walk through a simple threat model:
   - What are the main assets?
   - Who are the possible attackers?
   - What are the main entry points?
2. List:
   - top 10 realistic threats,
   - their impact (high/med/low),
   - their likelihood (high/med/low).
3. For the top 5 threats:
   - suggest practical mitigations,
   - mention logging/monitoring I should add.

Explain everything clearly so a 10th grader interested in cybersecurity can follow it.
```

### Strategy

* Use this **early** in design, before implementation locks in risky patterns.
* Re-run when you add new external integrations or data flows.
* Ask: “Turn this into a short risk summary I can paste into a design doc.”

### 3 Example Use Cases

1. **New public-facing API**:
   You expose an endpoint to partners and want to understand auth, rate limiting, and injection risks.

2. **Zero Trust rollout**:
   Evaluate risks around access to internal admin portals via ZTNA.

3. **Data pipeline with PII**:
   Modeling how personal data flows between services, and where to encrypt, mask, and log.

---

## Prompt 9 – Automation & Scripting Planner

### Prompt Template

```text
You are my Automation Architect.

Context:
- I want to automate: [describe the manual process].
- Environment: [Windows, Linux, cloud provider, tools like Ansible, GitHub Actions, Power Automate, etc.].
- Constraints: [security, approvals, downtime, no local admin, etc.].

Task:
1. Analyze the current manual process and identify:
   - input data,
   - tools/commands used,
   - decision points.
2. Propose 2–3 automation approaches:
   - simple script,
   - scheduled job,
   - full CI/CD or workflow automation.
3. For the best option, give:
   - high-level design,
   - rough pseudo-code or script structure,
   - how to log, monitor, and safely roll back.

Explain it simply and avoid over-engineering.
```

### Strategy

* Use whenever you catch yourself doing **repetitive tasks** more than twice.
* After the plan, ask:

  * “Now write a first draft script in [language].”
  * “Now add comments as if teaching a beginner.”
* Store these scripts and notes in a personal “Automation Library.”

### 3 Example Use Cases

1. **Rotating logs or cleaning temp files**:
   Automatically archive logs and keep disk usage under control.

2. **User onboarding/offboarding**:
   Automate account creation, group membership, and access removal.

3. **Deploying a containerized app**:
   Design a small CI pipeline that builds, tests, and deploys Docker images.

---

## Prompt 10 – Career & Skill Roadmap Designer

### Prompt Template

```text
You are my Career & Skill Roadmap Advisor.

Context:
- Current role: [title, rough description of what you do now].
- Target role(s): [e.g., Senior Cloud Architect, Security Architect, SRE Lead].
- Strengths: [what I’m good at, e.g., scripting, networking, troubleshooting].
- Weaknesses: [what I struggle with or avoid].
- Time horizon: [1 year, 3 years, 5 years].

Task:
1. Identify 5–7 core skill areas I should focus on.
2. For each skill area:
   - explain why it matters,
   - list sub-skills or tools,
   - suggest concrete ways to practice at work or in a lab.
3. Create a 12-month roadmap split into quarters, with:
   - focus themes per quarter,
   - small projects or goals,
   - suggested certifications (if truly useful, not just for paper).

Explain this plainly, like you’re advising a smart 10th grader planning their future.
```

### Strategy

* Be honest about strengths/weaknesses; that’s where the value comes from.
* Re-run this every 6–12 months and compare: “What changed? What did I actually do?”
* Use the roadmap when choosing projects, training, or what to say “no” to.

### 3 Example Use Cases

1. **From sysadmin to cloud architect**:
   Building skills in IaC, cloud networking, identity, and cost management.

2. **From support engineer to SRE**:
   Focusing on observability, reliability patterns, and incident management.

3. **From IT generalist to security architect**:
   Deepening security design, policies, threat modeling, and tooling.

---

## Final Thought

These 10 prompts are like **reusable “spells”** for your IT career:

* Use them **early** (design, planning, learning) instead of just **after things break**.
* Combine them:

  * design (Prompt 1) → plan (Prompt 2) → secure (Prompt 8) → document (Prompt 5).

Over time, you’ll notice you’re delegating more routine thinking to ChatGPT so your brain can focus on judgment, strategy, and creativity—the parts that actually move your career from x1 to x10+.
