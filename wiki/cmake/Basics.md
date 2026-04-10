# CMake Basics

## 编译器

通过环境变量指定：

```bash
export CC=gcc
export CXX=g++
```

CMake 会读取 `CC`、`CXX` 环境变量。

## 构建类型

```bash
cmake -B build -S . -DCMAKE_BUILD_TYPE=DEBUG
```

常用选项：`DEBUG`、`Release`、`RelWithDebInfo`、`MinSizeRel`

## macOS 配置

```cmake
if (APPLE)
  set(CMAKE_OSX_DEPLOYMENT_TARGET "15.0" CACHE STRING "Minimum OS X deployment version" FORCE)
  set(CMAKE_MACOSX_RPATH ON)
endif()
```

## 生成器

### 单配置生成器 (Single-Config)

Unix Makefiles、Ninja（普通版）：
- 配置时必须指定 `CMAKE_BUILD_TYPE`
- 构建时无法改变配置类型

```bash
# Configure
cmake -B build -S . -DCMAKE_BUILD_TYPE=Debug

# Build：--config 在这里无效
cmake --build build
```

### 多配置生成器 (Multi-Config)

Visual Studio、Xcode、Ninja Multi-Config：
- 配置时不设置 `CMAKE_BUILD_TYPE`（设置了会被忽略）
- 构建时通过 `--config` 选择配置

```bash
# Configure：同时生成 Debug 和 Release 配置
cmake -B build -S . -G "Visual Studio 17 2022"

# Build
cmake --build build --config Debug
cmake --build build --config Release
```
