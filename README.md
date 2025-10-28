# STM32 VSCode 빌드 환경 설정 템플릿

STM32CubeIDE에서 생성한 프로젝트를 VSCode에서 빌드할 수 있도록 하는 표준 설정 템플릿입니다.

## 특징

- ✅ STM32CubeIDE와 VSCode 이중 환경 지원
- ✅ 같은 빌드 폴더 공유 (Debug/Release)
- ✅ IntelliSense 자동완성 및 오류 표시
- ✅ 빠른 빌드 및 플래시
- ✅ 모든 STM32 시리즈 지원

## 사용 방법

### 1. 사전 준비

다음 도구가 설치되어 있어야 합니다:

- **STM32CubeIDE** (예: `C:\ST\STM32CubeIDE_1.19.0`)
- **STM32CubeCLT** (예: `C:\ST\STM32CubeCLT_1.19.0`)

### 1-1. 환경 변수 설정 (권장)

**다른 환경에서도 쉽게 사용할 수 있도록 Windows 환경 변수를 설정하세요.**

#### 설정 방법

1. Windows 검색에서 "환경 변수" 검색
2. "시스템 환경 변수 편집" 실행
3. "환경 변수" 버튼 클릭
4. "사용자 변수" 또는 "시스템 변수"에서 "새로 만들기" 클릭
5. 다음 두 개의 변수 추가:

```
변수 이름: STM32_CUBE_IDE_PATH
변수 값:   C:\ST\STM32CubeIDE_1.19.0

변수 이름: STM32_CUBE_CLT_PATH
변수 값:   C:\ST\STM32CubeCLT_1.19.0
```

**주의사항:**
- 경로 끝에 백슬래시(`\`)를 붙이지 마세요
- 설치된 버전에 맞게 경로를 수정하세요
- 환경 변수 설정 후 VSCode를 재시작하세요

#### 환경 변수 사용의 장점

- ✅ 버전이 다른 환경에서도 환경 변수만 수정하면 됨
- ✅ `.vscode` 폴더 설정 파일은 수정 불필요
- ✅ 팀원들과 공유 시 각자의 환경에 맞게 쉽게 설정 가능
- ✅ GitHub에서 클론 후 바로 사용 가능

### 2. 프로젝트 생성

1. STM32CubeIDE에서 CubeMX로 새 프로젝트 생성
2. CubeIDE에서 한 번 빌드하여 Debug/Release 폴더 생성

### 3. VSCode 설정

#### 방법 1: 템플릿 복사 (빠른 방법)

```bash
# 이 저장소를 클론
git clone https://github.com/trionking/stm32-vscode-template.git

# .vscode 폴더를 프로젝트에 복사
cp -r stm32-vscode-template/.vscode YOUR_PROJECT_FOLDER/
```

#### 방법 2: 수동 생성

프로젝트 루트에 `.vscode` 폴더를 만들고 3개 파일 생성:
- `tasks.json`
- `c_cpp_properties.json`
- `settings.json`

### 4. 설정 수정

프로젝트에 맞게 다음 2곳을 수정하세요:

#### A. `tasks.json`의 프로젝트 이름

```json
"${workspaceFolder}/Debug/YOUR_PROJECT_NAME.elf"
```
→ 실제 프로젝트 이름으로 변경 (예: `my_project.elf`)

**프로젝트 이름 확인:**
```bash
grep "BUILD_ARTIFACT_NAME" Debug/makefile
```

#### B. `c_cpp_properties.json`의 MCU 설정

**1) MCU define 변경:**
```json
"defines": [
    "STM32H743xx",  // ← 사용하는 MCU로 변경
    ...
]
```

**MCU define 예시:**
- STM32H743: `STM32H743xx`
- STM32H723: `STM32H723xx`
- STM32F407: `STM32F407xx`
- STM32F103: `STM32F103xB`

**2) Include 경로 변경 (MCU 시리즈가 다를 경우):**

STM32F4를 사용한다면:
```json
"includePath": [
    "${workspaceFolder}/Core/Inc",
    "${workspaceFolder}/Drivers/STM32F4xx_HAL_Driver/Inc",        // H7 → F4
    "${workspaceFolder}/Drivers/STM32F4xx_HAL_Driver/Inc/Legacy",
    "${workspaceFolder}/Drivers/CMSIS/Device/ST/STM32F4xx/Include",
    "${workspaceFolder}/Drivers/CMSIS/Include"
]
```

**MCU define 확인 방법:**
```bash
grep "STM32" Debug/subdir.mk | grep "\-D"
```

### 5. VSCode에서 빌드

1. VSCode에서 프로젝트 폴더 열기
2. `Ctrl+Shift+B` 눌러서 빌드
3. 성공!

## 빌드 명령어

**VSCode에서:**
- 빌드: `Ctrl+Shift+B` 또는 `Terminal > Run Task > Build (Debug)`
- 클린: `Terminal > Run Task > Clean (Debug)`
- 플래시: `Terminal > Run Task > Flash (ST-Link)`
- 빌드+플래시: `Terminal > Run Task > Build and Flash`

**커맨드 라인:**
```bash
cd Debug
make -j all
```

## 도구 버전이 다른 경우

**환경 변수를 사용하면 이 문제가 간단해집니다!**

설치된 도구 버전이 다를 경우:

### 환경 변수를 설정한 경우 (권장)

1. Windows 환경 변수만 수정하면 됩니다
2. VSCode 설정 파일(`.vscode/*.json`)은 수정할 필요가 없습니다

**설정 방법:**

Windows 환경 변수에서 버전에 맞게 경로를 수정:

```
STM32_CUBE_IDE_PATH = C:\ST\STM32CubeIDE_X.XX.X
STM32_CUBE_CLT_PATH = C:\ST\STM32CubeCLT_X.XX.X
```

VSCode를 재시작하면 새 경로가 적용됩니다.

### 환경 변수를 사용하지 않는 경우

직접 `tasks.json`과 `c_cpp_properties.json`에서 경로를 수정해야 합니다.

**make.exe 플러그인 경로 찾기:**
```bash
dir "C:\ST\STM32CubeIDE_*\STM32CubeIDE\plugins" | findstr "externaltools.make"
```

## 문제 해결

### IntelliSense가 작동하지 않음 (빨간 밑줄)

1. `c_cpp_properties.json`에서 MCU define 확인
2. Include 경로가 올바른지 확인
3. VSCode 창 새로고침: `Ctrl+Shift+P` → "Reload Window"

### 빌드 실패: "make: not found"

1. STM32CubeIDE가 설치되어 있는지 확인
2. `tasks.json`의 make.exe 경로 확인

### Debug 폴더가 없음

STM32CubeIDE에서 프로젝트를 한 번 빌드하여 Debug 폴더 생성 필요

## 개발 워크플로우

**권장 프로세스:**

1. **프로젝트 생성**: STM32CubeIDE에서 CubeMX로 생성
2. **첫 빌드**: CubeIDE에서 한 번 빌드
3. **VSCode 설정**: 이 템플릿 사용
4. **코딩**: VSCode에서 편집 및 빌드
5. **하드웨어 변경**: CubeIDE에서 `.ioc` 파일 수정 및 코드 재생성
6. **디버깅**: CubeIDE 디버거 사용

## 지원하는 MCU

모든 STM32 시리즈:
- STM32H7 (H743, H723, H750 등)
- STM32F4 (F407, F429, F446 등)
- STM32F1 (F103 등)
- STM32G4, STM32L4, STM32WB 등

## 상세 가이드

더 자세한 설명은 `SETUP_GUIDE.md`를 참조하세요.

## 라이선스

MIT License

## 기여

이슈나 PR은 언제든지 환영합니다!

## 작성자

- GitHub: [@trionking](https://github.com/trionking)
