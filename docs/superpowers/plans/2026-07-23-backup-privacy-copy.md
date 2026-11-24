# Backup Privacy Copy Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Update the public website so its privacy and support copy accurately describes Android system backup, manual JSON backup, restore behavior, purchase restoration, and deletion.

**Architecture:** Keep `privacy.html` as the detailed source of truth. Use shorter summaries in `index.html` and task-oriented FAQ answers in `support.html` so visitors receive the right amount of detail at each point.

**Tech Stack:** Static HTML, CSS already embedded in each page, PowerShell content assertions.

## Global Constraints

- Preserve the current page layout and visual design.
- Use 2026-07-26 as the privacy policy update and effective date.
- Clearly distinguish Android system backup from the user-exported JSON backup file.
- Do not claim that system backup always occurs or that it is handled by the app server.
- Do not mention the removed in-app data reset feature.

---

### Task 1: Detailed privacy policy

**Files:**
- Modify: `privacy.html`

**Interfaces:**
- Consumes: Current local-first privacy summary and policy cards.
- Produces: Detailed public policy copy linked from the app and support page.

- [ ] **Step 1: Run failing content assertions**

Assert that the new date and backup section headings are present and that `데이터 초기화` is absent.

- [ ] **Step 2: Update the summary and policy cards**

Replace stale deletion copy and add Android system backup, manual JSON backup, restore/purchase-state, and backup-aware deletion sections.

- [ ] **Step 3: Re-run content assertions**

Confirm all required headings and statements are present and the stale feature name is absent.

### Task 2: Support and landing summaries

**Files:**
- Modify: `support.html`
- Modify: `index.html`

**Interfaces:**
- Consumes: The detailed policy from Task 1.
- Produces: Short task-oriented answers and a concise landing-page storage summary.

- [ ] **Step 1: Run failing content assertions**

Assert the concrete purchase restore path, backup FAQ, deletion instructions, and updated landing summary.

- [ ] **Step 2: Update FAQ copy**

Replace the placeholder purchase restore answer, replace the removed data-reset guidance, add a backup FAQ, and update the landing storage answer.

- [ ] **Step 3: Validate links and required copy**

Confirm all local links resolve and the required copy appears exactly once in its intended section.
