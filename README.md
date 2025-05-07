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

    /* CapsLock: tap -> Ctrl+Space (다음 입력소스), hold -> 원래 Caps */
    {
      "description": "Caps tap → Ctrl-Space, hold → CapsLock",
      "manipulators": [
        {
          "type": "basic",
          "from": { "key_code": "caps_lock",
                    "modifiers": { "optional": ["any"] } },

          "to_if_alone": [
            { "key_code": "spacebar", "modifiers": ["left_control"] }
          ],
          "to_if_held_down": [
            { "key_code": "caps_lock" }
          ],
          "parameters": { "basic.to_if_held_down_threshold_milliseconds": 400 }   /* 탭·홀드 판정 0.4초(300~500 조절) */
        }
      ]
    },

    /* 왼 Option 단독 → 영문 */
    {
      "description": "Left ⌥ alone → ABC",
      "manipulators": [
        {
          "type": "basic",
          "from": { "key_code": "left_option",
                    "modifiers": { "optional": ["any"] } },
          "to": [ { "key_code": "left_option", "lazy": true } ],
          "to_if_alone": [
            { "select_input_source": { "input_source_id": "com.apple.keylayout.ABC" } }
          ]
        }
      ]
    },

    /* 왼 Command 단독 → 두벌식 + Hangul 고정 */
    {
      "description": "Left ⌘ alone → 2-Set Korean + Hangul",
      "manipulators": [
        {
          "type": "basic",
          "from": { "key_code": "left_command",
                    "modifiers": { "optional": ["any"] } },
          "to": [ { "key_code": "left_command", "lazy": true } ],
          "to_if_alone": [
            {
              "select_input_source": {
                "input_source_id": "com.apple.inputmethod.Korean.2SetKorean",
                "input_mode_id":   "com.apple.inputmethod.Korean.2SetKorean"
              }
            },
            { "key_code": "spacebar", "modifiers": ["left_control"] }   /* 혹시 내부 모드가 A 로 남아 있을 때 대비 */
          ]
        }
      ]
    }
  ]
}
EOF
```

4. 우측 하단(또는 상단)의 **"Add predefined rule"** 버튼(버전에 따라 "Add rule"로 표기)을 클릭해 규칙 목록을 엽니다.  
   - 만약 목록에 새 규칙이 바로 보이지 않으면, 메뉴 ⚙ 아이콘 → **ReloadXML**(또는 앱 재시작) 후 다시 시도하세요.
5. 목록에서 "한글/영문 고정 전환 설정" 항목의 **Enable** 버튼을 눌러 규칙을 활성화합니다.

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

또는 Karabiner-EventViewer(⌥⇧⌘K) → Variables 패널에서 `input_source_id` 값을 실시간으로 확인할 수도 있습니다.

### 5. 시스템 설정 확인

1. 시스템 설정 → 키보드 → 텍스트 입력으로 이동합니다.
2. 한글(2벌식), ABC(영문) 두 가지 입력 소스가 추가되어 있는지 확인합니다.
3. "다음 입력 소스 선택" 단축키가 Ctrl + Space 로 설정·활성화되어 있는지 확인합니다.

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

## 자주 묻는 질문(FAQ)

**Q1. Caps Lock 탭이 한/영 전환이 아니라 대문자 고정만 됩니다.**  
A. macOS 키보드 단축키에서 "다음 입력 소스 선택"이 Ctrl + Space 로 켜져 있는지, Karabiner-Elements 에 충분한 권한(입력 모니터링, 접근성)이 부여되어 있는지 먼저 확인하세요.

**Q2. 왼쪽 ⌘ 단독을 눌러도 계속 'A' 모드(영문)가 나옵니다.**  
A. EventViewer 로 `input_source_id` 가 올바르게 전송되는지 확인하시고, JSON 안의 `input_source_id` / `input_mode_id` 값을 자신의 Mac 에서 조회한 실제 값으로 바꿔 주세요.

**Q3. 키가 두 번 눌리거나 잘못 인식됩니다.**  
A. `"basic.to_if_held_down_threshold_milliseconds"` 값을 400 → 500 정도로 늘리거나, 시스템 키보드 반응 속도를 '느리게' 로 조정해 보세요. 