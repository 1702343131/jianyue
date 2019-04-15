<template>
	<view class="container">
		<view class="title">
		<text class="article-title">{{ article.title }}</text>
		</view>
		<view class="article-info">
			<image :src="article.avatar" class="avatar-small"></image>
			<text class="article-nickname">{{ article.nickname }}</text>
			<view class="create">
			<text class="info-text">{{ article.createTime}}</text>
			</view>
			<!-- 登录用户和文章作者不是同一个人，就显示关注或取消关注按钮 -->
			<button v-if="userId != article.uId && !followed" class="green-btn follow-btn" @tap="follow">关注</button>
			<button v-if="userId != article.uId && followed" class="green-btn follow-btn cancel" @tap="cancelFollow">取消</button>
		</view>
         
		 
		<view class="grace-text" style="margin-top: 10px;"><rich-text :nodes="article.content" bindtap="tap"></rich-text></view>
		<view class="likebtn">
		<button v-if="userId != article.uId && !likeed" class="green-btn like-btn" @tap="like">喜欢</button>
		<button v-if="userId != article.uId && likeed" class="green-btn likecancel-btn" @tap="cancelLike">取消</button>
		</view>
		<view class="article-flow"></view>
		<view class="comment-box">
		<text class="info-text">评论{{ comments.length }}</text>
		</view>
		<view class="comment">
		<view class="comment-item" v-for="(comment, index) in comments" :key="index">
			<view class="left"><image :src="comment.avatar" class="avatar-small"></image></view>
			<view class="right">
				<view class="right-nickname">
				<text>{{ comment.nickname }}</text>
				</view>
				<view class="right-content">
				<text>{{ comment.content }}</text>
				</view>
				<view class="right-lou">
					<text style="margin-right: 10px;">{{ comments.length - index }}楼</text>
					<text>{{ comment.commentTime }}</text>
				</view>
			</view>
		</view>
		<input class="uni-input comment-box" type="text" placeholder="写下你的评论" v-model="content" required="required" />
		<button class="green-btn" @tap="send">提交</button>
	</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			article: {
				aId: 0,
				uId: 0,
				title: '',
				content: '',
				avatar: '',
				nickname: '',
				createTime: ''
			},
			comments: [],
			content: '',
			userId: uni.getStorageSync('login_key').userId,
			followed: false,
			likeed:false
		};
	},
	onLoad: function(option) {
		//option为object类型，会序列化上个页面传递的参数
		this.article.aId = option.aId;
	},
	onShow: function() {
		this.getArticle();
	},
	onPullDownRefresh: function() {
		this.getArticle();
	},
	methods: {
		getArticle: function() {
			var _this = this;
			uni.request({
				url: this.apiServer + '/article/' + this.article.aId,
				method: 'GET',
				header: { 'content-type': 'application/x-www-form-urlencoded' },
				data: {
					userId: this.userId
				},
				success: res => {
					// console.log(res.data.data.article);
					_this.article.aId = res.data.data.article.id;
					_this.article.uId = res.data.data.article.uid;
					_this.article.title = res.data.data.article.title;
					_this.article.content = res.data.data.article.content;
					_this.article.nickname = res.data.data.article.nickname;
					_this.article.avatar = res.data.data.article.avatar;
					_this.article.createTime = res.data.data.article.createTime;
					_this.comments = res.data.data.comments;
					_this.article.createTime = this.handleTime(_this.article.createTime);
					for(var i = 0;i<_this.comments.length;i++){
				    _this.comments[i].commentTime = this.handleTime(_this.comments[i].commentTime);
					}
					if (res.data.data.followed === '关注') {
						_this.followed = true;
						
					};
					if (res.data.data.Likeed === '喜欢') {
						_this.likeed = true;
						
					};
				},
				complete: function() {
					uni.stopPullDownRefresh();
				}
			});
		},
		handleTime: function(date) {
			var d = new Date(date);
			var year = d.getFullYear();
			var month = d.getMonth() + 1;
			var day = d.getDate() < 10 ? '0' + d.getDate() : '' + d.getDate();
			var hour = d.getHours() < 10 ? '0' + d.getHours() : '' + d.getHours();
			var minutes = d.getMinutes() < 10 ? '0' + d.getMinutes() : '' + d.getMinutes();
			var seconds = d.getSeconds() < 10 ? '0' + d.getSeconds() : '' + d.getSeconds();
			return year + '-' + month + '-' + day + ' ' + hour + ':' + minutes + ':' + seconds;
		},
		send: function() {
			console.log('评论人编号：' + this.userId + ',文章编号：' + this.article.aId + '，评论内容：' + this.content);
			uni.request({
				url: this.apiServer + '/comment/add',
				method: 'POST',
				header: { 'content-type': 'application/x-www-form-urlencoded' },
				data: {
					aId: this.article.aId,
					uId: this.userId,
					content: this.content
				},
				success: res => {
					if (res.data.code === 0) {
						uni.showToast({
							title: '评论成功'
						});
						this.getArticle();
						this.content = '';
					}
				}
			});
		},
		follow:function(){
			uni.request({
				url: this.apiServer + '/follow/add',
				method: 'POST',
				header: { 'content-type': 'application/x-www-form-urlencoded' },
				data: {
					fromUId: this.userId,
					toUId: this.article.uId
				},
				success: res => {
					if (res.data.code === 0) {
						uni.showToast({
							title: '关注成功'
						});
						this.followed = true;
					}
				}
			});
		},
		cancelFollow:function(){
			uni.request({
				url: this.apiServer + '/follow/cancel',
				method: 'POST',
				header: { 'content-type': 'application/x-www-form-urlencoded' },
				data: {
					fromUId: this.userId,
					toUId: this.article.uId
				},
				success: res => {
					if (res.data.code === 0) {
						uni.showToast({
							title: '已取消关注'
						});
						this.followed = false;
					}
				}
			});
		},
			like:function(){
			uni.request({
				url: this.apiServer + '/like/add',
				method: 'POST',
				header: { 'content-type': 'application/x-www-form-urlencoded' },
				data: {
					fromUId: this.userId,
					toAId: this.article.aId
				},
				success: res => {
					if (res.data.code === 0) {
						uni.showToast({
							title: '喜欢成功'
						});
						this.likeed = true;
					}
				}
			});
		},
			cancelLike:function(){
			uni.request({
				url: this.apiServer + '/like/cancel',
				method: 'POST',
				header: { 'content-type': 'application/x-www-form-urlencoded' },
				data: {
					fromUId: this.userId,
					toAId: this.article.aId
				},
				success: res => {
					if (res.data.code === 0) {
						uni.showToast({
							title: '已取消喜欢'
						});
						this.likeed = false;
					}
				}
			});
		}
		
	}
};
</script>

<style>
.content {
	margin-bottom: 10px;
	margin-top: 10px;
	padding: 5px;
	border-bottom: 1px solid #eee;
}
.avatar-small{
	width: 35px;
	height: 35px;
	border-radius: 50%;
}
.img-list {
	display: flex;
	flex-direction: column;
}
.img-item {
	width: 100%;
	height: 150px;
	margin-bottom: 5px;
}
.img-item image {
	width: 100%;
	height: 100%;
	border-radius: 5px;
}
.comment-item {
	display: flex;
	align-items: center;
	border-bottom: 1px solid #eee;
	margin-bottom: 10px;
	padding: 5px;
}
.comment-item .left {
	flex: 1 1 15%;
	margin-top: -5px;
}
.comment-item .right {
	flex: 1 1 85%;
	display: flex;
	flex-direction: column;
}
.comment-box {
	border: 1px solid #fff;
	border-radius: 5px;
	background-color: #eee;
}
.follow-btn {
	
	height: 30x;
	width: 80px;
	font-size: 15px;
	color: #FFFFFF;
	background-color: #E74A39;
	border-radius: 20px;
    margin-right: 5px;
}
.cancel{
	background-color: #aaa;
}
.article-info{
	display: flex;
}
.article-info .article-nickname{
	margin-top: 10px;
	margin-left: 10px;
}
.title{
	margin-top: 10px;
}
.article-title{
	font-size: 25px;
	}
	.grace-text{
		font-size: 17px;
	}
	.article-flow{
		height: 11px;
	background-color: #EEEEEE;
	}
	.comment-box{
		background-color: #f8f8f8;
	display: flex;
	align-items: center;
	height: 45px;
	border-bottom: 1px solid #eeeeee;
	}
	.comment{
		width: 100%;
	margin: 0 auto;
	}
	.comment-item {
	display: flex;
	padding-top: 15px;
	border-bottom: 1px solid #eeeeee;
	padding-bottom: 15px;
}

.right-nickename {
	color: #2e2e2e;
	font-size: 18px;
}
.right-content{
	font-size: 15px;
	color: #5e5e5e;
	margin-top: 10px;
}
.right-lou {
	margin-top: 12px;
	font-size: 12px;
	color: #8f8f94;
}
.create{
	margin-left: 10px;
	margin-top: 10px;
}
.like-btn{
	height: 30x;
	width: 80px;
	font-size: 15px;
	color: #FFFFFF;
	background-color: #E74A39;
	border-radius: 20px;
    margin-right: 130px;
	margin-bottom: 20px;
	
}
.likecancel-btn{
	background-color: #aaa;
	height: 30x;
	width: 80px;
	font-size: 15px;
	color: #FFFFFF;
	border-radius: 20px;
	margin-right: 130px;
	margin-bottom: 20px;
}
</style>
