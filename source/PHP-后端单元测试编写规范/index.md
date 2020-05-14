---
title: PHP 后端单元测试编写规范
date: 2020-05-14 15:47:28
categories: 环境
tags: 开发

english_title: PHP-backend-unit-test-writing-specification
---
## 命名

- 类名以 `Test.php` 结尾
- 方法名以 `test_` 开头

示例
```
// PrinterTest.php
class PrinterTest extends \Tests\TestCase
{
    public function test_printer_can_do_print()
    {
      // ...
    }
}
```

## 测试类别

- 功能测试

- 集成测试
详见文档：https://learnku.com/docs/laravel/7.x/http-tests/7506

## 建议

善用测试替身 Mockery、测试模拟器

## 推荐包

- mockery/mockery （测试替身）
- phpunit/phpunit （单元测试框架）
- orchestra/testbench （laravel 框架替身）