<script>
	export default {
		onLaunch: function() {
			try {
				const data = uni.getStorageSync('userInfo');
				// console.log(data);
				if(data == ''){
					uni.reLaunch({
						url: "/pages/login"
					})
				}
			} catch (err) {
				console.error('同步获取失败：', err);
			}
			console.log('App Launch')
		},
		onShow: function() {
			console.log('App Show')
		},
		onHide: function() {
			console.log('App Hide')
		}
	}
</script>

<style>
	/*每个页面公共css */
</style>
