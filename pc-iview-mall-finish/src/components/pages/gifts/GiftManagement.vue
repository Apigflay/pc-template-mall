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
            <!-- 礼物 -->
            <div class="top">
                <Button size="small" @click="online">在线礼物</Button>
                <Button size="small" @click="shelves">已下架礼物</Button>
            </div>
            <!-- 查询 新增 -->
            <div class="top">
                <Button type="primary" size="small" @click="">修改版本号</Button>
                <Button type="primary" size="small" @click="create">添加</Button>
                <Button type="primary" size="small" @click="lookup">保存排序</Button>
                <Button type="primary" size="small" @click="create">微秀服务器端刷新</Button>
                <Button type="primary" size="small" @click="labelmanagement">标签管理</Button>

                <label>选择平台：</label>
                <Select v-model="query" size="small" style="width:150px">
                    <Option v-for="item in cityList" :value="item.value" :key="item.value">{{ item.label }}</Option>
                </Select>
                <label>选择标签：</label>
                <Select v-model="query" size="small" style="width:150px">
                    <Option v-for="item in cityList" :value="item.value" :key="item.value">{{ item.label }}</Option>
                </Select>
            </div>
            <!-- 主页面内容 -->
            <div class="center" >
                <Table border :columns="columns" :data="tabledata" class="table"  ref="table">
                    <template slot-scope="{ row }" slot="name">
                        <strong>{{ row.name }}</strong>
                    </template>
                    <template slot-scope="{ row, index }" slot="action">
                        <Button type="text" size="small" style="margin-right: 5px" @click="update(row)">编辑</Button>
                        <Button type="text" size="small" @click="remove(index,row)">下架</Button>
                    </template>
                </Table>
            </div>

            <!-- 在线礼物页面遮罩层 -->
            <div class="marsk" v-if="showNone"></div>
            <!-- 标签管理页面遮罩层 -->
            <div class="fullmarsk" v-if="showNonefull"></div>
            <!-- 已下架页面遮罩层 -->
            <div class="shelvesmarsk" v-if="shelvesshowNone"></div>
            

            <!-- 在线礼物 -- 新增页面 -->
            <div class="pop" v-if="showNonecreate">
                <Form :label-width="80">
                    <FormItem label="支付类型：">
                        <Select v-model="pay">
                            <Option v-for="item in cityList" :value="item.value" :key="item.value">{{ item.label }}</Option>
                        </Select>
                    </FormItem>
                    <FormItem label="商品ID：">
                        <Input v-model="id" placeholder="请输入..."></Input>
                    </FormItem>
                    <FormItem label="金额：">
                        <Input v-model="money" placeholder="请输入..."></Input>
                    </FormItem>
                    <FormItem label="币值：">
                        <Input v-model="volue" placeholder="请输入..." ></Input>
                    </FormItem>
                    <FormItem label="赠送币：">
                        <Input v-model="gift" placeholder="请输入..."></Input>
                    </FormItem>
                    <FormItem label="说明：">
                        <Input v-model="address" type="textarea" :autosize="{minRows: 3,maxRows: 3}" placeholder="请输入..."></Input>
                    </FormItem>
                    <FormItem>
                        <Button type="primary" @click="newadd">保存</Button>
                        <Button style="margin-left: 8px" type="dashed" @click="reset">重置</Button>
                        <Button style="margin-left: 8px" @click="close">取消</Button>
                    </FormItem>
                </Form>      
            </div>
            <!-- 在线礼物 -- 修改编辑页面 -->
            <div class="pop" v-if="showNoneUpdate">
                <Form :label-width="80">
                    <FormItem label="礼物ID：">
                        <Input v-model="id" placeholder="请输入..."></Input>
                    </FormItem>
                    <FormItem label="名称：">
                        <Input v-model="money" placeholder="请输入..."></Input>
                    </FormItem>
                    <FormItem label="类型：">
                        <Select v-model="pay">
                            <Option v-for="item in cityList" :value="item.value" :key="item.value">{{ item.label }}</Option>
                        </Select>
                    </FormItem>
                    <FormItem label="价格：">
                        <Input v-model="volue" placeholder="请输入..."></Input>
                    </FormItem>
                    <FormItem label="说明：">
                        <Input v-model="gift" placeholder="请输入..."></Input>
                    </FormItem>
                    <FormItem label="获取比例：">
                        <Input v-model="gift" placeholder="请输入..."></Input>
                    </FormItem>
                    <FormItem label="房间比例：">
                        <Input v-model="gift" placeholder="请输入..."></Input>
                    </FormItem>
                    <FormItem label="单位：">
                        <Input v-model="gift" placeholder="请输入..."></Input>
                    </FormItem>
                    <FormItem label="上传礼物图片：">
                        <Input v-model="gift" placeholder="请输入..."></Input>
                    </FormItem>
                    <FormItem label="上传礼物热图：">
                        <Input v-model="gift" placeholder="请输入..."></Input>
                    </FormItem>
                    <FormItem label="50*50(微秀)：">
                        <Input v-model="gift" placeholder="请输入..."></Input>
                    </FormItem>
                    <FormItem label="临时图片：">
                        <Input v-model="gift" placeholder="请输入..."></Input>
                    </FormItem>
                    <FormItem label="webp动画：">
                        <Input v-model="gift" placeholder="请输入..."></Input>
                    </FormItem>
                    <FormItem label="SVGA动画：">
                        <Input v-model="gift" placeholder="请输入..."></Input>
                    </FormItem>
                    <FormItem label="平台：">
                        <Select v-model="pay">
                            <Option v-for="item in cityList" :value="item.value" :key="item.value">{{ item.label }}</Option>
                        </Select>
                    </FormItem>
                    <FormItem label="标签：">
                        <Select v-model="pay">
                            <Option v-for="item in cityList" :value="item.value" :key="item.value">{{ item.label }}</Option>
                        </Select>
                    </FormItem>
                    <FormItem>
                        <Button type="primary" @click="updateadd">保存</Button>
                        <Button style="margin-left: 8px" type="dashed" @click="reset">重置</Button>
                        <Button style="margin-left: 8px" @click="close">取消</Button>
                    </FormItem>
                </Form>      
            </div>

            <!-- 下架礼物 页面 -->
            <div class="fullpop" v-if="showNoneshelves">
                <!-- 礼物 -->
                <div class="top">
                    <Button size="small" @click="online">在线礼物</Button>
                    <Button size="small" @click="shelves">已下架礼物</Button>
                </div>
                <div class="top">
                    <label>选择平台：</label>
                    <Select v-model="query" size="small" style="width:150px">
                        <Option v-for="item in cityList" :value="item.value" :key="item.value">{{ item.label }}</Option>
                    </Select>
                    <label>选择标签：</label>
                    <Select v-model="query" size="small" style="width:150px">
                        <Option v-for="item in cityList" :value="item.value" :key="item.value">{{ item.label }}</Option>
                    </Select>
                </div>
                <!-- 下架页面表格 -->
                <div class="center" >
                    <Table border :columns="shelvescolumns" :data="tabledata" class="table"  ref="table">
                        <template slot-scope="{ row }" slot="name">
                            <strong>{{ row.name }}</strong>
                        </template>
                        <template slot-scope="{ row, index }" slot="action">
                            <Button type="text" size="small" style="margin-right: 5px" @click="shelvesupdate(row)">编辑</Button>
                            <Button type="text" size="small" @click="remove(index,row)">删除</Button>
                            <Button type="text" size="small" @click="remove(index,row)">上架</Button>
                        </template>
                    </Table>
                </div>
            </div>
            <!-- 已下架礼物 -- 修改编辑页面 -->
            <div class="pop" v-if="showNoneshelvesUpdate">
                <Form :label-width="80">
                    <FormItem label="礼物ID：">
                        <Input v-model="id" placeholder="请输入..."></Input>
                    </FormItem>
                    <FormItem label="名称：">
                        <Input v-model="money" placeholder="请输入..."></Input>
                    </FormItem>
                    <FormItem label="类型：">
                        <Select v-model="pay">
                            <Option v-for="item in cityList" :value="item.value" :key="item.value">{{ item.label }}</Option>
                        </Select>
                    </FormItem>
                    <FormItem label="价格：">
                        <Input v-model="volue" placeholder="请输入..."></Input>
                    </FormItem>
                    <FormItem label="说明：">
                        <Input v-model="gift" placeholder="请输入..."></Input>
                    </FormItem>
                    <FormItem label="获取比例：">
                        <Input v-model="gift" placeholder="请输入..."></Input>
                    </FormItem>
                    <FormItem label="房间比例：">
                        <Input v-model="gift" placeholder="请输入..."></Input>
                    </FormItem>
                    <FormItem label="单位：">
                        <Input v-model="gift" placeholder="请输入..."></Input>
                    </FormItem>
                    <FormItem label="上传礼物图片：">
                        <Input v-model="gift" placeholder="请输入..."></Input>
                    </FormItem>
                    <FormItem label="上传礼物热图：">
                        <Input v-model="gift" placeholder="请输入..."></Input>
                    </FormItem>
                    <FormItem label="50*50(微秀)：">
                        <Input v-model="gift" placeholder="请输入..."></Input>
                    </FormItem>
                    <FormItem label="临时图片：">
                        <Input v-model="gift" placeholder="请输入..."></Input>
                    </FormItem>
                    <FormItem label="webp动画：">
                        <Input v-model="gift" placeholder="请输入..."></Input>
                    </FormItem>
                    <FormItem label="SVGA动画：">
                        <Input v-model="gift" placeholder="请输入..."></Input>
                    </FormItem>
                    <FormItem label="平台：">
                        <Select v-model="pay">
                            <Option v-for="item in cityList" :value="item.value" :key="item.value">{{ item.label }}</Option>
                        </Select>
                    </FormItem>
                    <FormItem label="标签：">
                        <Select v-model="pay">
                            <Option v-for="item in cityList" :value="item.value" :key="item.value">{{ item.label }}</Option>
                        </Select>
                    </FormItem>
                    <FormItem>
                        <Button type="primary" @click="updateadd">保存</Button>
                        <Button style="margin-left: 8px" type="dashed" @click="reset">重置</Button>
                        <Button style="margin-left: 8px" @click="shelvesclose">取消</Button>
                    </FormItem>
                </Form>      
            </div>

            <!-- 标签管理页面 -->
            <div class="fullpop" v-if="showNonelabelmanagement">
                <div class="top">
                    <label>选择平台：</label>
                    <Select v-model="platform" size="small" style="width:150px; margin-right:10px;">
                        <Option v-for="item in platformList" :value="item.value" :key="item.value">{{ item.label }}</Option>
                    </Select>
                    <Input v-model="id" size="small" placeholder="请输入..." style="width:200px;"></Input>
                    <Button type="primary" size="small" @click="">添加</Button>
                    <Button type="primary" size="small" @click="">保存排序</Button>
                    <Button type="primary" size="small" @click="">微秀服务端刷新</Button>
                </div>
                <!-- 标签管理页面表格 -->
                <div class="center" >
                    <Table border :columns="platformcolumns" :data="table" class="table"  ref="table" :draggable="true" @on-drag-drop="onDragDrop">
                        <template slot-scope="{ row }" slot="name">
                            <strong>{{ row.name }}</strong>
                        </template>
                        <template slot-scope="{ row, index }" slot="action">
                            <Button type="text" size="small" style="margin-right: 5px" @click="labelupdata(row)">修改</Button>
                            <Button type="text" size="small" @click="labelremove(index,row)">删除</Button>
                        </template>
                    </Table>
                </div>
            </div>
            <!-- 标签管理页面 -- 修改功能 -->
            <div class="popmodifys" v-if="showNonelabelupdata">
                <Form :label-width="80">
                    <FormItem label="ID：">
                        <Input v-model="id" placeholder="请输入..."></Input>
                    </FormItem>
                    <FormItem label="名称：">
                        <Input v-model="money" placeholder="请输入..."></Input>
                    </FormItem>
                    <FormItem label="是否显示：">
                        <Select v-model="pay">
                            <Option v-for="item in cityList" :value="item.value" :key="item.value">{{ item.label }}</Option>
                        </Select>
                    </FormItem>
                    <FormItem>
                        <Button type="primary" @click="updatelabelmanagement">保存</Button>
                        <Button style="margin-left: 8px" type="dashed" @click="reset">重置</Button>
                        <Button style="margin-left: 8px" @click="closelabelmanagement">取消</Button>
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
        query: '', // 查询功能 绑定的数据
        platform: null, // 选择的平台选项值

        showNone: false, // 控制页面遮罩层开关
        showNonefull: false, // 标签管理页面遮罩层开关
        showNoneshelve: false,// 已下架礼物页面遮罩层开关

        showNoneUpdate: false, // 修改页面弹出控制
        showNonecreate: false, // 新增页面弹出控制

        showNoneshelves: false, // 下架页面弹出控制
        shelvesshowNone: false, // 下架页面的遮罩层
        showNoneshelvesUpdate: false, // 下架礼物修改编辑
        
        showNonelabelmanagement: false, // 标签管理页面
        showNonelabelupdata: false, // 标签管理页面 -- 修改功能
        

        cityList: [ // 查询条件
            {
                value: 'Android',
                label: 'Android'
            },
            {
                value: 'iPhone',
                label: 'iPhone'
            },
            {
                value: 'AppStore',
                label: 'AppStore'
            },
            {
                value: 'taiwangooglestore',
                label: '台湾googlestore'
            },
            {
                value: 'taiwanAppStore',
                label: '台湾AppStore'
            }
        ],
        platformList: [ // 标签管理页面---平台选择条件
            {
                value: '1',
                label: '喵播'
            },
            {
                value: '2',
                label: '微秀'
            }
        ],

        // 表格列名称
        columns: [ // 在线礼物页面表格
            {title: '排序',key: 'pay'},
            {title: 'ID',key: 'id'}, 
            {title: '名称',key: 'money'},
            {title: '图片',key: 'volue'},
            {title: '说明',key: 'gift'},
            {title: '价格',key: 'gift'},
            {title: '获得比例',key: 'gift'},
            {title: '房间比例',key: 'gift'},
            {title: '动画',key: 'gift'},
            {title: '平台',key: 'gift'},
            {title: '标签',key: 'address'},
            {title: '操作',slot: 'action',align: 'center'}
        ],
        shelvescolumns: [ // 已下架礼物页面表格
            {title: '排序',key: 'pay'},
            {title: 'ID',key: 'id'}, 
            {title: '名称',key: 'money'},
            {title: '图片',key: 'volue'},
            {title: '说明',key: 'gift'},
            {title: '价格',key: 'gift'},
            {title: '获得比例',key: 'gift'},
            {title: '房间比例',key: 'gift'},
            {title: '动画',key: 'gift'},
            {title: '平台',key: 'gift'},
            {title: '操作',slot: 'action',align: 'center'}
        ],
        platformcolumns: [  // 标签管理
            {title: '排序',key: 'pay'},
            {title: 'ID',key: 'id'}, 
            {title: '名称',key: 'money'},
            {title: '礼物数',key: 'volue'},
            {title: '是否显示',key: 'gift'},
            {title: '操作',slot: 'action',align: 'center'}
        ],
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
        tabledata1: [ // 数据表数据
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
            }
        ],
        tabledata2: [ // 数据表数据
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
        pay: '', // 新增和编辑页面使用的数据    支付类型
        id: '', //  新增和编辑页面使用的数据    商品ID
        money: '', //  新增和编辑页面使用的数据    支付金额
        volue: '', //  新增和编辑页面使用的数据    币值
        gift: '', //  新增和编辑页面使用的数据    赠送币
        address: '', //  新增和编辑页面使用的数据      说明

        resets: { // 暂存页面内容数据
            pay: '',  // 支付类型
            id: '', // 商品ID
            money: '', // 支付金额
            volue: '', // 币值
            gift: '', // 赠送币
            address: '' // 说明
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
      table: function () {  // 标签管理页面 平台选择 功能
          if (this.platform == 1)
          {
              return this.tabledata1;
          }
          else if (this.platform == 2)
          {
              return this.tabledata2;
          }
      }
  },
  methods: {
        //table组件处理函数
        getWidth:function(){
                var wid =document.body.clientWidth;        //网页可见区域宽(body)
                    this.widthMain.width=wid-200+'px';
        },


        updatelabelmanagement: function() { // 标签管理页面 - 修改功能 - 保存
                this.showNonefull = false, // 标签管理页面遮罩层开关
                this.showNonelabelupdata = false  // 标签管理页面 -- 修改功能
                console.log(this.id)
        },
        closelabelmanagement: function () { // 标签管理页面 - 修改功能 - 取消
                // this.showNonelabelmanagement = false, // 标签管理页面
                this.showNonefull = false, // 标签管理页面遮罩层开关
                this.showNonelabelupdata = false  // 标签管理页面 -- 修改功能
        },
        remove: function(index,row) { // 删除
            this.data.splice(index, 1);
            console.log(row.id)
        },
        create: function () { // 新建数据页面
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
        
        update: function(row) { // 修改页面
            this.resets.pay = row.pay
            this.resets.id = row.id
            this.resets.money = row.money
            this.resets.volue = row.volue
            this.resets.gift = row.gift
            this.resets.address = row.address,

            this.showNone = true,
            this.showNoneUpdate = true,
            this.pay = row.pay,
            this.id = row.id,
            this.money = row.money,
            this.volue = row.volue,
            this.gift = row.gift,
            this.address = row.address,
            console.log(row)
        },
        reset: function(row) { // 修改页面重置功能
            this.pay = this.resets.pay,
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
                
                this.showNoneshelves = false, // 下架页面弹出控制
                this.showNonelabelmanagement = false, // 标签管理页面
                this.showNonelabelupdata = false  // 标签管理页面 -- 修改功能

        },


        shelvesupdate: function(row) { // 已下架礼物页面 -- 修改页面
            this.shelvesshowNone = true,
            this.showNoneshelvesUpdate = true,
            console.log(row)
        },
        shelvesreset: function(row) { // 已下架礼物页面 -- 修改页面重置功能
            this.pay = this.resets.pay,
            this.id = this.resets.id,
            this.money = this.resets.money,
            this.volue = this.resets.volue,
            this.gift = this.resets.gift,
            this.address = this.resets.address
        },
        shelvesupdateadd: function() { // 已下架礼物页面 -- 修改页面保存
            this.showNone = false,
            this.showNoneUpdate = false,
            console.log(this.id)
        },
        shelvesclose: function () { // 已下架礼物页面 -- 新建页面和修改页面取消修改
            this.shelvesshowNone = false,
            this.showNoneshelvesUpdate = false
        },

        lookup: function () { // 查找
            console.log(this.query)
        },
        online: function () {  // 在线礼物按钮事件
            this.showNoneshelves = false
        },
        shelves: function () { // 已下架礼物按钮事件
            this.showNoneshelves = true
        },
        labelmanagement: function () { // 标签管理按钮
            this.showNonelabelmanagement = true
        },
        onDragDrop(a,b){ // 页面排序拖放功能
            console.log(a,b);
            this.table.splice(b,1,...this.table.splice(a, 1 , this.table[b]));
        },

        labelupdata: function () { // 标签管理页面 --- 修改功能
            this.showNonefull = true;
            this.showNonelabelupdata = true;  // 标签管理页面 -- 修改功能
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
              margin-left: 10px;
            //   margin-right: 10px;
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
    .shelvesmarsk{ // 下架礼物页面 -- 遮罩层
        position: absolute;
        top: 0;
        left: 0; 
        width: 100%;
        height: 100%;
        background: #000;
        opacity: 0.5;
        z-index: 3002;
    }
    .fullmarsk{ // 标签管理页面 --修改功能 遮罩层
        position: absolute;
        top: 0;
        left: 0; 
        width: 100%;
        height: 100%;
        background: #000;
        opacity: 0.5;
        z-index: 3002;
    }
    .pop{  // 新增页面和修改页面 弹出层
        padding:2% 4%;
        position: absolute;
        top: 9%;
        left: 18%; 
        width: 60%;
        height: 80%;
        background: #fff;
        z-index: 3003;
    }
    .popmodifys{  // 标签管理页面 -- 修改功能弹出层
        padding:4% 4%;
        position: absolute;
        top: 9%;
        left: 18%; 
        width: 60%;
        height: 50%;
        background: #fff;
        z-index: 3005;
    }
    .fullpop{  // 标签管理页面弹出层
        // padding:2% 4%;
        position: absolute;
        top: 0%;
        left: 0%; 
        width: 100%;
        height: 100%;
        background: #fff;
        z-index: 3001;
    }
}
</style>