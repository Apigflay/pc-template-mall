<template>
  <div class="content">
    <silderBar></silderBar>
    <div class="body_main">
        <navBar></navBar>
        <div class="main" :style="widthMain">
            <div class="tips" id="{this.$route.name}">
                <Breadcrumb separator="<b class='demo-breadcrumb-separator'>=></b>">
                    <BreadcrumbItem :to="item.path" v-for="(item,index) in this.$route.meta.index" :key="index">{{item.name}}</BreadcrumbItem>
                </Breadcrumb>
            </div>
          <!-- 查询 推荐 -->
          <div class="top">
            <label>用户idx：</label>
                <Input v-model="rec_id" placeholder="Enter something..." style="width: 120px" size='small'/>
                <Select v-model="rec_time" style="width:120px" size='small'>
                    <Option v-for="item in state" :value="item.value"  :key="item.value">{{ item.label }}</Option>
                </Select>
            <Button type="primary" size='small'  @click="rec2">添加二级推荐</Button>
            <Button type="primary" size='small' @click="add">批量添加</Button>
          </div>
          <div class="top">
            <label>用户idx：</label>
                <Input v-model="id" size='small' placeholder="Enter something..." style="width: 120px" />
            <label>房间id：</label>
                <Input v-model="id" size='small' placeholder="Enter something..." style="width: 120px" />
            <Button type="primary" @click="lookup" size='small'>查询</Button>
            
          </div>
          <!-- 主页面内容 -->
          <div class="center" >
            <Table border :columns="columns" :data="tabledata" class="table"  ref="table">
                <template slot-scope="{ row }" slot="name">
                    <strong>{{ row.name }}</strong>
                </template>
                <template slot-scope="{ row, index }" slot="action">
                    <Button type="text" size="small" @click="remove(index,row)">删除</Button>
                </template>
            </Table>
          </div>
          <!-- 遮罩层 -->
          <div class="marsk" v-if="showNone"></div>

        <!-- 新增页面 -->
         <div class="pop" v-if="showNonecreate">
            <Form :label-width="80">
                <FormItem label="二级添加：">
                    <Input v-model="address" type="textarea" :autosize="{minRows: 8,maxRows: 8}" placeholder="请输入..."></Input>
                </FormItem>
                <FormItem>
                    <Button type="primary" @click="newadd">保存</Button>
                    <Button style="margin-left: 8px" type="dashed" @click="reset">重置</Button>
                    <Button style="margin-left: 8px" @click="close">取消</Button>
                </FormItem>
                <h3>批量添加请用英文的‘,’将idx隔开，例：22222,33333,44444</h3>
            </Form>      
         </div>

        <!-- 分页页面 -->
        <div class="bottom">
            <Page :total="100" show-total show-elevator @on-change='fenye'/>
        </div>
        </div>
    </div>
  </div>
</template>

<script>
import {square} from '../../../assets/js/download'
export default {
  name: 'Index',
  data () {
    return {
        widthMain:{width:''},//table组件自适应
        //面包屑数组
        id: '', // 查询功能 绑定的数据
        rec_id: '', // 二级推荐 绑定的数据
        rec_time: '', // 推荐时长
        address: null, // 二级添加数据
        showNone: false, // 控制页面遮罩层开关
        showNoneUpdate: false, // 修改页面弹出控制
        showNonecreate: false, // 新增页面弹出控制
        // 表格列名称
        columns: [
            {title: '编号',key: 'pay'},
            {title: '用户编号',key: 'id'}, 
            {title: '用户昵称',key: 'money'},
            {title: '房间号',key: 'volue'},
            {title: '时间',key: 'gift'},
            {title: '推荐时长',key: 'gift'},
            {title: '操作人',key: 'gift'},
            {title: '操作',slot: 'action',align: 'center'}],
        tabledata: [ // 数据表数据
            {
                pay: 'Android', // 支付类型
                id: 'com.sunday.80', // 商品ID
                money: 1, // 金额
                volue: 7000, //  币值
                gift: 0, // 礼物赠送币
                address: 'New York No. 1 Lake Park' // 说明
            },
            {
                pay: 'taiwanAppStore',
                id: 'com.sunday.30',
                money: 1.99,
                volue: 15000,
                gift: 0,
                address: 'London No. 1 Lake Park'
            },
            {
                pay: 'iPhone',
                id: 'com.bunny.5',
                money: 3.99,
                volue: 30000,
                gift: 0,
                address: 'Sydney No. 1 Lake Park'
            },
            {
                pay: 'AppStore',
                id: 'com.bunny.6',
                money: 1,
                volue: 7000,
                gift: 0,
                address: 'Ottawa No. 2 Lake Park'
            }
        ],
        state: [ //  二级推荐栏选项框
            {
                value: 1,
                label: '7天'
            },
            {
                value: 2,
                label: '永久'
            }

        ],

    }
  },
  created() {
    window.addEventListener('resize', this.getWidth);
    this.getWidth()
  },
  destroyed () {
    window.removeEventListener('resize', this.getWidth)
  },
  mounted () {
      
  },
  computed: {
      
  },
  methods: {
      //table组件处理函数
      getWidth:function(){
              var wid =document.body.clientWidth;        //网页可见区域宽(body)
                this.widthMain.width=wid-200+'px';
      },

      remove: function(index,row) { // 删除
          this.tabledata.splice(index, 1);
          console.log(row.id)
      },

      lookup: function () { // 查找
          console.log(this.id)
      },
      rec2: function () {  // 二级推荐功能
          console.log(this.rec_id)
          console.log(this.rec_time)
      },
      add: function () { // 批量添加功能
        this.showNone = true;
        this.showNonecreate = true;
      },
      newadd: function () {
          console.log(this.address)
      },
      reset: function () {
          this.address = null;
      },
      close: function () {
          this.showNone = false;
          this.showNonecreate = false;
      },
      fenye: function (page) { // 页面的分页功能
          console.log(page)
      },
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.main{
    overflow-x: scroll;
    overflow: hidden;
    position: relative;
    .tips{
        text-align: left;
    }
    .top{margin-top:20px; margin-left: 20px; display: flex;
        button{
              margin-left: 15px;
          }
        label{
              margin-left: 15px;
          }
        }
    .center{width:100%; margin-top:20px;}
    .bottom{width:100%; margin-top:15px;}
    .marsk{
        position: absolute;
        top: 0;
        left: 0; 
        width: 100%;
        height: 100%;
        background: #000;
        opacity: 0.5;
        z-index: 2999;
    }
    .pop{
        padding:2% 4%;
        position: absolute;
        top: 9%;
        left: 18%; 
        width: 60%;
        height: 60%;
        background: #fff;
        z-index: 3001;
    }
}
</style>