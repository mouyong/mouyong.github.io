# 【代码片段】dcat-saas 135 编辑器，抓取远程图片保存到本地

编写于 2022年05月30日


## dcat-neditor 配置

```php
\Laradocs\DcatNeditor\Form\Neditor::$neditorConfigOptions = <<<OPTIONS
 {
     "UEDITOR_HOME_URL": "",
     "serverUrl": "",
     "allowDivTransToP": false,
     "xssFilterRules": false,
     "inputXssFilter": false,
     "outputXssFilter": false,
     "initialFrameHeight": 320,
     "catchRemoteImageEnable": true,
     "catcherFieldName": "images",
     "catcherPathFormat": "",
     "catcherUrlPrefix": "",
     "catcherMaxSize": 204800,
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
```


## 路由

```php
Route::middleware([
    'tenant',
    'api',
    // PreventAccessFromCentralDomains::class,
])->group(function () {
    Route::any('files/upload', [\App\AdminTenant\Controllers\FileController::class, 'neditorUpload']);
});
```


## 控制器

```php
public function catcherRemoteImage(string $image)
{
    $response = \Illuminate\Support\Facades\Http::get($image);
    $fileContent = $response->body();

    $type = str_contains($response->header('Content-Type'), 'image') ? 'image' : 'file';

    $newName = basename($image);
    $urlInfo = parse_url($image);
    
    $dir = sprintf('public/neditor/135/%s', config('admin-tenant.upload.directory.'.$type));
    $dir = sprintf('%s/%s', trim($dir, '/'), trim(dirname($urlInfo['path']), '/'));
    $filepath = sprintf('%s/%s', trim($dir, '/'), trim($newName, '/'));

    $result = $this->disk('local')->put($filepath, $fileContent);

    $filepath = str_replace(['public/'], '', $filepath);

    return [
        'source' => $image,
        'url' => $result ? \URL::tenantFile($filepath) : $image,
        'state' => $result ? 'SUCCESS' : 'FAIL',
    ];
}

public function neditorUpload()
{
    $data = \request()->all();

    if (!$images = \request('images')) {
        \response()->json(json_decode('{
            "list": [
                {
                    "source": "",
                    "url": "",
                    "state": "FAIL"
                }
            ],
            "state": "FAIL"
        }', true));
    }

    $list = [];
    foreach ($images as $image) {
        $item['source'] = $image;
        $item['url'] = $image;
        $item['state'] = 'SUCCESS'; // SUCCESS, FAIL
        $item = $this->catcherRemoteImage($image);

        $list[] = $item;
    }

    $data = [
        'list' => $list,
        'state' => 'SUCCESS', // SUCCESS, FAIL
    ];

    // {
    //     "list": [
    //         {
    //             "source": "", // 原链接
    //             "url": "", // 新链接
    //             "state": "SUCCESS"
    //         }
    //     ],
    //     "state": "SUCCESS" // SUCCESS、FAIL
    // }
    return \response()->json($data);
}
```