title: sublime文件tab自动转空格
date: 2016-01-30 22:11:22
categories: 工具
tags:
  - sublime
---

由于用Python开发项目时，开发人员的环境不同，造成了文件中存在tab和空格混用，从而导致解析失败。因此使用sublime，使保存时统一格式为空格。

1、打开sublime的Preferences -> Browse Packages目录
2、在目录下建立文件夹ExpandTabsOnSave
3、新建文件ExpandTabsOnSave.py

	import sublime, sublime_plugin, os

	class ExpandTabsOnSave(sublime_plugin.EventListener):
	    # Run ST's 'expand_tabs' command when saving a file
	    def on_pre_save(self, view):
	        if view.settings().get('expand_tabs_on_save') == 1:
	            view.window().run_command('expand_tabs')

4、应用在项目中
编辑项目文件.sublime-project添加：

	"settings": {
	    "expand_tabs_on_save": true
	}

5、整个编辑器应用
打开Preferences -> Settings - User添加：

	"settings": {
	    "expand_tabs_on_save": true
	}