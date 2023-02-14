<template>
	<view>
		<view class="search-box">
			<uni-search-bar @input="input" :radius="100" cancelButton="none"></uni-search-bar>
		</view>
		<!-- 搜索建议列表 -->
		<view class="sugg-list">
			<view class="sugg-item" v-for="(item,i) in searchResults.goods" :key='i' @click="gotoDetail(item)">
				<view class="goods-name">{{item.goods_name}}</view>
				<uni-icons type="arrowright" size='16'></uni-icons>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				timer:null,
				kw:'',
				// 搜索结果的列表
				searchResults:[]
			};
		},
		methods:{
			//input 输入事件处理函数
			
			input(e){
				clearTimeout(this.timer) 
				this.timer = setTimeout(()=>{
					this.kw = e
					this.getSearchList()
				},500)
			},
				
			async getSearchList(){
				if(this.kw.length === 0){
					this.searchResults = []
					return
				}
				const {data:res} = await uni.$http.get('/api/public/v1/goods/search',{query:this.kw})
				if(res.meta.status !== 200 ) return uni.$showMsg()
				this.searchResults = res.message
			},
			gotoDetail(item){
				console.log('item',item.goods_id)
				uni.navigateTo({
					url:'/subpkg/goods_detail/goods_detail?goods_id=' + item.goods_id
				})
			}
		}
	}
</script>

<style lang="scss">
.search-box{
	position: sticky;
	top:0;
	z-index: 999;
}
.sugg-list {
    padding: 0 5px;

    .sugg-item {
      display: flex;
      align-items: center;
      justify-content: space-between;
      font-size: 12px;
      padding: 13px 0;
      border-bottom: 1px solid #efefef;

      .goods-name {
        white-space: nowrap;
        overflow: hidden;
        text-overflow: ellipsis;
        margin-right: 3px;
      }
    }
  }
  
  .history-box {
    padding: 0 5px;
  
    .history-title {
      display: flex;
      justify-content: space-between;
      height: 40px;
      align-items: center;
      font-size: 13px;
      border-bottom: 1px solid #efefef;
    }
  
    .history-list {
      display: flex;
      flex-wrap: wrap;
  
      .uni-tag {
        margin-top: 5px;
        margin-right: 5px;
      }
    }
  }
</style>
