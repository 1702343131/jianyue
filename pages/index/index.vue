<template>
	<view class="container">
		<view class="article-box">
			<view class="article" v-for="(article,index) in articles" :key="index">
				
				<!-- 大于等于三张图片的显示方式 -->
				<view class="text-box three" v-if="article.imgs.length >= 3">
				<!-- 标题 -->
				<view class="articletitle">
				<text class="article-title" @tap="gotoDetail(article.id)">{{article.title}}</text>
					</view>
					<view class="thumbnail-box">
						<view class="thumbnail-item" v-for="(item,index1) in article.imgs" :key="index1">
							<image :src="item.imgUrl">
							</image>
						</view>
					</view>
				</view>
				<!-- 小于三张图片的显示方式 -->
				<view class="text-box one" v-else-if="article.imgs.length >= 1">
				<view class="articletitle">
				<text class="article-title" @tap="gotoDetail(article.id)">{{article.title}}</text></br>
				</view>
					<view class="text-img-box">
						<view class="left">
							<!-- 标题 -->
							
							<text class="article-content" >{{article.content}}...</text>
						</view>
						<view class="right">
							<image :src="article.imgs[article.imgs.length - 1].imgUrl"></image>
						</view>
					</view>
				</view>
				<!-- 没有图片的显示方式 -->
				<view class="text-box" v-else>
		
					<!-- 标题 -->
					<view class="articletitle">
					<text class="article-title" @tap="gotoDetail(article.id)">{{article.title}}</text></br>
					</view>
					<text class="article-content">{{article.content}}...</text>
				</view>
				<!-- 文章作者等信息 -->
				<view class="article-info">
					<image :src="article.avatar" class="avatar small"></image>
					<text class="info-text">{{ article.nickname }}</text>
					<text class="info-text">{{ article.createTime }}</text>
				</view>
				<view class="jg"></view>
				
			</view>
		</view>
		<button class="circle-btn" @tap="gotoWrite"><text class="icon-text">+</text></button>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				articles: []
			};
		},
		onLoad: function() {
			this.getArticles();
		},
		onShow: function() {},
		onPullDownRefresh: function() {
			this.getArticles();
		},
		methods: {
			getArticles: function() {
				var _this = this;
				uni.request({
					url: this.apiServer + "/article/list",
					method: 'GET',
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					success: res => {
						_this.articles = res.data.data;
						for(var i=0;i<_this.articles.length;i++){
							_this.articles[i].content=this.handleContent(this.handleContent2(_this.articles[i].content));
						 _this.articles[i].createTime = this.handleTime(_this.articles[i].createTime);
						}
					},
					complete: function() {
						uni.stopPullDownRefresh();
					}
				});
			},
			gotoDetail: function(aId) {
				uni.navigateTo({
					url: '../article_detail/article_detail?aId=' + aId
				});
			},
			gotoWrite:function(){
				if(uni.getStorageSync('login_key').login === true){
				uni.navigateTo({
					url:'../write/write'
				});
				}else{
					uni.showModal({
						
						title:'提示',
						content:'请先登录',
						success() {
						uni.navigateTo({
							url:'../signin/signin'
						})
					}

					});
				}
			},
			handleTime: function(date) {
				var d = new Date(date);
				var year = d.getFullYear();
				var month = d.getMonth() + 1;
				var day = d.getDate() < 10 ? '0' + d.getDate() : '' + d.getDate();
				var hour = d.getHours() < 10 ? '0' + d.getHours() : '' + d.getHours();
				var minutes = d.getMinutes() < 10 ? '0' + d.getMinutes() : '' + d.getMinutes();
				var seconds = d.getSeconds() < 10 ? '0' + d.getSeconds() : '' + d.getSeconds();
				return year + '' + month + '-' + day + '' + hour + ":" + minutes + ':' + seconds
			},
			handleContent: function(msg) {
				return msg.substring(0, 30);
			},
			handleContent2: function(description) {
			description = description.replace(/(\n)/g, '');
			description = description.replace(/(\t)/g, '');
			description = description.replace(/(\r)/g, '');
			description = description.replace(/<\/?[^>]*>/g, '');
			description = description.replace(/\s*/g, '');
			return description;
		}
		}
	};
</script>

<style>
.small{
	height: 30px;
	width: 30px;
}
.article-info{
	display: flex;
}
.info-text{
	margin-left: 11px;
	margin-top: 15px;
	font-size:10px;
}
.text-box{
	margin-top: 30px;
	
}
.text-img-box{
	display: flex;
	height: 95px;
}
.left{
			flex: 1 1 67%;
		font-size: 16px;
}
.right{
			flex: 1 1 33%;
}
.text-img-box image{
		
		width: 100%;
		height: 100%;
		/* border-radius: 10px; */
	
}.thumbnail-box {
		display: flex;

	}

	.thumbnail-item {
		flex: 1 1 33.3%;
		display: flex;		
		justify-content: center;
	
	}

	.thumbnail-item image {
		width: 94%;
height: 95px;
		/* border-radius: 20px; */
	}
   .article-title{
	   font-size: 20px;
	   font-weight: 500;
   }
 .article{
	
	 margin-top: 5px;
 }
 .article-content{
	 font-size: 16px;
 }
 .jg{
	 width: 100%;
	 height: 9px;
	 background-color:#EEEEEE;
	 margin-top: 10px;
 }
 .circle-btn {
	position: absolute;
	bottom: 30px;
	right: 10px;
	width: 45px;
	height: 45px;
	border-radius: 50%;
	background-color: #de533a;
	background: linear-gradient(40deg, #ffd86f, #fc6262);
	/* background-image: url(../../static/Pencil32.png); */
	box-shadow: 2px 5px 10px #aaa;
	cursor: pointer;
	border: none;
	outline: none;
	display: flex;
	justify-content: center;
	align-items: center;
	position: fixed;
}
.circle-btn:after{
	border: none;
	
}
.icon-text {
	color: #fff;
	}
.articletitle{
	margin-top: -18px;
	margin-bottom: 10px;
}
</style>
