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
          <!-- 查询 新增 -->
            <div class="top">
                <label>用户idx：</label>
                <Input v-model="RoomID" size="small" placeholder="Enter something..." style="width: 200px" />
                <Select v-model="query" size="small" style="width: 150px; margin-left: 5px;">
                    <Option v-for="item in FamilyLevel" size="small" :value="item.value" :key="item.value">{{ item.label }}</Option>
                </Select>
                <Button type="primary" size="small" @click="lookup">查询</Button>
            </div>
          <!-- 主页面内容 -->
            <div class="center" >
                <Table border :columns="biao" :data="tabledata" class="table"  ref="table">
                    <template slot-scope="{ row }" slot="name">
                        <strong>{{ row.name }}</strong>
                    </template>
                    <template slot-scope="{ row, index }" slot="action">
                        <Button type="text" size="small" @click="update(row)">解封</Button>
                    </template>
                </Table>
            </div>
          <!-- 遮罩层 -->
            <div class="marsk" v-if="showNone"></div>
          <!-- 新增页面 -->
            <div class="pop" v-if="showNonecreate">
                <Form :label-width="90">
                    <p>设置超管</p>
                    <FormItem label="用户IDX：">
                        <Input v-model="roomid" placeholder="请输入..."></Input>
                        <Select v-model="Signing">
                            <Option v-for="item in FamilyLevel " :value="item.value" :key="item.value">{{ item.label }}</Option>
                        </Select>
                    </FormItem>
                    <FormItem>
                        <Button type="primary" @click="newadd">保存</Button>
                        <Button style="margin-left: 8px" type="dashed" @click="reset">重置</Button>
                        <Button style="margin-left: 8px" @click="close">取消</Button>
                    </FormItem>
                </Form>         
            </div>
          <!-- 修改编辑页面 -->
            <div class="pop" v-if="showNoneUpdate">
                <Form :label-width="90">
                    <!-- <FormItem label="Input">
                        <Input v-model="addItem.pay" placeholder="请输入..."></Input>
                    </FormItem> -->
                    <FormItem label="房间ID：">
                        <Input v-model="roomid" placeholder="请输入..."></Input>
                    </FormItem>
                    <FormItem label="家族名称：">
                        <Input v-model="familyname" placeholder="请输入..."></Input>
                    </FormItem>
                    <FormItem label="是否签约：">
                        <Select v-model="Signing">
                            <Option v-for="item in FamilyLevel " :value="item.value" :key="item.value">{{ item.label }}</Option>
                        </Select>
                    </FormItem>
                    <FormItem label="支付宝账号：">
                        <Input v-model="volue" placeholder="请输入..."></Input>
                    </FormItem>
                    <FormItem label="支付宝昵称：">
                        <Input v-model="zfb_number" placeholder="请输入..."></Input>
                    </FormItem>
                    <FormItem label="家族级别：">
                        <Input v-model="Family_type" placeholder="请输入..."></Input>
                    </FormItem>
                    <FormItem label="归属运营：">
                        <Input v-model="Sub_operation" placeholder="请输入..."></Input>
                    </FormItem>
                    <FormItem label="归属小组：">
                        <Input v-model="Sub_group" placeholder="请输入..."></Input>
                    </FormItem>
                    <FormItem label="姓名">
                        <Input v-model="name" placeholder="请输入..."></Input>
                    </FormItem>
                    <FormItem label="银行卡号">
                        <Input v-model="bank_card" placeholder="请输入..."></Input>
                    </FormItem>
                    <FormItem label="开户行">
                        <Input v-model="open_card" placeholder="请输入..."></Input>
                    </FormItem>
                    <FormItem label="省份">
                        <Input v-model="province" placeholder="请输入..."></Input>
                    </FormItem>
                    <FormItem label="城市">
                        <Input v-model="city" placeholder="请输入..."></Input>
                    </FormItem>
                    <FormItem>
                        <Button type="primary" @click="updateadd">保存</Button>
                        <Button style="margin-left: 8px" type="dashed" @click="reset">重置</Button>
                        <Button style="margin-left: 8px" @click="close">取消</Button>
                    </FormItem>
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
        RoomID: null,
        query: null, // 查询功能 绑定的数据

        showNone: false, // 控制页面遮罩层开关
        showNoneUpdate: false, // 修改页面弹出控制
        showNonecreate: false, // 新增页面弹出控制

        FamilyLevel: [
            {
                label: '解封号码',
                value: '1'
            },
            {
                label: '解封设备',
                value: '2'
            },
            {
                label: '解麦',
                value: '3'
            }
        ],

        columns1: [ // 表格列名称
            {title: '用户ID',key: 'roomid'}, 
            {title: '到期时间',key: 'familyname'},
            {title: '添加时间',key: 'Signing'},
            {title: '操作人',key: 'Signing'},
            {title: '备注',key: 'Signing'},
            {title: '操作',slot: 'action',align: 'center'}
        ],
        columns2: [ // 表格列名称
            {title: '用户ID',key: 'roomid'}, 
            {title: '添加时间',key: 'familyname'},
            {title: '设备ID',key: 'Signing'},
            {title: '设备类型',key: 'Signing'},
            {title: '操作',slot: 'action',align: 'center'}
        ],
        columns3: [ // 表格列名称
            {title: '编号',key: 'roomid'}, 
            {title: '用户idx',key: 'familyname'},
            {title: '昵称',key: 'familyname'},
            {title: '性别	',key: 'Signing'},
            {title: '最后时间',key: 'Signing'},
            {title: '操作人',key: 'Signing'},
            {title: '备注',key: 'Signing'},
            {title: '操作',slot: 'action',align: 'center'}
        ],
        biao: [],
        tabledata: [ // 数据表数据
            {
                roomid: 'Android', // 支付类型
                familyname: 'com.sunday.80', // 商品ID
                money: 1, // 金额
                Signing: 7000, //  币值
                zfb_name: 0, // 礼物赠送币
                Sub_operation: 'New York No. 1 Lake Park' // 说明
            }
        ],
        resets: { // 暂存页面内容数据 用于重置输入框时候使用
                roomid: '', // 房间ID
        }
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
      update: function(row) { // 修改页面
          this.resets.roomid = row.roomid // 回复时候使用的数据

          this.showNone = true,
          this.showNoneUpdate = true,
          this.pay = row.pay,
          this.id = row.id
      },
      reset: function(row) { // 修改页面重置功能
          this.roomid = this.resets.roomid,
          this.id = this.resets.id,
          this.money = this.resets.money,
          this.volue = this.resets.volue,
          this.gift = this.resets.gift,
          this.address = this.resets.address


      },
      updateadd: function() { // 修改页面保存
          this.showNone = false,
          this.showNoneUpdate = false,
          console.log(this.id)
      },
      close: function () { // 新建页面和修改页面取消修改
          this.showNone = false, // 遮罩层关闭
          this.showNoneUpdate = false, // 修改页面关闭
          this.showNonecreate = false, // 新建页面关闭

          this.resets.pay = null, // 页面重置功能 使用的缓存数据清空
          this.resets.id = null,
          this.resets.money = null,
          this.resets.volue = null,
          this.resets.gift = null,
          this.resets.address = null

      },
      remove: function(index,row) { // 删除
          this.data.splice(index, 1);
          console.log(row.id)
      },
      add: function () { // 新建数据页面
          this.showNone = true,
          this.showNonecreate = true,
          this.pay = null, // 清空数据，防止之前点击过修改造成数据缓存
          this.id = null,
          this.money = null,
          this.volue = null,
          this.gift = null,
          this.address = null
      },
      newadd: function () { // 新建页面保存
          this.showNone = false,
          this.showNonecreate = false,
          console.log(this.id)
      },
      lookup: function () { // 查找
        //   console.log(this.query)
          if (this.query == '1')
          {
              this.biao = this.columns1
          }
          else if (this.query == '2')
          {
              this.biao = this.columns2
          }
          else if (this.query == '3')
          {
              this.biao = this.columns3
          }
          else 
          {
              console.log(this.query)
          }
      },
      fenye: function (page) { // 页面的分页功能
          console.log(page)
      }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.main{  // 主页面样式
    overflow-x: scroll;
    overflow: hidden;
    position: relative;
    .tips{ // 主页面
        text-align: left;
    }
    .top{margin-top:10px; margin-left: 20px; display: flex; padding: 5px;  // 表头部分 查找添加 样式
          button{
              margin-left: 15px;
          }
          label{
              margin-left: 15px;
            //   margin-right: 15px;
              font-size: 15px;
          }
    }
    .center{width:100%; margin-top:20px;
        button {
            margin-right: 5px
        }
    }  // 页面中部 表格部分样式
    .bottom{width:100%; margin-top:15px;} // 页面底部 分页部分样式
    .marsk{ // 遮罩层
        position: absolute;
        top: 0;
        left: 0; 
        width: 100%;
        height: 100%;
        background: #000;
        opacity: 0.5;
        z-index: 2999;
    }
    .pop{  // 弹出层
        padding:2% 4%;
        position: absolute;
        top: 9%;
        left: 18%; 
        width: 60%;
        height: 80%;
        background: #fff;
        z-index: 3001;
    }
}
</style>