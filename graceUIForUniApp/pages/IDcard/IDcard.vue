<template>
	<view>
		<view class="grace-idcard-main">
			<view class="grace-idcard-desc">
				描述示例文本 : graceUI  极速开发之旅 ! 基于 uni-app 的优秀前端框架，大幅度提高布局效率!
				<text>\n http://grace.hcoder.net</text>
			</view>
			<view class="grace-idcard-text">
				身份证照片 ( 正面 )
			</view>
			<view class="grace-idcard-items">
				<view class="grace-idcard-uper-btn" @tap="selectImg1">
					<view class="img"><image src="https://staticimgs.oss-cn-beijing.aliyuncs.com/camera.png" mode="widthFix" /></view>
					<view class="text">拍摄或选择照片</view>
				</view>
				<view class="grace-idcard-preview">
					<image :src="idCard1"  @tap="previewImg1" mode="widthFix"></image>
				</view>
			</view>
			<view class="grace-idcard-text">
				身份证照片 ( 背面 )
			</view>
			<view class="grace-idcard-items">
				<view class="grace-idcard-uper-btn" @tap="selectImg2">
					<view class="img"><image src="https://staticimgs.oss-cn-beijing.aliyuncs.com/camera.png" mode="widthFix" /></view>
					<view class="text">拍摄或选择照片</view>
				</view>
				<view class="grace-idcard-preview">
					<image :src="idCard2" @tap="previewImg2" mode="widthFix" />
				</view>
			</view>
			<view style="margin-top:38upx;">
				<button type="primary" @tap="uploadCards">上传</button>
			</view>
		</view>
	</view>
</template>
<script>
var _self;
export default {
	data() {
		return {
			idCard1 : 'https://staticimgs.oss-cn-beijing.aliyuncs.com/idcard1.png',
			idCard2 : 'https://staticimgs.oss-cn-beijing.aliyuncs.com/idcard2.png'
		};
	},
	onLoad:function(){
		_self = this;
	},
	methods: {
		selectImg1 : function() {
			uni.chooseImage({
				count:1,
				success:function(res){
					_self.idCard1 = res.tempFilePaths[0];
				}
			})
		},
		selectImg2 : function() {
			uni.chooseImage({
				count:1,
				success:function(res){
					_self.idCard2 = res.tempFilePaths[0];
				}
			})
		},
		previewImg1 : function(){
			uni.previewImage({
				urls:[_self.idCard1]
			});
		},
		previewImg2 : function(){
			uni.previewImage({
				urls:[_self.idCard2]
			});
		},
		uploadCards : function(){
			if(this.idCard1 == 'https://staticimgs.oss-cn-beijing.aliyuncs.com/idcard1.png' || this.idCard2 == 'https://staticimgs.oss-cn-beijing.aliyuncs.com/idcard2.png'){
				uni.showToast({title:"请选择身份证照片", icon:"none"});
				return;
			}
			// 因底层限制一次上传一个
			uni.showLoading({title:"上传中"});
			// 上传正面
			var uploadTask1 = uni.uploadFile({
				url: 'https://unidemo.dcloud.net.cn/upload',
				filePath: _self.idCard1,
				fileType: 'image',
				name: 'data',
				success: function (uploadFileRes) {
					// 上传成功后返回服务器端保存的路径
					console.log(uploadFileRes.data);
					// 上传背面
					var uploadTask2 = uni.uploadFile({
						url: 'https://unidemo.dcloud.net.cn/upload',
						filePath: _self.idCard2,
						fileType: 'image',
						name: 'data',
						success: function (uploadFileRes) {
							// 上传成功后返回服务器端保存的路径
							console.log(uploadFileRes.data);
							// 至此2张照片上传完毕
							uni.hideLoading();
						}
					 });
				}
			 });
		}
	},
}
</script>
<style>
view{font-size:28upx;}
.grace-idcard-main{margin:20upx 30upx;}
.grace-idcard-desc{line-height:2em; background:#FFFFFF; padding:20upx; border-radius:10upx;}
.grace-idcard-text{line-height:2em; margin-top:30upx;}
.grace-idcard-items{background:#FFFFFF; padding:30upx 0; display:flex; margin:30upx 0; border-radius:10upx; align-items: flex-start;}
.grace-idcard-uper-btn{width:276upx; margin:0 60upx; background:#F1F1F1; padding-bottom:10upx; border-radius:10upx; text-align:center;}
.grace-idcard-uper-btn .img{width:100upx; height:100upx; margin:0 auto; margin-top:30upx;}
.grace-idcard-uper-btn .img image{width:100upx; height:100upx;}
.grace-idcard-uper-btn .text{width:100%; margin-top:10upx; text-align:center; line-height:2em;}
.grace-idcard-preview{width:50%; margin:0 30upx;}
.grace-idcard-preview image{width:100%;}
</style>