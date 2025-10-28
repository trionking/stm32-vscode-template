# STM32 VSCode 빌드 환경 상세 설정 가이드

## 목차
1. [개요](#개요)
2. [필수 도구](#필수-도구)
3. [설정 파일 설명](#설정-파일-설명)
4. [단계별 설정 방법](#단계별-설정-방법)
5. [MCU별 설정 변경](#mcu별-설정-변경)
6. [문제 해결](#문제-해결)
7. [팁과 요령](#팁과-요령)

---

## 개요

이 가이드는 STM32CubeIDE에서 생성한 프로젝트를 VSCode에서 빌드할 수 있도록 설정하는 방법을 설명합니다.

### 왜 VSCode를 사용하나?

- **빠른 편집**: 가벼운 에디터로 빠른 코드 편집
- **빠른 빌드**: 병렬 빌드 지원
- **익숙한 환경**: 다른 프로젝트와 동일한 에디터 사용
- **유연성**: 확장 기능으로 자유로운 커스터마이징

### 이중 환경 전략

이 설정은 다음 전략을 사용합니다:

| 작업 | STM32CubeIDE | VSCode |
|------|--------------|--------|
| 프로젝트 생성 | ✅ | ❌ |
| CubeMX 설정 | ✅ | ❌ |
| 코드 편집 | ⭕ | ✅ |
| 빌드 | ⭕ | ✅ |
| 플래시 | ⭕ | ✅ |
| 디버깅 | ✅ | ⚠️ |

**핵심**: 같은 `Debug/Release` 폴더를 공유하여 양쪽에서 빌드 가능

---

## 필수 도구

### 1. STM32CubeIDE

**다운로드**: https://www.st.com/en/development-tools/stm32cubeide.html

**설치 경로 예시**: `C:/ST/STM32CubeIDE_1.19.0`

**포함 내용**:
- make.exe (빌드 도구)
- Eclipse 기반 IDE
- CubeMX (하드웨어 설정 도구)

### 2. STM32CubeCLT (Command Line Tools)

**다운로드**: https://www.st.com/en/development-tools/stm32cubeclt.html

**설치 경로 예시**: `C:/ST/STM32CubeCLT_1.19.0`

**포함 내용**:
- GNU ARM Toolchain (arm-none-eabi-gcc)
- STM32CubeProgrammer (플래시 도구)
- CMake, Ninja

### 3. VSCode 확장

필수 확장:
- **C/C++** (Microsoft) - IntelliSense 지원

선택 확장:
- **Cortex-Debug** - 디버깅 지원
- **STM32 VS Code Extension** - ST 공식 확장

### 4. Windows 환경 변수 설정 (권장)

**환경 변수를 설정하면 다른 환경에서도 쉽게 사용할 수 있습니다.**

#### 설정 방법

1. Windows 검색에서 "환경 변수" 검색
2. "시스템 환경 변수 편집" 실행
3. "환경 변수" 버튼 클릭
4. "사용자 변수" 또는 "시스템 변수"에서 "새로 만들기" 클릭
5. 다음 두 개의 환경 변수 추가:

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

#### 장점

- ✅ 버전이 다른 환경에서도 환경 변수만 수정하면 됨
- ✅ `.vscode` 폴더 설정 파일은 수정할 필요가 없음
- ✅ 팀원들과 공유 시 각자의 환경에 맞게 쉽게 설정 가능
- ✅ GitHub에서 클론 후 바로 사용 가능

---

## 설정 파일 설명

VSCode 설정은 `.vscode` 폴더 내 3개 파일로 구성됩니다.

### 1. tasks.json

**역할**: 빌드, 클린, 플래시 작업 정의

**주요 설정**:
```json
{
    "command": "경로/make.exe",    // make 실행 파일
    "options": {
        "cwd": "${workspaceFolder}/Debug",  // 작업 디렉토리
        "env": {
            "PATH": "컴파일러 경로;..."  // 환경 변수
        }
    }
}
```

**수정 필요 항목**:
- make.exe 경로 (버전에 따라)
- 프로젝트 이름 (Flash 작업의 ELF 파일명)

### 2. c_cpp_properties.json

**역할**: IntelliSense 설정 (자동완성, 오류 표시)

**주요 설정**:
```json
{
    "compilerPath": "arm-none-eabi-gcc.exe 경로",
    "includePath": [
        "헤더 파일 경로들..."
    ],
    "defines": [
        "MCU 타입 define"
    ]
}
```

**수정 필요 항목**:
- MCU define (예: STM32H743xx)
- Include 경로 (MCU 시리즈에 따라)

### 3. settings.json

**역할**: VSCode 에디터 설정

**주요 설정**:
- 파일 연결 (.c, .h → C 언어)
- 파일 탐색기 필터
- 검색 제외 폴더

**수정 불필요**: 대부분 프로젝트에서 그대로 사용 가능

---

## 단계별 설정 방법

### 단계 1: CubeIDE에서 프로젝트 생성

1. STM32CubeIDE 실행
2. `File > New > STM32 Project`
3. MCU 또는 보드 선택
4. 프로젝트 이름 입력 (예: `my_stm32_project`)
5. CubeMX에서 핀 설정, 클럭 설정
6. 코드 생성

### 단계 2: 첫 빌드

**중요**: CubeIDE에서 반드시 한 번 빌드해야 합니다!

1. CubeIDE에서 프로젝트 선택
2. `Project > Build Project` 또는 `Ctrl+B`
3. `Debug` 폴더와 `makefile` 생성 확인

### 단계 3: .vscode 폴더 복사

```bash
# 이 템플릿 저장소에서
cp -r .vscode YOUR_PROJECT_FOLDER/

# 또는 수동으로 3개 파일 생성
```

### 단계 4: tasks.json 수정

**A. make.exe 경로 확인**

```bash
# Windows
dir "C:\ST\STM32CubeIDE_*\STM32CubeIDE\plugins" | findstr "make"
```

찾은 경로를 `tasks.json`의 `command`에 입력

**B. 프로젝트 이름 변경**

Flash 작업의 ELF 파일명:
```json
"${workspaceFolder}/Debug/YOUR_PROJECT_NAME.elf"
```

프로젝트 이름 확인:
```bash
grep "BUILD_ARTIFACT_NAME" Debug/makefile
# 출력: BUILD_ARTIFACT_NAME := my_stm32_project
```

### 단계 5: c_cpp_properties.json 수정

**A. MCU define 변경**

`.cproject` 파일 열기 → `definedsymbols` 검색:
```xml
<listOptionValue builtIn="false" value="STM32H743xx"/>
```

또는 `Debug/subdir.mk` 확인:
```bash
grep "STM32" Debug/subdir.mk | grep "\-D"
# 출력: -DSTM32H743xx
```

`c_cpp_properties.json`에 입력:
```json
"defines": [
    "DEBUG",
    "USE_HAL_DRIVER",
    "STM32H743xx",  // ← 여기
    ...
]
```

**B. Include 경로 확인**

대부분의 경우 템플릿 그대로 사용 가능. 단, MCU 시리즈가 다르면 수정:

- STM32H7: `STM32H7xx_HAL_Driver`
- STM32F4: `STM32F4xx_HAL_Driver`
- STM32F1: `STM32F1xx_HAL_Driver`

### 단계 6: VSCode에서 테스트

1. VSCode로 프로젝트 폴더 열기
2. `Ctrl+Shift+B` 눌러서 빌드
3. 터미널에서 빌드 진행 상황 확인
4. 성공 시 `Debug/프로젝트명.elf` 생성됨

---

## MCU별 설정 변경

### STM32H7 시리즈 (예: H743, H723)

**c_cpp_properties.json:**
```json
"includePath": [
    "${workspaceFolder}/Core/Inc",
    "${workspaceFolder}/Drivers/STM32H7xx_HAL_Driver/Inc",
    "${workspaceFolder}/Drivers/STM32H7xx_HAL_Driver/Inc/Legacy",
    "${workspaceFolder}/Drivers/CMSIS/Device/ST/STM32H7xx/Include",
    "${workspaceFolder}/Drivers/CMSIS/Include"
],
"defines": [
    "DEBUG",
    "USE_HAL_DRIVER",
    "STM32H743xx",  // 또는 STM32H723xx
    "USE_PWR_LDO_SUPPLY"
]
```

### STM32F4 시리즈 (예: F407, F429)

**c_cpp_properties.json:**
```json
"includePath": [
    "${workspaceFolder}/Core/Inc",
    "${workspaceFolder}/Drivers/STM32F4xx_HAL_Driver/Inc",
    "${workspaceFolder}/Drivers/STM32F4xx_HAL_Driver/Inc/Legacy",
    "${workspaceFolder}/Drivers/CMSIS/Device/ST/STM32F4xx/Include",
    "${workspaceFolder}/Drivers/CMSIS/Include"
],
"defines": [
    "DEBUG",
    "USE_HAL_DRIVER",
    "STM32F407xx"  // 또는 STM32F429xx
]
```

### STM32F1 시리즈 (예: F103)

**c_cpp_properties.json:**
```json
"includePath": [
    "${workspaceFolder}/Core/Inc",
    "${workspaceFolder}/Drivers/STM32F1xx_HAL_Driver/Inc",
    "${workspaceFolder}/Drivers/STM32F1xx_HAL_Driver/Inc/Legacy",
    "${workspaceFolder}/Drivers/CMSIS/Device/ST/STM32F1xx/Include",
    "${workspaceFolder}/Drivers/CMSIS/Include"
],
"defines": [
    "DEBUG",
    "USE_HAL_DRIVER",
    "STM32F103xB"  // 메모리 크기에 따라 xB, x8, xE
]
```

### MCU Define 규칙

| MCU | Define |
|-----|--------|
| STM32H743VI | STM32H743xx |
| STM32H723ZG | STM32H723xx |
| STM32F407VG | STM32F407xx |
| STM32F103C8 | STM32F103xB |
| STM32F103RB | STM32F103xB |
| STM32F103RC | STM32F103xE |

---

## 문제 해결

### 1. "make: not found" 오류

**원인**: make.exe 경로가 잘못됨

**해결**:
1. STM32CubeIDE 설치 확인
2. `tasks.json`의 경로 재확인
3. 버전 번호 확인 (예: `1.19.0` → `1.16.0`)

### 2. 빨간 밑줄 (IntelliSense 오류)

**원인**: MCU define 또는 include 경로가 잘못됨

**해결**:
1. `c_cpp_properties.json`에서 MCU define 확인
2. Include 경로가 실제 폴더와 일치하는지 확인
3. VSCode 창 새로고침: `Ctrl+Shift+P` → "Reload Window"

### 3. 빌드는 되지만 IntelliSense 작동 안 함

**원인**: C/C++ 확장이 설치되지 않았거나 설정이 잘못됨

**해결**:
1. `Ctrl+Shift+X` → "C/C++" 검색 및 설치
2. `c_cpp_properties.json` 파일 존재 확인
3. 컴파일러 경로가 올바른지 확인

### 4. Debug 폴더가 없음

**원인**: CubeIDE에서 빌드를 하지 않음

**해결**:
STM32CubeIDE에서 프로젝트를 한 번 빌드하여 Debug 폴더 생성

### 5. 링커 오류

**원인**: CubeMX 재생성 후 설정 변경

**해결**:
1. `Terminal > Run Task > Clean (Debug)`
2. `Terminal > Run Task > Build (Debug)`
3. USER CODE 섹션에 문법 오류가 없는지 확인

---

## 팁과 요령

### 1. 빠른 빌드 단축키

VSCode에서 `Ctrl+Shift+B` 한 번에 빌드

### 2. 여러 프로젝트 관리

VSCode Workspace 기능 사용:
```json
{
    "folders": [
        {"path": "project1"},
        {"path": "project2"}
    ]
}
```

### 3. CubeMX 설정 변경 시

1. CubeIDE에서 `.ioc` 파일 열기
2. 변경 사항 적용
3. 코드 생성
4. VSCode로 돌아와서 바로 빌드 가능 (설정 변경 불필요!)

### 4. 빌드 속도 향상

`tasks.json`에서 병렬 빌드 수 조정:
```json
"args": ["-j8", "all"]  // 8개 코어 사용
```

### 5. 플래시 후 자동 리셋

`tasks.json`의 Flash 작업에서 `-rst` 옵션 확인:
```json
"args": [..., "-rst"]
```

### 6. Release 빌드

```json
"options": {
    "cwd": "${workspaceFolder}/Release"  // Debug → Release
}
```

### 7. Git과 함께 사용

`.gitignore`에 추가:
```
Debug/
Release/
*.elf
*.map
*.list
```

단, `.vscode` 폴더는 커밋하여 팀원과 공유!

---

## 추가 자료

- [STM32CubeIDE 문서](https://www.st.com/en/development-tools/stm32cubeide.html)
- [STM32CubeCLT 다운로드](https://www.st.com/en/development-tools/stm32cubeclt.html)
- [VSCode C/C++ 문서](https://code.visualstudio.com/docs/languages/cpp)

---

**문서 버전**: 1.0
**최종 수정**: 2025-10-28
**작성자**: [@trionking](https://github.com/trionking)
