<template>
  <div class="all">
    <div class="steps">
      <el-steps :active="active" finish-status="success" :space="800">
        <el-step title="步骤 1" icon="el-icon-edit"></el-step>
        <el-step title="步骤 2"></el-step>
      </el-steps>
    </div>
    <div class="show" v-if="active ===0">
      <div>
        <div class="top">
          <div>
            <span class="is-lef font-down">环节监管线名称：</span>
            <el-col :span="2">
              <el-input v-model="name"></el-input>
            </el-col>
          </div>
          <div class="spacing">
            <span class="is-lef font-down">处理类型：</span>
            <el-col :span="2">
              <el-select v-model="prosessType" placeholder="请选择">
                <el-option  :label="item.name" :value="item.id" v-for="item in processingList" :key="item.id"></el-option>
              </el-select>
            </el-col>
          </div>
          <div class="spacing-state">
            <span class="is-lef font-down">使用状态：</span>
            <el-col :span="2">
              <el-select v-model="status" placeholder="请选择">
                <el-option label="正常" value="0" ></el-option>
                <el-option label="异常" value="1" ></el-option>
              </el-select>
            </el-col>
          </div>
        </div>

        <div class="container">
          <!--纵列-->
          <div v-for="(col,index) in link" :key="index" class="col-box">
            <div class="col">
              <!--列内小div-->
              <div class="up-name"><div class="center"><div class="Pollution">{{index==0?"产污区" :" 环节名称："}}</div><div v-show="index !=0 "><el-col :span="7">
                <el-input class="es-input" v-model="col.name"></el-input></el-col>
                </div></div>
                <button @click="deletecol(index)" class="delete-col">×</button>
              </div>
              <div v-for="(item,inW) in col.project" :key="inW">
                <div class="roll">
                  <div class="inner-box">
                    <button @click="deleteinner(index,inW)" class="deleinner">×</button>
                    <!-- 产污区名称div -->
                    <div class="input-name">
                      <span class="is-left">{{index==0? "产污区名称：":"分项名称："}}</span>
                      <el-col :span="10">
                        <el-input v-model="item.name"></el-input>
                      </el-col>
                    </div>
                      <div class="input-ergodic" v-for="(demo,i) in item.equipment" :key="i">
                      <el-col :span="14">
                        <el-select  placeholder="请选择设备" v-model="demo.equipId" >
                          <el-option :label=item.name :value=item.id v-for="item in  index==0 ? equipmentList0 : equipmentList1" :key=item.id>
                          </el-option>
                        </el-select>
                      </el-col>
                      <button @click="addOption(index,inW,i)" class="addOption-input">+</button>
                      <el-button v-show="i > 0" @click="removeInput(index,inW, i)" class="addOption-input" >-</el-button>
                    </div>
                  </div>
                </div>

                <div class="input-adddown">
                  <Button @click="addInnerBox(index,inW)" class="add-down">新增</Button>
                </div>
              </div>
            </div>
            <Button @click="addCol(index)" class="button-column">+</Button>
          </div>
        </div>
        <div class="next-step">
          <el-button type="primary" @click="gotoTable" >下一步</el-button>
        </div>
      </div>
    </div>

    <div v-else class="all-down">
        <div class="down-content">
          <div class='first_box' v-for='(ia,index) in config' :key='index' >
            <div>
              <div class="dd-name">
                {{name}}
              </div>
              <div class='mode_box' v-for='(item,iE) in ia' :key='iE'>
                  <div class="firstShow">
                      <div class='cha' @click="deletemode(index,iE)">×</div>
                    <div class="item-name">{{item.name}}</div>
                    <div class='selected-box'>
                      <el-select class='selectA' :value='item.project[0].name' v-model="item.project.name">
                        <el-option v-for='(box,index) in item.project' :key='index' :value='box.id' :label='box.name'>{{box.name}}</el-option>
                      </el-select>
                    </div>
                  </div>
              </div>
            </div>
            </div><div @click='haddleClick()' class="plus">+</div>
        </div>
      <div class="nextStep"><el-button type="primary" @click="goback">上一步</el-button><el-button type="primary" @click="onSubmit">提交</el-button></div>
      
    </div>
  </div>
</template>

    <script>
export default {
  name: "demo",
  data() {
    return {
      items:[],
      path:"basics/api/v1/product/line/",
      paths:"basics/api/v1/product/line/link/",
      equipmentListUrl:"basics/api/v1/equipment/all",
      equipmentList0:[],
      equipmentList1:[],
      processingList:[],
      prosessType:'',
      processingUrl:"basics/api/v1/base/code/category/010102",
      config:[
      
      ],
      active: 0,
      input: "",
      field:'',
      name:"监管线",
      m:"",
      projectId:"",
      value: "",
      status:"",
      itemize:"",
      options: "",
      downValue:'',
      selectValue: "",
      subItem: '',
      link: [
        {
          name: "产污区",
          project: [
            {
              name: "",
              equipment: [
                {
                  equipId: ""
                }
              ]
            },
            {
              name: "产污区2",
              equipment: [
                {
                  equipId: "用气"
                }
              ]
            }
          ]
        },
        {
          name: "分解环节",
          project: [
            {
              name: "分解环节1",
              equipment: [
                {
                  equipId: "用气"
                }
              ]
            },
            {
              name: "分解环节2",
              equipment: [
                {
                  equipId: "用气"
                }
              ]
            },
            {
              name: "分解环节3",
              equipment: [
                {
                  equipId: "用气"
                }
              ]
            }
          ]
        },
        {
          name: "抽气环节",
          project: [
            {
              name: "抽气1",
              equipment: [
                {
                  equipId: "用气"
                }
              ]
            },
              {
              name: "抽气1",
              equipment: [
                {
                  equipId: "用气"
                }
              ]
            },
          ]
        },
        {
          name: "划分",
          project: [
            {
              name: "划分1",
              equipment: [
                {
                  equipId: "用气"
                }
              ]
            }
          ]
        },
        {
          name: "黎黎",
          project: [
            {
              name: "黎黎1",
              equipment: [
                {
                  equipId: "4"
                }
              ]
            }
          ]
        },
        {
          name: "欣欣",
          project: [
            {
              name: "欣欣1",
              equipment: [
                {
                  equipId: "用气"
                }
              ]
            }
          ]
        },
        {
          name: "生气",
          project: [
            {
              name: "生气1",
              equipment: [
                {
                  equipId: "幼体"
                }
              ]
            }
          ]
        },
        {
          name: "花",
          project: [
            {
              name: "话1",
              equipment: [
                {
                  equipId: "破"
                }
              ]
            }
          ]
        },
      ],
    };
  },
created(){
this.getProcessing();
this.getEquipmentList0();
this.getEquipmentList1();
},
  methods: {
    haddleClick(){
      this.config.push(JSON.parse(JSON.stringify(this.link)))
    },
      //获取设备列表
    getEquipmentList0(){
        this.$get(this.equipmentListUrl,{
          effect:0,
          companyId:"290013016496410624"
        }).then(response => {
            this.equipmentList0 = response.body;
        });
    },
    //获取设备列表
    getEquipmentList1(){
        this.$get(this.equipmentListUrl,{
          effect:1,
          companyId:"290013016496410624"
        }).then(response => {
            this.equipmentList1 = response.body;
        });
    },
    //获取数据类型
    getProcessing(){
      this.$get(this.processingUrl).then(response =>{
            this.processingList = response.body.data;
        })
    },
    //点击添加右侧
    addCol(index) {
      this.link.splice(index + 1, 0, {
        name: "",
        project: [
          {
            name: "",
            equipment: [
              {
                equipId: ""
              }
            ]
          }
        ]
      });
    },
    //点击div下方按钮，增加新的div
    addInnerBox(index, inW) {
      let newProject={
        name: "",
              equipment: [
                {
                  equipId: ""
                }
              ]
      };
      this.link[index].project.splice(inW+ 1, 0, newProject);
    },
    addEquipment(index, i, item,) {
      const selectValue ={equipId: ""
      }
      
      this.link[index][inW].splice(i+1, 0, selectValue);
    },
    //增加input
    addOption(index, inW, i,) {
      
      let ll= this.link;
      
      this.link[index].project[inW].equipment.splice(i+1, 0, {equipId:""});
      
      let ll2= this.link;
      // console.log(i)
    },
    addBigdiv(u){
    this.link.splice(u+1,0,{
      name:'',
      configLink:[{
        id:"",
        linkId:'',
        linkName:"",
        projectId:'',
      }]
    })
    },
    //deletemode
    deletemode(index,iE){
    this.config[index].splice(iE,1)
    },
    // 增加各个分项
    newlyAdded(index){
      
    const newSub ={
      field:''
    };
    this.link.splice(index+1 , 0 , newSub);
    },
    //删除 小div里面的input
    removeInput(index, inW, i) {
      this.link[index].project[inW].equipment.splice(i, 1);
    },
    //删除第一步分项
    deleteinner(index,inW){
      this.link[index].project.splice(inW,1)
    },
    //删除整个大div
    deletecol(index){
      this.link.splice(index,1)
    },
    //删除第二步div
    deleteButton(index,iQ){
      this.link[index].splice(iQ,1)
    },
    onSubmit(){
      debugger
      // const params = this.config;
     
      debugger
     console.log(params);

console.log(JSON.stringify(params));
      params:[
        {
          name:this.name,
          configLink:[{
            projectName:"",
            projectId:'',
            linkId:"",
            linkName:this.config.name,
          }]
        }
      ]
      debugger

    },
    //行动线
    gotoTable() {
      // let companyId="273032305130475520";
      // let maintain= "100000000000000001";
      // // localStorage.getItem("user_id");
      // this.link[0].name="产污区";
      // const lineOBJ={
      //   name:this.name,
      //   status:this.status,
      //   type:this.prosessType,
      //   link:this.link,
      //   linksNumber:this.link.length,
      //   maintain: maintain,
      //   companyId: companyId,
      // };
      // this.$post(this.path+"basic",lineOBJ).then(response =>{
      //   // console.log(lineOBJ)
      // if(response.head.respCode==="0000000"){
      //           this.$message({
      //             message:  "保存成功!",
      //             type: "success",
      //             center: true
      //           });
                
      this.config .push(JSON.parse(JSON.stringify(this.link)));
      
      if (this.active++ > 1) this.active = 0;
  //             } else {
  //               this.$message({
  //                 message:   "保存错误！",
  //                 type: "error"
  //               });
  //             }
  // })
},
    goback(){
      this.active = 0;
    }
  }
};
</script>

    <style scoped>
.bigdiv{
width:1600px;
height:200px;
background:antiquewhite;
}
.small-div{
  height:130px;
  width:130px;
  float:left;
  margin-right:2%;
  background-color: #ccc;
}
.all {
  height:790px;
  background-color: #fff;
}
.top {
  width:100%;
  height:100%;
  margin-left: 1%;
  margin-bottom: 3%;
  margin-top: 1%;
}
.font-down {
  margin-top: 3px;
}
.is-left {
  float: left;
  margin-left:3%;
  margin-top:2%;
  margin-right:6%;
}
.container {
  display: flex;
  width: 1600px;
  height: 550px;
  overflow: auto;
}
.container .col-box {
  display: flex;
  align-items: center;
}
.container .col-box .col {
  width: 262px;
  height: 520px;
  border: 1px solid #e9f6f9;
  border-radius: 5px;
  text-align: center;
  overflow-x: hidden;
  overflow: auto;
}
.col::-webkit-scrollbar {
  width: 4px;
  /*height: 4px;*/
}
.col::-webkit-scrollbar-thumb {
  border-radius: 5px;
  -webkit-box-shadow: inset 0 0 2px #ccc;
  background: rgba(0, 0, 0, 0.2);
}
.col::-webkit-scrollbar-track {
  -webkit-box-shadow: inset 0 0 2px #f2f2f2;
  border-radius: 0;
  background: #f1f1f1;
}
.container .col-box .col .inner-box {
  width: 258px;
  height: 120px;
  border: 1px solid #fff;
  background-color: #fff;
  overflow-x: hidden;
  overflow: auto;
}
.inner-box::-webkit-scrollbar {
  width: 4px;
  /*height: 4px;*/
}
.inner-box::-webkit-scrollbar-thumb {
  border-radius: 5px;
  -webkit-box-shadow: inset 0 0 2px #ccc;
  background: rgba(0, 0, 0, 0.2);
}
.inner-box::-webkit-scrollbar-track {
  -webkit-box-shadow: inset 0 0 2px #f2f2f2;
  border-radius: 0;
  background: #f1f1f1;
}
.add-chain{
  width:1600px;
  height:190px;
  overflow-y:hidden;
  overflow: auto;
}
.add-chain::-webkit-scrollbar {
  width: 4px;
  /*height: 4px;*/
}
.add-chain::-webkit-scrollbar-thumb {
  border-radius: 5px;
  -webkit-box-shadow: inset 0 0 5px #ccc;
  background: rgba(0, 0, 0, 0.2);
}
.add-chain::-webkit-scrollbar-track {
  -webkit-box-shadow: inset 0 0 5px #f2f2f2;
  border-radius: 0;
  background: #f1f1f1;
}
.up-name {
  height: 7%;
  width: 100%;
  background-color: #e9f6f9;
  position: relative;
}
.show {
  height: 665px;
}
.is-lef {
  float: left;
  margin-left:2%;
}
.input-ergodic {
  display: block;
  margin-left: 20px;
}
.container {
  width: 1545px;
  margin: 0 auto;
  height: 600px;
  overflow: auto;
}
.container::-webkit-scrollbar {
  width: 4px;
  /*height: 4px;*/
}
.container::-webkit-scrollbar-thumb {
  border-radius: 5px;
  -webkit-box-shadow: inset 0 0 5px #ccc;
  background: rgba(0, 0, 0, 0.2);
}
.container::-webkit-scrollbar-track {
  -webkit-box-shadow: inset 0 0 5px #f2f2f2;
  border-radius: 0;
  background: #f1f1f1;
}
.add-down {
  width: 240px;
  border: none;
  background-color: #e9f6f9;
  height: 30px;
}
.input-adddown {
  width: 260px;
  background-color: #e9f6f9;
}
.steps {
  padding-left: 500px;
}
.Determine {
  margin-left: 5%;
}
.newadd {
  width: 100%;
  height: 5%;
  padding-left: 95%;
}
.border-bottom {
  padding-left:5px;
  color:#333333;
  padding-bottom: 5px;
  border-bottom: 1px solid #ccc;
}
.add-div {
  width: 240px;
  height: 146px;
  float:left;
  margin:3px 0 5px 10px;
  background-color:#fff;
  border:1px solid #f1f1f1;
}
.div-up {
  width: 100%;
  height: 73px;
  background-color:#e9f6f9;
  text-align: center;
  line-height: 73px;
}
.input-name {
  margin-top: 2%;
  margin-left: 8%;
  height: 27%;
}
.addOption-input {
  border: none;
  font-size: 35px;
  text-align: center;
  height: 30px;
  width: 30px;
  line-height: 15px;
  color: rgb(115, 155, 230);
  background-color: #fff;
  border-radius: 90px;
}
.button-column {
  margin-top: -10px;
  border: none;
  font-size: 35px;
  text-align: center;
  height: 30px;
  width: 30px;
  line-height: 15px;
  color: #fff;
  background-color: rgb(115, 155, 230);
  border-radius: 90px;
}
.input-ergodic {
  margin-left: 10%;
  margin-top:2%;
  width: 90%;
  height: 27%;
}
.spacing-state {
  padding-bottom: 10px;
}
.div-delete{
    border: none;
  font-size: 35px;
  text-align: center;
  height: 30px;
  width: 30px;
  line-height: 15px;
  color: rgb(115, 155, 230);
  background-color:#e9f6f9;
  float:right;
}
.delete-col{
  border: none;
  font-size: 35px;
  text-align: center;
  height: 30px;
  width: 30px;
  line-height: 15px;
  color: rgb(115, 155, 230);
  background-color:#e9f6f9;
  position: absolute;
  right:0px;
  top:0px;
}
.deleinner{
  float:right;
  margin-top:5px;
  margin-right:3px;
  border:none;
  background-color:#fff;
  color:rgb(184, 180, 180);
  height:20px;
  width:20px;
  line-height: 20px;
  font-size: 28px;
  text-align: center;
}
.Pollution{
  float:left;
  margin-top:5px;
}
.es-input{
  /* float:left; */
  width:100px;

  position: absolute;
  top:2px;
  left: 110px;
}
.center{
  width:260px;
  text-align: center;
  display: flex;
  justify-content: center;
}
.center>div {

  width:130px;
  flex:1;
}
.all-down{
  width:1600px;
  height:700px;
}
.nextStep{
  width:1600px;
  height:30px;
  text-align: center;
}
.next-content{
  height:650px;
  width:1600px;
  overflow-x: hidden;
}
.next-content::-webkit-scrollbar {
  width: 4px;
  /*height: 4px;*/
}
.next-content::-webkit-scrollbar-thumb {
  border-radius: 10px;
  -webkit-box-shadow: inset 0 0 2px #ccc;
  background: rgba(0, 0, 0, 0.2);
}
.next-content::-webkit-scrollbar-track {
  -webkit-box-shadow: inset 0 0 2px #f2f2f2;
  border-radius: 0;
  background: #f1f1f1;
}
.next-step{
  height:45px;
  width:1600px;
  text-align: center;
  line-height: 45px;
}

.first_box{
  width:1600px;
  height:185px;
  display: flex;
  align-items: center;
  overflow-x: auto;
  border:1px solid rgb(153, 148, 148);
}
.firstShow{
  width:200px;
  height:130px;
}
.mode_box{
  float:left;
  align-items: center;
  margin:10px 20px;
  border:1px solid #f1f1f1;
  width:182px;
  height:90px;
  position: relative;
}
.cha{
  width:30px;
  height:30px;
  font-size:35px;
  color:#3333;
  position: absolute;
  right:0px;
  top:0px;
  line-height: 30px;
  text-align: center;
}
.selected-box{
  width:180px;
  height:70px;
  line-height: 70px;
  text-align: center;
  background-color: #fff;
}
.selectA{
  width:100px;
  height:30px;
}
.p-div{
  width:100%;
  padding-left:20px;
  padding-bottom:5px;
  border-bottom:1px solid #ccc;
}
.item-name{
  height:50px;
  width:180px;
  line-height: 50px;
  text-align: center;
  background-color: #e9f6f9;
  color:#333333;
}
.plus{
  font-size:35px;
  color:#333333;
  height:30px;
  width:30px;
  text-align: center;
  line-height:30px;
  margin:0 auto;
}
.down-content{
  width:1630px;
  height:650px;
  overflow: auto
}
</style>
