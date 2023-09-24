<template>
	<view class="login">
		<!-- 模块1-头部逛一逛区域 -->
		<view class="login-head">
			<view class="head-left">
				<image class="left-logo" src="../../static/logo/狗卡.png" mode="widthFix"></image>
				<text class="left-name">GouKa Coffee</text>
			</view>
			<view class="head-right" @click="GoTabBar">先逛一逛~</view>
		</view>
		
		<!-- 模块2-欢迎回来区域-->
		<view class="login-welcome">
			<view class="welcome-title">欢迎回来！</view>
			<view class="welcome-desc">Please login to your acounts</view>
		</view>
		
		<!-- 登录区域 -->
		<uni-section title="" class="login-container">
			<uni-forms class="login-container-form" ref="login" :modelValue="login" :rules="rules">
				<uni-forms-item required label="手机号码:" name="phone">
					<uni-easyinput v-model="login.phone" maxlength="11" placeholder="请输入11位数字号码" type="number"/>
				</uni-forms-item>
				<uni-forms-item required label="用户号码:" name="password">
					<uni-easyinput v-model="login.password" type="password" placeholder="请输入指定格式的密码"/>
				</uni-forms-item>
			</uni-forms>
		
		
			<!-- 2.0忘记密码按钮 -->
			<view class="is-forget-password">忘记密码？</view>
			
			<!-- 3.0登录按钮区域 -->
			<button @click="log('login')" class="login-btns btns-items" type="primary">登录</button>
			
			<!-- 4.0注册按钮区域 -->
			<button @click="open" class="register-btns btns-items" type="primary">注册</button>
			
			<!-- 模块4-包含注册表单在内-->
			<uni-section title="" class="register-container">
				<uni-popup ref='popup' background-color="#fff">
					
					<!-- 上部分 -->
					<view class="popup-head">
						<view class="popup-head-title">注册</view>
						<uni-icons type="closeempty" size="30" @click="close"></uni-icons>
					</view>
					
					<!-- 下部分 -->
					<uni-forms class="popup-register-container-form" ref="register" :modelValue="register" :rules="rules">
						<uni-forms-item label="手机号码:" name="phone">
							<uni-easyinput v-model="register.phone" maxlength="11" placeholder="请输入11位数字号码" type="number"/>
						</uni-forms-item>
						<uni-forms-item label="用户密码:" name="password">
							<uni-easyinput v-model="register.password" type="password" placeholder="请输入指定格式的密码"/>
						</uni-forms-item>
						<uni-forms-item label="用户昵称:" name="nickName">
							<uni-easyinput v-model="register.nickName" placeholder="请输入指定格式的昵称"/>
						</uni-forms-item>
					</uni-forms>
					
					<button @click="submitRegister('register')" class="popup-btns btns-items" type="primary">开始注册</button>
				</uni-popup>
			</uni-section>
			
		</uni-section>	
	</view>
</template>

<script>
	export default {
		data() {	
			return {
				//登录校验表单数据
				login:{
					phone:"",
					password:"",
				},
				//注册校验表单数据
				register:{
					phone:'',
					password:'',
					nickName:'',
				},
				
				//校验规则
				rules:{
					phone:{
						rules:[
							{
								required:true,
								errorMessage:'手机号不能为空',
								
							},
							{
								format:'number',
								errorMessage:'手机号码只能输入数字'
							}
						]
					},
					password:{
						rules:[{
							required:true,
							errorMessage:'密码不能为空'
						}]
					},
					nickName:{
						rules:[{
							required:true,
							errorMessage:'昵称不能为空'
						}]
					}
				}
			};
		},
		methods:{
			open() {
				this.$refs.popup.open('bottom')
			},
			close(){
				this.$refs.popup.close()
			},
			GoTabBar(){
				uni.switchTab({
					url:'/pages/home/home'
				})
			},
			//校验数值
			regFunction(str,reg,err){
				let isSucc = reg.test(str);
				let desc = "你输入" + err + "的格式不正确";
				
				return new Promise((resolve) =>{
					if(!isSucc){
						uni.showToast({
							title:desc
						})
					}else{
						resolve();
					}
				})
			},
			
			//注册功能
			submitRegister(ref){
				this.$refs[ref].validate().then(res=>{
					//手机号码验证
					this.regFunction(res.phone,/^1[3-9]\d{9}$/,"手机号码").then(()=>{
						//密码验证
						this.regFunction(res.password,/^[A-Za-z][A-Za-z\d]{5,15}$/,"手机密码").then(()=>{
							//昵称验证
							this.regFunction(res.nickName,/^[A-Za-z][A-Za-z\d]{1,5}$/,"用户昵称").then(()=>{
								//发起网络请求
								uni.request({
									url:"http://www.kangliuyong.com:10002/register",
									method:"POST",		//请求类型
									header:{
										"Content-type":"application/x-www-form-urlencoded;charset=utf-8"
										},
									data:{		//请求参数
										appkey:'U2FsdGVkX19WSQ59Cg+Fj9jNZPxRC5y0xB1iV06BeNA=',
										nickName:res.nickName,
										password:res.password,
										phone:res.phone
									},
									success:(res)=>{
										console.log("res===>",res)
										uni.showToast({
											icon:res.data.code === 100?'success':'error',
											title:res.data.msg
										})
										//注册成功后，使用for in 语句遍历register 清空表单输入文本框内容
										for(let key in this.register){
											this.register[key] = '';
										}
										this.close();
									},
								})
							})
						})
					})
				})
			},
			
			//登录功能（登录成功会返回一个token值）
			log(ref){
				this.$refs[ref].validate().then(res=>{
					this.regFunction(res.phone,/^1[3-9]\d{9}$/,"手机号码").then(()=>{
						this.regFunction(res.password,/^[A-Za-z][A-Za-z\d]{5,15}$/,"手机密码").then(()=>{
							uni.request({
								url:"http://www.kangliuyong.com:10002/login",
								method:"POST",		//请求类型
								header:{
									"Content-type":"application/x-www-form-urlencoded;charset=utf-8"
									},
								data:{		//请求参数
									appkey:'U2FsdGVkX19WSQ59Cg+Fj9jNZPxRC5y0xB1iV06BeNA=',
									password:res.password,
									phone:res.phone
								},
								success:(res)=>{
									
									//code为201时，未注册登录失败
									if(res.data.code === 201){
										uni.showToast({
											title:res.data.msg,
											icon:"error",
										})
									}
									//code为202时，手机号码or密码不正确，登录失败
									if(res.data.code === 202){
										uni.showToast({
											title:res.data.msg,
											icon:"error",
										})
									}
									//code为200时，手机号码or密码都正确，登录成功 success
									if(res.data.code === 200){
										uni.showToast({
											title:res.data.msg,
											icon:"success"
										})
									}
									
									for(let key in this.login){
										this.login["password"] = "";//	密码框清空
									}
									
									console.log("登录页面——打印token字符串",res.data.token);
									uni.setStorageSync("token",res.data.token);
									
									//时隔2s后,跳转到指定页面
									setTimeout(()=>{
										uni.switchTab({
											url:"/pages/my/my"
										})
									},2000)
								},
							})
						})
					})
				})
			}
		},
	}
</script>

<style lang="less">
	.login{
		min-width: 640rpx;
		//calc() 动态计算长度值 任意单位
		height: calc(100vh - 88rpx);
		padding:0 30rpx;
		background-color: #fff;
		
		//逛一逛区域
		.login-head{
			display: flex;
			justify-content: space-between;
			align-items: center;
			padding: 20rpx 0;
			font-weight: bold;
			font-size: 32rpx;
			border-top: 1rpx solid #eee;
			border-bottom: 1rpx solid #eee;
			background-color: #fff;
			.head-left{
				
				display: flex;
				align-items: center;
				
				.left-logo{
					width: 100rpx;
					
				}
				
				.left-name{
					color: #646566;
				}
			}
			
			.head-right{
				color:#0c34ba
			}
		}
	}
	
	//欢迎回来
	.login-welcome{
		padding: 150rpx 0 100rpx;
		
		.welcome-title{
			font-weight: bold;
			font-size: 50rpx;
			color: #646566;
		}
		
		.welcome-desc{
			margin-top: 20rpx;
			color: #9fa2b6;
		}
	}
	
	//登录表单区域
	.login-container{
		
		//深度选择器		::v-deep 去更改组件的自带样式
		::v-deep .uni-forms-item__label{
			width: 165rpx !important;
			
		}
	
		
		.is-forget-password{
			display:flex;
			justify-content: flex-end;
			color: #646566;
			font-size: 24rpx;
		}
		
		.btns-items{
			display: flex;
			justify-content: center;
			align-items: center;
			width: 80%;
			height: 80rpx;
			border-radius: 40rpx;
			font-size: 34rpx;
			background-color: #fff;
			border: 1rpx solid #aaa;
		}
		
		.login-btns{
			margin: 60rpx auto 40rpx;
			background-color: #0c34ba;
		}
		
		.register-btns{
			color: #646566;
		}
		
		//注册表单区域
		.register-container{
			::v-deep .uni-popup__wrapper{
				padding: 30rpx;
			}
			.popup-btns{
				display: flex;
				justify-content: center;
				align-items: center;
				width: 90%;
				height: 80rpx;
				border-radius: 20rpx;
				font-size: 34rpx;
				background-color: #0c34ba;
				border: 1rpx solid #aaa;
			}
			.popup-head{
				height: 80rpx;
				display: flex;
				justify-content: space-between;
				align-items: center;
				.popup-head-title{
					font-weight: bold;
					font-size: 50rpx;
				}
			}
			.register-container-form{
				padding: 30rpx 0;
			}
		}
	}
	
</style>
