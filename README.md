#  fastlane 설치

### 번들러 설치
```
sudo gem install bundler
```

### 프로젝트 루트에서 Gemfile 생성
```
nano Gemfile
```

### Gemfile 파일 내 아래 내용 입력 후 저장
```
source "https://rubygems.org"

gem "fastlane"
```

### 번들 업데이트
```
bundle update
```

### 프로젝트 경로에서 fastlane 초기화
```
fastlane init
```

#  match 설치

### fastlane match 설정 

```
fastlane match init
```

### Matchfile 파일 설정
```
app_identifier(["******"]) # The bundle identifier of your app

git_url("http://sample-git.com/sample-ios")
storage_mode("git")

# For all available options run `fastlane match --help`
# Remove the # in the beginning of the line to enable the other options

# The docs are available on https://docs.fastlane.tools/actions/match
```

### fastlane match 실행
```
fastlane match [[type]] # type : appstore, adhoc, enterprise or development
```

# fastlane 설정

### AppFile 설정
```
app_identifier("*******") # The bundle identifier of your app
apple_id("*******@gmail.com") # Your Apple Developer Portal username

team_id("*******") # Developer Portal Team ID
itc_team_id("*******") # App Store Connect Team ID
```


### FastFile 설정
```
default_platform(:ios)
platform :ios do
  desc "Push a new production build to TestFlight"
  lane :production do
    api_key = app_store_connect_api_key(
      key_id: "*******",
      issuer_id: "*******",
      key_filepath: "./fastlane/AuthKey_*******.p8",
      duration: 1200, # optional (maximum 1200)
      in_house: false # optional but may be required if using match/sigh
    )
    match(
      api_key: api_key,
      type: "appstore", 
      force_for_new_devices: true
    )
    build_app(
      workspace: "Sample.xcworkspace", 
      scheme: "Sample",
      export_method: "app-store"
    )
    upload_to_testflight(
      api_key: api_key
    )
  end
  desc "Push a new stage build to App Distribution"
  lane :stage do
    api_key = app_store_connect_api_key(
      key_id: "H7FWG43W39",
      issuer_id: "69a6de74-5086-47e3-e053-5b8c7c11a4d1",
      key_filepath: "./fastlane/AuthKey_H7FWG43W39.p8",
      duration: 1200, # optional (maximum 1200)
      in_house: false # optional but may be required if using match/sigh
    )
    match(
      api_key: api_key,
      type: "development", 
      force_for_new_devices: true
    )
    build_app(
      workspace: "Sample.xcworkspace", 
      scheme: "Sample.Stage",
      export_method: "development"
    )
    firebase_app_distribution(
	    firebase_cli_token: ENV["FIREBASE_TOKEN"],
	    release_notes_file: "./fastlane/changelog.txt",
      groups: "iOS"
    )
  end
end
```

# App Distribution 연동

### 플러그인 설치
```
fastlane add_plugin firebase_app_distribution
```

### 구글 로그인 시도
```
bundle exec fastlane run firebase_app_distribution_login
```

### 환경 변수 설정
```
export FIREBASE_TOKEN=[[구글 로그인 시도 결과 값 token]]
```

# fastlane 시작

### fastlane 실행
```
fastlane production | fastlane stage
```
