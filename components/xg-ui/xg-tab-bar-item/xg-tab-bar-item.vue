<template>
	<view ref="tab-bar-item" class="tab-bar-item" @tap="itemTap">
		<slot></slot>
	</view>
</template>

<script>
	export default {
		inject: ['tabBar'],
		props: {
			//唯一标识
			// id: {
			// 	type: String|Number,
			// 	required: true
			// },
		},
		data() {
			return {
				index: 0,
				shouldScrollLeft: 0,
				tabBarItemWidth: undefined,
			}
		},
		methods: {
			// #ifdef APP-NVUE
			getComponentRect(ref) {
				
				const dom = uni.requireNativePlugin('dom');
				
				return new Promise(function (resolve, reject) {
					dom.getComponentRect(ref, data => {
						resolve(data);
					})
				})
			},
			// #endif
			itemTap() {
				this.tabBar.scrollLeft = this.shouldScrollLeft;
			}
		},
		created() {
			this.index = this.tabBar.itemIndex++;
		},
		mounted() {
			this.$nextTick(function () {
				setTimeout(async () => {
					// #ifndef APP-PLUS-NVUE
					const selector = uni.createSelectorQuery().in(this);
					selector.select('.tab-bar-item').fields({size: true});
					selector.exec(data => {
						const tabBarItemWidth = data[0].width;
						
						this.tabBar.tabBarItemWidthSum += tabBarItemWidth;
						
						this.shouldScrollLeft = this.tabBar.tabBarItemWidthSum - this.tabBar.tabBarWidth/2;
					})
					// #endif
					
					// #ifdef APP-PLUS-NVUE
					const tabBarItemData = await this.getComponentRect(this.$refs['tab-bar-item']);
					const tabBarItemWidth = tabBarItemData.size.width;
					
					this.tabBar.tabBarItemWidthSum += tabBarItemWidth;
					
					this.shouldScrollLeft = this.tabBar.tabBarItemWidthSum - this.tabBar.tabBarWidth/2;
					// #endif
				}, 10);
			})
		}
	}
</script>

<style scoped>
	.tab-bar-item {
		/* height: 100rpx; */
		/* border-width: 1px; */
	}
</style>