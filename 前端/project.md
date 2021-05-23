# 相关概念

八爪图：对页面的颜色、大小、位置等进行标记

base64：适合小图片，减少请求次数

@2x、@3x

## 样式

PXCOOK







# 工单系统

工单系统是用来处理客户的投诉或建议，通过嵌入oss运营系统，复用其机构、角色、权限、用户体系。通过oss运营系统跳转到工单系统，并赋予其相关工单模块的权限。

1. 实现了权限限制：不同岗位有不同的操作权限
2. 实现的功能点：工单创建、工单列表查询、工单详情查看、工单处理、归口部门维护、工单作废、工单催办、工单归档、工单回访、工单收藏、工单导出
3. 封装请求方法：请求参数的加解密
   - rsa解密（私钥，`jsencrypt`库）：从iframe标签的src中获取密文，对密文进行rsa解密（token、key、secret）
   - aes加密（`crypto-js`库）：对请求参数进行aes加密--->拼接成接口所需的key=value--->用md5再次加密
   - aes解密（crypto-js库）：对接口返回的数据做aes解密
   - `js-md5`库
4. 根据业务需求，封装功能函数
5. 监听工单创建、处理、详情页面发布的事件，从而更新工单列表（先路由跳转--->$nextTick中发布事件）
6. 工单导出：调接口--->返回blob流--->转为流对象--->转为流文件--->创建a标签--->将流文件写入到a的href属性--->设置文件名（download属性）--->障眼法隐藏a标签--->将a标签追加到文档对象中--->点击a标签--->删除a标签
   - 导出报错时返回json，成功返回文件流对象。需判断返回的blob流的type类型，如果报错，则将blob对象再转为json对象，并提示导出报错信息，不再导出

```javascript
//后端返回blob流文件，前端接收并导出
async exportExcel(){
  let params = {}
  const res = await this.$api.export(params) //调后台接口，得到blob流
  //将blob流先转为流对象，再转为流文件
  let url=window.URL.createObjectURL(new Blob([res]),{type:"application/vnd.ms-excel"})
  let a=document.createElement('a')//创建a标签
  a.href=url//将流文件写入到href属性
  a.setAttribute('download','工单列表数据')//设置文件名
  a.style.display='none'//障眼法隐藏a标签
  
  document.body.appendChild(a)//将a标签追加到文档对象中
  a.click()//模拟点击了a标签，会触发a标签href属性的读取，浏览器就会自动下载了
  a.remove()//一次性的，用完就删除a标签
}


```









# 热点百科

建融家园pc端热点百科改造：用户可以对已审核发布的信息进行浏览、收藏与取消收藏、分享等。

1. 产品经理开需求会议，讲解需实现的功能点，确定开发进度
2. 根据ui图，搭建热点百科首页、详情页、收藏页的静态页面，并将可复用的进行组件封装
   - 与ui人员沟通，获取需要的切图并展示
3. 与后端联调接口
4. 重构热点百科详情页（之前由iframe嵌套），修改富文本中图片大小
   - 二维码分享：`qrcodejs2库`，并解决多次点击生成多个二维码问题
   - 最新热点模块：跳转到详情页
   - 收藏与取消收藏
   - 点赞
5. 我的收藏页面：3个接口均需登录认证，authMsg字段值为加密后的
   - 收藏列表展示
   - 收藏与取消收藏
   - 置顶与取消置顶
6. 热点百科首页搜索栏默认展示最热文章标题

```javascript
//二维码
import QRCode from 'qrcodejs2'
//放置生成二维码图片的容器
<div id="qrcode" ref="qrcode"></div>



methods:{
  //生成二维码的方法
  qrcode(url){
    this.$refs.qrcode.innerHTML='',//解决多次点击生成多个二维码问题：生成二维码之前先将存放二维码的标签清空
      new QRCode('qrcode',{
        width:200,//
        height:200,
        text:url,//显示内容，必须是utf-8编码格式
    })
  }
  //调用二维码
  handleShare(){
    let codeUrl=window.location.href
    this.$nextTick(()=>{//确保二维码容器已经存在
      this.qrcode(codeUrl)
    })
  }
  
}



//修改富文本中图片大小
updated(){//在此钩子函数中才可以获取到img节点
  let el=this.$refs.content
  let imgList=el.querySelectorAll('img')
  for(let i in imgList){
    if(imgList[i].style){
      imgList[i].style.width="70%"
    }
  }
}

```













# 懂你找图uniapp











# 黑马优购uniapp小程序





# 黑马优购uniapp小程序







# 黑马优购小程序uniapp

1. 封装请求方法、封装消息提示方法

2. 小程序分包：将tabbar相关的4个页面放到主包中，其他页面如商品详情页、商品列表页放到分包中
   - 分包：减少小程序首次启动时的加载时间
   
3. 实现首页轮播图展示及跳转到商品详情页面

4. 首页导航数据展示及跳转到分类页面、楼层数据的展示（图片宽度是`动态`的）及跳转到商品列表页面（处理返回的navigator_url，动态拼接）
   - 分包的路径需写到pages.json中
   
5. 分类：
   - 动态设置可滚动的高度：let sysInfo=uni.getSystemInfoSync()
   - 切换一级分类时，动态设置滚动条的位置（scroll-view的scroll-top属性）
   - 点击三级分类跳转到商品列表页面，并传递商品分类id
   
6. 搜索组件的封装：

   - uni-icons组件
   - 通过自定义属性增强搜索组件的通用性，如自定义背景颜色和圆角尺寸
   - 为自定义搜索组件封装click事件：组件内部触发，点击跳转到搜索页面
   - 首页搜索组件吸顶效果

7. 搜索页面：

   - uni-search-bar组件

   - 搜索框自动获取焦点：修改源码，data中的show、showSync修改为true

   - 搜索框防抖：当触发input事件时，先清除延时器，再重新开启一个延迟器

   - 搜索建议：搜索框变化--->请求搜索建议--->渲染--->点击跳转到商品详情页面

   - 搜索历史：将搜索关键字unshift到histroyList中

     - 解决关键字前后顺序：定义计算属性，return [...histroyList].reverse()
     - 解决关键字重复:
       - 将搜索历史数组转为set对象：const set=new Set(this.histroyList)
       - 调用set对象的delete方法，移除对应的元素：set.delete(this.kw)
       - 调用set对象的add方法，添加对应的元素：set.add(this.kw)
       - 将set对象转回数组：`Array.from(set)`
     - 将搜索历史存储到本地
     - 清空搜索历史
     - 点击搜索历史跳转到商品列表页面

   - 搜索建议与搜索历史按需展示：当搜索建议列表长度不为0时，展示搜索建议，否则展示搜索历史

     



```javascript
//封装请求方法和消息提示方法
// 导入网络请求的包
import { $http } from "@escook/request-miniprogram";
// 将其挂载到uni对象中，供全局使用
uni.$http = $http;
// 请求根路径
$http.baseUrl = "https://www.uinav.com";
//请求拦截器
$http.beforeRequest = function() {
  uni.showLoading({
    title: "数据加载中。。。",
  });
};
// 响应拦截器
$http.afterRequest = function() {
  uni.hideLoading();
};

// 封装弹窗的方法
uni.$showMsg = function(title = "数据请求失败", duration = 1500) {
  uni.showToast({
    title,
    duration,
    icon: "none",
  });
};



//分包步骤
//1.在项目根目录下，创建分包的根目录，命名subpkg
//2.在pages.json中，和pages节点平级的位置声明`subPackages`节点，用来定义分包相关的结构
{
  "pages":[
     "path":"",
       "style":{
        
        }
  ],
  "subPackages":[
    {
      "root":"subpcg",
      "pages":[
        "path":"",
        "style":{
        
        }
      ]
    }
  ]
}

//动态设置可滚动的高度：
let sysInfo=uni.getSystemInfoSync()
let height=sysInfo.windowHeight
//首页搜索组件吸顶效
.search-box{
	position:sticky;//设置定位的效果为吸顶
  top:0;//吸顶的位置
  z-index:999;//提高层级
}
```









