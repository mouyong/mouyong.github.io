# 【代码片段】php excel 导入功能，转换单元格正确的时间格式

文章编写于 2022年05月18日

参考：https://stackoverflow.com/questions/44304795/how-to-retrieve-date-from-table-cell-using-phpspreadsheet

```php
use PhpOffice\PhpSpreadsheet\Shared\Date;

$birthday =  Date::excelToDateTimeObject($value)->format('Y-m-d H:i:s');
$birthday =  date('Y-m-d H:i:s', Date::excelToTimestamp($data['生日']));
```