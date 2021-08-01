<template>
	<view class="detailPage">
		<view class="swiper">
			<swiper  :indicator-dots="indicatorDots" :autoplay="autoplay" :interval="interval" :duration="duration"
				style="width: 100%h;height: 20rem;">
				<swiper-item v-for="(item,index) in img" :key="index" style="width: 100%;height: 20rem;">
					<view class="uni-img" style="width: 100%;height: 20rem;">
						<image class="image" :src="item" mode="" style="width: 100%;height: 20rem;;"></image>
					</view>
				</swiper-item>
			</swiper>
		</view>
		<view style="width: 100%;background-color: white;">
			<view class="info">
				<view class="info-price" style="display: flex;justify-content: space-between;">
					<text style="font-size: 1.1rem;color: red;">¥{{info.price}}</text>
					<text style="font-size: 0.7rem;color: #555555;">{{info.buy_count}}人已购买</text>
				</view>
				<view class="info-intro">
					<view class="left-font">
						<view class="top-font">
							<text>{{info.name}}</text>
						</view>
						<view class="buttom-font">
							<text >{{info.brief}}</text>
						</view>
					</view>
					<view class="right-img">
						<image src="../../static/image/share.png" mode=""></image>
					</view>
				</view>
				<view class="rule">
					<text style="margin-right: 1.5rem;">规格</text>
					<text>{{info.product.spes_desc}}</text>
				</view>
			</view>
		</view>

		<view class="list" style="width: 100%;margin-top: 1rem;">
			<uni-segmented-control :current="current" :values="fonts" @clickItem="onClickItem" styleType="text"
				activeColor="#000"></uni-segmented-control>
			<view class="content">
				<view v-show="current === 0">
					<view v-html="intro">
					</view>
				</view>
				<view v-show="current === 1">
					<image style="width: 10rem;height: 10rem;margin: 5rem auto;display:block"
						src="../../static/image/order.png" mode=""></image>
				</view>
				<view v-show="current === 2">
					<image style="width: 10rem;height: 10rem;margin: 5rem auto;display:block"
						src="../../static/image/order.png" mode=""></image>
				</view>
			</view>

		</view>


		<view class="cart">
			<view class="customer">
				<image src="../../static/image/customerservice.png" mode=""></image>
				<text>客服</text>
			</view>
			<view class="car" @click="goCart">
				<image src="../../static/image/ic-car.png" mode=""></image>
				<text>购物车</text>
				<view class="cartCount" :class="this.statc==0 || this.token.length == 0?'unCartCount':''">
					<text style="font-size: 0.6rem;margin-bottom: 0.8rem;display:block;color: white;">{{statc}}</text>
				</view>
			</view>
			<view class="collect" @click="collects(id)">
				<image :src="imgSrc" mode="" ></image>
				<text>{{font}}</text>

			</view>
			<view class="addCart" @click="cart">
				<text>加入购物车</text>
			</view>
			
			<view class="buy" @click="buy">
				<text>立即购买</text>
			</view>
		</view>

		<!-- //加入购物车 -->
		<view :class="cartShow == false? 'unCartShow':'cartShow'">
			<view class="close" @click="close" style="z-index:99;">
				<image src="../../static/image/close.png" mode="" style="width: 100%;height: 100%;"></image>
			</view>
			<view class="top">
				<view class="top-img">
					<image :src="cartLists.image_path" mode=""
						style="width: 100%;height: 100%; border: 0.1rem solid white;"></image>
				</view>
				<view class="top-info">
					<view class="infos-name">
						<text>{{cartLists.name}}</text>
					</view>
					<view class="infos-price">
						<text>¥&nbsp;{{cartLists.price}}</text>
					</view>
					<view class="infos-count">
						<text>库存{{cartLists.stock}}{{info.unit}}</text>
					</view>
				</view>
			</view>
			<view class="color-rule" v-for="(item,index) in default_spes_desc" :key="index">
				<text style="margin-left: 1rem;margin-top: 1rem;font-size: 0.9rem;color: #8E8E8E;">{{index}}</text>
				<view class="leftBox" v-for="(ite,inde) in item" :key="inde" @click="changeColor(ite.product_id)"
					:class="ite.is_default?'unLeftBox':''">
					<text>{{inde}}</text>
				</view>
			</view>
			<view class="center" style="display: flex;">
				<text style="margin-left: 1rem;font-size: 0.9rem;color: #555555;">数量</text>
				<text style="margin-left: 1rem;" @click="reduct"
					:class="this.valNum == 1? 'fontGray':'fontBlack'">-</text>
				<input style="margin-left: 1rem;width: 1.5rem;margin-top: 0.9rem;text-align: center;font-size: 0.9rem;" v-model="valNum" type="number" maxlength="this.cartLists.stock">
				<text style="margin-left: 1rem;" @click="add">+</text>
			</view>
			<view class="buttom" @click="addGoCart()
			" v-show="!nullShow">
				<view>确定</view>
			</view>
			
			<view style="width: 100%;height:3rem;background-color: #d5d5d5;text-align: center;line-height: 3rem;color: #FFFFFF;" v-show="nullShow">
				<text>已售空</text>
			</view>
		</view>
		
		<!-- //立即支付-->
		<view :class="cartsShow == false? 'unCartShow':'cartShow'" >
			<view  @click="cartsShow = false"  class="close">
				<image src="../../static/image/close.png" mode="" style="width: 100%;height: 100%;"></image>
			</view>
			<view class="top">
				<view class="top-img">
					<image :src="cartLists.image_path" mode=""
						style="width: 100%;height: 100%; border: 0.1rem solid white;"></image>
				</view>
				<view class="top-info">
					<view class="infos-name">
						<text>{{cartLists.name}}</text>
					</view>
					<view class="infos-price">
						<text>¥{{cartLists.price}}</text>
					</view>
					<view class="infos-count">
						<text>库存{{cartLists.stock}}{{info.unit}}</text>
					</view>
				</view>
			</view>
			<view class="color-rule" v-for="(item,index) in default_spes_desc" :key="index">
				<text style="margin-left: 1rem;margin-top: 0.5rem;display: block;">{{index}}</text>
				<view class="leftBox" v-for="(ite,inde) in item" :key="inde" @click="changeColor(ite.product_id)"
					:class="ite.is_default?'unLeftBox':''">
					<text>{{inde}}</text>
				</view>
			</view>
			<view class="center" style="display: flex;">
				<text style="margin-left: 1rem;">数量</text>
				<text style="margin-left: 1rem;" @click="reduct"
					:class="this.valNum == 1? 'fontGray':'fontBlack'">-</text>
				<input style="margin-left: 1rem;width: 1.5rem;margin-top: 0.8rem;text-align: center;" v-model="valNum" type="number">
				<text style="margin-left: 1rem;" @click="add">+</text>
			</view>
			<view class="buttom"  @click="ok" v-show="!nullShow">
				<view>确定</view>
			</view>
			<view style="width: 100%;height:3rem;background-color: #d5d5d5;text-align: center;line-height: 3rem;color: #FFFFFF;" v-show="nullShow">
				<text>已售空</text>
			</view>
		</view>
	</view>
	</view>
</template>

<script>
	// import uniSegmentedControl from '../../components/uniSegmentedControl.vue'
	import {
		pageInfo,
		collect,
		cartCount
	} from '../../api/index.js'
	import {
		cartList,
		changes,
		buyCartList
	} from '../../api/list.js'
	import {addCart} from '../../api/cart.js'
	export default {
		// components: {
		// 	uniSegmentedControl
		// },
		data() {
			return {
				shopId:'',
				ids: '',
				// num:'',
				valNum: 1, //数量加减的值
				cols: [],
				default_spes_desc: [],
				cartShow: false,
				cartsShow:false,
				cartLists: [],
				show: true,
				current: 0,
				fonts: ["图文详情", "商品参数", "买家评论"],
				id: "",
				info: [], //用户信息
				intro: '',
				token: '', //token值
				img: [], //获取轮播图的图片
				indicatorDots: true,
				autoplay: true,
				interval: 3000,
				duration: 500,
				token: '',
				imgSrc:'../../static/image/ic-me-collect.png',
				font:'',
				rule:'',
				statc:0,
				cartCountShow:true,
				stock:'',
				list:[] ,
				nullShow:false,
				tanchuang:false
			}
		},


		onLoad(options) {
			// console.log(options)
			// console.log(options.id)
			this.id = options.id
			// this.getInfo()
			// this.makeTrue()
		},
		onShow() {
			this.getCartList()
			this.getInfo() 
			this.getCartCount()
		},
		methods: {
			//去购物车页面
			goCart(){
				uni.switchTab({
					url:'../cart/cart'
				})({
					
				})
			},
			//获取购物车商品数量
			getCartCount(){
				cartCount(this.token).then(res=>{
					// console.log(res)
					this.statc = res.data.data
					this.getCartCount()	
				})
			},
			//加入购物车
			addGoCart(){
				if(this.token) {
					if(this.tanchuang == false) {
						uni.showToast({
							title: '加载中',
							icon: 'loading',
						})
						setTimeout(function() {
							uni.showToast({
								title: '加入成功',
								icon: 'success',
							})
						}, 300);
						addCart(this.valNum,this.shopId,this.token).then(res=>{
							// console.log(this.val)
							// console.log(res)
							
						})
						this.cartShow = false
					}else {
						this.ok()
					}
				}else {
					uni.showToast({
						title: '登陆失败!',
						icon: 'none',
					})
					setTimeout(function() {
						uni.navigateTo({
							url:'../login/login'
						})
					}, 200);
				}
			},
			//切换样式
			changeColor(itemId) {
				// console.log(itemId)
				if (itemId) {
					this.token = uni.getStorageSync('token')
					changes(itemId, this.token).then(res => {
						// console.log(res)
						this.cartLists = res.data.data
						this.stock = this.cartLists.stock
						// console.log(this.stock)
						this.default_spes_desc = res.data.data.default_spes_desc
						this.shopId = itemId
						// console.log(this.shopId)
						
					})
				} else {
					return
				}
			},
			//去结算
			buy(){
				this.cartsShow = true //弹出弹窗
				this.tanchuang = true
				if(this.cartLists.stock == 0) {
					this.nullShow  = true
				}
			},
			
			//去结算里面的确定
			ok(){
				this.token = uni.getStorageSync('token')
				// console.log(this.shopId)
				if(!this.token) {
					uni.showToast({
						title: '请先登录',
						icon: 'loading',
					})
					setTimeout(function() {
						uni.navigateTo({
							url:'../login/login'
						})
					}, 300);
				}else {
					addCart(this.valNum,this.shopId,this.token).then(res=>{
						// console.log(this.shopId)
						// console.log(res)
						buyCartList(this.token).then(res=>{
							// console.log(res)
							
						    this.list = res.data.data.list
							this.list.forEach(item=>{
								if(this.shopId ==  item.product_id) {
									uni.navigateTo({
										url:'../cart/submitOrder?id=' + item.id + '?nums=' + this.val
									})
								}
							})
						})
					})
				}
			},
			//数量的增加
			add() {
				if (this.valNum >=1 || this.valNum <= this.stock ) {
					this.valNum ++
				}
			},
			//数量减少
			reduct() {
				if (this.valNum > 1) {
					this.valNum--
				}
			},
			//收藏
			collects(id) {
				this.token = uni.getStorageSync('token')
				if (this.token) {
					collect(this.token, id).then(res => {
						if (res.data.msg == '收藏成功') {
							uni.showToast({
								title: '加载中',
								icon: 'loading',
							})
							setTimeout(function() {
								uni.showToast({
									title: '收藏成功',
									icon: 'success',
								})
							}, 200);
							this.imgSrc = '../../static/image/ic-me-collect2.png'
							this.font = '已收藏'
						}else if (res.data.msg=='取消收藏成功') {
							uni.showToast({
								title: '加载中',
								icon: 'loading',
							})
							setTimeout(function() {
								uni.showToast({
									title: '取消收藏',
									icon: 'success',
								})
							}, 200);
							this.imgSrc = '../../static/image/ic-me-collect.png'
							this.font = '收藏'
						}
					})

				} else {
					uni.showToast({
						title: '收藏失败！请先登录',
						icon: 'loading'
					})
					setTimeout(function() {
						uni.navigateTo({
							url: '../login/login'
						})
					}, 1500);
					if(this.token) {
						uni.navigateTo({
							url:'../class/class'
						})
					}
					
				}

				


				// }else {
				// 	uni.showToast({
				// 		title: '收藏失败！请先登录',
				// 		icon: 'loading'
				// 	})
				// 	setTimeout(function () {
				// 	    uni.navigateTo({
				// 	    	url:'../login/login'
				// 	    })
				// 	}, 1500);

				// }
			},

			//tab切换内容
			onClickItem(e) {
				// console.log(e)
				if (this.current != e.currentIndex) {
					this.current = e.currentIndex
				}
			},


			//获取购物车的信息
			getCartList() {
				this.token = uni.getStorageSync('token')
				// console.log(this.token)
				cartList(this.token, this.id).then(res => {	
					// console.log(res)
					this.cartLists = res.data.data.product
					this.shopId = res.data.data.product.id
					this.default_spes_desc = res.data.data.product.default_spes_desc
					// console.log(this.default_spes_desc)
					this.cols = res.data.data.product.default_spes_desc
					// console.log(this.cols)
				})
			},

			//点击购物车显示弹出框
			cart() {
				this.cartShow = !this.cartShow
				if(this.cartLists.stock == 0) {
					this.nullShow  = true
				}
			},
			//关闭
			close() {
				this.cartShow = false
			},
			
			//关闭
			closePay(){
				this.cartShow = false
			},


			//详情页渲染
			getInfo() {
				this.token = uni.getStorageSync("token")
				// pageInfo({
				// 	id:this.$route.query.id,
				// 	token:this.token
				// }).then(res=>{
				// 	console.log(res.data.data)
				// })
				// console.log(this.id)
				pageInfo(this.id, this.token).then(res => {
					// console.log(this.$route.query.id)
					// console.log(res)
					this.img = res.data.data.album
					this.info = res.data.data
					this.intro = res.data.data.intro
					// console.log(this.info)
					if(this.info.isfav == 'true') {
						this.imgSrc = '../../static/image/ic-me-collect2.png'
						this.font = '已收藏'
					}else {
						this.imgSrc = '../../static/image/ic-me-collect.png'
						this.font = '收藏'
					}
				})
				

			},

		},

	}
</script>

<style scoped>
	.detailPage {
		height: 93vh;
		background-color: #F8F8F8;
		position: relative;
	}

	.swiper {
		width: 100%;
		height: 20rem;
		/* background-color: #007AFF; */
	}

	.info {
		width: 90%;
		/* height: 11rem; */
		/* background-color: pink; */
		margin: 0 auto;
		background-color: white;
	}

	.info-price {
		width: 100%;
		/* height: 3rem; */
		/* background-color: #2C405A; */
		line-height: 3rem;
		font-size: 0.8rem;
	}
	.buttom-font {
		font-size: 0.6rem;
		height: 2.5rem;
		color: #9D9D9F;
		margin-top: 0.2rem;
	}
	.info-intro {
		width: 100%;
		/* height: 5rem; */
		/* background-color: #3F536E; */
		display: flex;
		border-bottom: 1px solid #cfcfcf;
	}

	.info-intro .left-font {
		width: 85%;
		font-size: 0.9rem;
		/* height: 100%; */
		/* background-color: #EB642C; */
	}

	.info-intro .right-img {
		width: 15%;
		height: 100%;
		/* background-color: #007AFF; */
	}

	.info-intro .right-img image {
		width: 1.5rem;
		height: 1.5rem;
		margin-left: 1rem;
		margin-top: 1.5rem;
	}


	.rule {
		width: 100%;
		height: 3rem;
		/* background-color: #007AFF; */
		line-height: 3rem;
		/* margin-top: 1rem; */
		font-size: 0.8rem;
		color: #555555;
	}

	.control {
		/* background-color: #2C405A; */
		width: 100%;

	}

	.list {
		height: 100vh;
		background-color: white;

	}


	.cart {
		width: 100%;
		height: 3.2rem;
		background-color: #fff;
		position: fixed;
		bottom: 0;
		display: flex;

	}

	.customer {
		width: 15%;
		height: 100%;
		/* background-color: #333333; */
		text-align: center;
		margin-top: 0.2rem;
		font-size: 0.8rem;
		color: #555;
	}

	.customer image {
		width: 1.5rem;
		height: 1.5rem;
		display: block;
		margin: 0 auto;
		margin-top: 0.4rem;
	}

	.car {
		width: 15%;
		height: 100%;
		background-color: #FFFFFF;
		text-align: center;
		margin-top: 0.2rem;
		position: relative;
		font-size: 0.8rem;
		color: #555;
		z-index: 99;
	}

	.car image {
		width: 1.5rem;
		height: 1.5rem;
		display: block;
		margin: 0 auto;
		margin-top: 0.4rem;
	}

	.collect {
		width: 15%;
		height: 100%;
		/* background-color: #333333; */
		text-align: center;
		margin-top: 0.2rem;
		font-size: 0.8rem;
		color: #555;
	}

	.collect image {
		width: 1.5rem;
		height: 1.5rem;
		display: block;
		margin: 0 auto;
		margin-top: 0.4rem;
	}

	.addCart {
		width: 30%;
		height: 100%;
		background-color: #e5e5e5;
		color: black;
		text-align: center;
		/* line-height: 100%; */
		/* margin-top: 0.2rem; */
	}

	.addCart text {
		line-height: 3rem;
	}

	.buy {
		width: 30%;
		height: 100%;
		background-color: #111;
		color: #FFFFFF;
		text-align: center;
		/* line-height: 100%; */
		/* margin-top: 0.2rem; */
	}

	.buy text {
		line-height: 3rem;
	}





	.cartShow {
		width: 100%;
		/* height: 21rem; */
		background-color: #fff;
		position: fixed;
		bottom: 0;
		transition: height 0.1s linear;
		z-index: 1;
	}

	.color-rule {
		width: 100%;
		height: 3rem;
		/* background-color: #3F536E; */
		border-bottom: 0.1rem solid #dcdcdc; 
		display: flex;
		flex-wrap: wrap; 
		
	}

	.leftBox {
		width: 2.5rem;
		height: 1.5rem;
		border: 0.1rem solid  black;
		margin-left: 1rem;
		margin-top: 0.5rem;
		/* display: inline-block; */
		/* background-color: #000077; */
		margin-top: 0.7rem;
		/* font-size: 0.8rem; */
		text-align: center;
	}

	.unLeftBox {
		width: 2.5rem;
		height: 1.5rem;
		border: 0.1rem solid black;
		margin-left: 1rem;
		margin-top: 0.7rem;
		display: inline-block;
		color: white;
		background-color: black;
		
	}

	.leftBox text {
		font-size: 0.8rem;
		text-align: center;
		line-height: 1.5rem;
		text-align: center;
	}

	.rightBox {
		width: 2rem;
		height: 1.2rem;
		border: 0.1rem solid black;
		margin-left: 1rem;
		margin-top: 0.7rem;
		display: inline-block;
	}

	.unCartShow {
		display: none;
	}

	.top {
		width: 100%;
		height: 6rem;
		/* background-color: #007AFF; */
		position: relative;
		/* overflow: hidden; */
		border-bottom: 0.01rem solid #ADADAD;
	}

	.top-img {
		width: 5rem;
		height: 5rem;
		/* background-color: #EB642C; */
		position: absolute;
		bottom: 1.3rem;
		left: 1rem;
	}

	.top-info {
		width: 65%;
		height: 100%;
		/* background-color: #4CD964; */
		margin-left: 7rem;
		overflow: hidden;

	}

	.infos-name {
		width: 80%;
		height: 1.5rem;
		/* background-color: pink; */
		/* margin-top: 0.1rem; */
		overflow: hidden;
		text-overflow: ellipsis;
		white-space: nowrap;
		margin-top: 0.6rem;
		font-size: 0.8rem;
	}

	.infos-price {
		width: 100%;
		height: 1.5rem;
		/* background-color: #3F536E; */
		color:#FF5D7B;
		font-size: 0.9rem;
		padding-left: 0.5rem;
	}

	.infos-count {
		width: 100%;
		height: 1.5rem;
		/* background-color: #808080; */
		font-size: 0.8rem;
		color: #8c8c8c;
		margin-top: 0.2rem;
	}

	.buttom {
		width: 100%;
		border: none;
		height: 3rem;
		text-align: center;
		line-height: 3rem;
		color: white;
		background-color: #222222;
	}

	.center {
		width: 100%;
		height: 3rem;
		background-color: white;
		line-height: 3rem;
	}

	.close {
		/* position: absolute; */
		/* right: 0.5rem; */
		width: 1.2rem;
		height: 1.2rem;
		margin-left: 22rem;
		margin-top: 1rem;
		/* background-color: #007AFF; */
		/* top: 0.5rem; */
	}

	.fontGray {
		color: #9E9E9E;
	}

	.fontBlack {
		color: black;
	}
	
	.cartCount {
		/* width: 1.2rem; */
		height: 1.1rem;
		line-height: 1.1rem;
		background-color: red;
		border-radius: 3rem;
		position: absolute;
		top: 0;
		right: 0.5rem;
		text-align: center;
		padding: 0 0.4rem;
		box-sizing: border-box;
	}
	.unCartCount {
		display: none;
	}
</style>
