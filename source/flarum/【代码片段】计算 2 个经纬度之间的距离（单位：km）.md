# 【代码片段】计算 2 个经纬度之间的距离（单位：km）

文章编写于 2022年05月18日

公式来源：https://segmentfault.com/a/1190000013922206

坐标拾取器：https://lbs.amap.com/tools/picker

示例经纬度：
```php
$a = explode(',', "121.137543,31.4171"); // 太仓科技园
$b = explode(',', "121.133618,31.435604"); // 常丰社区
```

**推荐使用方式：2**

1. 直接使用 `mysql` 计算
```sql
drop function if exists get_distance;
delimiter //
create function get_distance (
  lng1 double,
  lat1 double,
  lng2 double,
  lat2 double
)
returns double
begin
    declare distance double;
    declare a double;
    declare b double;

    declare radLat1 double;
    declare radLat2 double;
    declare radLng1 double;
    declare radLng2 double;
 
    set radLat1 = lat1 * PI() / 180;
    set radLat2 = lat2 * PI() / 180;
    set radLng1 = lng1 * PI() / 180;
    set radLng2 = lng2 * PI() / 180;

    set a = radLat1 - radLat2;
    set b = radLng1 - radLng2;

    set distance = 2 * asin(
      sqrt(
        pow(sin(a / 2), 2) + cos(radLat1) * cos(radLat2) * pow(sin(b / 2), 2)
      )
    ) * 6378.137;
    return distance;
end//
delimiter ;

select get_distance(121.137543,31.4171, 121.133618,31.435604);
```

2. `Laravel` 业务中生成 `sql` 语句, 并临时设置查询字段, 最后通过 `having` 进行排序
```shell
composer require zhenmu/support
```

```php
use ZhenMu\Support\Utils\Distance;
use Illuminate\Support\Facades\DB;

$sql = DB::raw(sprintf('select %s', Distance::getDistanceSql(121.137543, 31.4171, 121.133618, 31.435604)));
$result = DB::selectOne($sql);

var_dump($result);
```

**计算距离的 sql 生成逻辑**
```php
<?php

function getDistanceSql($sqlLongitude, $sqlLatitude, $longitude, $latitude, $alias = 'distance')
{
    $sql = <<<SQL
2 * ASIN(
      SQRT(
        POW(
          SIN(
            (
                $latitude * PI() / 180 - $sqlLatitude * PI() / 180
            ) / 2
          ), 2
        ) + COS($latitude * PI() / 180) * COS($sqlLatitude * PI() / 180) * POW(
          SIN(
            (
                $longitude * PI() / 180 - $sqlLongitude * PI() / 180
            ) / 2
          ), 2
        )
      )
    ) * 6378.137
SQL;
    return sprintf('(%s) as %s', $sql, $alias);
}

$sql = \Illuminate\Support\Facades\DB::raw(sprintf('select %s', AppUtility::getDistanceSql(121.137543, 31.4171, 121.133618, 31.435604)));
$result = \Illuminate\Support\Facades\DB::selectOne($sql);

var_dump($result);
```

3. php 业务中使用：先创建函数，再执行 `sql` 查询。将其中 1个经纬度替换成数据库字段，另一个经纬度传入用户当前地址。查询结果可进行排序
```php
use Illuminate\Support\Facades\DB;
use Illuminate\Support\Facades\Cache;

function ensureDistanceFunctionExists()
{
    $getDistanceSqlFunctionExists = Cache::remember('get_distance_sql_exists', now()->addDays(7), function () {
        $getDistanceSqlFunctionSql = "SHOW FUNCTION STATUS where name = 'get_distance'";
        $getDistanceSqlFunction = DB::selectOne($getDistanceSqlFunctionSql);
        $getDistanceSqlFunctionExists = boolval($getDistanceSqlFunction);

        if ($getDistanceSqlFunctionExists) {
            return true;
        }

        $createGetDistanceFunctionSql = <<<SQL
drop function if exists get_distance;
delimiter //
create function get_distance (
  lng1 double,
  lat1 double,
  lng2 double,
  lat2 double
)
returns double
begin
    declare distance double;
    declare a double;
    declare b double;

    declare radLat1 double;
    declare radLat2 double;
    declare radLng1 double;
    declare radLng2 double;
 
    set radLat1 = lat1 * PI() / 180;
    set radLat2 = lat2 * PI() / 180;
    set radLng1 = lng1 * PI() / 180;
    set radLng2 = lng2 * PI() / 180;

    set a = radLat1 - radLat2;
    set b = radLng1 - radLng2;

    set distance = 2 * asin(
      sqrt(
        pow(sin(a / 2), 2) + cos(radLat1) * cos(radLat2) * pow(sin(b / 2), 2)
      )
    ) * 6378.137;
    return distance;
end//
delimiter ;
SQL;

        return DB::statement($createGetDistanceFunctionSql);
    });

    if (!$getDistanceSqlFunctionExists) {
        Cache::forget('get_distance_sql_exists');
    }

    return $getDistanceSqlFunctionExists;
}

ensureDistanceFunctionExists();

DB::selectOne(select get_distance(121.137543,31.4171, 121.133618,31.435604));
```

4. php 代码对指定的 2个经纬度进行业务计算
```php
/**
 * 根据两点间的经纬度计算距离
 * @param $lng1
 * @param $lat1
 * @param $lng2
 * @param $lat2
 * @return int
 */
function getDistance($lng1, $lat1, $lng2, $lat2)
{
    //将角度转为狐度
    $radLat1 = deg2rad($lat1);//deg2rad()函数将角度转换为弧度
    $radLat2 = deg2rad($lat2);
    $radLng1 = deg2rad($lng1);
    $radLng2 = deg2rad($lng2);
    $a = $radLat1 - $radLat2;
    $b = $radLng1 - $radLng2;
    $distance = 2 * asin(
    	sqrt(
    		pow(sin($a / 2), 2) + cos($radLat1) * cos($radLat2) * pow(sin($b / 2), 2)
    	)
    ) * 6378.137;
    return $distance;
}

$a = explode(',', "121.137543,31.4171");
$b = explode(',', "121.133618,31.435604");


$res = getDistance($a[0], $a[1], $b[0], $b[1]);
var_dump($res);
```