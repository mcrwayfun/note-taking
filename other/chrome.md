# chrome使用小技巧

* [chrome插件推荐](#1-chrome插件推荐)
* [彻底禁用Chrome的“请停用以开发者模式运行的扩展程序”提示](#2-彻底禁用Chrome的“请停用以开发者模式运行的扩展程序”提示)

## 1. chrome插件推荐
1. LastPass：密码管理工具
2. Grammarly：英语书写纠错
3. Raindrop.io New Tab：书签管理工具
4. 翻译侠：支持划词翻译


## 2. 彻底禁用Chrome的“请停用以开发者模式运行的扩展程序”提示

下载工具：https://x64dbg.com/

**执行操作前，一定要先备份chrome.dll!!!**

(a) **双击使用x32dbg**

(b) **将chrome版本号文件夹下的 chrome.dll 拖至主窗口，然后连续多次点击运行到用户代码按钮，直至窗口标题处的模块变成 chrome.dll**

![](https://i.imgur.com/KlR5ZKy.png) 

(c) **然后在主面板右键依次选择搜索 -> 当前模块 -> 字符串**

![](https://i.imgur.com/YcUWTx7.png)

(d) **然后会打开一个搜索界面，等待进度条加载完毕，搜索ExtensionDeveloperModeWarning**

![](https://i.imgur.com/gWRBr7a.png)

(e) **会搜到2条结果，双击第一个，跳转到反汇编界面，往上翻一点，找到cmp eax,2（也有可能是cmp eax,3)**

![](https://i.imgur.com/Ndb8reK.png)

(f) **双击打开编辑页面，修改成cmp eax,9，然后点击确定，注意只需要点击一次确定即可，点击确定后它还是会继续弹出其它行的编辑界面，此时直接关闭对话框即可**

![](https://i.imgur.com/4o90XPm.png)

(g) **修改完之后Ctrl+P导出修改过的dll文件（点击修补文件按钮就是导出dll文件）**

![](https://i.imgur.com/L15dyLl.png)
