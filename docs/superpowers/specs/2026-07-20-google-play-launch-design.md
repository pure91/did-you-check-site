# Google Play Launch Website Update

## Goal

Update the didyoucheck.kr website from a pre-launch state to an Android launch state while preserving the existing calm, friendly visual identity. The mobile heart status strip remains the main launch accent.

## Confirmed Release Details

- App: 했어?
- Platform: Android
- Store: Google Play
- Release date: 2026-07-20
- Store URL: https://play.google.com/store/apps/details?id=app.didit.did_it
- iOS status: In preparation

## Home Page

### Google Play Links

All three Google Play buttons must:

- Link to the confirmed public Google Play URL.
- Be enabled links with normal pointer behavior.
- Use the caption `지금 다운로드`.
- Open in a new tab with safe external-link attributes.
- Have an accessible label that identifies Google Play download availability.

### App Store Links

All App Store buttons remain disabled and keep the caption `iOS 출시 예정`. Clicking one continues to show the fixed message `iOS 버전은 준비 중 입니다.` without changing the page layout.

### Mobile Launch Strip

Keep the existing heart strip directly below the mobile hero and turn the whole strip into a Google Play link. The visible text is:

`♥ Google Play 출시 완료 · 지금 다운로드`

The strip should retain its current visual role and spacing, with only restrained launch emphasis such as a clearer mint tint and a small directional cue. It must remain hidden in the desktop layout, matching current responsive behavior.

### Status Copy

Desktop and other visible download status text should say:

`Android 버전이 Google Play에 출시되었습니다.`

The status area keeps its existing reserved height so interaction does not move nearby content.

## Support Page

Replace the pre-launch store message with:

- A direct Google Play link.
- Android release date: `2026년 7월 20일`.
- iOS status: `준비 중`.

No unrelated support content changes are included.

## Privacy Policy

Update the policy metadata to:

- Final update: `2026년 7월 20일`.
- Effective date: `2026년 7월 20일`.

The release itself does not change data collection or processing behavior, so no new privacy clauses are added.

## Behavior and Accessibility

- External Google Play links use `target="_blank"` and `rel="noopener noreferrer"`.
- Disabled App Store controls retain `aria-disabled="true"`.
- Enabled Google Play links must not be intercepted by the disabled-store JavaScript handler.
- Focus and hover states remain consistent with existing store buttons.
- The mobile launch strip has a clear accessible label and visible keyboard focus.

## Verification

- Confirm every Google Play link points to the exact package URL.
- Confirm no Android pre-launch copy remains.
- Confirm the App Store remains disabled.
- Check desktop and mobile layouts in the browser.
- Verify the mobile heart strip is visible only on mobile and links correctly.
- Verify privacy and support dates and links.
- Run a local static-site link and markup check where available.
