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

- **STM32CubeIDE** (예: `C:/ST/STM32CubeIDE_1.19.0`)
- **STM32CubeCLT** (예: `C:/ST/STM32CubeCLT_1.19.0`)

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

설치된 STM32CubeIDE 또는 STM32CubeCLT 버전이 다르면 `tasks.json`과 `c_cpp_properties.json`에서 경로를 수정하세요.

**make.exe 경로 찾기:**
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
