# Recycleview
### 简介
支持超大数据量渲染列表，DOM节点数可空，如示例只需要数十个节点即可服用节点信息，通过肉眼无法观察到的抖动位移和显示更新达到复用目的。
### 背景
项目接口一次性返回大量数据，严重影响render时间，本人Android开发5年以上，前端和小程序开发2年，计划模仿Android上Recycleview机制，由于无法操作DOM（uniapp/小程序），故采用快速动画原理，不让人看出移动即可。
### 前提条件和局限性
再次声明，下面讲的部分内容具有开发Android习惯，命名、使用和思想
支持两种模式，当数据量大于minNum时启用优化，否则常规for添加
* 前提条件
	* 每项ItemView布局高度一致且初始计算好，例如指定100px
	* 知道页面布局中要显示的数据数量，计算出scrollview最小高度
	* 预估缓存ItemView数量

* 局限性
	* 滑动过快效果略差
	* 能PK过项目/产品/甲方的就不要用这种优化

### 技术方案
播放动画实质上是一张张图片快速切换，因为频率高，肉眼无法观察出切换过程，同理，本套方案亦是通过瞬间位移达到位置替换目的，快到你看不出来，移动了吗？没动(肉眼不可见)，但又好像动了(动的太快了)😁。

这个方案前提比较明确，计算ScrollView(最小)高度需要知道每个ItemViewHeight和VisibleViewCount(可见View数量，不含上下预留缓存View)，如果设备显示高度过大，可以设置ScrollView高度为100%。
* 让ScrollView正常显示滚动条：内部有一个ItemViewHeight x items.length 高度的View
* 显示数据的布局用translateY来移动布局：每滑动一点距离显示数据的布局跟着滑动，此时translateY是不随着滑动而变化，当滑动距离达到一个ItemViewHeight时，translateY减去(另一个方向增加)一个ItemViewHeight，相当于第0个刚刚划走就立刻移动到曾经第1个位置，data显示的也是第一个位置的数据。节点复用。
* 在滑动距离不到一个ItemViewHeight时，scrollTop - scrollTop % itemViewHeight恒等于0，也就是translateY不会变，整体布局也是跟随滑动一致的，至于取余这个不懂得。。。。你设置几个值计算一下。
* 在滑动距离达到一个ItemViewHeight时，scrollTop - scrollTop % itemViewHeight等于scrollTop，也就是translateY=scrollTop。
* 减去 this.previousDataCount * this.itemViewHeight是一个位移而已，上边缘留出一个隐藏的View，理解原理可以不用管，没这个就会出现滑动过程中有空白部分。
* 如果设置的visibleViewNum较少，部分留白，可以适当增加prepareViewNum数量，scrollview的height是个苦力活。

如图，10000条数据渲染列表只需要145个节点即可，常规方式至少要30000个，如果复杂点的布局就更多
### 数据对比
此数据含框架本身节点数和内存占用，真实情况我们也要算上的，毕竟内存条16GB你不能完全使用，系统占用一部分🤣。测试不具权威性，因为差距过大，就一次定胜负了，图已上传

||recycleview|普通创建|
|---|---|---|
|DOM节点数|145|30152|
|JS堆大小|18.1MB|239MB|

### 安装方式
只传了uni_modules版本，HBuilder支持直接导入，传统方式直接下载解压拷贝文件即可

### 基本用法

``` 
<itgowo-recycleview :items="items" :minNum="100" :prepareViewNum="10" scrollViewHeight="100vh" :itemViewHeight="itemHeight" :visibleViewNum="6">
	<template v-slot:default="slotItem">
		<child :item="slotItem.item"></child>
	</template>
</itgowo-recycleview>
```
```
const items = [];
for (let i = 0; i < 1000; i++) {
	items[i] = { id: i, value: `item${i}` };
}
this.items = items;
this.itemHeight = +uni.upx2px(80);
```

`child.vue就是你的组件`
```
<template>
	<view style="height: 80upx;border: 3rpx solid #000000;box-sizing: border-box;">{{ item.value }}</view>
</template>

<script>
export default {
	name: 'child',
	props: {
		item: {
			type: Object,
			default: () => {
				return { id: 0 };
			}
		}
	},
	data() {
		return {};
	},
	mounted() {
		console.log('mounted',this)
	}
};
</script>
```

### API
Props

|属性名|是否必须|类型|默认值|说明|
|---|---|---|---|---|
|items|是|Array|[]|列表数据|
|scrollViewHeight|是|String|100vh|scrollview的高度，一般开发者算出来赋值，字符串例如“100rpx”|
|visibleViewNum|是|Number|5|可视范围数量，滑动显示半个不算，所有完整显示的最大数量|
|prepareViewNum|否|Number|5|预留view数量，是previousDataCount和nextDataCount的最大值|
|itemViewHeight|是|Number|50|单位px，每条数据展示view的高度，不设置不行的，滑动区域高度=itemViewHeight*visibleViewNum|
|minNum|否|Number|100|模式切换，数据量很小(小于等于minNum时)可以不用复用，某些极限操作会有未知bug|
|extClass|否|String|''|指定class|
