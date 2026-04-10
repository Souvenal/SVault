# text-template

Go 标准库 `text/template` 的 `quote` 函数用法。

---

ref: https://pkg.go.dev/text/template

## quote 函数

```go
email = {{ .email | quote }}
```

`quote` 是内置的模版函数，对字符串进行引号包裹 + 特殊字符转义。

> 什么时候需要用 quote？
> 输出内容会被 shell 或其他工具解析时，用 quote 防止注入和转义问题。
