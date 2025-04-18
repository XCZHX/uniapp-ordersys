<template>
	<view class="container">
		<view :style="`height: ${statusBarHeight}px;`"></view>
		<view class="header">
			<view class="header-box">
				<uni-icons class="header-icon" type="back" size="35" @click="handleBackClick"></uni-icons>
				<view class="header-search" style="flex: 1;">
					
				</view>
				<uni-icons class="header-icon" type="cart" size="35" @click="handleCartClick"></uni-icons>
				<uni-icons class="header-icon" type="redo" size="35" @click=""></uni-icons>
			</view>
		</view>
		
		<view class="content">
			<image class="seller-img" :src="detailData.seller.img" mode="widthFix"></image>
			<view class="price-box">
				<text style="font-size: 30rpx;">￥</text>
				<text style="font-size: 45rpx; font-weight: bold;">{{detailData.seller.price/100}}</text>
			</view>
			<view class="title-box">
				<text>{{detailData.seller.title}}</text>
			</view>
			<view class="class-box" @click="handleClassBoxClick">
				<text class="class-box-text">已选: {{isClassItem.length>0?detailData.seller.sClass[isClassItem.indexOf(true)]:''}}</text>
				<uni-icons type="forward"></uni-icons>
			</view>
		</view>
		
		<view class="bottom-nav">
			<uni-goods-nav :fill=true @click="handleNavClick" :button-group="navButtonGroup" @buttonClick="handleNavButtonClick" />
		</view>
		
		<view>
			<!-- 加入购物车弹窗 -->
			<uni-popup type="bottom" ref="popup" background-color="#fff" @change="">
				<view class="popup-header">
					<uni-icons type="closeempty" size="27" @click="handleClosePopup"></uni-icons>
				</view>
				<view class="popup-content">
					<image class="seller-img-popup" :src="detailData.seller.img" mode="heightFix"></image>
					<view class="">
						<view class="price-box">
							<text style="font-size: 30rpx;">￥</text>
							<text style="font-size: 45rpx; font-weight: bold;">{{detailData.seller.price/100}}</text>
						</view>
						<view class="seller-count">
							<uni-number-box v-model="cnt" :min="1" :max="999"></uni-number-box>
						</view>
					</view>
				</view>
				<view class="seller-class">
					<text>分类 ({{isClassItem.length}})</text>
					<view class="seller-class-items">
						<view class="seller-class-item" :style="isClassItem[index]?isClass:''" v-for="(item,index) in detailData.seller.sClass" @click="handleItemClick(index)" >{{item}}</view>
					</view>
				</view>
				<view class="popup-footer">
					<button class="add-cart-button" @click="handleAddCartClick">加入购物车</button>
				</view>
			</uni-popup>
		</view>
	</view>
</template>

<script setup>
import { onLoad } from '@dcloudio/uni-app'
import { ref } from 'vue'
import request from '@/utils/request.js'

const sellerId = ref({})
const detailData = ref({
	seller: {
		img: ''
	}
})
const isClassItem = ref([])
let statusBarHeight = 0
const popup = ref(null)
const cnt = ref(1)

const isClass = 'border: 1rpx solid #02B6FD; color: #02B6FD; background-color: #e1f8ff'
const navButtonGroup = [
	{
		text: '加入购物车',
		backgroundColor: 'linear-gradient(90deg, #1E83FF, #0053B8)',
		color: '#fff'
	},
	{
		text: '立即购买',
		backgroundColor: 'linear-gradient(90deg, #60F3FF, #088FEB)',
		color: '#fff'
	}
]

onLoad((option) => {
	sellerId.value.id = option.id
	getSeller()
})

function getSeller(){
    request.post('/seller_select_byId', sellerId.value).then(res => {
        if(res.code === 200) {
            detailData.value.seller = res.data
            request.post('/user_select_byId', { id: detailData.value.seller.shopId}).then(res1 => {
                if(res1.code === 200) {
                    detailData.value.shop = res1.data
                } else {
                    ElMessage.error('查询失败')
                }
            })
            for (let i in detailData.value.seller.sClass) {
                if(i == 0){
                    isClassItem.value.push(true)
                }else {
                    isClassItem.value.push(false)
                }
            }
			// console.log(detailData.value)
        } else {
            ElMessage.error('查询失败')
        }
    })
}

function getStatusBarHeight() {
	const res = uni.getSystemInfoSync()
	statusBarHeight = res.statusBarHeight
}

function handleBackClick() {
	uni.navigateBack()
}

function handleCartClick() {
	uni.switchTab({
		url: '/pages/cart/index'
	})
}

function handleClassBoxClick() {
	popup.value.open()
}

function handleItemClick(index) {
	for (let i in detailData.value.seller.sClass) {
		if(i == index){
			isClassItem.value[i] = true
		}else {
			isClassItem.value[i] = false
		}
	}
}

function handleClosePopup() {
	popup.value.close()
}

function handleNavClick(e) {
	if(e.index == 0) {
		
	}else {
		uni.switchTab({
			url: '/pages/cart/index'
		})
	}
}

function handleNavButtonClick(e) {
	if(e.index == 0) {
		popup.value.open()
	}else {
		// 立即购买实现区
		// console.log(detailData.value)
		const selectData = ref([
			{
				sellerClass: detailData.value.seller.sClass[isClassItem.value.indexOf(true)],
				sellerCount: cnt.value,
				sellerId: detailData.value.seller._id,
				sellerImg: detailData.value.seller.img,
				sellerPrice: detailData.value.seller.price,
				sellerTitle: detailData.value.seller.title,
				shopId: detailData.value.shop._id,
				shopName: detailData.value.shop.name
			}
		])
		// console.log(selectData.value)
		uni.navigateTo({
			url: '/pages/order?data='+JSON.stringify(selectData.value)
		})
	}
}

function handleAddCartClick() {
	
}

getStatusBarHeight()

</script>

<style scoped>
.header {
	border-bottom: 1rpx solid #c3c3c3;
	display: flex;
	flex-direction: column;
	position: fixed;
	left: 0;
	right: 0;
	z-index: 100;
	background-color: #FFF;
	height: 80rpx;
}

.content {
	margin-top: 80rpx;
}

.header-box {
	display: flex;
}

.header-box .uni-icons {
	margin: 10rpx 20rpx;
}

.seller-img {
	width: 100%;
}

.bottom-nav {
	display: flex;
	flex-direction: column;
	position: fixed;
	left: 0;
	right: 0;
	bottom: 0;
}

.seller-class-items {
	display: flex;
	flex-wrap: wrap;
}

.seller-class-item {
	height: 60rpx;
	background-color: #eaeaea;
	margin: 10rpx;
	display: flex;
	justify-content: center;
	align-items: center;
	padding: 0 15rpx;
	border-radius: 5rpx;
}

.popup-footer {
	display: flex;
	justify-content: center;
	align-items: center;
}

.add-cart-button {
	width: 92%;
	margin: 25rpx 0;
	background-color: #02B6FD;
	color: #FFF;
}

.header-icon {
	/* background-color: rgba(0, 0, 0, 0.3); */
}

.price-box {
	margin-left: 15rpx;
	color: #02B6FD;
}

.title-box {
	margin: 20rpx 30rpx;
}

.class-box {
	width: 84%;
	justify-self: center;
	background-color: #f0f0f0;
	padding: 20rpx;
	border-radius: 5rpx;
}

.seller-count {
	margin: 20rpx;
}

.seller-class {
	margin: 20rpx;
}

.popup-content {
	margin: 20rpx;
	display: flex;
}

.seller-img-popup {
	height: 120rpx;
	margin: 0 20rpx;
	border-radius: 10rpx;
}

.class-box-text {
	white-space: nowrap;
	overflow: hidden;
	text-overflow: ellipsis;
}
</style>