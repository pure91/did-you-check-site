# Launch Strip Refinement

## Goal

Restore the mobile Google Play launch strip to the website's original warm, translucent appearance and remove redundant Android release status text.

## Mobile Launch Strip

- Keep the entire strip linked to `https://play.google.com/store/apps/details?id=app.didit.did_it`.
- Keep the visible text `♥ Google Play 출시 완료 · 지금 다운로드 →`.
- Restore the original background to `rgba(255, 255, 255, .88)`.
- Restore the original muted text color while retaining the pink heart.
- Use only a subtle warm hover background; do not use the current mint-green background.
- Keep the existing keyboard focus treatment.
- Keep the strip hidden at desktop breakpoints.

## Download Status Text

- Remove both visible `Android 버전이 Google Play에 출시되었습니다.` paragraphs from the home page.
- Remove the associated `.store-status` styles so no empty vertical space remains.
- Keep all three active Google Play buttons and their `지금 다운로드` captions unchanged.
- Keep all three App Store buttons disabled with their `iOS 출시 예정` captions.
- Disabled App Store controls only prevent navigation; they do not reveal a separate status paragraph.

## Scope

- Modify only `index.html`.
- Do not change support or privacy content.
- Do not change store URLs, release dates, responsive breakpoints, or the existing heart strip placement.

## Verification

- Confirm the two Android status paragraphs are absent.
- Confirm `.store-status` styles and `[data-download-status]` behavior are absent.
- Confirm all four Google Play links remain correct.
- Confirm all three App Store controls remain disabled.
- Confirm the mobile strip uses the original background value and desktop rules still hide it.
