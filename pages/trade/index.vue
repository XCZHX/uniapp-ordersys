<template>
	<view class="trade-header-box" :style="`padding-top: ${statusBarHeight}px;`">
		<view class="trade-header">
			<view class="trade-title">
				<text style="font-size: 40rpx; margin: 0 20rpx;">订单</text>
			</view>
			<uni-easyinput prefixIcon="search" v-model="searchInput" placeholder="搜索订单" />
			<button class="search-button">搜索</button>
		</view>
	</view>
	
	<view class="trade-body" :style="`margin-top: calc(${statusBarHeight}px + 80rpx);`">
		<view class="trade-item" v-for="(item,index) in tradeData" :key="item">
			<view class="item-header">
				<view class="shop-title">
					<text style="font-size: 28rpx;">{{item.shopTitle}}</text>
					<uni-icons type="forward"></uni-icons>
				</view>
				<view class="item-state">
					<text v-if="item.state === 1">已下单</text>
				</view>
			</view>
			<view class="item-in">
				<view class="item-in-item" v-for="i in item.sellers" :key="i">
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
				<view class="sum" style="align-self: end;">
					<text style="font-size: 25rpx;">实付款 </text>
					<text style="font-size: 25rpx; color: #02B6FD;">￥</text>
					<text style="font-size: 38rpx; font-weight: bold; color: #02B6FD;">{{item.sum/100}}</text>
				</view>
				<view class="footer-btn" style="align-self: end;">
					<view class="trade-detail-button" @click="handleDetailClick(index)">查看详情</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script setup>
import { ref } from 'vue'
import request from '@/utils/request.js'

const searchInput = ref('')
let statusBarHeight = 0
const tradeData = ref([])

const user = {
	_id: '67dc1ef65cc2b0b92f5fe2fc',
}

function getTrade(){
    request.post('/trade_select_byCusId', {id: user._id}).then(res => {
        if(res.code === 200) {
            tradeData.value = res.data
			// console.log(tradeData.value)
        } else {
            ElMessage.error(res.msg)
        }
        tradeData.value.reverse()
    })
}

function handleDetailClick(index){
	// console.log(tradeData.value[index])
	uni.navigateTo({
		url: '/pages/tradeDetail?data='+JSON.stringify(tradeData.value[index])
	})
}

function getStatusBarHeight() {
	const res = uni.getSystemInfoSync()
	statusBarHeight = res.statusBarHeight
}

getTrade()
getStatusBarHeight()

</script>

<style scoped>
.trade-header-box {
	position: fixed;
	z-index: 999;
	top: 0;
	width: 100%;
	background-color: #FFF;
}

.trade-header {
	display: flex;
	margin: 0 20rpx;
	height: 80rpx;
	align-items: center;
}

.search-button {
	margin: 20rpx 10rpx;
	width: 140rpx;
	font-size: 27rpx;
	height: 80%;
	display: flex;
	align-items: center;
	justify-content: center;
	background-color: #02B6FD;
	color: #FFF;
}

.trade-body {
	background-color: #dadada;;
}

.trade-item {
	background-color: #FFF;
	margin-bottom: 10rpx;
	display: flex;
	flex-direction: column;
	padding: 20rpx 30rpx;
}

.item-header {
	display: flex;
}

.item-state {
	color: #02B6FD;
}

.shop-title {
	flex: 1;
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

.trade-detail-button {
	margin-top: 20rpx;
	background-color: #c2f0fd;
	border-radius: 10rpx;
	color: #02B6FD;
	width: 180rpx;
	height: 60rpx;
	display: flex;
	align-items: center;
	justify-content: center;
	font-size: 28rpx;
}
</style>