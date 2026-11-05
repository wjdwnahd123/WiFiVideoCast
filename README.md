name: Build phone APK
on:
  workflow_dispatch:
  push:
    branches: [main, master]
jobs:
  build:
    runs-on: ubuntu-latest
    permissions:
      contents: read
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: '17'
      - uses: android-actions/setup-android@v3
      - run: sdkmanager 'platforms;android-35' 'build-tools;35.0.0'
      - uses: gradle/actions/setup-gradle@v4
        with:
          gradle-version: '8.9'
      - name: Extract project
        run: mkdir src && unzip -q WiFiVideoCast-Phone-Source.zip -d src
      - name: Build APK
        working-directory: src
        run: gradle --no-daemon :app:assembleDebug --stacktrace
      - name: Download APK
        uses: actions/upload-artifact@v7
        with:
          archive: false
          path: src/app/build/outputs/apk/debug/app-debug.apk
          if-no-files-found: error