<template>
	<view class="content">
		<view v-if="list.length !==0" class="todo-header">
			<view class="todo-header_left">
				<text class="active-text">{{text}}</text>
				<text>{{listData.length}}条</text>
			</view>
			<!-- 状态栏右侧 -->
			<view class="todo-header_right">
				<view class="todo-header_right-item" :class="{'active-tab' :activeIndex === 0}"@click="tab(0)">全部</view>
				<view class="todo-header_right-item" :class="{'active-tab' :activeIndex === 1}" @click="tab(1)">待办</view>
				<view class="todo-header_right-item" :class="{'active-tab' :activeIndex === 2}"@click="tab(2)">已完成</view>
			</view>
		</view>
		<!-- 没有数据 -->
		
		<view v-if="list.length === 0" class="default">
			<view class="image-default">
				<image src="../../static/default.png" mode="aspectFit"></image>
			</view>
			<view class="default-info">
				<view class="defalut-info_text">你还没有创建任何待办事项</view>
				<view class="defalut-info_text">点击下方加号创建一个吧</view>
			</view>
		</view>
		<!-- 内容 -->
		<view v-else class="todo-content">
			<view class="todo-list" :class="{'todo--finish':item.checked}" v-for="(item,index) in listData" :key="index" @click="finish(item.id)">
				<view class="todo-list_checkbox">
					<view class="checkbox">
						
					</view>
				</view>
				<view class="todo-list_content">{{item.content}}</view>
			</view>
		</view>
		<!-- 创建按钮 -->
		<view class="create-todo" v-on:click="create">
			<text class="iconfont icon-add" :class="{'create-todo-active':textShow}"></text>
		</view>
		<!-- 输入框 -->
		<view v-if="active" class="create-content" :class="{'create-show': textShow}">
			<!-- input输入 -->
			<view class="create-input">
				<input type="text" v-model="value" placeholder="请输入您要创建的todo" />
			</view>
			<!-- 发布按钮 -->
			<view class="create-button" @click="submit">
				创建
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				value:'',
				list:[],
				active:false,
				activeIndex:0,
				text:'全部',
				textShow:false
			}
		},
		onLoad() {

		},
		computed:{
			listData() {
				let list = JSON.parse(JSON.stringify(this.list))
				let newList = []
				//点击全部
				this.text = "全部"
				if(this.activeIndex === 0) {
					return list
				}
				//点击待办
				if(this.activeIndex === 1) {
					// checked = false
					
					list.forEach((item) => {
						if(!item.checked) {
							newList.push(item)
						}
					})
					this.text = "待办"
					return newList
				}
				//点击已完成
				if(this.activeIndex === 2) {
					// checked = true
					list.forEach((item) => {
						if(item.checked) {
							newList.push(item)
						}
					})
					this.text = "已完成"
					return newList
				}
			}
		},
		methods: {
			create() {
				if (this.active) {
					this.close()
				} else {
					this.open()
				}
			},
			open() {
					this.active = true
					this.$nextTick(() => {
						setTimeout(() => {
							this.textShow = true
						}, 50)
					})
				},
				// 关闭动画
				close() {
					this.textShow = false
					this.$nextTick(()=>{
						setTimeout(() => {
							this.active = false
						}, 350)
					})
				},
			submit() {
				console.log(this.value)
				if(this.value === "") {
					uni.showToast({
						title:'请输入内容',
						icon:"none"
					})
					return
				}
				this.list.unshift({
					id:'id' + new Date().getTime(),
					content:this.value,
					checked:false
				})
				this.value = ''
				this.close()
			},
			finish(id) {
				let index = this.list.findIndex((item) => item.id === id)
				this.list[index].checked = !this.list[index].checked
			},
			tab(index) {
				this.activeIndex = index
			}
		}
	}
</script>

<style lang="scss">
	@import '../../common/icon.css';
	.default{
		padding-top: 100px;
		.image-default{
			display: flex;
			justify-content: center;
			image{
				width: 100%;
			}
		}
		.default-info{
			text-align: center;
			font-size: 14px;
			color: #ccc;
		}
	}
	.todo-header{
		box-sizing: border-box;
		z-index: 10;
		position: fixed;
		top: 0;
		left: 0;
		padding: 0 15px;
		display: flex;
		align-items: center;
		justify-content: space-between;
		font-size: 12px;
		width: 100%;
		color: #333;
		height: 45px;
		background-color: #FFFFFF;
		box-shadow: -1px 1px 5px 0 rgba(0,0,0,0.1);
		.todo-header_left{
			.active-text{
				font-size: 14px;
				color: #279abf;
				padding-right: 10px;
			}
		}
		.todo-header_right{
			display: flex;
			.todo-header_right-item{
				padding: 0 5px;
				&.active-tab{
					color: #279abf;
				}
			}
		}
	}
	.todo-content{
		padding-top: 50px;
		padding-bottom: 100px;
		position: relative;
		.todo-list{
			position: relative;
			display: flex;
			align-items: center;
			padding:15px;
			margin: 15px;
			border-radius: 10px;
			color: #0c3854;
			font-size: 14px;
			background-color: #cfebfd;
			box-shadow: -1px 1px 5px 1px rgba(0,0,0,0.1), -1px 2px 1px 0px rgba(255,255,255,100) inset;
			overflow: hidden;
			&.todo--finish{
				.todo-list_checkbox{
					.checkbox{
						color: #eee;
						position: relative;
						&:after{
							content:'';
							position:absolute;
							width: 8px;
							height: 8px;
							top: 0;
							left: 0;
							right: 0;
							bottom: 0;
							margin: auto;
							background:black;
							border-radius: 10px;
						}
					}
				}
				.todo-list_content{
					
					color: #999999;
				}
				&::before{
					content: '';
					position: absolute;
					top: 0;
					bottom: 0;
					height: 2px;
					left: 40px;
					right: 10px;
					margin: auto 0;
					background-color: #bdcdd8;
					
				}
				&:after{
					position: absolute;
					content: "";
					top: 0;
					bottom: 0;
					left: 0;
					background-color: #bdcdd8;
					width: 5px;
				}
				
			}
			
			
			&:after{
				position: absolute;
				content: "";
				top: 0;
				bottom: 0;
				left: 0;
				background-color: #91d1e8;
				width: 5px;
			}
		}
		.todo-list_checkbox{
			padding-right:15px;
			.checkbox{
				width: 15px;
				height: 15px;
				border-radius: 50%;
				background: #FFFFFF;
				box-shadow: 0 0 5px 1px rgba(0, 0, 0, 0.1);
				z-index: 10;
			}
		}
	}
	.create-todo{
		display: flex;
		justify-content: center;
		align-items: center;
		position: fixed;
		bottom: 20px;
		left: 0;
		right: 0;
		width: 50px;
		height: 50px;
		border-radius: 50%;
		background-color: #deeff5;
		margin: 0 auto;
		box-shadow: -1px 1px 5px 2px rgba(0,0,0,0.1),-1px 1px 1px 0 rgba(255,255,255,100) inset;
		.icon-add{
			font-size: 30px;
			color: #add8e6; 
		}
	}
	.create-content{
		display: flex;
		align-items: center;
		position: fixed;
		bottom: 95px;
		left: 20px;
		right: 20px;
		padding:0 15px;
		border-radius: 50px;
		background-color: #deeff5;
		box-shadow: -1px 1px 5px 2px rgba(0,0,0,0.1);
		transition: all 0.3s;
		opacity: 0;
		transform: scale(0) translateY(200%);
		&.create-show{
			opacity: 1;
			transform: scale(1) translateY(0);
		}
		.create-input{
			width: 100%;
			padding-right:15px;
		}
		.create-button{
			display: flex;
			justify-content: center;
			align-items: center;
			flex-shrink: 0;
			width: 80px;
			height: 50px;
			border-radius: 50px;
			font-size: 16px;
			color: #88d4ec;
			box-shadow: -2px 0px 2px 1px rgba(0,0,0,0.1);
			&:after{
				content:'';
				position: absolute;
				right: 0;
				left: 0;
				bottom: -8px;
				width: 20px;
				height: 20px;
				background: #deeff5;
				margin: 0 auto;
				transform: rotate(45deg);
				z-index: -1;
			}
			
		}
	}
	.icon-add{
		transition: transform 0.3s;
		&.create-todo-active{
			transform: rotate(135deg);
		}
	}
	
</style>
