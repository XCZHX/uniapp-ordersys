<template>
	<view class="container">
		<view class="order-header-box" :style="`padding-top: ${statusBarHeight}px;`">
			<view class="order-header">
				<uni-icons class="header-icon" type="back" size="32" @click="handleBackClick"></uni-icons>
				<text style="font-size: 38rpx; font-weight: bolder;">确认订单</text>
			</view>
		</view>
		
		<view class="order-body" :style="`margin-top: calc(${statusBarHeight}px + 80rpx);`">
			<view class="chosen-address">
				<uni-icons type="location" size="25" style="align-self: center;"></uni-icons>
				<view class="address-item">
					<text style="font-size: 35rpx; font-weight: bolder;">{{user.address[addressIndex].detail}}</text>
					<text style="font-size: 28rpx;">{{user.address[addressIndex].name}} {{user.address[addressIndex].mobile}}</text>
				</view>
				<uni-icons type="right" size="20" style="align-self: center;"></uni-icons>
			</view>
			<view class="order-item" v-for="(item,index) in orderData" :key="item">
				<view class="img-box">
					<image class="seller-img" :src="item.sellerImg" mode="heightFix"></image>
				</view>
				<view class="seller-detail">
					<text class="seller-title" style="font-size: 30rpx; font-weight: bold;">{{item.sellerTitle}}</text>
					<text class="seller-class" style="font-size: 25rpx; margin: 10rpx 0; color: #838383;">{{item.sellerClass}}</text>
					<view class="detail-bottom">
						<text style="color: #838383; font-size: 25rpx;">×{{item.sellerCount}}</text>
						<view style="color: #02B6FD;" class="price-box">
							<text style="font-size: 25rpx;">￥</text>
							<text style="font-size: 38rpx; font-weight: bold;">{{item.sellerPrice/100}}</text>
						</view>
					</view>
				</view>
			</view>
		</view>
		
		<view class="order-footer-box">
			<view class="order-footer">
				<view class="footer-top">
					<text style="font-size: 28rpx;">共{{sumCount}}件,合计:</text>
					<view style="color: #02B6FD" class="price-box">
						<text style="font-size: 25rpx;">￥</text>
						<text style="font-size: 38rpx; font-weight: bold;">{{sumPrice/100}}</text>
					</view>
				</view>
				<view class="footer-bottom">
					<view class="order-button" @click="handleOrderClick">立即支付</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script setup>
import { onLoad } from '@dcloudio/uni-app'
import { ref } from 'vue'
import request from '@/utils/request.js'

const orderData = ref([])
let statusBarHeight = 0
const sumCount = ref(0)
const sumPrice = ref(0)
const addressIndex = ref(0)
const user = ref({})
user.value = JSON.parse(uni.getStorageSync('userInfo'));

onLoad((option) => {
	orderData.value = JSON.parse(option.data)
	// console.log(orderData.value)
	for(let item of orderData.value){
		sumCount.value ++
		sumPrice.value += item.sellerCount * item.sellerPrice
	}
	// console.log(user.value.address)
})

function handleBackClick() {
	uni.navigateBack()
}

function handleOrderClick() {
	
}

function getStatusBarHeight() {
	const res = uni.getSystemInfoSync()
	statusBarHeight = res.statusBarHeight
}

getStatusBarHeight()
</script>

<style scoped>
	
.order-header-box {
	position: fixed;
	z-index: 999;
	top: 0;
	width: 100%;
	background-color: #FFF;
}

.order-footer-box {
	position: fixed;
	z-index: 999;
	bottom: 0;
	width: 100%;
	background-color: #FFF;
	height: 150rpx;
}

.order-header {
	display: flex;
	align-items: center;
	height: 90rpx;
}

.order-button {
	background-color: #02B6FD;
	width: 600rpx;
	height: 70rpx;
	border-radius: 10rpx;
	display: flex;
	justify-content: center;
	align-items: center;
	color: #FFF;
}

.footer-bottom {
	display: flex;
	justify-content: center;
	align-items: center;
}

.order-body {
	margin-bottom: 150rpx;
	background-color: #c5d1da;
	height: 100vh;
}

.footer-top {
	display: flex;
	justify-content: space-between;
	padding: 0 30rpx;
}

.order-item {
	padding: 45rpx 30rpx;
	margin-bottom: 20rpx;
	display: flex;
	background-color: #FFF;
}

.seller-img {
	height: 200rpx;
	border-radius: 10rpx;
}

.seller-detail {
	display: flex;
	flex-direction: column;
	width: 70%;
	margin-left: 20rpx;
	justify-content: center;
}

.detail-bottom {
	display: flex;
	justify-content: space-between;
	align-items: center;
}

.seller-title {
	white-space: nowrap;
	overflow: hidden;
	text-overflow: ellipsis;
}

.seller-class {
	white-space: nowrap;
	overflow: hidden;
	text-overflow: ellipsis;
}

.chosen-address {
	background-color: #FFF;
	margin-bottom: 20rpx;
	display: flex;
	padding: 30rpx 20rpx;
}

.address-item {
	display: flex;
	flex-direction: column;
	flex: 1;
}
</style>