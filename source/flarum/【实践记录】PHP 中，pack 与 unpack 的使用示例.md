# 【实践记录】PHP 中，pack 与 unpack 的使用示例

文章编写于 2022年04月15日

论坛：https://laravel-workerman.iwnweb.com/d/11-php-pack-unpack

pack 文档：https://www.php.net/manual/en/function.pack.php
unpack 文档：https://www.php.net/manual/en/function.unpack.php
PHP中pack、unpack的详细用法：https://segmentfault.com/a/1190000008305573

在线示例：https://sandbox.onlinephpfunctions.com/c/56feb

```
<?php

$encodeFormat = 'a6N';
$decodeFormat = 'a6random_str/Ntimestamp';
$data = [
	uniqid(),
	time()
];

$res = pack($encodeFormat, ...$data);

$encode = base64_encode($res);
var_dump($encode);

$data = unpack($decodeFormat, base64_decode($encode));
var_dump($data);

```

![示例][示例]

[示例]: https://laravel-workerman.iwnweb.com/assets/files/2022-04-15/1649999990-980574-image.png