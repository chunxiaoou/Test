# **CMAKE**
1. 根目錄建立 CMakeLists.txt

### 指令集
cmake -S . -B build   執行CMake Configure


### 語法介紹
<pre>
file(GLOB SOURCES "src/**/*.cpp" "src/*.cpp")    // 抓取 src資料夾 .cpp檔案至 "SOURCES"/ "HEADER"，添加可執行文件 
</pre>

<pre> 
add_executable(${PROJECT_NAME} ${SOURCES} ${HEADER})    // 輸出"可執行檔"
</pre>

<pre>
target_compile_features(${PROJECT_NAME} PRIVATE cxx_std_20)      // 指定使用語法版本
</pre>

<pre>
add_library(${PROJECT_NAME} (STATIC/ SHARED/ INTERFACE) ${SOURCES} ${HEADERS})

// STATIC：產生 .a 或 .lib
// SHARED：產生 .so 或 .dll
// INTERFACE：只定義介面，沒有實體檔案（常用於 header-only 函式庫）
// 在 Windows 上構建 = .lib 靜態庫 
// 交叉編譯到 Android = .a 靜態庫 
</pre>

<pre>
add_subdirectory(src)    // src為資料夾名稱 (引用 ./src/CMakeLists.txt)
</pre>

<pre>
find_package(Boost CONFIG REQUIRED ONLY_CMAKE_FIND_ROOT_PATH)

// 用來查找並載入外部庫的信息，它會：
// 尋找已安裝的庫或包
// 載入該包提供的設置和變量
// 有時會定義導入的目標(imported targets)
// Ex: 指定路徑尋找 Boost 庫的配置文件，並載入相關信息（包括頭文件路徑、庫文件位置等）。
</pre>

<pre>
target_include_directories(${PROJECT_NAME} PUBLIC ${SASLib_INCLUDE_DIRS})    
// 此命令用於指定目標（如可執行文件或庫）需要的頭文件包含路徑

target_link_directories(${PROJECT_NAME} PRIVATE ${SASLib_LIBRARY_DIRS})    
//用於指定查找庫文件時要搜索的目錄路徑

target_link_libraries(${PROJECT_NAME} PRIVATE ${SASLib_LIBRARIES})      
//此命令指定目標需要鏈接的庫
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
      "name": "default",                                  // 此 preset 的 ID 名稱
      "displayName": "Default Build",  
      "description": "Configure with default settings",   // 顯示用的文字敘述
      "generator": "Ninja",                               // CMake 用來產生 build 系統的工具（如 Ninja, MinGW Makefiles）
      "binaryDir": "${sourceDir}/build/default",          // CMake 生成檔案的資料夾路徑
      "cacheVariables": {                                 // -DVAR=VAL	設定變數 VAR 的值為 VAL，會寫入 CMakeCache.txt | cmake -DMY_FEATURE=ON (宣告 MY_FEATURE = ON)
        "CMAKE_BUILD_TYPE": "Debug",
        "CMAKE_EXPORT_COMPILE_COMMANDS": "ON",
        "CMAKE_BUILD_TYPE": "Debug",
        "CMAKE_C_COMPILER": "$env{ANDROID_NDK}/toolchains/llvm/prebuilt/windows-x86_64/bin/clang",
        "CMAKE_CXX_COMPILER": "$env{ANDROID_NDK}/toolchains/llvm/prebuilt/windows-x86_64/bin/clang++",
        "CMAKE_LINKER": "$env{ANDROID_NDK}/toolchains/llvm/prebuilt/windows-x86_64/bin/ld",
        "ANDROID_NDK": "$env{ANDROID_NDK}",
        "ANDROID_ABI": "arm64-v8a",
        "ANDROID_PLATFORM": "android-22"
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
      "configurePreset": "default"      // 建構時用哪個 configure preset（必填）
    },
    {
      "name": "build-release",
      "configurePreset": "release"      // 建構時用哪個 configure preset（必填）
    }
  ],
  "testPresets": [
    {
      "name": "test-default",
      "configurePreset": "default"      // 建構時用哪個 configure preset（必填）
    }
  ]
}
```

Visual Studio (MSBuild)	.sln, .vcxproj, .vcxproj.filters
MinGW Makefiles	Makefile, .o、.a、.exe
Ninja	build.ninja, .o、.a、.so
