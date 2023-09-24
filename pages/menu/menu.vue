<template>
	<view class="menu">
		<!--part01-商品类型导航条区域 start-->
		<view class="menu-type">
			<view class="type-list">
				<view class="list-items" v-for="(item,index) in typeList" :key="item.id" @click="getProductList(index,item.key,item.type)">
					<image class="items-picture" :src="item.isActive ? item.activeImg : item.img" mode="widthFix">
						
					</image>
					<view class="items-desc" :class="item.isActive ? 'active':'' ">{{item.typeDesc}}</view>
				</view>
			</view>
		</view>
		<!--part01-商品类型导航条区域 end-->
		
		<!--part02-商品类型对应的列表数据区域 start-->
		<view class="menu-products">
			列表数据
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				searchValue:"",//	搜索输入框的文本内容
				//	存放商品类型对应的图标
				typeList:[
					{
						img:'../../static/menuIcon/icons_01.png',
						activeImg:'../../static/menuIcon/icons_02.png',
						isActive:false,
						key:"isHot"
					},
					{
						img:'../../static/menuIcon/icons_03.png',
						activeImg:'../../static/menuIcon/icons_04.png',
						isActive:false,
						key:"type"
					},
					{
						img:'../../static/menuIcon/icons_05.png',
						activeImg:'../../static/menuIcon/icons_06.png',
						isActive:false,
						key:"type"
					},
					{
						img:'../../static/menuIcon/icons_07.png',
						activeImg:'../../static/menuIcon/icons_08.png',
						isActive:false,
						key:"type"
					},
					{
						img:'../../static/menuIcon/icons_09.png',
						activeImg:'../../static/menuIcon/icons_10.png',
						isActive:false,
						key:"type"
					},
				],
			
				//	定义一个属性		存放列表数据
				typeProductListData:[]
			};
			
		},
		onShow(){
			this.getType();
			this.getProductList(0,"isHot",1)
		},
		methods: {
			getType(){
					uni.request({
					url:"http://www.kangliuyong.com:10002/type",
					method:"GET",
					data:{
						appkey:"U2FsdGVkX19WSQ59Cg+Fj9jNZPxRC5y0xB1iV06BeNA=",
					},
					success:(res)=>{
						//console.log("根据type接口 获取4个商品类型数据=>",res);
						
						let hot = {
							id:0,
							typeDesc:"推荐",
							type:1
						}
						
						//	把新创建的hot对象数据 添加到数组的开头 通过unshift()方法
						res.data.result.unshift(hot);
						
						//	定义一个新数组
						let arr = [];
						
						//	再定义一个空对象 obj
						let obj = {};
						
						//	处理数据
						res.data.result.forEach((item,index)=>{
							obj = {
								...item,
								...this.typeList[index]
							}
							arr.push(obj)
							
						})
						this.typeList = arr;
						console.log(this.typeList)
					}
				})
			},
		
			getProductList(index,key,value){
				//	判断	若重复点击某个商品类型 不执行发起网络请求
				if(this.typeList[index].isActive){
					return	//终止后序代码的运行
				}
				
				uni.request({
					url:'http://www.kangliuyong.com:10002/typeProducts',
					method:"GET",
					data:{
						appkey:"U2FsdGVkX19WSQ59Cg+Fj9jNZPxRC5y0xB1iV06BeNA=",
						key,
						value
					},
					success:(res)=>{
						//console.log("res=>",res)
						
						this.typeProductListData = res.data.result;
						console.log("商品类型是：",value,"类型对应的数据列表是：",this.typeProductListData)
						
						this.typeList.forEach(item =>{
							item.isActive = false;
						})
						
						this.typeList[index].isActive = true;
					}
				})
			}
		}
	}
</script>

<style lang="less">
.menu {
	.menu-type {
		.type-list {
			display: flex;
			justify-content: space-evenly;
			align-items: center;
			height: 200rpx;
			border-bottom: 1px solid red;
			background-color: #fff;
			.list-items {
				.items-picture{
					width: 90rpx;
				}
				.items-desc{
					font-weight: bold;
					font-size: 32rpx;
					text-align: center;
				}
			}
		}
	}
}
</style>
