<template>
	<view
		style="display: flex;flex-direction: column;justify-content: space-between;padding: 100rpx;min-height: 200rpx;">
		<button @click="go2R(3000)">打开RecycleViewList 3000</button>
		<button @click="go2R(10000)">打开RecycleViewList 10000</button>
		<button @click="go2R(20000)">打开RecycleViewList 20000</button>
		<button @click="go2R(50000)">打开RecycleViewList 50000</button>
		<button @click="go2R(100000)">打开RecycleViewList 100000</button>
		<button @click="go2D(3000)">打开默认实现页面 3000</button>
		<button @click="go2D(10000)">打开默认实现页面 10000</button>
		<button @click="go2D(20000)">打开默认实现页面 20000</button>
		<button @click="go2D(50000)">打开默认实现页面 50000</button>
		<button @click="go2D(100000)">打开默认实现页面 100000</button>
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
			}
		},
		onLoad() {

		},
		methods: {
			go2R(count) {
				uni.navigateTo({
					url: `/pages/index/recycle-page${count}`
				})
			},
			go2D(count) {
				uni.navigateTo({
					url: `/pages/index/default-page${count}`
				})
			}
		},
		onReady() {
			const items = [];
			for (let i = 0; i < 1000; i++) {
				items[i] = {
					id: i,
					value: `item${i}`
				};
			}
			this.items = items;
			this.itemHeight = +uni.upx2px(80);
		}
	}
</script>

<style>

</style>
