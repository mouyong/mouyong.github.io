# 【实践记录】为项目增加命令行工具，支持在项目中查找与调用 shell 命令，默认支持获取与执行 php、composer 命令

文章编写于 2022年05月05日


## 背景
在项目中偶尔会需要用到命令行程序, 在此为命令行程序调用进行一个简单的封装. 已增加至包 `zhenmu/support`.

可直接引入 `composer require zhenmu/support` 后使用.


## 前置要求

```
composer require symfony/process
```


## 代码与示例

### 1. 代码

`app/Tools/CommandTool`
```php
<?php

namespace App\Utils;

class CommandTool
{
    protected $executableFinder;

    protected $defaultExtraDirs = [
        '/usr/bin/',
        '/usr/local/bin/',
    ];

    public function __construct()
    {
        $this->executableFinder = new \Symfony\Component\Process\ExecutableFinder();

        if (function_exists('base_path')) {
            $this->defaultExtraDirs = array_merge($this->defaultExtraDirs, [base_path()]);
        }
    }

    public static function make()
    {
        return new static();
    }

    public static function getRealpath($path)
    {
        return realpath($path);
    }

    public static function formatCommand($command)
    {
        if (is_string($command)) {
            $command = explode(' ', $command);
        }

        return $command;
    }

    public function createProcess(array $command, string $cwd = null, array $env = null, $input = null, ?float $timeout = 60)
    {
        return tap(new \Symfony\Component\Process\Process(...func_get_args()));
    }

    public static function findBinary(string $name, array $extraDirs = [])
    {
        $instance = static::make();

        $extraDirs = array_merge($instance->defaultExtraDirs, $extraDirs);

        $extraDirs = array_map(fn ($dir) => rtrim($dir, '/'), $extraDirs);

        return $instance->executableFinder->find($name, null, $extraDirs);
    }

    public static function getPhpProcess(array $argument)
    {
        $instance = new static();

        $php = $instance->findBinary('php');

        return $instance->createProcess([$php, ...$argument]);
    }

    public static function getComposerProcess(array $argument)
    {
        $instance = new static();

        $php = $instance->findBinary('php');

        $composer = $instance->findBinary('composer');

        return $instance->createProcess([$php, $composer, ...$argument]);
    }
}

```

### 2. 代码调用示例
```php
$phpProcessOutput = \App\Utils\CommandTool::getPhpProcess(['-v'])->run()->getOutput();
$composerProcessOutput = \App\Utils\CommandTool::getComposerProcess(['-V'])->run()->getOutput();

$start = \App\Utils\CommandTool::findBinary('start.php', [
    \App\Utils\CommandTool::getRealpath(base_path().'/../../workerman.net')
]);
$webmanStartOutput = \App\Tools\CommandTool::getPhpProcess([$start, 'status'])->run()->getOutput();
var_dump($webmanStartOutput);

$ip = \App\Tools\CommandTool::make()->createProcess(\App\Tools\CommandTool::formatCommand("dig +short myip.opendns.com @resolver1.opendns.com"))->run()->getOutput();

var_dump($ip, $phpProcessOutput, $composerProcessOutput);die;
```

## 截图

![php命令与composer命令][php命令与composer命令]

[php命令与composer命令]: https://laravel-workerman.qiniu.iwnweb.com/php%E5%91%BD%E4%BB%A4%E4%B8%8Ecomposer%E5%91%BD%E4%BB%A4.png