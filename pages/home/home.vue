<template>
	<view class="container">
		
		<!--01-页面头部区域 start-->
		<view class="head">
			<view class="head-left">
				<view style="color: gray;">{{ hourText }}</view>
				<view style="color: blue;">{{ nickName }}</view>
			</view>
			<uni-search-bar class="head-right" v-model="searchValue" radius="18" cancel-button="none">
				
			</uni-search-bar>
		</view>
		
		<!--02-轮播图区域 start-->
		<swiper class="swiper-container" :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" circular="true">
			<swiper-item v-for="item in bannerImgs" :key="item.pid">
				<image class="item-picture" :src="item.bannerImg"></image>
				<view class="item-name"> {{ item.name }} </view>
			</swiper-item>
		</swiper>
		
		<!--03-热卖推荐 start-->
		<view class="recommend">
			<view class="recommend1">热卖推荐</view>
		</view>
	
		<!--04-热卖推荐咖啡 start-->
		<view class="goodCoffeeList">
			<view class="goodCoffeeListX" v-for="item in goodCoffeeList" :key="item.id">
				<view class="hot">hot</view>
				<image :src="item.largeImg">
					
				</image>
				<view class="name">{{item.name}}</view>
				<view class="enname">{{item.enname}}</view>
				<view class="price">￥{{item.price}}</view>
			</view>
		</view>


	</view>
</template>

<script>
	export default {
		data() {
			return {
				//	第一步：先定义一个属性 存放小时形容词	初始值为空
				hourText:"",
				nickName:"",	//定义存放用户昵称的属性
				searchValue:"",//	搜索输入框的文本内容
				
				bannerImgs:[],
				mode:'default',
				current:0,
				
				goodCoffeeList:[],
			};
		},
		// 使用onShow()调用函数1 ———— 
		onShow(){
			this.getHourTimes();
			
			this.getNickName();
			
			this.getBanner();
			
			this.getCoffee();
		},
		methods:{
			getHourTimes(){
				//	获取当前的小时时间
				let times = new Date().getHours();
				//console.log("当前为",times)
				
				//	判断时间
				if(times >=6 && times<10){			//6-10点	显示上午好
					this.hourText = '上午好'
				}else if(times >=10 && times<14){	//10-14点	显示中午好
					this.hourText = '中午好'
				}else if(times >=14 && times<18){	//14-18点	显示下午好
					this.hourText = '下午好'
				}else if(times >=18 && times<24){	//18-24点	显示晚上好
					this.hourText = '晚上好'
				}else{
					this.hourText = '通宵好'			//0-6点	显示通宵好
				}
			},
			
			getNickName(){
				//先判断用户是否登录
				let tokenString = uni.getStorageSync("token")
				
				uni.request({
					url:"http://www.kangliuyong.com:10002/findMy",
					method:"GET",
					data:{
						appkey:"U2FsdGVkX19WSQ59Cg+Fj9jNZPxRC5y0xB1iV06BeNA=",
						tokenString
					},
					success: (res)=> {
						//console.log("red==>",res);
						this.nickName = res.data.result[0].nickName;
					}
				})
			},
			
			getBanner(){
				uni.request({
					url:"http://www.kangliuyong.com:10002/banner",
					data:{
						appkey:"U2FsdGVkX19WSQ59Cg+Fj9jNZPxRC5y0xB1iV06BeNA="
					},
					success: (res)=> {
						//console.log("拿到res数据=>",res)
						//console.log("拿到轮播图数据=>", res.data.result);
						
						//给bannerImg属性 重新赋值
						this.bannerImgs = res.data.result;
					}
				})
			},
			
			getCoffee(){
				uni.request({
					url:"http://www.kangliuyong.com:10002/typeProducts",
					method:"GET",
					data:{
						appkey:"U2FsdGVkX19WSQ59Cg+Fj9jNZPxRC5y0xB1iV06BeNA=",
						key:'isHot',
						value:'1',
					},
					success:(res)=> {
						console.log(res.data);
						this.goodCoffeeList = res.data.result
					}
				})
			},
			
			change(e) {
				this.current = e.detail.current;
			}
		}
	}
</script>

<style lang="less">
	//	头部
	.head{
		padding: 0 20rpx ;
		display: flex;
		justify-content: space-between;
		align-items: center;
		background-color: #fff;
		.head-left{
			font-weight: bold;
			display: flex;
			view:nth-child(1){
				min-width: 84rpx;
				color: #646566;
				margin-right: 15rpx;
			}
			view:nth-child(2){
				width: 100rpx;
				color: #0c34ba;
				//	设置文本不换行
				white-space: nowrap;
				//	设置隐藏溢出的文本内容
				overflow: hidden;
				//	把溢出文本的内容转为省略号
				text-overflow: ellipsis;
			}
		}
		.head-right{
			flex:1;
		}
		
	}
		
	//	轮播图
	.swiper-container{
		position: relative;
		height: 564rpx;
		padding: 20rpx;
		
		.item-picture{
			width: 100%;
			height: 100%;
			border-radius: 20rpx;
		}
		
		.item-name{
			position:absolute;
			left: 20rpx;
			bottom: 20rpx;
			padding: 10rpx 28rpx;
			background-color: #0c34ba;
			font-size: 28rpx;
			color: #fff;
			border-radius: 32rpx;
		}
		
		//	使用深度选择器
		::v-deep .uni-swiper-dots-horizontal {
			left: 80%;
			
			//	所有未显示的图片对应的导航点
			.uni-swiper-dot {
				width: 50rpx;
				border-radius: 8rpx;
				background-color: #646566;
			}
			//	当前图片对应的导航点
			.uni-swiper-dot-active {
				background-color: #0c34ba;
			}
		}
	}
	
	//	热卖推荐
	.recommend{
		width: 95%;
		height: 120rpx;
		margin: 0 20rpx;
		background-color: #fff;
		display: flex;
		justify-content: space-between;
		align-items: center;
		.recommend1{
			display: flex;
			justify-content: space-evenly;
			align-items: center;
			width: 32%;
			height: 80rpx;
			background-color: #0c34ba;
			border-radius: 0 40rpx 0 0;
			font-size: 32rpx;
			color: #fff;
		}
	}

	//	热卖推荐咖啡列表
	.goodCoffeeList{
		display: flex;
		justify-content: space-evenly;
		
		flex-direction:row;/* 设置方向*/
		flex-wrap:wrap; /*换行设置*/
		
		.goodCoffeeListX{
			margin-top: 30rpx;
			position: relative;
			image{
				border-radius: 10px;
				width: 320rpx;
				height: 240rpx;
			}
			.hot{
				z-index: 1;
				width: 50rpx; 
				height: 40rpx; 
				background-color: #0c34ba; 
				color:#fff;
				position: absolute;
				left:20rpx
			}
			.name{
				font-size: 30rpx
			}
			.enname{
				color: #646566;
				width: 320rpx;
				font-size: 8rpx;
				overflow: hidden;
			}
			.price{
				width: 320rpx;
				font-size: 32rpx;
				font-weight: bold;
				color: #0c34ba;
			}
		}
		
	}
</style>