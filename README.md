# pc-template-mall
# apk

> A Vue.js project

## Build Setup

``` bash
# install dependencies
npm install

# serve with hot reload at localhost:8080
npm run dev

# build for production with minification
npm run build

# build for production and view the bundle analyzer report
npm run build --report
```

For a detailed explanation on how things work, check out the [guide](http://vuejs-templates.github.io/webpack/) and [docs for vue-loader](http://vuejs.github.io/vue-loader).

 1 webpack 3.+版本有bug 可以退到 2.8+

 2 更改配置 自动打开浏览器
//找到/config/index.js文件，你会发现很多参数配置都在里面
// 各种设备设置信息
host: 'localhost', //主机名
port: 8080, // 端口号（默认8080）
autoOpenBrowser: false,//是否自动打开浏览器
//想让浏览器自动打开，只需将false改为true即可，为防止端口号冲突，这里也可以随意更改端口号

 3 打包之后无法本地打开
 更改config/index.js下build的assetsPublicPath属性值为"./"，妥

 4 使用axios  npm install axios --save
 import axios from 'axios';//引入axios
Vue.prototype.$axios = axios; //添加axios到Vue的原型对象上

5 vue项目打包后资源相对引用路径的和背景图片路径问题
1、修改config => index.js => build => assetsPublicPath 中的'/'成为'./'
2、在build => util.js 里找到ExtractTextPlugin.extract增加一行：publicPath: '../../'，主要解决背景图片路径的问题。


6 vue  线上请求出问题
https://www.jianshu.com/p/686f17737ffb

7 vue  使用scss  安装依赖包
步骤一： 安装node-sass、sass-loader、style-loader
    cnpm install node-sass --save-dev
    cnpm install sass-loader --save-dev
    cnpm install style-loader --save-dev  
步骤二： 安装sass-resources-loader	
	cnpm install sass-resources-loader --save-dev
步骤三： 修改build中的utils.js
scss: generateLoaders('sass')

    修改成:

    scss: generateLoaders('sass').concat(
      {
        loader: 'sass-resources-loader',
        options: {
          resources: path.resolve(__dirname, '../src/assets/global.scss')
        }
      }
    )
步骤四：在assets中创建了一个global.scss文件。并在mainjs引入 import './assets/global.scss'
在组件中的style标签添加lang="scss"，就OK了。

8 vue中使用axios
安装axios：cnpm install axios
在main.js文件引入axios：import Axios from 'axios'
将axios全局挂载到VUE原型上：Vue.prototype.$axios=Axios
//方法一传递参数
this.$http.get('https://cnodejs.org/api/v1/topics',{
      params: {                           //参数
        page: this.page,
        limit: this.limit,
      },
    }).then(res => {                   //请求成功后的处理函数     
      this.isLoading = false;
      this.items = res.data.data;
      console.log(this.items);   
    }).catch(err => {                 //请求失败后的处理函数   
      console.log(err)
    })
//方法二传递参数
this.$http.get('https://cnodejs.org/api/v1/topics?page=1&limit=15')
或
this.$axios({  
            url: '/api/Auto',
            method: 'get',
          //params参数必写 , 如果没有参数传{}也可以
            data:{ 
            }
          })
          .then((res)=>{
            this.codeImgUrl="data:image/png;base64,"+res.data.msg;
            this.codeId=res.data.code;
          })
          .catch((err)=>{
            console.log(err)
          })
		  
9 设置axios 请求拦截
// import axios from 'axios';//引入axios;
import axios from './assets/js/axios';   //并依据创建文件
Vue.prototype.$axios = axios; //添加axios到Vue的原型对象上
axios文件
import Axios from 'axios';
var root = process.env.API_ROOT;
// console.log(root+"1111")
const axios = Axios.create();
//请求拦截
axios.interceptors.request.use((config) => {
    //请求之前重新拼装url
    config.url = root + config.url;
    return config;
});
export default axios;

dev:
proxyTable: {
      '/api': {  
        // target: 'http://192.168.84.170:9005',  
        target: '',  
        changeOrigin: true,  
        pathRewrite: {  
            '^/api': '/'  
            //写'/api'就等于写'https://api.douban.com'
            //写"/api/v2/movie/top250"就等于写"https://api.douban.com/v2/movie/top250"
        }  
      }
    }
prod:添加
// API_ROOT: '""'   //线上请求前缀
  API_ROOT: '"http://47.99.201.159:9025"'   //线上请求前缀

10 移动端长按复制文本
当e.prenventDefault()无效时试试这个
*{
  -webkit-touch-callout:none;  /*系统默认菜单被禁用*/
  -webkit-user-select:none; /*webkit浏览器*/
  -khtml-user-select:none; /*早期浏览器*/
  -moz-user-select:none; /*火狐*/
  -ms-user-select:none;  /*IE10*/
  user-select:none;
}
如果页面上有input框也会被禁止输入，记得加上这个
input{
  -webkit-user-select: auto;
}

11 项目中使用svg图片
  1. 安装依赖：npm install svg-sprite-loader --save-dev
  2. 配置build文件夹中的webpack.base.conf.js，主要在两个地方添加代码，如下所示
    exclude: [resolve('src/icons')],       ------url-loader
    {                                      ------svg-sprite-loader
        test: /\.svg$/,
        loader: 'svg-sprite-loader',
        include: [resolve('src/icons')],
        options: {
          symbolId: 'icon-[name]'
        }
    },
  3. 在src/components/component下新建文件夹及文件Svg.vue中内容如下
  <template>
    <svg :class="svgClass" aria-hidden="true" v-on="$listeners">
      <use :xlink:href="iconName"/>
    </svg>
  </template>
  <script>
  export default {
    name: 'SvgIcon',
    props: {
      iconClass: {
        type: String,
        required: true
      },
      className: {
        type: String,
        default: ''
      }
    },
    computed: {
      iconName() {
        return `#icon-${this.iconClass}`
      },
      svgClass() {
        if (this.className) {
          return 'svg-icon ' + this.className
        } else {
          return 'svg-icon'
        }
      }
    }
  }
</script>
  <style scoped>
    /* .svg-icon {
      width: 1em;
      height: 1em;
      vertical-align: -0.15em;
      fill: currentColor;
      overflow: hidden;
    } */
  </style>
  4. 在src/assets下新建svgImg文件夹，及svgImg文件夹下svg文件夹、index.js文件， index.js文件内容如下
  import Vue from 'vue'
  import SvgIcon from '@/components/component/Svg.vue'// svg组件
  
  // register globally
  Vue.component('svg-icon', SvgIcon)
  
  const req = require.context('./svg', false, /\.svg$/)
  const requireAll = requireContext => requireContext.keys().map(requireContext)
  requireAll(req)
  5. 在main.js中引入svg
  import './assets/svgImg'
  6. 在页面使用
  <svg-icon icon-class="test"></svg-icon>
12 修改第三方ui框架使用 穿透函数不生效问题 在全局css修改

13 vue打包.map文件 去掉  index.js cssSourceMap改为false

14全局注册组件
// 以下是注册组件的方法
import NavBar from './components/component/Nav.vue';
Vue.component("NavBar",NavBar); // 全局注册组件

15 iview-admin npm i 报错问题
	在使用2.0+版本iview-admin的时候，npm  i 报错
	code Z_BUF_ERROR npm ERR! errno -5 npm ERR! zlib: unexpected end of file
	可能由五种问题出现
	2  npm i babel-loader babel-core babel-preset-env babel-plugin-transform-runtime -D
	3 npm config get proxy
	返回null
	4 执行npm config get https-proxy
	也返回null
	如果3和4你的机器返回的都不是null的话  需要执行
	npm config set proxy null
	npm config set https-proxy null
	5 执行npm config set registry http://registry.cnpmjs.org/
	6 npm i babel-loader babel-core babel-preset-env babel-plugin-transform-runtime -D

16  iview table等组件不能自适应的问题
在父元素加 :style="widthMain"
data        widthMain:{width:''},//table组件自适应
 created() {
    window.addEventListener('resize', this.getWidth);
    this.getWidth()
  },
  destroyed () {
    window.removeEventListener('resize', this.getWidth)
  },

   method //table组件处理函数
      getWidth:function(){
              var wid =document.body.clientWidth;        //网页可见区域宽(body)
                this.widthMain.width=wid-200+'px';
      },

17  vue element 纯导出功能
	npm install --save xlsx file-saver
	import FileSaver from 'file-saver';
	import XLSX from 'xlsx';
	
	导出函数---wb为需要导出的table  两种方式获取，vue ref  或原生 获取
exportExcel:function() {
//  let wb =XLSX.utils.table_to_book(this.$refs.exportExcel);
 let wb = XLSX.utils.table_to_book(document.querySelector('#out-table'));
 let wbout = XLSX.write(wb, { bookType: 'xlsx', bookSST: true, type: 'array' });
 try {
	 FileSaver.saveAs(new Blob([wbout], { type: 'application/octet-stream' }), 'exportExcel.xlsx')
 } catch (e) { if (typeof console !== 'undefined') console.log(e, wbout) }
 return wbout
},

18 vue iview  导出 https://www.cnblogs.com/aidixie/p/9606855.html
   https://pan.baidu.com/s/1PvP-NxmONNh71SRDvlL_9A 密码：3h82
   在项目下还有有以下操作：

　　　　npm install -S file-saver //用来生成文件的web应用程序

　　　　npm install -S xlsx //电子表格格式的解析器

　　　　npm install -D script-loader //将js挂在在全局下
	outPortxlsx() {
        this.downloadLoading = true;
        require.ensure([], () => {
            const {export_json_to_excel} = require('../../assets/js/Export2Excel') //这个地址和页面的位置相关，这个地址是Export2Excel.js相对于页面的相对位置
            const tHeader = ["支付类型","商品ID","金额","币值","赠送币","说明",'操作']; //这个是表头名称 可以是iveiw表格中表头属性的title的数组
            const filterVal = ['pay','id','money','volue','gift','address']; //与表格数据配合 可以是iview表格中的key的数组
            const list = this.data; //表格数据，iview中表单数据也是这种格式！
            const data = this.formatJson(filterVal, list)
            export_json_to_excel(tHeader, data, '列表excel') //列表excel  这个是导出表单的名称
            this.downloadLoading = false
        })
    },
    formatJson(filterVal, jsonData) {
        return jsonData.map(v => filterVal.map(j => v[j]))
    }
	
19 工具函数的写法
export function square(n) {
	return n * n;
}
import { square } from './util.js'
console.log(square(2)); // 4

在main.js里进行全局注册 Vue.prototype.ajax = function (){}
在所有组件里可调用 this.ajax 以及vue.use()

20 Vue.prototype 和vue.use()的疑问
不管你采用哪种方式，最终实现的调用方式都是 vm.api()
也就是说，两种方法，实现的原理都是在Vue.prototype上添加了一个方法。所以结论是“没有区别”。
再来说说Vue.use()到底干了什么。我们知道，Vue.use()可以让我们安装一个自定义的Vue插件。为此，我们需要声明一个install函数
// 创建一个简单的插件 say.js
var install = function(Vue) {
  if (install.installed) return // 如果已经注册过了，就跳过
  install.installed = true

  Object.defineProperties(Vue.prototype, {
    $say: {
      value: function() {console.log('I am a plugin')}
    }
  })
}
module.exports = install
然后我们要注册这个插件
import say from './say.js'
import Vue from 'vue'

Vue.use(say)
这样，在每个Vue的实例里我们都能调用say方法了。

我们来看Vue.use方法内部是怎么实现的
Vue.use = function (plugin) {
  if (plugin.installed) {
    return;
  }
  // additional parameters
  var args = toArray(arguments, 1);
  args.unshift(this);
  if (typeof plugin.install === 'function') {
    plugin.install.apply(plugin, args);
  } else {
    plugin.apply(null, args);
  }
  plugin.installed = true;
  return this;
};
其实也就是调用了这个install方法而已。

21 echarts https://echarts.baidu.com/tutorial.html#%E5%9C%A8%20webpack%20%E4%B8%AD%E4%BD%BF%E7%94%A8%20ECharts

22 markdown 编辑器
 https://www.jianshu.com/p/04376d0c9ff1

23 富文本 编辑器 https://www.cnblogs.com/wisewrong/p/8985471.html

24 vuex npm install vuex --save

25 npm install es6-promise --save

然后，将下列代码添加到你使用 Vuex 之前的一个地方：
import 'es6-promise/auto'




