<template>
	<view class="content">
		<view>{{count}}条数据，复用优化</view>
		<itgowo-recycleview extClass="recycle-class" :items="items" :minNum="100" :prepareViewNum="10" scrollViewHeight="400px"
			:itemViewHeight="itemHeight" :visibleViewNum="10">
			<template v-slot:default="slotItem">
				<child :item="slotItem.item"></child>
			</template>
		</itgowo-recycleview>
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
				count: 3000
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
	.recycle-class{
		width: 100%;
	}
	.content {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		width: 100%;
	}
</style>
