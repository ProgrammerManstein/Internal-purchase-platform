<template>
  <div id="myList" class="myList">
    <ul>
      <li v-for="(item,index) in list" :key="item.product_id">
        <el-popover placement="top">
          <p>确定删除吗？</p>
          <div style="text-align: right; margin: 10px 0 0">
            <el-button type="primary" size="mini" @click="deleteCollect(item.product_id)">确定</el-button>
          </div>
          <i class="el-icon-close delete" slot="reference" v-show="isDelete"></i>
        </el-popover>

        <router-link :to="{ path: '/goods/details', query: {productID:item.product_id} }">
          <img :src="imgList[index]" alt />
          <h2>{{item.product_name}}</h2>
          <h3>{{item.product_title}}</h3>
          <p>
            <span>{{item.product_selling_price}}元</span>
            <span v-show="item.product_price != item.product_selling_price" class="del">{{item.product_price}}元</span>
          </p>
        </router-link>
      </li>
      <li v-show="isMore && list.length>=1" id="more">
        <router-link :to="{ path: '/goods', query: {categoryID: classid } }">
          浏览更多
          <i class="el-icon-d-arrow-right"></i>
        </router-link>
      </li>
    </ul>
  </div>
</template>
<script>
export default {
  name: 'MyList',
  // list为父组件传过来的商品列表
  // isMore为是否显示“浏览更多”
  props: ['list', 'isMore', 'isDelete', 'classid'],
  data() {
    return {}
  },
  computed: {
    // 计算图片路径,显示图片
    imgList: function () {
      let imgList = []
      for (let i = 0; i < this.list.length; i++) {
        imgList[i] = this.list[i].product_picture
        // console.log(imgList[i])
      }
      return imgList
    }
  },
  methods: {
    async deleteCollect(product_id) {
      let { data } = await this.$axios.post('/api/deletecollect', {
        user_id: this.$store.state.user.user.username,
        product_id: product_id
      })
      // console.log(data)
      if (data.code == '200') {
        this.$message.success('删除成功!')
        for (let i = 0; i < this.list.length; i++) {
          const temp = this.list[i]
          if (temp.product_id == product_id) {
            this.list.splice(i, 1)
          }
        }
      } else {
        this.$message.error('删除失败!')
      }
    }
  }
}
</script>
<style scoped>
.myList ul li {
  z-index: 1;
  float: left;
  width: 234px;
  height: 280px;
  padding: 10px 0;
  margin: 0 0 14.5px 13.7px;
  background-color: white;
  -webkit-transition: all 0.2s linear;
  transition: all 0.2s linear;
  position: relative;
}
.myList ul li:hover {
  z-index: 2;
  -webkit-box-shadow: 0 15px 30px rgba(0, 0, 0, 0.1);
  box-shadow: 0 15px 30px rgba(0, 0, 0, 0.1);
  -webkit-transform: translate3d(0, -2px, 0);
  transform: translate3d(0, -2px, 0);
}
.myList ul li img {
  display: block;
  width: 160px;
  height: 160px;
  background: url(../assets/imgs/placeholder.png) no-repeat 50%;
  margin: 0 auto;
}
.myList ul li h2 {
  margin: 25px 10px 0;
  font-size: 14px;
  font-weight: 400;
  color: #333;
  text-align: center;
  text-overflow: ellipsis;
  white-space: nowrap;
  overflow: hidden;
}
.myList ul li h3 {
  margin: 5px 10px;
  height: 18px;
  font-size: 12px;
  font-weight: 400;
  color: #b0b0b0;
  text-align: center;
  text-overflow: ellipsis;
  white-space: nowrap;
  overflow: hidden;
}
.myList ul li p {
  margin: 10px 10px 10px;
  text-align: center;
  color: #ff6700;
}
.myList ul li p .del {
  margin-left: 0.5em;
  color: #b0b0b0;
  text-decoration: line-through;
}
.myList #more {
  text-align: center;
  line-height: 280px;
}
.myList #more a {
  font-size: 18px;
  color: #333;
}
.myList #more a:hover {
  color: #ff6700;
}
.myList ul li .delete {
  position: absolute;
  top: 10px;
  right: 10px;
  display: none;
}
.myList ul li:hover .delete {
  display: block;
}
.myList ul li .delete:hover {
  color: #ff6700;
}
</style>