<template>
	<view class="container">
		<view class="login-form">
			<uni-forms ref="loginForm" :rules="rules" :modelValue="loginFormData">
				<uni-forms-item label="账号" name="username">
					<uni-easyinput v-model="loginFormData.username" placeholder="请输入账号" />
				</uni-forms-item>
				<uni-forms-item label="密码" name="password">
					<uni-easyinput type="password" v-model="loginFormData.password" placeholder="请输入密码" />
				</uni-forms-item>
			</uni-forms>
			<button type="primary" @click="submit">登录</button>
		</view>
		<uni-popup ref="message" type="message">
			<uni-popup-message :type="msgType" :message="messageText" :duration="2000"></uni-popup-message>
		</uni-popup>
	</view>
</template>

<script setup>
import { ref } from 'vue'
import request from '@/utils/request.js'

const msgType = ref("success")
const messageText = ref("这是一条成功提示")
const loginForm = ref(null)
const message = ref(null)

const loginFormData = ref({
	username: '',
	password: '',
	role: 'CUS'
},)

const rules = ref({
	username: {
		rules: [{
			required: true,
			errorMessage: '账号不能为空'
		}]
	},
	password: {
		rules: [{
			required: true,
			errorMessage: '密码不能为空'
		}]
	}
})

function submit(){
	loginForm.value.validate().then(res => {
		// console.log('success', res)
		request.post('/login', loginFormData.value).then(res => {
			if(res.code === 200) {
				messageToggle('success', '登录成功')
				try {
				  uni.setStorageSync('userInfo', JSON.stringify(res.data));
				  console.log('同步存储成功！');
				} catch (err) {
				  console.error('同步存储失败：', err);
				}
				uni.switchTab({
					url: '/pages/index/index'
				})
			} else {
				messageToggle('error', res.msg)
			}
		})
		
	}).catch(err => {
		// console.log('err', err)
	})
}

function messageToggle(type, text) {
	msgType.value = type
	messageText.value = text
	message.value.open()
}

</script>

<style scoped>
.container {
	display: flex;
	align-items: center;
	justify-content: center;
	height: 90vh;
}
	
.login-form {
	width: 80%;
	background-color: rgba(0, 243, 255, 0.5);
	padding: 30rpx;
	border-radius: 20rpx;
}
</style>