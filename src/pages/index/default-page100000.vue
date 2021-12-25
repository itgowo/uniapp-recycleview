<template>
	<view class="content">
		<view>{{count}}条数据，无优化</view>
		<scroll-view style="height: 400px;" scroll-y="true">
			<view :style="{height: itemHeight}" v-for="item in items">
				<child :item="item"></child>
			</view>
		</scroll-view>

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
				itemHeight: 0,
				count: 100000
			}
		},
		methods: {

		},
		onReady() {
			const items = [];
			for (let i = 0; i < this.count; i++) {
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
	.content {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}
	*{
		font-size: 48rpx;
	}
</style>
