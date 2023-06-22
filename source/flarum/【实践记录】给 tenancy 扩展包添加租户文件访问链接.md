# 【实践记录】给 tenancy 扩展包添加租户文件访问链接

文章编写于 2022年05月02日

issue: https://github.com/archtechx/tenancy/pull/689#issuecomment-1114640522

在 `AppServiceProvider.php` 中，给 `URL` 添加 `tenantFile` 函数。并将 `storage/tenant-xxx` => `public/public-xxx` 的租户文件映射关系添加到 `config/storage.php` 中。

`config/tenancy.php -> filesystem`
```php
/*
 * Use this to support Storage url method on local driver disks.
 * You should create a symbolic link which points to the public directory using command: artisan tenants:link
 * Then you can use tenant aware Storage url: Storage::disk('public')->url('file.jpg')
 */
'url_override' => [
    // The array key is local disk (must exist in root_override) and value is public directory (%tenant_id% will be replaced with actual tenant id).
    'public' => 'public-%tenant_id%',
],
```

`AppServiceProvider.php`
```php
\Illuminate\Support\Facades\URL::macro('tenantFile', function(?string $file = '') {    
    if (is_null($file)) {
        return null;
    }

    // file is the url information, which is returned directly. No tenant domain name splicing.
    if (str_contains($file, '://')) {
        return $file;
    }
    
    $prefix = str_replace(
        '%tenant_id%', 
        tenant()->getKey(), 
        config('tenancy.filesystem.url_override.public', 'public-%tenant_id%')
    );

    return url($prefix.'/'.$file);
});
```

`TenancyServiceProvider.php`
```php
Events\TenantCreated::class => [
    JobPipeline::make([
        Jobs\CreateDatabase::class,
        Jobs\MigrateDatabase::class,
        Jobs\SeedDatabase::class,

        // Your own jobs to prepare the tenant.
        // Provision API keys, create S3 buckets, anything you want!

    ])->send(function (Events\TenantCreated $event) {
        $tenant = $event->tenant;
        $target = base_path(sprintf("storage/%s%s/app/public", 
            config('tenancy.filesystem.suffix_base'),
            $tenant->id));
        $link = str_replace('%tenant_id%', $tenant->id, config('tenancy.filesystem.url_override.public', 'public-%tenant_id%'));
        \Illuminate\Support\Facades\File::link($target, $link);
        return $event->tenant;
    })->shouldBeQueued(false), // `false` by default, but you probably want to make this `true` for production.
],

Events\TenantDeleted::class => [
    JobPipeline::make([
        Jobs\DeleteDatabase::class,
    ])->send(function (Events\TenantDeleted $event) {
        $tenant = $event->tenant;
        $target = base_path(sprintf("storage/%s%s/app/public", 
            config('tenancy.filesystem.suffix_base'),
            $tenant->id));
        $link = str_replace('%tenant_id%', $tenant->id, config('tenancy.filesystem.url_override.public', 'public-%tenant_id%'));
        \Illuminate\Support\Facades\File::delete($link);
        \Illuminate\Support\Facades\File::deleteDirectory(dirname($target));
        return $event->tenant;
    })->shouldBeQueued(false), // `false` by default, but you probably want to make this `true` for production.
],
```


`usage`
```php
$path = str_replace('public/', '', $path);
\Illuminate\Support\Facades\URL::tenantFile($path);
```