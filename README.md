name: Build Android APK

on:
  push:
    branches: [main, master]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: 18
      - run: |
          mkdir www
          cp index.html www/
          npm init -y
          npm install @capacitor/core @capacitor/cli @capacitor/android
          npx cap init "WidianApp" com.widian.app --web-dir www
          npx cap add android
          npx cap sync
      - uses: actions/setup-java@v3
        with:
          distribution: 'zulu'
          java-version: '17'
      - run: |
          cd android
          chmod +x gradlew
          ./gradlew assembleDebug
      - uses: actions/upload-artifact@v3
        with:
          name: widian-app-apk
          path: android/app/build/outputs/apk/debug/app-debug.apk
