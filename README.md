workflows:
  android-workflow:
    name: Android Build
    max_build_duration: 120
    environment:
      vars:
        PACKAGE_NAME: "com.example.healthtracking"
      android:
        ndk: "25.1.8937393"
    triggering:
      events:
        - push
        - pull_request
    scripts:
      - name: Build APK
        script: |
          ./gradlew assembleRelease
    artifacts:
      - build/app/outputs/flutter-apk/app-release.apk
    publishing:
      email:
        recipients:
          - "your-email@example.com"

  ios-workflow:
    name: iOS Build
    max_build_duration: 120
    environment:
      vars:
        XCODE_WORKSPACE: "Runner.xcworkspace"
        XCODE_SCHEME: "Runner"
      ios:
        cocoapods: default
    triggering:
      events:
        - push
        - pull_request
    scripts:
      - name: Install dependencies
        script: |
          pod install
      - name: Build iOS app
        script: |
          xcodebuild -workspace $XCODE_WORKSPACE -scheme $XCODE_SCHEME -sdk iphoneos -configuration Release archive -archivePath build/Runner.xcarchive
      - name: Export IPA
        script: |
          xcodebuild -exportArchive -archivePath build/Runner.xcarchive -exportOptionsPlist ExportOptions.plist -exportPath build/
    artifacts:
      - build/*.ipa
    publishing:
      email:
        recipients:
          - "your-email@example.com"
