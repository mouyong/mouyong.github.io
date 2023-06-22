# 【实践记录】在 dcat-admin 表格中, 增加多视频预览与多图片预览

文章编写于 2022年05月03日


## 背景

项目中的相关信息会使用到视频与图片进行关联,用作封面形象展示, 因此需要在列表中查看视频封面与形象封面信息.


## 代码与调用示例

### 1. 代码

`app/Admin/bootstrap.php`
```
\Dcat\Admin\Admin::asset()->alias('@dplayer', [
    'js' => [
        '/js/DPlayer.min.js',
    ],
]);
\Dcat\Admin\Admin::requireAssets('@dplayer');
\Dcat\Admin\Grid\Column::extend('video', function (\Illuminate\Support\Collection $videos, $title = '视频', $btn = '查看视频') {
    // 1. 引入 DPlayer.min.js 文件, 保存到本地可访问路径, 如 /js/DPlayer.min.js
    // @see https://github.com/DIYgod/DPlayer/blob/master/dist/DPlayer.min.js
    // 2. 移除文件内部的 sourcemap 映射
    // 3. 在 dcat-admin 的引导文件 bootstrap.php 中进行插件注册
    // 4. 如果需要 hls 支持, 需要自行根据 dplayer 文档进行 js 资源引入
    // @see https://dplayer.js.org/zh/guide.html#hls\Dcat\Admin\Grid\Column::extend('video', function (\Illuminate\Support\Collection $videos, $title = '视频', $btn = '查看视频') {
    // 1. 引入 DPlayer.min.js 文件, 保存到本地可访问路径, 如 /js/DPlayer.min.js
    // @see https://github.com/DIYgod/DPlayer/blob/master/dist/DPlayer.min.js
    // 2. 移除文件内部的 sourcemap 映射
    // 3. 在 dcat-admin 的引导文件 bootstrap.php 中进行插件注册
    // 4. 如果需要 hls 支持, 需要自行根据 dplayer 文档进行 js 资源引入
    // @see https://dplayer.js.org/zh/guide.html#hls

    if ($videos->isEmpty()) {
        return '无';
    }
    
    return $videos->map(function ($video, $index) use ($title, $btn) {
        $id = 'dplayer_'.$this->getKey().'_'.uniqid();

        \Dcat\Admin\Admin::script(<<<JS
const $id =  new DPlayer({
container: document.getElementById('$id'),
video: {
url: '$video',
},
});

$(".dplayer-video-wrap").addClass('embed-responsive embed-responsive-16by9');
$(".dplayer-video-wrap video").addClass('embed-responsive-item');
JS);

        $modal = \Dcat\Admin\Widgets\Modal::make()
            ->lg()
            ->title(sprintf("$title - %s", $index + 1))
            ->body("<div id='$id' style='' class=''></div>")
            ->button(sprintf("<button class='btn'>$btn - %s</button>", $index + 1));
        
        return $modal->render();
    })->implode('<br />');
});

\Dcat\Admin\Grid\Column::extend('gallery', function (\Illuminate\Support\Collection $images, $width = 100, $height = 100) {
    // 引入 dengje.gallery
    // https://gitee.com/edwinhuish/dcat-extension-gallery
    // composer config repositories.1 vcs https://gitee.com/edwinhuish/dcat-extension-gallery
    // composer require dengje/gallery:dev-master
    // 在扩展中启用插件(dcat-admin 单体项目或 dcat-admin 中心应用)

    if ($images->isEmpty()) {
        return '无';
    }

    $groupId = '_'.$this->getKey().'_gallery_group';

    return \Dcat\Admin\Admin::view('dengje.gallery::gallerys', ['src' => $images, 'width' => $width, 'height' => $height, 'id' => $groupId]);
});
```

### 2. 调用示例

```
$grid->column('video_url', '视频形象')->display(function ($v) {
    return $this->covers->where('type', ImageCover::TYPE_VIDEO)->pluck('url_link');
})->video();
$grid->column('image_url', '照片形象')->display(function ($v) {
    return $this->covers->where('type', ImageCover::TYPE_IMAGE)->pluck('url_link');
})->gallery();
```

## 示例

![列表示例]: [列表示例]
![视频弹窗示例]: [视频弹窗示例]
![多图弹窗示例]: [多图弹窗示例]

[列表示例]: http://laravel-workerman.qiniu.iwnweb.com/%E5%88%97%E8%A1%A8%E7%A4%BA%E4%BE%8B.png
[视频弹窗示例]: http://laravel-workerman.qiniu.iwnweb.com/%E8%A7%86%E9%A2%91%E5%BC%B9%E7%AA%97%E7%A4%BA%E4%BE%8B.png
[多图弹窗示例]: http://laravel-workerman.qiniu.iwnweb.com/%E5%A4%9A%E5%9B%BE%E5%BC%B9%E7%AA%97%E7%A4%BA%E4%BE%8B.png
