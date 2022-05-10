# Windows10右键创建MarkDown文件

本文以 **typaro** 为例，其它软件请自行修改代码中的关键词

1. 首先新建一个txt文件，名称随意
2. 然后输入以下代码：

```reg
Windows Registry Editor Version 5.00

[HKEY_CLASSES_ROOT\.md]
@="Typora.md"
"Content Type"="text/markdown"
"PerceivedType"="text"

[HKEY_CLASSES_ROOT\.md\ShellNew]
"NullFile"=""
```

1. 另存为——选择**所有格式**——把文件名后缀改为**“reg”**
2. 双击保存后的文件
3. 点击确定
4. 此时右键如果没有，则重启