# 【实践记录】给 dcat-admin 增加 NEditor 编辑器, 并集成 135 编辑器

文章编写于 2022年05月03日

## 背景
管理后台需要对相关文章进行管理, 涉及到富文本编辑器.

在此采用基于 [百度 ueditor 编辑器(已停止维护)](http://fex.baidu.com/ueditor/) 二开的 [neditor 编辑器(美观, 大气, 简洁)](https://github.com/notadd/neditor). 并为其集成 [135 编辑器](http://www.135plat.com/135_ueditor_plugin.html). 以达到可以快捷使用 135 编辑器[模板功能](https://www.135editor.com/beautify_editor.html), 并且不失美观的特性.

下方是关于集成的步骤记录.

## 实践

```php
// 135 编辑器配置
// 1. 下载 neditor 编辑器, 解压至项目访问目录
// 2. 下载 135 编辑器, 135EditorDialogPage.html, 保存至 neditor/dialogs/135editor/ 目录
// 3. 下载 135 编辑器, 135editor.js, 保存至 neditor/dialogs/135editor/ 目录
// 4. 编辑 135EditorDialogPage.html 文件内容, 修改 internal.js 引入为正确地址, 如: src="../internal.js"
// 5. 编辑 135editor.js, 内容修改为下方 diff 代码, 使用 dialog 编辑, 关闭 window.open 编辑. 避免在移动端打开 135 编辑器页面后, 无法返回表单页面.

// neditor 配置修改
// 1. 修改 neditor.config.js, 关闭 inputFilter, 避免 135 编辑的文章, 样式被 xss 过滤
// 2. 修改 neditor.config.js, 关闭 outputXssFilter, 避免 135 编辑无法正确获取将要编辑文章, 样式被 xss 过滤
// 3. 保存 edtior.blade.php 到 resources/view/editor.blade.php

// dcat-admin 配置 neditor 编辑器
// dcat-admin 配置 neditor 编辑器
// 1. 保存 NEditor.php 到 app/Admin/Extensions/Form 目录下
// 2. 配置 NEditor::Js 常量, 引入正确的 js 地址
// 3. 配置 NEditor::serverUrl 属性, 修改文件上传地址
// 4. 配置 NEditor::neditorHomeUrl 属性, 修改 neditor 访问地址
// 5. 配置 NEditor::neditorConfigOptions 属性, 修改, 这里可以配置编辑器默认的工具栏
// 6. 在 app/Admin/bootstrap.php 增加 Form::extend('neditor', \App\Admin\Extensions\Form\NEditor::class); 配置, 定义 @editor 资源便恶魔, 全局引入资源
Admin::asset()->alias('@neditor', [
    // 为了方便演示效果，这里直接加载CDN链接，实际开发中可以下载到服务器加载
    'js' => \App\AdminTenant\Extensions\Form\NEditor::JS,
    'css' => \App\AdminTenant\Extensions\Form\NEditor::CSS,
]);
\Dcat\Admin\Admin::requireAssets('@neditor');
Form::extend('neditor', \App\AdminTenant\Extensions\Form\NEditor::class);
```


`diff --color -u 135editorold.js 135editor.js`
```diff
diff --color -u 135editorold.js 135editor.js

--- 135editorold.js     2022-01-06 23:14:21.000000000 +0800
+++ 135editor.js        2022-05-03 22:24:19.944000000 +0800
@@ -1,4 +1,4 @@
-UE.registerUI('135editor',function(editor,uiName){
+UE.registerUI('135editor', function (editor, uiName) {
     // var dialog = new UE.ui.Dialog({
     //     iframeUrl: editor.options.UEDITOR_HOME_URL+'dialogs/135editor/135EditorDialogPage.html',
     //     cssRules:"width:"+ parseInt(document.body.clientWidth*0.9) +"px;height:"+(window.innerHeight -50)+"px;",
@@ -9,36 +9,45 @@
     // dialog.fullscreen = false;
     // dialog.draggable = false;
 
-    var editor135;
-    function onContentFrom135(event) {
-        if (typeof event.data !== 'string') {
-            if(event.data.ready) {
-                editor135.postMessage(editor.getContent(),'*');
-            }
-            return;
-        };
-
-        if(event.data.indexOf('<') !== 0) return;
-
-        editor.setContent(event.data);
-        editor.fireEvent("catchRemoteImage");
-        window.removeEventListener('message', onContentFrom135);
-    }
+    // var editor135;
+
+    // function onContentFrom135(event) {
+    //     if (typeof event.data !== 'string') {
+    //         if (event.data.ready) {
+    //             editor135.postMessage(editor.getContent(), '*');
+    //         }
+    //         return;
+    //     };
+
+    //     if (event.data.indexOf('<') !== 0) return;
+
+    //     editor.setContent(event.data);
+    //     editor.fireEvent("catchRemoteImage");
+    //     window.removeEventListener('message', onContentFrom135);
+    // }
 
     var btn = new UE.ui.Button({
-        name:'btn-dialog-' + uiName,       
-        className:'edui-for-135editor',
-        title:'135编辑器',
-        onclick:function () {
-            // dialog.render();
-            // dialog.open();
+        name: 'btn-dialog-' + uiName,
+        className: 'edui-for-135editor',
+        title: '135编辑器',
+        onclick: function () {
+            var dialog = new UE.ui.Dialog({
+                iframeUrl: editor.options.UEDITOR_HOME_URL+'dialogs/135editor/135EditorDialogPage.html',
+                cssRules:"width:"+ parseInt(document.body.clientWidth*0.9) +"px;height:"+(window.innerHeight -50)+"px;",
+                editor:editor,
+                name:uiName,
+                title:"135编辑器"
+            });
+            dialog.render();
+            dialog.open();
 
             // 由于登录存在跨域问题，请使用如下方式调用135编辑器
-            editor135 = window.open('https://www.135editor.com/simple_editor.html?callback=true&appkey=')
-            
-            window.removeEventListener('message', onContentFrom135);
-            window.addEventListener('message', onContentFrom135, false);
+            // editor135 = window.open(
+            //     'https://www.135editor.com/simple_editor.html?callback=true&appkey=')
+
+            // window.removeEventListener('message', onContentFrom135);
+            // window.addEventListener('message', onContentFrom135, false);
         }
     });
     return btn;
-},undefined);
\ No newline at end of file
+}, undefined);
\ No newline at end of file
```

`135editor.js`
```js
UE.registerUI('135editor', function (editor, uiName) {
    // var dialog = new UE.ui.Dialog({
    //     iframeUrl: editor.options.UEDITOR_HOME_URL+'dialogs/135editor/135EditorDialogPage.html',
    //     cssRules:"width:"+ parseInt(document.body.clientWidth*0.9) +"px;height:"+(window.innerHeight -50)+"px;",
    //     editor:editor,
    //     name:uiName,
    //     title:"135编辑器"
    // });
    // dialog.fullscreen = false;
    // dialog.draggable = false;

    // var editor135;

    // function onContentFrom135(event) {
    //     if (typeof event.data !== 'string') {
    //         if (event.data.ready) {
    //             editor135.postMessage(editor.getContent(), '*');
    //         }
    //         return;
    //     };

    //     if (event.data.indexOf('<') !== 0) return;

    //     editor.setContent(event.data);
    //     editor.fireEvent("catchRemoteImage");
    //     window.removeEventListener('message', onContentFrom135);
    // }

    var btn = new UE.ui.Button({
        name: 'btn-dialog-' + uiName,
        className: 'edui-for-135editor',
        title: '135编辑器',
        onclick: function () {
            var dialog = new UE.ui.Dialog({
                iframeUrl: editor.options.UEDITOR_HOME_URL+'dialogs/135editor/135EditorDialogPage.html',
                cssRules:"width:"+ parseInt(document.body.clientWidth*0.9) +"px;height:"+(window.innerHeight -50)+"px;",
                editor:editor,
                name:uiName,
                title:"135编辑器"
            });
            dialog.render();
            dialog.open();

            // 由于登录存在跨域问题，请使用如下方式调用135编辑器
            // editor135 = window.open(
            //     'https://www.135editor.com/simple_editor.html?callback=true&appkey=')

            // window.removeEventListener('message', onContentFrom135);
            // window.addEventListener('message', onContentFrom135, false);
        }
    });
    return btn;
}, undefined);
```


`neditor.blade.php`
```php
<div class="{{$viewClass['form-group']}}">
    <label class="{{$viewClass['label']}} control-label">{{$label}}</label>
    <div class="{{$viewClass['field']}}">
        @include('admin::form.error')
        <textarea id="{{ $editor_id }}" name="{{ $name}}" placeholder="{{ $placeholder }}" {!! $attributes !!} >{!! $value !!}</textarea>
        @include('admin::form.help-block')
    </div>
</div>

<script init="{!! $selector !!}">
    var options = {{ Js::from($config) }};
    var editor = UE.getEditor('{{ $editor_id }}', options);
</script>

<style>
    .edui-button.edui-for-135editor .edui-button-wrap .edui-button-body .edui-icon{
        background-image: url("//static.135editor.com/img/icons/editor-135-icon.png") !important;
        background-size: 85%;
        background-position: center;
        background-repeat: no-repeat;
    }
</style>
```

`NEditor.php`
```php
<?php

namespace App\AdminTenant\Extensions\Form;

use Illuminate\Support\Arr;

class NEditor extends \Dcat\Admin\Form\Field
{
    const JS = [
        '/neditor/neditor.config.js',
        '/neditor/neditor.all.min.js',
        '/neditor/neditor.service.js',
        '/neditor/i18n/zh-cn/zh-cn.js',
        '/neditor/third-party/browser-md5-file.min.js',

        '/neditor/dialogs/135editor/135editor.js',
    ];

    const CSS = [
        //
    ];
    
    protected static $js = NEditor::JS;

    protected static $css = NEditor::CSS;

    protected $view = 'neditor';

    public static $serverUrl = '/dashboard/files/upload';

    public static $neditorHomeUrl = '/tenancy/assets/neditor/';

    public static $neditorConfigOptions = <<<OPTIONS
{
    "UEDITOR_HOME_URL": "",
    "serverUrl": "",
    "allowDivTransToP": false,
    "inputXssFilter": false,
    "outputXssFilter": false,
    "initialFrameHeight": 320,
    "toolbars": [
        [
            "135editor",
            "fullscreen",
            "source",
            "|",
            "undo",
            "redo",
            "|",
            "bold",
            "italic",
            "underline",
            "fontborder",
            "strikethrough",
            "rowspacingtop",
            "rowspacingbottom",
            "lineheight",
            "removeformat",
            "formatmatch",
            "autotypeset",
            "blockquote",
            "pasteplain",
            "|",
            "forecolor",
            "backcolor",
            "insertorderedlist",
            "insertunorderedlist",
            "selectall",
            "cleardoc",
            "|",
            "superscript",
            "subscript"
        ]
    ]
}
OPTIONS;

    protected $config = [];

    public function setNeditorhomeUrl()
    {
        $this->config('UEDITOR_HOME_URL', static::$neditorHomeUrl);

        return $this;
    }

    public function setServerUrl()
    {
        $this->config('serverUrl', static::$serverUrl);

        return $this;
    }

    protected function config($key = '', $value = '')
    {
        if (!$this->config) {
            $this->config = json_decode(static::$neditorConfigOptions, true) ?? [];
        }

        if ($key) {
            $this->config = Arr::set($this->config, $key, $value);
        }
        
        return $this->config;
    }

    public function render()
    {
        $config = $this->setNeditorhomeUrl()
            ->setServerUrl()
            ->config();
            
        $this->addVariables([
            'editor_id' => 'neditor_'.uniqid(),
            'config' => $config,            
        ]);

        return parent::render();
    }
}
```

## 截图

![neditor&135编辑器集成示例][neditor&135编辑器集成示例]

[neditor&135编辑器集成示例]: http://laravel-workerman.qiniu.iwnweb.com/neditor%26135%E7%BC%96%E8%BE%91%E5%99%A8.png