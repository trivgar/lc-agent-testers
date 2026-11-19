# mobile-agent-testers

**20 AI testers on 20 different phones, in parallel, for $0.**

No test scripts to write. Each tester is a Claude Code agent with its own iOS
Simulator or Android emulator on GitHub Actions. It plays a persona (iPhone SE
in Swedish at the largest text size, Pixel 7 at 200%, iPad in German, Arabic
right to left...), uses your app like a new user and writes a bug report with
screenshots. Flutter, React Native, Expo, native Kotlin and Swift, Kotlin
Multiplatform, Capacitor and .NET MAUI.

<p align="center"><a href="assets/localsend-20.mp4"><img src="assets/localsend-20.gif" alt="Real screen recordings of 20 AI testers using LocalSend at the same time: 10 Android emulators and 10 iOS Simulators in 14 languages, from a small Pixel to a 13-inch iPad. When a tester finishes, its phone shows how many issues it found. Opens the video." width="100%"></a></p>

[![Stars](https://img.shields.io/github/stars/pierridotite/mobile-agent-testers?style=flat)](https://github.com/pierridotite/mobile-agent-testers/stargazers)
[![License: MIT](https://img.shields.io/badge/license-MIT-green)](LICENSE)
![iOS + Android](https://img.shields.io/badge/platforms-iOS%20%2B%20Android-blue)
![Claude Code plugin](https://img.shields.io/badge/Claude%20Code-plugin-orange)
[![Last commit](https://img.shields.io/github/last-commit/pierridotite/mobile-agent-testers)](https://github.com/pierridotite/mobile-agent-testers/commits/main)

## Real findings

<p align="center"><img src="assets/real-findings-localsend-20.png" alt="Four LocalSend screens with Flutter's overflow stripes, each boxed in red: a Pixel 7 at 200% text where all six Send buttons overflow, an iPhone SE in Swedish at the largest text size where the message dialog overflows by 149 pixels and hides the text field and Confirm, an iPad Air in German where the Add button overflows its card by 114 pixels, and an iPhone SE in French at the default text size where a row overflows by 28 pixels" width="100%"></p>

- **[LocalSend](https://github.com/localsend/localsend)** (100k stars, Flutter), 20 agents on 20 devices in 14 languages:
  - iPhone SE in Swedish at iOS's largest text size: the message dialog overflows by 149 px, so the text field and Confirm are off screen and you can't send text at all.
  - Pixel 7 at Android's 200% font: all six Send buttons overflow and cut their labels in half. Same on an iPhone 16 Plus in Italian at the first accessibility size.
  - iPad Air in German with large text: "Hinzufügen" runs 114 px past its card, and Settings values break mid-word.
  - iPhone SE in French at the default text size: "Recevoir via un lien" overflows by 28 px and hides a checkbox.
  - Pixel Tablet in Spanish: the keyboard covers the address dialog's Cancel and Confirm.
  - A failed manual send says only "Error", "Erreur", "Fehler", "エラー", "Hata" or "Erro", with no reason. On iOS, an address that doesn't answer leaves the dialog waiting with no progress shown (three agents).

  103 reports in all (1 blocker, 15 major), which group into 46 distinct bugs: 29 of them, the blocker included, were found by a single persona. 69 minutes from start to the last report (the Free plan runs 5 macOS jobs at a time), $4.62 of model usage for the 20. Run it yourself:

  ```bash
  gh workflow run agent-testers.yml -f source_repo=localsend/localsend -f app_dir=app \
    -f app_id=org.localsend.localsend_app -f android_personas=all -f ios_personas=all -f record_video=true
  ```

<p align="center"><img src="assets/real-findings-ankidroid.png" alt="Four AnkiDroid screens, each problem boxed in red: a Pixel 7 at 200% text where the Again, Hard, Good and Easy buttons are cut in half (shown magnified), the same phone's Statistics page where the lines run off the right edge, and the welcome screen in Portuguese and in Japanese where the Get started button is still in English" width="100%"></p>

- **[AnkiDroid](https://github.com/ankidroid/Anki-Android)** (12k stars, Kotlin), 10 agents on 10 Android emulators, straight from the APK of their GitHub release, no build:
  - Pixel 7 at Android's 200% font: the Again / Hard / Good / Easy buttons are cut in half, and the Statistics text runs off the right edge. A second agent found the buttons still cut on their 2.26 alpha, in both rows when "Show large answer buttons" is on.
  - The welcome screen says "Get started" in English on devices set to Portuguese, Spanish and Japanese, while the rest of the screen is translated (three agents, each on its own).

  83 reports, which group into 43 distinct issues (two of them come from the outdated system WebView of the Android 10 and 11 emulator images, not from the app). Under 10 minutes from start to the last report, $4.69 of model usage for the 10. Run it yourself:

  ```bash
  gh workflow run agent-testers.yml -f app_dir=. -f app_id=com.ichi2.anki -f brief=briefs/ankidroid.md \
    -f android_build="curl -fsSL -o anki.apk https://github.com/ankidroid/Anki-Android/releases/download/v2.25.3/AnkiDroid-2.25.3-full-universal.apk" \
    -f apk_path=anki.apk -f android_personas=all -f ios_personas=none -f block_hosts="ankiweb.net www.google-analytics.com"
  ```

<p align="center"><img src="assets/real-finding-flutter.png" alt="Two phones on the same screen of Flutter's animations example app, a Pixel 7 and an iPhone SE in French, with the title Streamling your courses circled: both agents reported this typo on their own" width="100%"></p>

- **Flutter's official animations sample** ([flutter/packages](https://github.com/flutter/packages/tree/main/packages/animations/example)): an agent on a Pixel 7 and another on an iPhone SE in French each reported the same typo, "Streamling your courses" ([line 110](https://github.com/flutter/packages/blob/main/packages/animations/example/lib/shared_axis_transition.dart#L110)), and the Android one noticed the app ignores dark mode. 19 minutes, $0.47 of model usage.

Ran it on your app? [Share what it found](https://github.com/pierridotite/mobile-agent-testers/issues/new?title=Finding:%20) and it goes in this list.

## Quick start

```bash
gh repo create my-testers --public --template pierridotite/mobile-agent-testers --clone
cd my-testers && ./setup.sh           # key pair + secrets + Claude token (Windows: .\setup.ps1)
gh workflow run agent-testers.yml     # demo: 2 Android + 2 iOS testers, ~20 min
bash scripts/collect.sh <run id>      # decrypts reports, groups duplicates: runs/<id>/SUMMARY.md
```

The demo app has [11 deliberate bugs](demo-app/BUGS.md). On the recorded run above, the 4 demo testers found 9 of them (2 to 7 minutes per agent, about 21 minutes for the whole run, builds included), plus real issues nobody planted (a button under the Android nav bar, no data kept after a relaunch). Model usage: $0.87 for the 4.

Or let Claude set it up for your app:

```
/plugin marketplace add pierridotite/mobile-agent-testers
/plugin install mobile-agent-testers@mobile-agent-testers
```

## Why not Maestro, Appium or agent-device?

They run the tests you write. These agents look for what nobody thought to test.

| | mobile-agent-testers | Maestro, Appium, Detox, e2e |
|---|---|---|
| You write | Nothing: pick personas from the list | Flows or test code |
| It catches | Layout at large text, dark mode, confusing copy, dead ends | What each test checks |
| Same result every run | No: each run explores, so check the screenshots | Yes |

[agent-device](https://github.com/callstackincubator/agent-device) gives one coding agent hands on one simulator. This runs a whole session: it builds the app, gives each persona its own device and collects encrypted reports. Use agents before a release, and scripted flows for regressions.

## Test your own app

```bash
gh workflow run agent-testers.yml -f source_repo=you/app -f app_id=com.you.app \
  -f android_personas=anna,malik -f ios_personas=clara,jonas
```

| Input | |
|---|---|
| `source_repo`, `ref` | App repo and branch (empty = this repo). Private repo: add a read-only `SOURCE_REPO_TOKEN` secret |
| `app_dir`, `app_id` | App folder, Android package / iOS bundle id (if the other platform uses another id, it is read from its built app) |
| `android_personas`, `ios_personas` | Ids from [personas.json](personas.json), one phone each, `all`, or `none` to skip a platform. 20 ready: 14 languages, right to left, tablets, text from small to the largest accessibility size |
| `brief` | Agent instructions ([AGENT_BRIEF.md](AGENT_BRIEF.md)) |
| `framework` | `auto` (default) or force one of the frameworks below |
| `android_build` + `apk_path`, `ios_build` + `ios_app_path` | Your own build command, or a download of a prebuilt app (paths relative to `app_dir`) |
| `block_hosts` | Analytics hosts blocked during tests |
| `record_video` | Screen recording of each phone, encrypted with the reports |

**Supported out of the box** (detected automatically):

| Framework | Android | iOS Simulator |
|---|---|---|
| Flutter | `flutter build apk --debug` | `flutter build ios --simulator` |
| React Native / Expo | Gradle release | Xcode, arm64 |
| Native Kotlin / Java, Swift / Objective-C | Gradle debug | Xcode, arm64 |
| Kotlin Multiplatform | Gradle debug | Xcode project of the iOS app |
| Capacitor / Ionic | Gradle debug after `cap sync` | Xcode after `cap sync` |
| .NET MAUI (beta) | `dotnet publish` | `dotnet build` for `iossimulator-arm64` |
| Anything else | `android_build` + `apk_path` | `ios_build` + `ios_app_path` |

Agents read the screen from screenshots and the accessibility tree, so any UI
toolkit works. Labelled widgets (Flutter `Semantics`, `contentDescription`,
`accessibilityLabel`) give them more precise tap targets.

## How it works

<p align="center"><img src="assets/how-it-works.svg" alt="How it works: one workflow builds the app, then one emulator or simulator with one agent per persona, encrypted reports, summary on your machine" width="100%"></p>

- **Personas** set the device: model, OS, font size, dark mode, language, location.
- **Three tools per agent**: see the screen (with tap coordinates), gestures, typing.
- **Encrypted results**: public repo logs are public, so everything is encrypted with your key.
- **Analytics blocked** inside the phones, so tests never touch your real stats.
- **One summary** on your machine: Claude groups the reports that describe the same bug, so you see how many personas hit each one and which personas found which kind of issue.

## Limits

- GitHub runners are free for public repos: about 20 jobs at once, 5 on macOS.
- On plans with a 5-hour usage limit, 20 agents can use it up in ~15 min: run batches of 6-10.
- Hosted runners are meant for the repo's own project. For a closed-source app, run the workflow in your private repo or on your own Mac.

## Star history

[![Star History Chart](https://api.star-history.com/svg?repos=pierridotite/mobile-agent-testers&type=Date)](https://star-history.com/#pierridotite/mobile-agent-testers&Date)

## Credits

[Claude Code](https://docs.claude.com/en/docs/claude-code) ·
[AXe](https://github.com/cameroncooke/axe) ·
[android-emulator-runner](https://github.com/ReactiveCircus/android-emulator-runner)

Made by [@pierridotite](https://github.com/pierridotite) ([LinkedIn](https://www.linkedin.com/in/pierre-raffalli/)) · MIT license
