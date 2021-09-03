<template>
	<view class="content">
		<view>1000条数据，回收复用优化</view>
		<itgowo-recycleview :items="items" :minNum="100" :prepareViewNum="10" scrollViewHeight="100vh" :itemViewHeight="itemHeight" :visibleViewNum="10">
			<template v-slot:default="slotItem">
				<child :item="slotItem.item"></child>
			</template>
		</itgowo-recycleview>
		<view>1000条数据，未启用回收复用优化，代码已注释，手动释放测试</view>
		<!-- <itgowo-recycleview :items="items" :minNum="10000" :itemViewHeight="itemHeight" :visibleViewNum="6">
			<template v-slot:default="slotItem">
				<child :item="slotItem.item"></child>
			</template>
		</itgowo-recycleview> -->
	</view>
</template>

<script>
import child from './child.vue';
export default {
	components: {
		child
	},
	data() {
		return {
			items: [],
			itemHeight: 0
		};
	},
	methods: {
		scroll1(e) {
			console.log(e.detail.scrollTop);
		}
	},
	onReady() {
		const items = [];
		for (let i = 0; i < 1000; i++) {
			items[i] = { id: i, value: `item${i}` };
		}
		this.items = items;
		this.itemHeight = +uni.upx2px(80);
	}
};
</script>

<style>
.scroll-view {
	height: 100vh;
}
</style>
