# Windows rc 资源文件

Windows 资源文件（.rc）中 VERSIONINFO 资源类型用于存储可执行文件或 DLL 的版本元数据。

---

## 资源定义结构

```rc
VS_VERSION_INFO VERSIONINFO
    FILEVERSION     1, 0, 0, 0
    PRODUCTVERSION  1, 0, 0, 0
    FILEFLAGSMASK   VS_FFI_FILEFLAGSMASK
    FILEFLAGS       0
    FILEOS           VOS__WINDOWS32
    FILETYPE         VFT_APP
    FILESUBTYPE      0
    BEGIN
        BLOCK "StringFileInfo"
        BEGIN
            BLOCK "040904B0"
            BEGIN
                VALUE "CompanyName",     "Your Company\0"
                VALUE "FileDescription", "Your Application\0"
                VALUE "FileVersion",     "1.0.0.0\0"
                VALUE "ProductName",     "Your Product\0"
                VALUE "ProductVersion",  "1.0.0.0\0"
            END
        END
        BLOCK "VarFileInfo"
        BEGIN
            VALUE "Translation", 0x0409, 0x04B0
        END
    END
```

## 关键字段

| 字段 | 用途 |
|------|------|
| FILEVERSION | 文件版本号 |
| PRODUCTVERSION | 产品版本号 |
| CompanyName | 公司名称 |
| FileDescription | 文件描述 |
| FileVersion / ProductVersion | 版本号字符串 |

## API 调用

程序可通过 `GetFileVersionInfo()` 和 `VerQueryValue()` API 读取版本信息，常用于：

- 文件属性页右键查看详情
- 安装程序检测版本
- 自动更新逻辑

---

## 相关

- [[Windows-Dev/_index|Windows-Dev]] — Windows 平台开发笔记索引
