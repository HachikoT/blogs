- [include guard](#include-guard)
- [参考资料](#参考资料)

# include guard

vscode提供了用户代码片段功能，可以在全局用户代码片段中添加下面的模板。

```json
	"C C++ include guard": {
		"scope": "c,cpp",
		"prefix": "include-once",
		"description": "Add include guard",
		"body": [
			"#ifndef _${RELATIVE_FILEPATH/([^\\.\\\\\\/]+)([\\.\\\\\\/]?)/${1:/upcase}_/g}",
			"#define _${RELATIVE_FILEPATH/([^\\.\\\\\\/]+)([\\.\\\\\\/]?)/${1:/upcase}_/g}",
			"",
			"$0",
			"",
			"#endif // _${RELATIVE_FILEPATH/([^\\.\\\\\\/]+)([\\.\\\\\\/]?)/${1:/upcase}_/g}"
		]
	}
``` 

这里`RELATIVE_FILEPATH`的含义是相对于当前工作区或目录的文件路径。比如`libs/string.h`。

后面的替换语法`([^\\.\\\\\\/]+)([\\.\\\\\\/]?)/${1:/upcase}_/g`是对路径进行匹配处理。匹配分为组1和组2，组1就是所有不包含`.`，`/`，`\`的字符串，组2就是所有包含的字符串。所以匹配结果就是`libs/`和`string.`和`h`。然后对组1进行大写转换，丢弃组2换为`_`字符。所以最终结果就是`_LIBS_STRING_H_`。

# 参考资料

- [vscode进阶：运用代码片段提高效率](https://zhuanlan.zhihu.com/p/357377511)
