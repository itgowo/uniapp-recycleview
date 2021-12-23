<template>
	<view style="height: 100%;border: 3rpx solid #000000;box-sizing: border-box;">{{ item.value }}</view>
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
	}
};
</script>

<style></style>
