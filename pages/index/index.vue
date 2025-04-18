<template>
	<view class="container">
		<view class="header">
			<view :style="`margin-top: ${statusBarHeight}px;`"></view>
			<view class="search-box">
				<input class="search-input" v-model="getData.condition" type="text" placeholder="猜你喜欢"/>
				<button class="search-button" @click="handleSearchClick">搜 索</button>
			</view>
		</view>
		
		<view class="content" :style="`padding-top: calc(120rpx + ${statusBarHeight}px);`">
			<view class="seller-left wt-list">
				<view class="seller-item" v-for="(item,index) in LeftList" :key="index" @click="handleSellerClick(item._id)">
					<view class="seller-img">
						<image :src="item.img" mode="widthFix"></image>
					</view>
					<!-- 介绍部分 -->
					<view class="seller-detail">
						<text class="seller-title">{{item.title}}</text>
						<view class="seller-price">
							<text class="price-icon">￥</text>
							<text class="price-number">{{item.price/100}}</text>
						</view>
					</view>
				</view>
			</view>
			
			<view class="seller-right wt-list">
				<view class="seller-item" v-for="(item,index) in RightList" :key="index" @click="handleSellerClick(item._id)">
					<view class="seller-img">
						<image :src="item.img" mode="widthFix"></image>
					</view>
					<!-- 介绍部分 -->
					<view class="seller-detail">
						<text class="seller-title">{{item.title}}</text>
						<view class="seller-price">
							<text class="price-icon">￥</text>
							<text class="price-number">{{item.price/100}}</text>
						</view>
					</view>
				</view>
			</view>
		</view>
		<view class="content-bottom">
			<text>没有内容了</text>
		</view>
	</view>
</template>

<script setup>
import { ref } from 'vue';
import request from '@/utils/request.js'

const getData = ref({
    condition: ''
})
const sellerData = ref()
const LeftList = ref([])
const RightList = ref([])
let statusBarHeight = 0

function getSellers(){
    request.post('/seller_select', getData.value).then(res => {
        if(res.code === 200) {
            sellerData.value = res.data
        } else {
            ElMessage.error('查询失败')
        }
		// console.log(sellerData.value)
		setList()
    })
}

function setList(){
	LeftList.value = []
	RightList.value = []
	for(let i = 0; i < sellerData.value.length; i++){
		if(i%2 === 0) {
			LeftList.value.push(sellerData.value[i])
		}else{
			RightList.value.push(sellerData.value[i])
		}
	}
}

function getStatusBarHeight() {
	const res = uni.getSystemInfoSync()
	statusBarHeight = res.statusBarHeight
}

function handleSearchClick() {
	getSellers()
}

function handleSellerClick(id) {
	uni.navigateTo({
		url: '/pages/detail?id='+id
	})
}

getSellers()
getStatusBarHeight()

</script>

<style scoped>
.container {
	display: flex;
	flex-direction: column;
	/* align-items: center; */
	/* justify-content: center; */
}
	
.header {
	height: 180rpx;
	display: flex;
	flex-direction: column;
	align-items: center;
	justify-content: center;
	background-color: #02B6FD;
	position: fixed;
	top: 0;
	width: 100%;
	z-index: 999;
}	

.content {
	flex: 1;
	display: flex;
	align-items: center;
	justify-content: center;
	background-color: #F5F5F5;
	width: 100%;
	padding-bottom: 50rpx;
}

.search-box {
	display: flex;
	align-items: center;
	border-radius: 10rpx;
	margin: 20rpx;
	width: 90vw;
	height: 10vw;
	background-color: #FFF;
}

.search-input {
	flex: 1;
	margin: 0 20rpx;
	font-size: 27rpx;
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

.wt-list {
	display: flex;
	flex-direction: column;
	width: 50%;
	justify-content: center;
	align-items: center;
}

.seller-item {
	border-radius: 10rpx;
	background-color: #FFF;
	width: 95%;
	margin-top: 15rpx;
}

.seller-detail {
	padding: 15rpx;
}

.seller-title {
	display: block;
	font-size: 27rpx;
	overflow: hidden;
	white-space: nowrap;
	text-overflow: ellipsis;
}

.seller-price {
	/* font-size: 35rpx; */
	color: #02B6FD;
}

.price-icon {
	font-size: 20rpx;
	font-weight: bold;
}

.price-number {
	font-size: 37rpx;
	font-weight: bold;
}

.seller-img {
	width: 100%;
}

.seller-img image{
	width: 100%;
	border-radius: 10rpx;
}

.content-bottom {
	padding-bottom: 100rpx;
	background-color: #F5F5F5;
}

.uni-tab-bar {
	height: 50rpx !important;
}
</style>
