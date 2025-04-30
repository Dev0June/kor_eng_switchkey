# 맥북 한글/영문 전환 키 설정 방법

## Karabiner-Elements를 사용한 한글/영문 전환 설정

### 1. 설치 방법

```bash
# Homebrew를 통해 카라비너 설치
brew install --cask karabiner-elements
```

### 2. 기본 설정 방법

1. Karabiner-Elements 앱을 실행합니다.
2. 앱이 요청하는 모든 권한을 허용합니다.
3. "Complex Modifications" 탭을 클릭합니다.
4. "Add rule" 버튼을 클릭합니다.
5. "Import more rules from the internet" 링크를 클릭합니다.
6. 웹 브라우저에서 "Korean" 또는 "한글"을 검색하여 적절한 규칙을 찾습니다.
7. 원하는 규칙을 찾았다면 "Import" 버튼을 클릭합니다.

### 3. 수동 설정 방법 (왼쪽 Command/Option 키로 한글/영문 전환)

1. 터미널에서 필요한 디렉토리를 생성합니다:
```bash
mkdir -p ~/.config/karabiner/assets/complex_modifications
```

2. 아래 내용으로 설정 파일을 생성합니다:
```bash
cat > ~/.config/karabiner/assets/complex_modifications/korean_english_fixed_switch.json << 'EOF'
{
  "title": "한글/영문 고정 전환 설정",
  "rules": [
    {
      "description": "왼쪽 Command 키 - 한글 전환",
      "manipulators": [
        {
          "type": "basic",
          "from": {
            "key_code": "left_command",
            "modifiers": {
              "optional": ["any"]
            }
          },
          "to": [
            {
              "key_code": "left_command",
              "lazy": true
            }
          ],
          "to_if_alone": [
            {
              "select_input_source": {
                "input_mode_id": "com.apple.inputmethod.Korean.2SetKorean"
              }
            }
          ],
          "parameters": {
            "basic.to_if_alone_timeout_milliseconds": 150,
            "basic.to_if_held_down_threshold_milliseconds": 100
          }
        }
      ]
    },
    {
      "description": "왼쪽 Option 키 - 영문 전환",
      "manipulators": [
        {
          "type": "basic",
          "from": {
            "key_code": "left_option",
            "modifiers": {
              "optional": ["any"]
            }
          },
          "to": [
            {
              "key_code": "left_option",
              "lazy": true
            }
          ],
          "to_if_alone": [
            {
              "select_input_source": {
                "language": "en",
                "input_source_id": "com.apple.keylayout.ABC"
              }
            }
          ],
          "parameters": {
            "basic.to_if_alone_timeout_milliseconds": 150,
            "basic.to_if_held_down_threshold_milliseconds": 100
          }
        }
      ]
    }
  ]
}
EOF
```

3. Karabiner-Elements 앱을 실행하고 "Complex Modifications" 탭으로 이동합니다.
4. "Add rule" 버튼을 클릭하고 방금 생성한 "한글/영문 고정 전환 설정" 규칙을 활성화합니다.

### 4. 입력 소스 ID 확인 방법

입력 소스의 ID를 확인해야 할 경우 다음 명령어들 중 하나를 사용합니다:

```bash
# 현재 설정된 모든 입력 소스 정보 확인
plutil -p ~/Library/Preferences/com.apple.HIToolbox.plist

# 현재 활성화된 입력 소스만 확인
plutil -p ~/Library/Preferences/com.apple.HIToolbox.plist | grep -A 5 "AppleCurrentKeyboardLayoutInputSourceID"

# 한글 입력 소스 ID 예시: com.apple.inputmethod.Korean.2SetKorean
# 영문 입력 소스 ID 예시: com.apple.keylayout.ABC
```

### 5. 시스템 설정 확인

1. 시스템 설정 → 키보드 → 텍스트 입력으로 이동합니다.
2. 한글(2벌식), ABC(영문) 두 가지 입력 소스가 추가되어 있는지 확인합니다.
3. 언어 전환 단축키 설정은 위 설정에서 직접 전환하므로 크게 중요하지 않습니다.

이 설정을 완료하면:
- 왼쪽 Command 키를 눌렀다 떼면 항상 한글로 전환
- 왼쪽 Option 키를 눌렀다 떼면 항상 영문으로 전환
- Command+Z, Command+C 등 조합키는 정상적으로 동작

## 원래 상태로 되돌리는 방법

### 1. 카라비너 규칙 비활성화/제거

1. Karabiner-Elements 앱을 실행합니다.
2. "Complex Modifications" 탭으로 이동합니다.
3. 추가했던 한글/영문 전환 규칙 옆의 "Remove" 버튼을 클릭합니다.
4. 또는 규칙 앞의 체크박스를 해제하여 일시적으로 비활성화할 수 있습니다.

### 2. 카라비너 완전 제거하기

1. 앱을 종료합니다.
2. 터미널에서 다음 명령어를 실행합니다:
```bash
# 설정 파일 삭제
rm -rf ~/.config/karabiner

# 앱 제거
brew uninstall --cask karabiner-elements
```

### 3. 시스템 설정 되돌리기

1. 시스템 설정 → 키보드 → 단축키 → 입력 소스로 이동합니다.
2. "이전 입력 소스 선택" 단축키를 기본값 또는 원하는 값으로 재설정합니다.

위 과정을 완료하면 키보드 설정이 원래 상태로 복원됩니다. 