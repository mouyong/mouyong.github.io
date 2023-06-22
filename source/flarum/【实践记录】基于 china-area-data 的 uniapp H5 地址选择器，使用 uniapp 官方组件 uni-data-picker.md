# 【实践记录】基于 china-area-data 的 uniapp H5 地址选择器，使用 uniapp 官方组件 uni-data-picker

文章编写于 2022年04月18日

查看 [原文](https://laravel-workerman.iwnweb.com/d/14-china-area-data-uniapp-h5-uniapp-uni-data-picker)

[china-area-data 数据来源](https://github.com/airyland/china-area-data/)（最新同步可在仓库中查看）
[uni-data-picker](https://uniapp.dcloud.io/component/uniui/uni-data-picker.html) 使用说明


1. 从 [china-area-data](https://github.com/airyland/china-area-data/blob/master/v5/data.json) 获取最新的地址数据，保存到项目目录 `/static/h5/data.json` 中。

2. 在项目中创建 `/static/h5/area.js` 文件，提供获取地址信息的操作便捷性。内容如下：

```js
const defaultCode = 86

const PCAA = require('./data.json')

export const uniappInitRegionData = (code) => {
	if (typeof PCAA[code] === 'undefined') {
		return []
	}

	const data = [];
	for (let [k, v] of Object.entries(PCAA[code])) {
		const item = {
			text: v,
			value: v,
			children: uniappInitRegionData(k),
		}

		data.push(item)
	}
	return data
}

const createRegionFromString = (address, separator = ',') => {
	return address.split(separator).map(item => {
		return {
			text: item,
			value: item,
		}
	})
}

const createRegionTextDataFromString = (address, separator = ',') => {
	if (typeof address !== 'string') {
		console.info('area.toRegion', address)
		throw new TypeError('except string address, get type ' + (typeof address))
	}

	return address.split(separator)
}

const getRegionTextDataFromUser = (event, field = 'text') => {
	return event.detail.value.map(item => item[field])
}

const getRegionTextStringFromUser = (event, field = 'text', separator = ',') => {
	return event.detail.value.map(item => item[field]).join(separator)
}

const onRegionChange = (event, field = 'text', separator = ',') => {
	console.log('area.onRegionChange', event)

	const data = getRegionTextDataFromUser(event, field)
	const text = getRegionTextStringFromUser(event, field)

	uni.$emit('region.update', {
		data,
		text,
	})

	return data
}

const onNodeClick = (event) => {
	console.log('area.onNodeClick', event)
}

const replaceSeparator = (address, old, current) => {
	return address.replace(new RegExp(`${old}`, "g"), '/')
}

export default {
	defaultCode,
	regionData: uniappInitRegionData(defaultCode),
	uniappInitRegionData,
	createRegionFromString,
	createRegionTextDataFromString,
	getRegionTextDataFromUser,
	getRegionTextStringFromUser,
	replaceSeparator,
	onRegionChange,
	onNodeClick,
}

```

3. 在需要使用地址的地方按照如下示例进行使用。

```js
<template>
	<!-- #ifdef H5 -->
	<!-- TODO: 需要获取省市区的数据 -->
	<uni-data-picker style="flex: 1; margin-left: 30px;"  v-model="region" placeholder="请选择地址" popup-title="请选择城市" :localdata="regionData"
		@change="regionChange" @nodeclick="nodeclick">
	</uni-data-picker>
	<!-- #endif -->
</template>

<script>
// #ifdef H5
import area from '@/static/h5/area.js'
// #endif

export default {
	data() {
		return {
			// #ifdef H5
			regionData: area.regionData, // 初始化的所有省市区数据
			onRegionChange: area.onRegionChange, // 需要先注册，才可以直接在页面调用
			onNodeClick: area.onNodeClick, // 节点点击事件
			region: [], // v-model 绑定的数据。格式为 [{text: 'xxx', value: 'xxx'}, {text: 'xxx', value: 'xxx'}]
			regionTextData: [], // 地址信息数组 ['北京市', '市辖区', '东城区']
			regionText: '', // 地址文字信息，默认分隔符为 ","。示例：'北京市,市辖区,东城区'
			// #endif
		}
	},
	onLoad() {
		// #ifdef H5
		// 用户选择了地址 data: { data: ['北京市', '市辖区', '东城区'], text: '北京市,市辖区,东城区' }
		uni.$on('region.update', (data) => {
			this.regionTextData = data.data
			this.regionText = data.text
		})
		// 获取到了用户过去选择的地址信息 data: { address: '北京市,市辖区,东城区', separator: ',' }
		// 触发更新：uni.$emit('address.update', { address: res.data.address, separator: ',' })
		uni.$on('address.update', (data) => {
			const address = data.address || ''
			const separator = data.separator || ','
			
			this.regionText = address
			this.regionTextData = area.createRegionTextDataFromString(address)
			this.region = area.createRegionFromString(address, separator)
		})
		// #endif
	},
}
</script>
```