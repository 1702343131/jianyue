<template>
	<view style='height:100%;'>
		<view style="width:80%; padding:30rpx 10%; flex-wrap:nowrap;">
			<view class="grace-search">
				<view class="grace-search-in">
					<view class="grace-search-icon"></view>
					<input type="search" @confirm="searchNow" value="" placeholder="搜索商品"></input>
				</view>
			</view>
		</view>
		<view class="grace-cate" style='width:100%; height:calc(100% - 126upx);'>
			<scroll-view scroll-y class="grace-cate-left" :scroll-into-view="leftTo">
				<view v-for="(item, index) in mainCate" :key="index" :class="['item', currentCateIndex == item.cateid ? 'current' : '']" :data-cateid="item.cateid" @tap='changCate' :id="'cate'+item.cateid">{{item.name}}</view>
			</scroll-view>
			<scroll-view class="grace-cate-right" scroll-y :scroll-into-view="productListTo" @scroll="rscroll">
				<block v-for="(cate, index) in mainCate" :key="index">
					<view class="grace-title grace-nowrap grace-space-between" style="margin-top:15px;" :id="'productList'+cate.cateid">
						<view class="grace-h5 grace-blod">{{cate.name}}</view>
					</view>
					<view class="grace-news-list">
						<view class="grace-news-list-items" v-for="(product, productIndex) in allProducts['cate'+cate.cateid+'products']" :key="productIndex">
							<image :src="product.img" class="grace-news-list-img grace-list-imgs-l" mode="widthFix"></image>
							<view class="grace-news-list-title">
								<view class="grace-news-list-title-main">{{product.name}}</view>
								<view class="price">￥{{product.price}}</view>
								<view><view :data-productid='product.productId' class='grace-add-to-card' @tap='addtocard'>+</view></view>
							</view>
						</view>
					</view>
				</block>
			</scroll-view>
	  </view>
	</view>
</template>
<script>
var scrollTimer = null;
var pageHeight = 100;
var productsData = require('../../data/products.js');
export default {
	data() {
		return {
			currentCateIndex : 1,
			// 左侧滚动定位
			leftTo : 'cate1',
			// 产品列表滚动定位
			productListTo: 'productList1',
			//分类
			mainCate: [
				{ cateid: 1, name: '热门推荐' },
				{ cateid: 2, name: '促销打折' },
				{ cateid: 3, name: '国际名牌' },
				{ cateid: 4, name: '大衣外套' },
				{ cateid: 5, name: '汽车用品' },
				{ cateid: 6, name: '儿童用品' },
				{ cateid: 7, name: '文具用品' },
				{ cateid: 8, name: '精品男装' },
				{ cateid: 9, name: '精品女装' },
				{ cateid: 10, name: '电脑办公' },
				{ cateid: 11, name: '礼品鲜花' }
			],
			// 产品列表对应分类
			allProducts: productsData.allProducts
		}
	},
	onLoad:function(){
		uni.getSystemInfo({
			success: function(res) {
			pageHeight = res.windowHeight;
			},
		});
	},
	methods:{
		// 分类切换
		changCate:function(e){
			var cateid = e.currentTarget.dataset.cateid;
			this.currentCateIndex = cateid;
			this.leftTo           = 'cate'+cateid;
			this.productListTo    = 'productList' + cateid;
		},
		// 产品区域滚动
		rscroll : function(e){
			var sctop = e.detail.scrollTop;
			if (scrollTimer != null) { clearTimeout(scrollTimer);}
			scrollTimer = setTimeout(function(){
			  this.getIndex(0, sctop);
			}.bind(this), 80);
		},
		// 动态识别分类激活
		getIndex: function (i, sctop){
			var _self = this;
			var query = wx.createSelectorQuery();
			query.select('#productList' + this.mainCate[i].cateid).boundingClientRect();
			query.selectViewport().scrollOffset();
			query.exec(function (res){
				if(res[0] && res[0].top){
					if (res[0].top + pageHeight / 2 > 0){
						_self.currentCateIndex =  _self.mainCate[i].cateid;
						_self.leftTo           =  'cate' + _self.mainCate[i].cateid;
					}else{
						i++;
						if (i < _self.mainCate.length) { _self.getIndex(i, sctop); }
					}
				}
			});
		},
		// 加入到购物车
		addtocard : function(e){
			var productid = e.currentTarget.dataset.productid;
			uni.showToast({
				title: '产品id : ' + productid+', 请根据项目需求自行完善后续代码',
				icon:"none"
			})
		},
		// 搜索
		searchNow : function(e){
			var k = e.detail.value;
			uni.showToast({
				title: '关键字 : ' + k + ', 请根据项目需求自行完善后续代码',
				icon: "none"
			});
		}
	}
}
</script>
<style>
page{height:100%;}
.grace-search-icon:before{color:#E2231A;}
.grace-news-list-items > image{width:80px; height:80px;}
.grace-news-list-title .price{line-height:1.8em; color:#E2231A;}
.grace-add-to-card{width:26px; height:26px; line-height:22px; border:1px solid #E2231A; font-size:26px; color:#E2231A; float:right; text-align:center; border-radius:26px;}

/*
版权声明 : 
GraceUI 的版权约束是不能转售或者将 GraceUI 直接发布到公开渠道！
侵权必究，请遵守版权约定！
*/
</style>