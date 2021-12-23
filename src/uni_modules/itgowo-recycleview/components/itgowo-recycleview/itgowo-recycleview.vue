<template>
	<view :class="extClass">
		<block v-if="items && items.length > minNum">
			<scroll-view scroll-y
				:style="{ minHeight: scrollviewMinHeight + 'px', height: scrollViewHeight, position: 'relative' }"
				@scroll="handleScroll">
				<view :style="{ height: layoutHeight + 'px' }">
					<view :style="{ transform: `translateY(${offset}px)` }">
						<view v-for="(item, index) in viewCount" :key="index" v-show="visibleData[index] !== undefined">
							<slot name="default" :item="visibleData[index]"></slot>
						</view>
					</view>
				</view>
			</scroll-view>
		</block>
		<block v-else>
			<scroll-view scroll-y :style="{ height: scrollViewHeight + 'px' }">
				<view v-for="(item, index) in items" :key="index">
					<slot name="default" :item="item"></slot>
				</view>
			</scroll-view>
		</block>
	</view>
</template>

<script>
	/**
	 * @description Android 开发多年，代码风格请前端多担待
	 * @property {Array} items 列表数据
	 * @property {Number} visibleViewNum 可视范围数量，滑动显示半个不算，所有完整显示的最大数量
	 * @property {Number} itemViewHeight 每条数据展示view的高度，不设置不行的，滑动区域高度=itemViewHeight*visibleViewNum
	 * @property {Number} minNum 模式切换，数据量很小可以不用复用，某些极限操作会有未知bug
	 * @property {Number} startPosition 可视数据的第一条在items中的起点位置减1，如果是位置0则不减1
	 * @property {Number} endPosition 可视数据的最后一条在items中的起点位置加1，如果是最后一条数据，则不加1
	 * @property {Number} offset 滑动显示信息偏移量，滑动距离不够itemViewHeight时，offset不变，当超过itemViewHeight时，offset加减一个itemViewHeight距离，从视觉上就是位移一个itemViewHeight距离，来达到这几个view不断复用的目的，主要还是设备性能够强，同时把数据也更新一遍，人眼发现不了这个变化
	 * @property {Number} viewCount 真正创建的节点数，显示区域上下各留出一个view用来滑动时显示
	 * @property {Number} scrollViewHeight scrollview的高度
	 * @property {Number} layoutHeight scrollview里所有item加起来的高度，目的是撑出来一个符合逻辑效果的滚动条
	 * @property {Number} previousDataCount 显示区域前一个的数据，如果起始位置是0则为0，其余位置则为1，边界取不到数据
	 * @property {Number} nextDataCount 显示区域后一个的数据，如果结束位置是items.length-1则为0，其余位置则为1，边界取不到数据
	 * @property {Number} prepareViewNum 预留view数量，是previousDataCount和nextDataCount的最大值
	 * @property {Arrary} visibleViewData 需要显示的数据，最多viewCount个，因为边界问题，最少是viewCount-1个，如果你非要说也有viewCount-2的时候，数据太少，那为什么还用这个优化组件呢，是吧？我只考虑数据量大，不大还得判断
	 */
	export default {
		name: 'itgowo-recycleview',
		props: {
			items: {
				type: Array,
				default: function() {
					return [];
				}
			},
			extClass: {
				type: String,
				default: ''
			},
			scrollViewHeight: {
				type: String,
				default: '100vh'
			},
			visibleViewNum: {
				type: Number,
				default: 5
			},
			prepareViewNum: {
				type: Number,
				default: 5
			},
			itemViewHeight: {
				type: Number,
				default: 50
			},
			minNum: {
				type: Number,
				default: 100
			}
		},
		data() {
			return {
				startPosition: 0,
				endPosition: this.visibleViewNum,
				offset: 0,
				viewCount: 0,
				layoutHeight: 0,
				scrollviewMinHeight: 0
			};
		},
		watch: {
			visibleViewNum: {
				handler() {
					this.viewCount = this.visibleViewNum + 2 * this.prepareViewNum;
					this.scrollviewMinHeight = this.visibleViewNum * this.itemViewHeight;
				},
				immediate: true
			},
			itemViewHeight: {
				handler() {
					if (this.items) {
						this.layoutHeight = this.items.length * this.itemViewHeight;
						this.scrollviewMinHeight = this.visibleViewNum * this.itemViewHeight;
					}
				},
				immediate: true
			}
		},
		computed: {
			previousDataCount() {
				return Math.min(this.startPosition, this.prepareViewNum);
			},
			nextDataCount() {
				return Math.min(this.items.length - this.endPosition, this.prepareViewNum);
			},
			visibleData() {
				if (this.items.length < this.minNum) {
					return [];
				}
				const startPosition = this.startPosition - this.previousDataCount;
				const endPosition = this.endPosition + this.nextDataCount;
				return this.items.slice(startPosition, endPosition);
			}
		},
		methods: {
			handleScroll(e) {
				//获取滑动位置
				const scrollTop = e.detail.scrollTop;
				// 计算开始位置 start position
				this.startPosition = Math.floor(scrollTop / this.itemViewHeight);
				// 计算结束位置 end position   开始位置所有显示view数
				this.endPosition = this.startPosition + this.visibleViewNum;
				// scrollTop % this.itemViewHeight 取余，核心思想
				this.offset = scrollTop - (scrollTop % this.itemViewHeight) - this.previousDataCount * this.itemViewHeight;
			}
		}
	};
</script>

<style>
	*{
		font-size: 48rpx;
	}
</style>
