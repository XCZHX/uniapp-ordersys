<template>
	<view class="container">
		<view class="user-item">
			<view class="avatar-box">
				<image class="avatar" :src="user.avatar" mode="heightFix"></image>
			</view>
			<view class="username">{{user.name}}</view>
		</view>
		<view class="logout-button" @click="handleLogout">退出</view>
	</view>
</template>

<script setup>
import { ref } from 'vue'
const user = ref({})
user.value = JSON.parse(uni.getStorageSync('userInfo'));

function handleLogout(){
	try {
	  uni.removeStorageSync('userInfo');
	  console.log('同步移除成功！');
	  uni.reLaunch({
	  	url: '/pages/login'
	  })
	} catch (err) {
	  console.error('同步移除失败：', err);
	}
}

</script>

<style scoped>
.container {
	margin-top: 300rpx;
	display: flex;
	flex-direction: column;
	margin-left: 50rpx;
	margin-right: 50rpx;
}

.avatar {
	height: 200rpx;
	border-radius: 50%;
	border: 5rpx solid #02B6FD;
}

.logout-button {
	width: 200rpx;
	height: 60rpx;
	background-color: #ff6969;
	border-radius: 10rpx;
	color: #FFFFFF;
	display: flex;
	align-items: center;
	justify-content: center;
}
</style>