# 【实践记录】给 Relation 添加 getMorphAlias 函数，通过 model 类名获取 relation 映射值

文章编写于 2022年05月02日

## Laravel discussions
https://github.com/laravel/framework/discussions/42215

## 背景
Some times, we need to find the morphed alias by model class.

hope to use `Relation::getMorphAlias(\App\Models\Xxx::class)` get it.

## 代码

```php
\Illuminate\Database\Eloquent\Relations\Relation::macro('getMorphedAlias', function ($value) {
    $morphMap = \Illuminate\Database\Eloquent\Relations\Relation::morphMap();

    $flipMorphed = array_flip($morphMap);

    $alias = $flipMorphed[$value] ?? null;

    return $alias;
});
```

## 示例

```php
$id = 1;
$class = \App\Models\Organization::class;
$classAlias = Relation::getMorphedAlias($class);

ImageCover::create([
    'coverable_id' => $id,
    'coverable_type' => $classAlias,
]);
```

`AppServiceProvider.php`
```php
    public function register()
    {
       \Illuminate\Database\Eloquent\Relations\Relation::macro('getMorphAlias', function ($value) {
            if (is_object($value)) {
                $value = get_class($value);
            }

            $morphMap = \Illuminate\Database\Eloquent\Relations\Relation::morphMap();

            $flipMorphed = array_flip($morphMap);

            $alias = $flipMorphed[$value] ?? throw new \RuntimeException("Please add class $value to \Illuminate\Database\Eloquent\Relations\Relation::enforceMorphMap");

            return $alias;
        });

        // ImageCover
        \Illuminate\Database\Eloquent\Relations\Relation::enforceMorphMap([
            \App\Models\ImageCover::CONVERABLE_ORGANIZATION => \App\Models\Organization::class,
        ]);
    }
```