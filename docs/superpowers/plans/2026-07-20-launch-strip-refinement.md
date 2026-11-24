# Launch Strip Refinement Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Restore the original warm mobile launch-strip appearance and remove redundant Android release status text without changing store links or platform states.

**Architecture:** Make one focused edit in `index.html`. Restore the previous strip colors, remove the two status paragraphs and their unused styles, and simplify the disabled App Store click handler so it only prevents navigation.

**Tech Stack:** Static HTML5, CSS, vanilla JavaScript, PowerShell validation.

## Global Constraints

- Modify only `index.html`.
- Keep Google Play URL `https://play.google.com/store/apps/details?id=app.didit.did_it`.
- Keep launch-strip text `♥ Google Play 출시 완료 · 지금 다운로드 →`.
- Restore strip background `rgba(255, 255, 255, .88)`.
- Keep all three Google Play buttons active.
- Keep all three App Store buttons disabled with `iOS 출시 예정`.
- Remove all `Android 버전이 Google Play에 출시되었습니다.` paragraphs.
- Remove `.store-status` styling and `[data-download-status]` update behavior.

---

### Task 1: Refine Launch Strip and Remove Status Copy

**Files:**
- Modify: `index.html:372`
- Modify: `index.html:405`
- Modify: `index.html:797`
- Modify: `index.html:1416`
- Modify: `index.html:1551`
- Modify: `index.html:1603`

**Interfaces:**
- Consumes: Existing `.status-strip`, `.store-status`, disabled App Store anchors, and store-link JavaScript handler.
- Produces: Warm mobile launch strip with no separate Android status paragraphs or empty reserved status space.

- [ ] **Step 1: Run pre-change assertions and verify they fail**

Run:

```powershell
$html = Get-Content -Raw index.html
if ($html -match 'Android 버전이 Google Play에 출시되었습니다\.') { throw 'Redundant Android status copy remains.' }
if ($html -match '\.store-status\s*\{') { throw 'Unused store-status CSS remains.' }
if ($html -match 'data-download-status') { throw 'Unused download-status markup or behavior remains.' }
if ($html -notmatch 'background:\s*rgba\(255,\s*255,\s*255,\s*\.88\)') { throw 'Original strip background is not restored.' }
```

Expected: FAIL with `Redundant Android status copy remains.`

- [ ] **Step 2: Restore the original warm strip colors**

Update the strip rules to:

```css
.status-strip {
  position: relative;
  z-index: 2;
  display: block;
  padding: 22px 28px;
  border-bottom: 1px solid var(--line);
  background: rgba(255, 255, 255, .88);
  color: var(--muted);
  text-align: center;
  text-decoration: none;
  font-size: .88rem;
  line-height: 1.45;
  font-weight: 780;
  transition: background-color .2s ease, color .2s ease;
}

.status-strip:hover {
  background: rgba(255, 247, 239, .96);
}
```

Keep the pink heart, arrow styling, focus-visible outline, and desktop hide rule unchanged.

- [ ] **Step 3: Remove redundant status UI**

Delete both:

```html
<p class="store-status" data-download-status aria-live="polite">Android 버전이 Google Play에 출시되었습니다.</p>
```

Delete both mobile and desktop `.store-status` CSS blocks.

- [ ] **Step 4: Simplify disabled App Store behavior**

Replace the current handler with:

```javascript
document.querySelectorAll('.store-link[aria-disabled="true"]').forEach((button) => {
  button.addEventListener('click', (event) => {
    event.preventDefault();
  });
});
```

The App Store caption already communicates `iOS 출시 예정`, so no secondary status text is needed.

- [ ] **Step 5: Run post-change assertions**

Run:

```powershell
$html = Get-Content -Raw index.html
if ($html -match 'Android 버전이 Google Play에 출시되었습니다\.') { throw 'Redundant Android status copy remains.' }
if ($html -match '\.store-status\s*\{') { throw 'Unused store-status CSS remains.' }
if ($html -match 'data-download-status') { throw 'Unused download-status markup or behavior remains.' }
if ($html -notmatch 'background:\s*rgba\(255,\s*255,\s*255,\s*\.88\)') { throw 'Original strip background is not restored.' }
if (($html | Select-String -AllMatches 'href="https://play\.google\.com/store/apps/details\?id=app\.didit\.did_it"').Matches.Count -ne 4) { throw 'Expected four Google Play links.' }
if (($html | Select-String -AllMatches '<a[^>]+data-store="app-store"[^>]+aria-disabled="true"').Matches.Count -ne 3) { throw 'Expected three disabled App Store controls.' }
if ($html -notmatch 'Google Play 출시 완료 · 지금 다운로드') { throw 'Launch-strip copy is missing.' }
```

Expected: no output and exit code 0.

- [ ] **Step 6: Run repository checks**

Run:

```powershell
git diff --check
git status --short
```

Expected: only `index.html` is modified and `git diff --check` exits 0.

- [ ] **Step 7: Commit**

```powershell
git add index.html
git commit -m "Refine Google Play launch strip"
```

### Task 2: Verify the Local Page

**Files:**
- Verify: `index.html`

**Interfaces:**
- Consumes: Completed Task 1 markup and styles.
- Produces: Verified local page response and preserved store-link structure.

- [ ] **Step 1: Start the local site**

Run:

```powershell
python -m http.server 4174 --bind 127.0.0.1
```

Expected: `http://127.0.0.1:4174/index.html` responds with HTTP 200.

- [ ] **Step 2: Verify the local response and link structure**

Run:

```powershell
$response = Invoke-WebRequest -Uri 'http://127.0.0.1:4174/index.html' -UseBasicParsing
if ($response.StatusCode -ne 200) { throw 'Local home page did not return HTTP 200.' }
$html = $response.Content
if ($html -match 'Android 버전이 Google Play에 출시되었습니다\.') { throw 'Rendered response contains redundant status copy.' }
if ($html -notmatch 'background:\s*rgba\(255,\s*255,\s*255,\s*\.88\)') { throw 'Rendered response does not contain restored strip color.' }
```

Expected: no output and exit code 0.

- [ ] **Step 3: Verify final repository state**

Run:

```powershell
git diff --check HEAD~1..HEAD
git status --short
git log -3 --oneline
```

Expected: clean working tree with `Refine Google Play launch strip` as the newest implementation commit.
