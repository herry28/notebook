# css样式

- 解决span元素空格：给父元素添加`font-size:0;`
- `display:inline-block;`可以实现高度不等的布局
- `word-break:break-all;`：强制英文单词断行











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
   - 点赞：
     - 未登录时点赞：将文章id及登录状态存储到`sessionStorage`中
       - 先判断点赞状态，若已点赞，则提示用户
       - 判断用户是否登录，未登录，声明一个arr存储文章id及点赞状态（arr初始值来自`缓存`中或`[ ]`）
       - 判断文章点赞状态，若未点赞，则将其改为点赞状态；并判断该文章是否已存在缓存中，如果没有，则将其push到arr中，然后存入缓存中
       - 调用点赞接口
       - 点赞成功，重新加载热点详情信息：
         - 根据用户是否登录，判断登录状态是来自热点详情接口返回的数据还是缓存中（查找到当前文章）
   
5. 我的收藏页面：3个接口均需登录认证，authMsg字段值为加密后的。先调check接口，得到check接口的返回值，再加密，因为是异步的，必须return 加密后的结果，在调用时用await接收该结果，再调用其他接口。
   - 收藏列表展示
   - 收藏与取消收藏
   - 置顶与取消置顶

   方案更改：登录成功后，每隔15分钟，`轮询`（刷新续命，供后台判断用户身份合法性）调用check接口，得到authMsg，存储到本地缓存中，`退出登录时，再清空authMsg`。

   - 其他接口需要authMsg时，`直接`从缓存中取（不能将其放到data或computed中，因为当缓存中的authMsg变化时，接口中的authMsg不会变）

6. 热点百科首页搜索栏默认展示最热文章标题

7. 怎么获取异步的结果？
   - `return 异步的结果`
   - `await 接收异步的结果`
   
8. `一个元素hover时，显示另一个元素`

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





# 商户入驻

1. 建融家园的商户入驻页面是通过iframe嵌入oss运营系统的商户入驻，当点击认证时，商户入驻页面被替换成第三方上上签页面，待认证完成，再回到商户入驻页面

2. 因新版chrome浏览器无法读取iframe嵌的上上签页面的cookie，导致功能无法实现，所以需要将上上签页面以新标签页打开，而不是iframe
3. 修改后端渲染的商户入驻页面，当点击认证时得到上上签的url，`window.open(url)`以新的标签页打开上上签页面，并封装弹窗提示用户，进行下一步页面的跳转







# 懂你找图uniapp











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

   - `uni-search-bar`组件
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
     - 清空搜索历史（数组和本地）
     - 点击搜索历史跳转到商品列表页面
   - 搜索建议与搜索历史按需展示：当搜索建议列表长度不为0时，展示搜索建议，否则展示搜索历史
8. 商品列表页面：

   - 将页面跳转时携带的参数转存到请求参数对象中
   - 将商品item项封装成`goods-item`组件
   - 上拉加载更多：
     - 配置`onReachBottomDistance`字段，上拉触底的距离
     - `onReachBottom`事件处理函数中监听页面的上拉触底事件：页码值+1--->调用函数
     - 改造函数：当请求成功后，进行新旧数据的拼接
     - 通过`节流阀`防止发起额外的数据请求：isLoading（即第2次请求回来后，才能发起第3次请求）
     - 判断数据是否加载完毕：pagenum*pagesize >= total
   - 下拉刷新：
     - 配置`"enablePullDownRefresh":true,`
     -  监听onPullDownRefresh：重置关键数据--->重新发起请求--->修改函数，接收cb，并按需进行调用
   - 跳转到商品详情页
9. 商品详情页面：
   - 获取商品详情数据
   - 渲染轮播图区域--->轮播图大图预览（`uni.previewImage()`）
   - <rich-text :nodes=""></rich-text>组件渲染商品详情信息（富文本）
   - 解决商品图文详情中图片底部空白间隙的问题：
     - 替换后端返回的img标签
     - 使用字符串的replace()方法，为img标签添加行内样式`sytle="display:block;"`，从而解决图片底部空白间隙问题
   - 解决`webp`格式图片在ios设备无法正常显示的问题：利用正则，将webp替换为jpg
   - 解决商品价格、名称等闪烁问题：
     - 因为商品详情数据请求回来之前为空对象，因此初次渲染页面时会导致闪烁
     - 使用v-if控制页面有值时才显示
   - 使用`uni-goods-nav`组件渲染商品导航区域：
     - 点击购物车--->跳转购物车页面
10. 加入购物车：
    - 配置vuex--->创建购物车的store模块--->映射数据：...mapState('cart',[ ])
    - 加入购物车（addToCart）：
      - 先判断购物车是否已存在该商品，若存在，则更新数量；若不存在，则直接push
      - 将addToCart方法映射到商品详情页
      - 当点击加入购物车时--->组织商品信息对象--->调用addToCart，将商品信息对象存储到购物车中
    - 动态统计购物车中商品总数量：
      - vuex的cart模块定义total的getters属性，循环cart，得到商品总数量
      - 将total映射到商品详情页
      - 监视total变化，动态为购物车按钮的徽标赋值，并优化total监听器：
        - 普通函数，页面加载后不会调用
        - 对象形式，当设置`immediate:true`时，页面初次加载完后会立即调用
    - 持久化存储购物车的商品数据：
      - vuex的cart模块定义saveToStorage方法
      - 当数据变化时，通过commit方法，调用cart命名空间下的saveToStorage：this.commit('cart/saveToStorage')
      - 修改state中的cart数组：读取本地存储的购物车数据
    - 动态为tabbar页面设置数字徽标：
      - 将store中的total映射到cart页面
      - 在cart页面刚显示出来时，立即调用setBadge方法，onShow(){ this.setBadge() }
      - `uni.setTabBarBadge({})`
    - 将设置tabbar徽标的代码抽取为mixins：因为跳转到首页、分类、购物车、我的页面时都需要设置徽标
      - 根目录下创建mixins文件夹--->向外导出一个mixins对象（设置tabbar徽标的代码）
      - 在首页、分类、购物车、我的页面导入mixins对象--->通过`mixins:[]`混入到当前页面中
11. 购物车页面：
    - 将vuex的cart模块的购物车数据映射到购物车页面，循环渲染
    - goods-item组件：
      - 封装radio勾选状态--->通过props属性控制radio按需展示--->动态绑定radio的checked值
      - 封装`radio-change`事件：监听radio的click事件--->触发radio-change，并传递商品id及商品state
      - 修改购物车中商品的勾选状态：vuex的cart模块定义修改勾选状态的方法--->映射到cart页面--->当radio改变时调用修改状态的方法
    - goods-item组件：
      - 封装`uni-number-box`组件，通过props属性控制uni-number-box按需展示
      - 封装num-change事件：监听uni-number-box的change事件--->触发num-change，并传递商品id及商品数量
      - 修改购物车中商品的数量：vuex的cart模块定义修改商品数量的方法--->映射到cart页面--->当num改变时调用修改数量的方法
    - 滑动删除：
      - `uni-swipe-action、uni-swipe-action-item`组件，渲染滑动删除的ui效果
      - 删除购物车中的商品：vuex的cart模块定义删除商品的方法（过滤出不需要删除的商品）--->映射到cart页面--->当点击删除时调用删除方法
    - 收获地址组件：
      - 根据收获地址对象，按需展示选择收获地址或收获信息
      - 选择收获地址：
        - `uni.chooseAddress()`
        - 建user模块，将address存储到vuex中
        - 将vuex中的address持久化存储到本地：
          - mutations中定义持久化存储本地的方法saveAddressToStorage
          - undateAddress方法中调用：this.commit('user/saveAddressToStorage')
          - 读取本地存储的收获地址，初始化address对象：address:JSON.parse(uni.getStorageSync('address') || '{}')
          - 抽取detailAddress为getters
      - 解决收获地址授权失败问题：判断用户是否授权，若没有授权，则让用户重新授权
        - `uni.showModal()`：弹窗提示用户未打开地址权限
        - 若点击确认，则重新授权：`uni.openSetting()`
      - 解决iphone真机无法重新授权问题：加上iphone上错误消息判断
    - 结算组件：
      - 动态渲染已勾选商品的总数量：
        - vuex中定义checkedCount计算属性
        - filter方法从购物车中过滤出已勾选的商品
        - reduce方法将已勾选的商品总数量进行累加
        - mapGetters映射checkedCount：...mapGetters('模块名'，['变量名'])
      - 动态渲染全选radio的选中状态：
        - mapGetters映射商品总数量
        - 定义计算属性：购物车的商品总数量与购物车已选中的商品总数量进行对比
      - 实现商品的全选与反选：
        - vuex中定义updateAllGoodsState方法：循环更新每件商品的勾选状态，并持久化存储到本地
        - mapMutations映射updateAllGoodsState方法
        - 当点击全选时，调用updateAllGoodsState方法，传递最新的状态
      - 动态渲染商品的总价格
        - vuex中定义checkedAmount计算属性
        - filter方法从购物车中过滤出已勾选的商品
        - reduce方法将已勾选的商品价格进行累加（`必须return累加的值`）
        - mapGetters映射checkedAmount
      - 动态计算tabbar中购物车的徽标值：
        - 监听mixins中total的变化
        - 调用设置徽标值的方法
    - 渲染购物车为空时的页面
12. 登录与支付：
    - 点击结算按钮时进行条件判断：
      - 是否勾选了商品
      - 是否选择了收获地址
      - 用户是否已登录
    - 根据token，实现登录组件与用户信息组件的按需展示
    - 一键登录：
      - `uni.login()`：获取用户登录凭证code参数
      - button按钮设置`open-type="getUserInfo"--->@getuserinfo="getUserInfo"`--->获取微信用户的基本信息（其他参数）--->将用户的基本信息存储到vuex中，并持久化存储到本地
        - 处理用户取消登录授权的情况：判断是否取消了登录授权，如果取消了，则提示用户
      - 调用登录接口获取token--->将token存储到vuex中，并持久化存储到本地
    - 用户信息组件：
      - 退出登录：清空存储的用户信息
    - 3s后自动跳转到登录页面：
      - 封装展示倒计时提示消息的方法
      - 封装延时导航到登录页面的方法
    - 登录成功后重定回之前的页面：
      - vuex中定义重定向的redirectInfo对象
      - vuex中定义方法，修改redirectInfo对象
      - 当跳转登录页面成功后，更新redirectInfo对象
      - 当登录成功后，根据redirectInfo对象，导航回登录之前的页面
13. 微信支付
    - 请求头中添加token字段
    - 创建订单：
      - 请求创建订单的API：把订单金额、收获笛子、订单中包含的商品信息发送到服务器
      - 服务器响应的结果：订单编号
    - 订单预支付：
      - 请求订单预支付的API：把订单编号发送到服务器
      - 服务器响应的结果：订单预支付的参数对象，里面包含了订单支付相关的必要参数
    - 发起微信支付：
      - 调用`uni.requestPayment()`，发起微信支付，传递参数订单预支付对象
      - 监听uni.requestPayment的回调函数
    - 查询支付的结果
14. 



   

   

   

   

   

   

   

   



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









