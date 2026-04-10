# xmake 命令

## 创建项目

```shell
xmake create -l <language> [-t <template>] <project-name>
```

## 配置

```shell
xmake f # xmake config
xmake f --toolchain=clang
```

## 构建

```shell
xmake        # 默认行为等于 xmake build
xmake -r     # xmake --rebuild，重新构建
xmake -vD    # 查看详细构建错误和堆栈
```

## 运行

```shell
xmake run
```

## Unity Build

xmake 支持 Unity Build 模式，可合并源文件加速编译。
