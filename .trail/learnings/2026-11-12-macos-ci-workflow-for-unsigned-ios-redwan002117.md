---
title: macOS CI workflow for unsigned iOS beta IPA (.github/workflows/ios-beta.yml)
author: Redwan002117
date: 2026-11-12
branch: main
id: l-8dc91fe9
scope: repo
status: active
src: agent
touches:
  - ".github/workflows/ios-beta.yml"
  - capacitor.config.json
---

## Decided
ios-beta.yml runs on macos-15, reads live app version from singercms.bohuvuj.com/downloads/android.json, generates ios/ via npx cap add ios && npx cap sync ios, stamps 1024x1024 icons via sips, and sets CFBundleDisplayName and FaceID/Location/Camera/PhotoLibrary Info.plist usage descriptions via PlistBuddy.

## Tried and ruled out
Committing generated xcodeproj/xcuserdata blobs or paying Apple Developer fees during early unsigned beta distribution.

## Watch out
Use [skip ci] when pushing docs/.trail updates so macOS-15 IPA builds are not triggered unnecessarily; cleartext stays false in capacitor.config.json.
