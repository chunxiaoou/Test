# **CMAKE**
1. 根目錄建立 CMakeLists.txt

### 指令集
cmake -S . -B build   執行CMake Configure


### 語法介紹
```
file(GLOB SOURCES "src/**/*.cpp" "src/*.cpp") </br> 
add_executable(${PROJECT_NAME} ${SOURCES})
```
<pre> 
抓取 src資料夾 .cpp檔案至 "SOURCES"/ "HEADER"，添加可執行文件 
</pre>

```
target_compile_features(${PROJECT_NAME} PRIVATE cxx_std_20)
```
<pre> 
指定使用語法版本 
</pre>

```
add_library(${PROJECT_NAME} (STATIC/ SHARED/ INTERFACE) ${SOURCES} ${HEADERS})
```
<pre>
STATIC：產生 .a 或 .lib
SHARED：產生 .so 或 .dll
INTERFACE：只定義介面，沒有實體檔案（常用於 header-only 函式庫）

在 Windows 上構建 = .lib 靜態庫 
交叉編譯到 Android = .a 靜態庫
</pre>


### CMakePresets.json & CMakeUserPresets.json
功能: 執行 CMake Configure時 CMake Tools擴充套件會自動偵測，此檔案通常放在你的專案根目錄，用來預先定義常見的 build 參數、build folder、編譯器選項 等等。

your-project/ </br>
├── CMakeLists.txt  </br>
├── CMakePresets.json        ← 專案提供的預設組態（團隊共用） </br>
└── CMakeUserPresets.json    ← 你自己定義的個人 preset（建議不要加入 Git（.gitignore）） </br>


| 指令 | 功能 |
| :--: | :--: |
| cmake --preset (名稱 Ex: default) | 配置（configure） |
| cmake --preset (名稱 Ex: default) | 編譯（build） |
| ctest --preset (名稱 Ex: test-default) | 測試（ctest） |

```
{
  "version": 3,
  "cmakeMinimumRequired": {
    "major": 3,
    "minor": 23,
    "patch": 0
  },
  "configurePresets": [
    {
      "name": "default",
      "displayName": "Default Build",
      "description": "Configure with default settings",
      "generator": "Ninja",
      "binaryDir": "${sourceDir}/build/default",
      "cacheVariables": {
        "CMAKE_BUILD_TYPE": "Debug",
        "CMAKE_EXPORT_COMPILE_COMMANDS": "ON"
      }
    },
    {
      "name": "release",
      "displayName": "Release Build",
      "generator": "Ninja",
      "binaryDir": "${sourceDir}/build/release",
      "cacheVariables": {
        "CMAKE_BUILD_TYPE": "Release"
      }
    }
  ],
  "buildPresets": [
    {
      "name": "build-default",
      "configurePreset": "default"
    },
    {
      "name": "build-release",
      "configurePreset": "release"
    }
  ],
  "testPresets": [
    {
      "name": "test-default",
      "configurePreset": "default"
    }
  ]
}
```

| 欄位 | 功能 | 舉例 |
| :--: | :-- | :-- |
| name  | 此 preset 的 ID 名稱 |
| inherits | 從其他 preset 繼承欄位（可重用設定） |
| description | 顯示用的文字敘述 |
| binaryDir | CMake 生成檔案的資料夾路徑 |
| generator | CMake 用來產生 build 系統的工具（如 Ninja, MinGW Makefiles） |
| toolchainFile | 	指定交叉編譯所用的工具鏈 |
| cacheVariables  | -DVAR=VAL	設定變數 VAR 的值為 VAL，會寫入 CMakeCache.txt | cmake -DMY_FEATURE=ON (宣告 MY_FEATURE = ON) |
| configurePreset | 建構時用哪個 configure preset（必填）|
| configuration | Debug / Release 等編譯型態（multi-config generator 專用）|



Visual Studio (MSBuild)	.sln, .vcxproj, .vcxproj.filters
MinGW Makefiles	Makefile, .o、.a、.exe
Ninja	build.ninja, .o、.a、.so
