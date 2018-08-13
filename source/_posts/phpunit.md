---
title: Laravel PHPUnit 配置及美化
date: 2018-07-12 20:05:35
categories: 开发
tags:
  - 开发
  - PHP
  - Laravel

english_title: Laravel-PHPUnit-configuration-and-beautification
---

## 美化 phpunit 输出

前往 [GitHub](https://github.com/mikeerickson/phpunit-pretty-result-printer)

1. 安装
`composer require --dev codedungeon/phpunit-result-printer`

2. 修改 phpunit.xml 配置
```
<?xml version="1.0" encoding="UTF-8"?>
<phpunit printerClass="Codedungeon\PHPUnitPrettyResultPrinter\Printer">
// ....
</phpunit>
```

## 修改配置

编辑 phpunit.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<phpunit backupGlobals="false"
         backupStaticAttributes="false"
         bootstrap="vendor/autoload.php"
         colors="true"
         convertErrorsToExceptions="true"
         convertNoticesToExceptions="true"
         convertWarningsToExceptions="true"
         processIsolation="false"
         stopOnFailure="false"
         verbose="true"
         printerClass="Codedungeon\PHPUnitPrettyResultPrinter\Printer">
    <testsuites>
        <testsuite name="Feature">
            <directory suffix="Test.php">./tests/Feature</directory>
        </testsuite>

        <testsuite name="Unit">
            <directory suffix="Test.php">./tests/Unit</directory>
            <exclude>./tests/Unit/Example</exclude>
        </testsuite>
    </testsuites>
    <filter>
        <whitelist processUncoveredFilesFromWhitelist="true">
            <directory suffix=".php">./app</directory>
        </whitelist>
    </filter>
    <php>
        <env name="APP_ENV" value="testing"/>
        <env name="DB_CONNECTION" value="sqlite"/>
        <env name="DB_DATABASE" value=":memory:"/>
        <env name="CACHE_DRIVER" value="array"/>
        <env name="SESSION_DRIVER" value="array"/>
        <env name="QUEUE_DRIVER" value="sync"/>
        <env name="MAIL_DRIVER" value="array"/>
    </php>
</phpunit>

```
