# Google Play Launch Website Update Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Convert didyoucheck.kr from Android pre-launch messaging to a live Google Play download experience while preserving the existing mobile heart accent and iOS coming-soon state.

**Architecture:** Keep the current single-file static page structure. Update repeated Google Play anchors in `index.html`, convert the mobile status strip into an external store link, retain the existing disabled App Store handler, and update release metadata in `support.html` and `privacy.html`.

**Tech Stack:** Static HTML5, CSS, vanilla JavaScript, PowerShell validation, local HTTP server, browser responsive inspection.

## Global Constraints

- Google Play URL: `https://play.google.com/store/apps/details?id=app.didit.did_it`
- Android release date: `2026년 7월 20일`
- Google Play button caption: `지금 다운로드`
- Mobile launch strip text: `♥ Google Play 출시 완료 · 지금 다운로드`
- Android status copy: `Android 버전이 Google Play에 출시되었습니다.`
- App Store caption remains `iOS 출시 예정`.
- Disabled App Store message remains `iOS 버전은 준비 중 입니다.`
- Do not add new runtime dependencies.
- Preserve the current responsive layout and existing visual identity.

---

### Task 1: Activate Google Play Download Paths

**Files:**
- Modify: `index.html:240`
- Modify: `index.html:372`
- Modify: `index.html:1378`
- Modify: `index.html:1408`
- Modify: `index.html:1509`
- Modify: `index.html:1547`
- Modify: `index.html:1581`

**Interfaces:**
- Consumes: Existing `.store-link`, `.store-status`, `.status-strip`, and `[data-download-status]` markup and styles.
- Produces: Three enabled Google Play links and one mobile-only launch strip link to the public Play Store URL.

- [ ] **Step 1: Run pre-change assertions and verify they fail**

Run:

```powershell
$html = Get-Content -Raw index.html
if (($html | Select-String -AllMatches 'href="https://play\.google\.com/store/apps/details\?id=app\.didit\.did_it"').Matches.Count -ne 4) { throw 'Expected four live Google Play links.' }
if ($html -notmatch 'Google Play 출시 완료 · 지금 다운로드') { throw 'Expected launch strip copy.' }
if ($html -match 'Android 준비 중|Google Play 출시 준비 중') { throw 'Android pre-launch copy remains.' }
```

Expected: FAIL with `Expected four live Google Play links.`

- [ ] **Step 2: Enable all Google Play buttons**

Replace each of the three disabled Google Play anchors with this structure:

```html
<a class="store-link" data-store="google-play" href="https://play.google.com/store/apps/details?id=app.didit.did_it" target="_blank" rel="noopener noreferrer" aria-label="Google Play에서 했어? 다운로드" title="Google Play에서 다운로드">
  <span class="store-icon store-icon-google" aria-hidden="true">
    <svg viewBox="0 0 24 24" focusable="false"><use href="#icon-google-play"></use></svg>
  </span>
  <span class="store-copy">
    <span class="store-label">Google Play</span>
    <span class="store-caption">지금 다운로드</span>
  </span>
</a>
```

Change each visible Android status paragraph to:

```html
<p class="store-status" data-download-status aria-live="polite">Android 버전이 Google Play에 출시되었습니다.</p>
```

- [ ] **Step 3: Convert the mobile heart strip into a launch link**

Replace the existing status paragraph with:

```html
<a class="status-strip" href="https://play.google.com/store/apps/details?id=app.didit.did_it" target="_blank" rel="noopener noreferrer" aria-label="Google Play에서 했어? 다운로드">
  <span aria-hidden="true">♥</span>
  Google Play 출시 완료 · 지금 다운로드
  <span class="status-strip-arrow" aria-hidden="true">→</span>
</a>
```

Update the existing styles with:

```css
.status-strip {
  display: block;
  text-decoration: none;
  background: rgba(239, 250, 244, .94);
  color: var(--green-strong);
  transition: background-color .2s ease, color .2s ease;
}

.status-strip:hover {
  background: #e4f7ec;
}

.status-strip:focus-visible {
  outline: 3px solid rgba(52, 142, 100, .35);
  outline-offset: -3px;
}

.status-strip-arrow {
  display: inline-block;
  margin-left: 4px;
  color: var(--green-strong);
}
```

Keep the existing desktop media rule that hides `.status-strip`.

- [ ] **Step 4: Keep only App Store controls in the disabled handler**

Retain:

```javascript
document.querySelectorAll('.store-link[aria-disabled="true"]').forEach((button) => {
  button.addEventListener('click', (event) => {
    event.preventDefault();
    const message = button.dataset.storeMessage || '스토어 공개 출시가 준비되면 이 버튼에 연결됩니다.';
    document.querySelectorAll('[data-download-status]').forEach((status) => {
      status.textContent = message;
    });
  });
});
```

Because Google Play anchors no longer have `aria-disabled="true"`, only App Store controls are intercepted.

- [ ] **Step 5: Run post-change assertions**

Run:

```powershell
$html = Get-Content -Raw index.html
if (($html | Select-String -AllMatches 'href="https://play\.google\.com/store/apps/details\?id=app\.didit\.did_it"').Matches.Count -ne 4) { throw 'Expected four live Google Play links.' }
if (($html | Select-String -AllMatches '<span class="store-caption">지금 다운로드</span>').Matches.Count -ne 3) { throw 'Expected three live Google Play captions.' }
if ($html -notmatch 'Google Play 출시 완료 · 지금 다운로드') { throw 'Expected launch strip copy.' }
if ($html -match 'Android 준비 중|Google Play 출시 준비 중') { throw 'Android pre-launch copy remains.' }
if (($html | Select-String -AllMatches '<a[^>]+aria-disabled="true"').Matches.Count -ne 3) { throw 'Only three App Store controls should remain disabled.' }
```

Expected: no output and exit code 0.

- [ ] **Step 6: Commit home-page changes**

```powershell
git add index.html
git commit -m "Activate Google Play launch links"
```

### Task 2: Publish Release Metadata

**Files:**
- Modify: `support.html:678`
- Modify: `privacy.html:655`

**Interfaces:**
- Consumes: Existing support information rows and privacy metadata block.
- Produces: A linked Google Play support entry and exact Android release/effective dates.

- [ ] **Step 1: Run metadata assertions and verify they fail**

Run:

```powershell
$support = Get-Content -Raw support.html
$privacy = Get-Content -Raw privacy.html
if ($support -notmatch 'Android 출시일</strong><span>2026년 7월 20일') { throw 'Android release date missing.' }
if ($support -notmatch 'play\.google\.com/store/apps/details\?id=app\.didit\.did_it') { throw 'Support store link missing.' }
if ($privacy -notmatch '최종 업데이트: 2026년 7월 20일') { throw 'Privacy update date missing.' }
if ($privacy -notmatch '시행일: 2026년 7월 20일') { throw 'Privacy effective date missing.' }
```

Expected: FAIL with `Android release date missing.`

- [ ] **Step 2: Update support release information**

Replace the pre-launch store row and add the release date:

```html
<div class="info-row"><strong>Google Play</strong><span><a href="https://play.google.com/store/apps/details?id=app.didit.did_it" target="_blank" rel="noopener noreferrer">Android 앱 다운로드</a></span></div>
<div class="info-row"><strong>Android 출시일</strong><span>2026년 7월 20일</span></div>
<div class="info-row"><strong>iOS</strong><span>준비 중</span></div>
```

- [ ] **Step 3: Update privacy policy dates**

Use this exact metadata:

```html
<div class="meta">
  <span>최종 업데이트: 2026년 7월 20일</span>
  <span>시행일: 2026년 7월 20일</span>
</div>
```

Do not change the privacy clauses because the release does not alter data handling.

- [ ] **Step 4: Run metadata assertions**

Run:

```powershell
$support = Get-Content -Raw support.html
$privacy = Get-Content -Raw privacy.html
if ($support -notmatch 'Android 출시일</strong><span>2026년 7월 20일') { throw 'Android release date missing.' }
if ($support -notmatch 'play\.google\.com/store/apps/details\?id=app\.didit\.did_it') { throw 'Support store link missing.' }
if ($support -match 'Google Play 출시 준비 중') { throw 'Support pre-launch copy remains.' }
if ($privacy -notmatch '최종 업데이트: 2026년 7월 20일') { throw 'Privacy update date missing.' }
if ($privacy -notmatch '시행일: 2026년 7월 20일') { throw 'Privacy effective date missing.' }
```

Expected: no output and exit code 0.

- [ ] **Step 5: Commit release metadata**

```powershell
git add support.html privacy.html
git commit -m "Publish Android release metadata"
```

### Task 3: Verify Responsive Launch Experience

**Files:**
- Verify: `index.html`
- Verify: `support.html`
- Verify: `privacy.html`

**Interfaces:**
- Consumes: Completed static-site changes from Tasks 1 and 2.
- Produces: Verified desktop/mobile behavior with no stale Android pre-launch copy.

- [ ] **Step 1: Run static integrity checks**

Run:

```powershell
git diff --check HEAD~2..HEAD
rg -n "Android 준비 중|Google Play 출시 준비 중|앱 공개 시점부터 적용" index.html support.html privacy.html
```

Expected: `git diff --check` exits 0 and `rg` returns no matches.

- [ ] **Step 2: Start the local site**

Run:

```powershell
python -m http.server 4174
```

Expected: local site available at `http://127.0.0.1:4174/index.html`.

- [ ] **Step 3: Verify desktop layout**

At a 1440 x 1000 viewport, confirm:

- Google Play button caption is `지금 다운로드`.
- App Store caption is `iOS 출시 예정`.
- Status text says `Android 버전이 Google Play에 출시되었습니다.`
- The mobile heart strip is hidden.
- The hero and preview sections do not overlap.

- [ ] **Step 4: Verify mobile layout**

At a 390 x 844 viewport, confirm:

- The heart strip appears directly below the hero.
- It reads `♥ Google Play 출시 완료 · 지금 다운로드`.
- The full strip is clickable and points to the correct Play Store URL.
- Text fits without clipping or overlap.
- The App Store control remains visibly disabled.

- [ ] **Step 5: Verify supporting pages**

Confirm:

- `support.html` contains the live Google Play link, Android release date, and iOS preparation state.
- `privacy.html` shows both dates as `2026년 7월 20일`.
- Navigation and footer links still work.

- [ ] **Step 6: Review final diff**

Run:

```powershell
git status --short
git log -3 --oneline
```

Expected: clean working tree with the home-page and metadata commits present.
