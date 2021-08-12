# win10右键新建md文件

---

1.  使用下述文本创建.reg文件

    ```
    Windows Registry Editor Version 5.00
    [HKEY_CLASSES_ROOT\.md]
    @="Typora.exe"
    [HKEY_CLASSES_ROOT\.md\ShellNew]
    "NullFile"=""
    [HKEY_CLASSES_ROOT\Typora.exe]
    @="Markdown"
    ```

2.  双击执行reg文件

