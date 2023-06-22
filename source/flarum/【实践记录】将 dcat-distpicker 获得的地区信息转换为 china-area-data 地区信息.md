# 【实践记录】将 dcat-distpicker 获得的地区信息转换为 china-area-data 地区信息

文章编写于 2022年05月07日

## 背景
项目需要用到地区信息，在 dcat-distpicker 中，使用的是修改过的 china-area-data 信息。

为了多端地址数据统一。让管理后台、h5、小程序使用相同的地址数据：[airyland/china-area-data 定期更新的中国行政区域数据](https://github.com/airyland/china-area-data)。在项目中对 dcat-distpicket 做了点小修改。

在多端均使用 `省,市,区` 格式处理地址信息。

uniapp 使用 china-area-data 参考：[基于 china-area-data 的 uniapp H5 地址选择器，使用 uniapp 官方组件 uni-data-picker](https://laravel-workerman.iwnweb.com/d/14-china-area-data-uniapp-h5-uniapp-uni-data-picker)

## 前置要求
1. 缓存驱动需要支持 laravel cache tagging
2. 将[最新的地址数据(当前 v5 版本)](https://github.com/airyland/china-area-data/blob/master/v5/data.json)保存到 `config/chinaAreaData.php` 中，文件内容不要改动，保存到这里仅仅是为了方便，可以使用 `config('chinaAreaData')` 获取文件内容。
3. 在项目全局帮助文件中增加下方代码。
4. 按照示例使用 dcat-distpicker。并在 saving 阶段通过 code 获取地区名称。
示例：
```php
$form->distpicker([
    'province_name' => '省份',
    'city_name' => '市',
    'district_name' => '区'
], '省市区')->select2()->type('code')->required()->customFormat(function () {
    $info = getAddresssInfo($this->address);

    return [
        'province' => $info['area'][0] ?? '',
        'city' => $info['area'][1] ?? '',
        'district' => $info['area'][2] ?? '',
    ];
});

$form->text('address')->customFormat(function () {
    $info = getAddresssInfo($this->address);

    return $info['detail'];
});

$form->saving(function (Form $form) {
    $data = $form->input();

    $provinceName = getChinaAreaDataNameByCode($data['province_name']);
    $cityName = getChinaAreaDataNameByCode($data['city_name']);
    $districtName = getChinaAreaDataNameByCode($data['district_name']);

    $address = getDetailAddress([$provinceName, $cityName, $districtName], $data['address']);
    $form->deleteInput('province_name');
    $form->deleteInput('city_name');
    $form->deleteInput('district_name');

    $form->input(['address' => $address], '');
});
```


`helpers.php`
```php
if (!function_exists('getDetailAddresss')) {
    function getDetailAddress(array $area, string $detail)
    {
        $address = implode(',', $area);
        $address = implode('-', [$address, $detail]);

        return $address;
    }
}

if (!function_exists('getAddresssInfo')) {
    function getDetailAddress($address)
    {
        if (empty($address)) {
            return [
                'area' => [],
                'detail' => '',
            ];
        }

        list($address, $detail) = explode('-', $address);

        $area = explode(',', $address);

        return [
            'area' => $area,
            'detail' => $detail,
        ];
    }
}

if (!function_exists('getChinaAreaDataNameByCode')) {
    function getChinaAreaDataNameByCode(string $code)
    {
        $code = str_pad($code, 6, '0', STR_PAD_RIGHT);

        $codeDataMap = \Illuminate\Support\Facades\Cache::remember('chinaAreaDataCodeNameMap', now()->addMinutes(10), function() {
            $data = json_decode(config('chinaAreaData'), true) ?? [];

            $codeDataMap = [];
            foreach ($data as $dataCode => $dataItem) {
                if (is_array($dataItem)) {
                    foreach ($dataItem as $key => $value) {
                        $codeDataMap[$key] = $value;
                    }

                    continue;
                }

                if (is_string($dataItem)) {
                    $codeDataMap[$dataCode] = $dataItem;
                }
            }
            return $codeDataMap;
        });

        $codeName = $codeDataMap[$code] ?? throw new \RuntimeException("未知 code $code, 无法获取地区名称");

        return $codeName;
    }
}
```