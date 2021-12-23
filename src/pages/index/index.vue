<template>
	<view
		style="display: flex;flex-direction: column;justify-content: space-between;padding: 100rpx;min-height: 200rpx;">
		<button @click="go2R">打开RecycleViewList</button>
		<button @click="go2D">打开默认实现页面</button>
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
			go2R() {
				uni.navigateTo({
					url: '/pages/index/recycle-page'
				})
			},
			go2D() {
				uni.navigateTo({
					url: '/pages/index/default-page'
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
