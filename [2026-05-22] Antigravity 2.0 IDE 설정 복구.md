날씨도 맑고 미세먼지도 없는 날이라서 영종도 해변 카페로 나와 기분 좋게 맥북을 펼쳤다. Antigravity를 열었더니 업데이트 알림이 떠서 설치 버튼을 눌렀다. 해변을 바라보며 기분 좋게 업데이트 완료를 기다린 후 Antigravity 2.0을 열었더니...

ㅆㅂ... 설정이 다 날아가 버렸다. 

Antigravity 2.0 IDE로 분리한 건 좋다. 그래, 뭐... 앱들이 중구난방이라 좀 정리하고 싶을 수도 있지...

근데... IDE를 초기화해 버리는 건 도대체 무슨 생각이지?

아래는 기존 설정을 Antigravity 2.0 IDE로 옮기는 명령어다. macOS 및 Fish shell 기준이므로 다른 환경이라면 조금 수정해야 할 수도 있다.

```fish
# 경로가 없을 경우를 대비해 생성
mkdir -p ~/"Library/Application Support/Antigravity IDE/User"
mkdir -p ~/.antigravity-ide/extensions

# 기존 설정으로 새 경로의 파일들을 강제 덮어쓰기
cp -Rf ~/"Library/Application Support/Antigravity/User/"* ~/"Library/Application Support/Antigravity IDE/User/"

# 기존 플러그인들로 새 경로의 폴더들을 강제 덮어쓰기
cp -Rf ~/.antigravity/extensions/* ~/.antigravity-ide/extensions/
```

내 경우에는 위 방법으로 기존 설정이 잘 복구되었다.

...

ㅆㅂ... 다시 생각해도 열받네. 이 정도로 어이없는 개발 도구 업데이트는 처음이라 "이건 도대체 뭐지?" 싶기도 하고...