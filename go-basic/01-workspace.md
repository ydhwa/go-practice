# 워크스페이스 설정

## 폴더 구성

- `src/`: 모든 소스코드 저장. 새로운 프로그램을 만들고 싶다면 src 폴더에 프로그램명을 딴 하위폴더를 만든다.
- `pkg/`: 컴파일된 패키지 파일 저장
- `bin/`: Go 프로그램의 바이너리 저장

## 환경변수 설정
- `GoRoot`: Go의 설치 경로
- `GoPath`: 현재 작업 중인 Go 워크스페이스 폴더의 경로
  - <https://github.com/golang/go/wiki/SettingGOPATH>
  - 유닉스: `$Home/go`
  - 윈도우: `%USERPROFILE%\go`
