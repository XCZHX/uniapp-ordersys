<template>
	<view class="container">
		<!-- <view :style="`height: ${statusBarHeight}px;`"></view> -->
		<view class="header" :style="`padding-top: ${statusBarHeight}px;`">
			<view class="header-box">
				<uni-icons class="header-icon" type="back" size="35" @click="handleBackClick"></uni-icons>
			</view>
		</view>
		
		<view class="content" :style="`margin-top: calc(${statusBarHeight}px + 80rpx);`">
			<view class="step-box">
				<uni-steps :options="[{title: '已下单'}, {title: '已发货'}, {title: '已签收'}, {title: '已评价'}]" :active="tradeData.state-1" active-color="#02B6FD"></uni-steps>
			</view>
			<view class="item-header">
				<view class="shop-title">
					<text style="font-size: 28rpx;">{{tradeData.shopTitle}}</text>
					<uni-icons type="forward"></uni-icons>
				</view>
			</view>
			<view class="item-in">
				<view class="item-in-item" v-for="i in tradeData.sellers" :key="i">
					<view class="item-img-box">
						<image class="seller-img" :src="i.img" mode="heightFix"></image>
					</view>
					<view class="seller-detail">
						<text class="title-text" style="font-size: 30rpx; font-weight: bold;">{{i.title}}</text>
						<text class="class-text" style="font-size: 25rpx; margin: 10rpx 0; color: #838383;">{{i.sClass}}</text>
						<view class="count-and-price">
							<text style="flex: 1; font-size: 25rpx; margin: 10rpx 0; color: #838383;">×{{i.count}}</text>
							<view class="price-box">
								<text style="font-size: 25rpx;">￥</text>
								<text style="font-size: 32rpx; font-weight: bold;">{{i.price/100}}</text>
							</view>
						</view>
					</view>
				</view>
			</view>
			<view class="item-footer">
				<view class="sum">
					<text style="font-size: 28rpx;">实付款</text>
					<view class="">
						<text style="font-size: 25rpx; color: #02B6FD;">￥</text>
						<text style="font-size: 38rpx; font-weight: bold; color: #02B6FD;">{{tradeData.sum/100}}</text>
					</view>
				</view>
				<view class="tradeId">
					<text style="font-size: 28rpx;">订单号</text>
					<text style="font-size: 28rpx;">{{tradeData.id}}</text>
				</view>
				<view class="tradeTime">
					<text style="font-size: 28rpx;">创建时间</text>
					<text style="font-size: 28rpx;">{{tradeData.date}}</text>
				</view>
			</view>
		</view>
	</view>
</template>

<script setup>
import { onLoad } from '@dcloudio/uni-app'
import { ref } from 'vue'

const tradeData = ref({
	date: "",
	id: "",
	sellers: [],
	shopTitle: "",
	sum: 0
})
let statusBarHeight = 0

onLoad((option) => {
	tradeData.value = JSON.parse(option.data)
	// console.log(tradeData.value)
})

function handleBackClick() {
	uni.navigateBack()
}

function getStatusBarHeight() {
	const res = uni.getSystemInfoSync()
	statusBarHeight = res.statusBarHeight
}

getStatusBarHeight()

</script>

<style scoped>
.header {
	border-bottom: 1rpx solid #c3c3c3;
	position: fixed;
	z-index: 999;
	top: 0;
	width: 100%;
	background-color: #FFF;
}

.content {
	margin-top: 80rpx;
	margin-left: 40rpx;
	margin-right: 40rpx;
}

.header-box {
	display: flex;
}

.header-box .uni-icons {
	margin: 10rpx 20rpx;
}

.item-header {
	display: flex;
}

.item-in {
	display: flex;
	flex-direction: column;
}

.item-in-item {
	display: flex;
	margin: 10rpx 0;
}

.item-img-box {
}

.seller-img {
	height: 170rpx;
	border-radius: 10rpx;
}

.seller-detail {
	display: flex;
	flex-direction: column;
	width: 480rpx;
	margin-left: 20rpx;
}

.count-and-price {
	display: flex;
}

.title-text {
	overflow: hidden;
	white-space: nowrap;
	text-overflow: ellipsis;
}

.class-text {
	overflow: hidden;
	white-space: nowrap;
	text-overflow: ellipsis;
}

.item-footer {
	display: flex;
	flex-direction: column;
}

.sum {
	display: flex;
	justify-content: space-between;
}

.tradeId {
	display: flex;
	justify-content: space-between;
}

.tradeTime {
	display: flex;
	justify-content: space-between;
}

.step-box {
	margin-bottom: 50rpx;
	padding-top: 50rpx;
}
</style>