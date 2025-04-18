<template>
	<view class="cart-header-box" :style="`padding-top: ${statusBarHeight}px;`">
		<view class="cart-header">
			<view class="cart-title">
				<text style="font-size: 40rpx;">购物车</text>
				<text style="font-size: 32rpx;">({{cartData.length}})</text>
			</view>
			
			<view v-if="!isManage" class="header-item-default">
				<uni-easyinput prefixIcon="search" class="search-input" v-model="searchInput" placeholder="搜索购物车商品" />
				<text style="font-size: 32rpx;" @click="isManage=!isManage">管理</text>
			</view>
			
			<view v-if="isManage" class="header-item">
				<text style="font-size: 32rpx;">批量清理</text>
				<view class="line">
				</view>
				<text style="font-size: 32rpx;" @click="isManage=!isManage">退出管理</text>
			</view>
		</view>
	</view>
	
	<view class="cart-body" :style="`margin-top: calc(${statusBarHeight}px + 80rpx);`">
		<view class="cart-item" v-for="(item,index) in cartData" :key="item">
			<uni-swipe-action>
				<uni-swipe-action-item :right-options="swipeOption">
					<view class="cart-item-in">
						<uni-icons style="margin-right: 15rpx;" size="30" :color="isSelect[index]?'#02B6FD':'#F9F9F9'" :type="isSelect[index]?'checkbox-filled':'circle'" @click="handleSelectClick(index)"></uni-icons>
						<view class="img-box">
							<image class="seller-img" :src="item.sellerImg" mode="heightFix" @click="handleItemClick(item.sellerId)"></image>
						</view>
						<view class="seller-detail">
							<text style="font-size: 30rpx; font-weight: bold;" @click="handleItemClick(item.sellerId)">{{item.sellerTitle}}</text>
							<text style="font-size: 25rpx; margin: 10rpx 0; color: #838383;" @click="handleItemClick(item.sellerId)">{{item.sellerClass}}</text>
							<uni-number-box style="margin: 10rpx 0;" :min="1" :max="999" :width="60" v-model="item.sellerCount" @change="handleCountChange"></uni-number-box>
							<view style="color: #02B6FD" class="price-box">
								<text style="font-size: 25rpx;">￥</text>
								<text style="font-size: 38rpx; font-weight: bold;">{{item.sellerPrice/100}}</text>
							</view>
						</view>
					</view>
				</uni-swipe-action-item>
			</uni-swipe-action>
		</view>
	</view>
	
	<view class="cart-footer-box">
		<view class="cart-footer">
			<view class="footer-left">
				<uni-icons style="margin-right: 15rpx;" size="30" :color="isAllSelect?'#02B6FD':'#F9F9F9'" :type="isAllSelect?'checkbox-filled':'circle'" @click="handleAllSelectClick"></uni-icons>
				<text style="font-size: 32rpx;">全选</text>
			</view>
			
			<view class="footer-right">
				<text v-show="sumCount>0?true:false" style="font-size: 28rpx;">已选{{sumCount}}件,</text>
				<text style="font-size: 28rpx;">合计:</text>
				<view style="color: #02B6FD" class="price-box">
					<text style="font-size: 25rpx;">￥</text>
					<text style="font-size: 38rpx; font-weight: bold;">{{sumPrice/100}}</text>
				</view>
				<button class="order-button" @click="handleOrderClick">结算</button>
			</view>
		</view>
	</view>
</template>

<script setup>
import { ref } from 'vue'
import request from '@/utils/request.js'
	
const cartData = ref([
	{
		sellerId: '67df00ac4f4fc0a28ab51361',
		sellerImg: 'https://objectstorageapi.hzh.sealos.run/kdubuo6i-sealaf-douumbmxo2-cloud-bin/ordersys/seller1.jpg',
		sellerTitle: '春光食品 海南特产冲饮速溶椰子粉 椰奶代餐早餐椰汁粉',
		sellerClass: '速溶椰子粉400g*2【量贩罐装】',
		sellerPrice: 2691,
		sellerCount: 1
	},
	{
		sellerId: '67df043f4f4fc0a28ab51362',
		sellerImg: 'https://objectstorageapi.hzh.sealos.run/kdubuo6i-sealaf-douumbmxo2-cloud-bin/ordersys/seller2.jpg',
		sellerTitle: '304不锈钢油炸锅家用带过滤网小炸串锅小型电磁炉专用炸炸壶2351',
		sellerClass: '胡桃木-油炸锅【1.6L】送夹子+导热板',
		sellerPrice: 4930,
		sellerCount: 2
	}
])

const swipeOption = [
	{
		text: '删除',
		style: {
			backgroundColor: '#e5434b'
		}
	}
]
const isSelect = ref([])
const sumPrice = ref(0)
const sumCount = ref(0)
const searchInput = ref('')
const isManage = ref(false)
const isAllSelect = ref(false)
let statusBarHeight = 0
const user = {
	_id: '67dc1ef65cc2b0b92f5fe2fc',
}

function getCarts(){
    request.post('/select_cart_byId', {id: user._id}).then(res => {
		if(res.code === 200) {
			// console.log(res.cart)
			cartData.value = res.cart
			for(let item of cartData.value){
				isSelect.value.push(false)
			}
		} else {
			ElMessage.error(res.msg)
		}
	})
}

function handleSelectClick(i) {
	sumPrice.value = 0
	sumCount.value = 0
	isSelect.value[i] = !isSelect.value[i]
	for(let i in isSelect.value){
		if(isSelect.value[i] === true){
			sumPrice.value += cartData.value[i].sellerCount * cartData.value[i].sellerPrice
			sumCount.value ++
		}
	}
	if(sumCount.value === cartData.value.length) isAllSelect.value = true
	if(sumCount.value !== cartData.value.length) isAllSelect.value = false
	// console.log(sumCount.value)
	// console.log(sumPrice.value)
}

function handleCountChange() {
	sumPrice.value = 0
	sumCount.value = 0
	for(let i in isSelect.value){
		if(isSelect.value[i] === true){
			sumPrice.value += cartData.value[i].sellerCount * cartData.value[i].sellerPrice
			sumCount.value ++
		}
	}
}

function handleItemClick(id) {
	uni.navigateTo({
		url: '/pages/detail?id='+id
	})
}

function handleAllSelectClick() {
	sumPrice.value = 0
	sumCount.value = 0
	isAllSelect.value = !isAllSelect.value
	for(let i in isSelect.value){
		isSelect.value[i] = isAllSelect.value
		if(isSelect.value[i] === true){
			sumPrice.value += cartData.value[i].sellerCount * cartData.value[i].sellerPrice
			sumCount.value ++
		}
	}
}

function handleOrderClick() {
	if(sumCount.value>0){
		const selectData = ref([])
		for(let index in isSelect.value){
			if(isSelect.value[index] === true){
				selectData.value.push(cartData.value[index])
			}
		}
		// console.log(selectData.value)
		uni.navigateTo({
			url: '/pages/order?data='+JSON.stringify(selectData.value)
		})
	}
}

function getStatusBarHeight() {
	const res = uni.getSystemInfoSync()
	statusBarHeight = res.statusBarHeight
}

getStatusBarHeight()
getCarts()

</script>

<style scoped>
.cart-body {
	display: flex;
	flex-direction: column;
	background-color: #dadada;
	/* margin-top: 80rpx; */
	padding-bottom: 100rpx;
}

.cart-item {
	margin-bottom: 10rpx;
}

.seller-detail {
	display: flex;
	flex-direction: column;
	flex: 1;
}

.img-box {
	height: 170rpx;
	width: 170rpx;
	margin-right: 20rpx;
}

.seller-img {
	height: 100%;
	border-radius: 10rpx;
}

.cart-item-in {
	display: flex;
	align-items: center;
	/* justify-content: center; */
	background-color: #FFF;
	padding: 10rpx 20rpx;
}

.cart-header {
	display: flex;
	margin: 0 20rpx;
	height: 80rpx;
}

.cart-footer {
	display: flex;
}

.cart-title {
	display: flex;
	align-items: center;
}

.header-item-default {
	display: flex;
	margin-left: auto;
	align-items: center;
	height: 80rpx;
}

.header-item {
	display: flex;
	margin-left: auto;
	align-items: center;
	height: 80rpx;
}

.line {
	background-color: #c8c8c8;
	width: 3rpx;
	height: 38rpx;
	margin: 0 20rpx;
}

.cart-footer {
	display: flex;
}

.search-input {
	margin-right: 30rpx;
}

.footer-left {
	display: flex;
	align-items: center;
	flex: 1;
}

.footer-right {
	display: flex;
	align-items: center;
}

.order-button {
	margin: 10rpx 10rpx;
	width: 200rpx;
	font-size: 32rpx;
	height: 80%;
	display: flex;
	align-items: center;
	justify-content: center;
	background-color: #02B6FD;
	color: #FFF;
}

.cart-header-box {
	position: fixed;
	z-index: 999;
	top: 0;
	width: 100%;
	background-color: #FFF;
}

.cart-footer-box {
	position: fixed;
	z-index: 999;
	bottom: 0;
	width: 100%;
	background-color: #FFF;
	/* 手机运行删除这一项 */
	/* margin-bottom: 100rpx; */
	height: 100rpx;
}
</style>