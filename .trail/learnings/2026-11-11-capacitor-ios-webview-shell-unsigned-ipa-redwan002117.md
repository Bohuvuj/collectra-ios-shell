---
title: "Capacitor iOS WebView Shell & Unsigned IPA CI Pipeline"
author: Redwan002117
date: 2026-11-11
branch: main
id: l-4e6dfada
scope: repo
status: active
src: agent
touches:
  - capacitor.config.json
  - package.json
  - ".github/workflows/ios-beta.yml"
  - ".gitignore"
  - README.md
  - assets/icon.png
---

## Decided
- collectra-ios-shell is a thin Capacitor 8.5.3 iOS WebView shell (appId com.bohuvuj.singercms, appName "Collectra CMS") with zero bundled app logic: capacitor.config.json sets server.url to https://singercms.bohuvuj.com/dashboard, allowNavigation to ["singercms.bohuvuj.com", "*.supabase.co"], cleartext false, and Keyboard.resize "native".
- Native capabilities declared in package.json: @capacitor/app 8.1.2, @capacitor/browser 8.0.5, @capacitor/keyboard 8.0.6, and @aparajita/capacitor-biometric-auth 10.0.0.
- .github/workflows/ios-beta.yml synthesizes the iOS Xcode project on macos-15 in CI (since ios/ and www/ are gitignored in .gitignore): fetches the live app version from https://singercms.bohuvuj.com/downloads/android.json, writes a placeholder www/index.html, runs npx cap add ios && npx cap sync ios, resizes assets/icon.png to 1024x1024 AppIcon-512@2x.png via sips, injects Info.plist usage strings (NSFaceIDUsageDescription, NSLocationWhenInUseUsageDescription, NSCameraUsageDescription, NSPhotoLibraryUsageDescription) via PlistBuddy, builds an unsigned .xcarchive with CODE_SIGNING_ALLOWED=NO, zips Payload/App.app into collectra-cms-ios-beta.ipa, and publishes a latest GitHub Release tagged beta-<run_number>.

## Tried and ruled out
- Committing the generated ios/ Xcode project or bundling static web assets inside www/: kept gitignored so all UI/business updates ship immediately from https://singercms.bohuvuj.com without rebuilding the IPA.
- Code-signing inside CI without an Apple Developer certificate: workflow builds an unsigned IPA for Sideloadly/AltStore sideloading with a personal Apple ID (7-day re-sign cycle, no APNs push notifications).

## Watch out
- .github/workflows/ios-beta.yml triggers on both workflow_dispatch and push to main (plus reads live version from https://singercms.bohuvuj.com/downloads/android.json), so any push to main triggers a macos-15 runner build and publishes a new beta-<run_number> release.
- Never drop *.supabase.co from capacitor.config.json allowNavigation or remove Info.plist permission keys in ios-beta.yml, or Supabase auth redirects and Face ID / camera / location prompts will fail inside WKWebView.
