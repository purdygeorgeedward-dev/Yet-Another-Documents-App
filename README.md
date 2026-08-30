# Fossify Documents

<img alt="Logo" src="graphics/icon.webp" width="120" />

<a href='https://play.google.com/store/apps/details?id=org.fossify.documents'><img alt='Get it on Google Play' src='https://play.google.com/intl/en_us/badges/static/images/badges/en_badge_web_generic.png' height=80/></a> <a href="https://apt.izzysoft.de/fdroid/index/apk/org.fossify.documents"><img src="https://gitlab.com/IzzyOnDroid/repo/-/raw/master/assets/IzzyOnDroid.png" alt="Get it on IzzyOnDroid" height=80/></a>

Fossify Documents is a private, offline document reader and organizer for Android.

It supports PDF, DOCX, plain text, Markdown, CSV, and HTML documents. Text,
Markdown, and CSV files can be edited, while DOCX files open in a clean mobile
reading view and CSV files use a dedicated table viewer by default. Documents can
be opened directly, organized through selected folders, searched, filtered, sorted,
and marked as favorites.

The app contains no ads or unnecessary permissions and integrates with Fossify's
shared themes, custom fonts, and appearance settings.

➡️ Explore more Fossify apps: https://www.fossify.org<br>
➡️ Open-Source Code: https://www.github.com/FossifyOrg<br>
➡️ Join the community on Reddit: https://www.reddit.com/r/Fossify<br>
➡️ Connect on Telegram: https://t.me/Fossify

<div align="center">
<img alt="Documents home screen" src="fastlane/metadata/android/en-US/images/phoneScreenshots/1_en-US.png" width="30%">
<img alt="Folder browser" src="fastlane/metadata/android/en-US/images/phoneScreenshots/3_en-US.png" width="30%">
<img alt="Markdown document" src="fastlane/metadata/android/en-US/images/phoneScreenshots/4_en-US.png" width="30%">
</div>
---

## Fork changes (Yet-Another-Documents-App)

- **Fixed the color scheme clash reported in this app's own screenshots**
  and **added HSB sliders to the shared color picker** - both fixed at
  the source in the forked Yet-Another-Commons dependency (see that
  repo's own README/commits), not locally in this app. This app now
  depends on that fork via JitPack.

- **Bugfix pass: investigated thoroughly, no changes needed.** This is a
  genuinely well-built app - checked four distinct, high-risk areas and
  found each correctly implemented:
  - Both `TextDocumentViewModel` and `StructuredDocumentViewModel`
    correctly dispatch file loading via `viewModelScope.launch(Dispatchers.IO)`,
    not on the main thread, and switch back to `Dispatchers.Main` for UI
    updates.
  - `PdfDocumentAdapter` (the print support) creates its own
    `CoroutineScope(SupervisorJob() + Dispatchers.IO)` for background
    writes - confirmed it's properly cancelled in `onFinish()`, the
    correct lifecycle callback for this, not left to leak.
  - Every `LazyColumn` `items()` call already specifies a stable `key`
    (based on each document/folder's URI) - not the common Compose
    pitfall of keyless items causing excessive recomposition.

  **Known, unresolved risk carried over from the commons dependency
  switch**: JitPack build success for Yet-Another-Commons still hasn't
  been verified in a real Gradle sync - `jitpack.io` isn't reachable from
  this environment. See the dependency-switch commit for full detail.
