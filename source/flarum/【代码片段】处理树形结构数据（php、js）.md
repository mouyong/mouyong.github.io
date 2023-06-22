# 【代码片段】处理树形结构数据

编写于 2022年05月25日

参考：https://github.com/jiaxincui/closure-table/blob/master/src/Extensions/CollectionExtension.php#L15-L34


## php 树形结构处理
```php
<?php

public function toTree($data = [], $primaryId = 'id', $parentId = 'parent_id', $children = 'children')
{
    // data is empty
    if (count($data) === 0) {
        return null;
    }

    // parameter missing
    if (!array_key_exists($primaryId, head($data)) || !array_key_exists($parentId, head($data))){
        return null;
    }

    // map
    $items = array();
    foreach ($data as $v) {
        $v[$children] = [];
        $items[@$v[$primaryId]] = $v;
    }

    // tree
    $tree = array();
    foreach ($items as &$item) {
        if (array_key_exists($item[$parentId], $items)) {
            $items[$item[$parentId]][$children][] = &$items[$item[$primaryId]];
        } else {
            $tree[] = &$items[$item[$primaryId]];
        }
    }

    return $tree;
}


$data = [
   ['id' => 1, 'name' => '1-1', 'parent_id' => 0],
   ['id' => 2, 'name' => '1-2', 'parent_id' => 0],
   ['id' => 3, 'name' => '1-3', 'parent_id' => 0],
   ['id' => 4, 'name' => '2-1', 'parent_id' => 1],
   ['id' => 5, 'name' => '2-2', 'parent_id' => 2],
   ['id' => 6, 'name' => '2-3', 'parent_id' => 2],
   ['id' => 7, 'name' => '3-1', 'parent_id' => 5],
   ['id' => 8, 'name' => '4-1', 'parent_id' => 7],
];

var_export(toTree($data));
```


## js 树形结构处理
```js
function toTree(data = [], primary_id = 'id', parent_id = 'parent_id') {
    let result = [];

    // 先将具有相同父节点的单项整理到同个 “集合” 下
    const map = data.reduce((last, item) => {
        // 仅在当前遍历到的节点有父节点时进行整理
        if (item[parent_id] !== undefined) {
            if (Boolean(last[item[parent_id]]) === false) {
                last[item[parent_id]] = [];
            }
            last[item[parent_id]].push(item);
        }
        return last;
    }, {});

    data.forEach(item => {
        // 将根节点放置到最顶层的数组中
        if (Boolean(item[parent_id]) === false) {
            result.push(item);
        }

        item.children = [];

        if (Boolean(map[item[primary_id]]) !== false) {
            item.children = map[item[primary_id]];
        }
    });

    return result;
}

const data = [
    {
        id: 1
    },
    {
        id: 2,
        parent_id: 1
    },
    {
        id: 3,
        parent_id: 2
    },
    {
        id: 4,
        parent_id: 1
    },
    {
        id: 5
    }
]

console.log(toTree(data))
```