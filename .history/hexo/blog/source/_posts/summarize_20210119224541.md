---
title: 日常总结
tags: 总结
---
# 1: router-view
路由匹配到的组件将显示在这里 

# 2: 三位逗号分开
```
Vue.filter('numFix', num => {
  let result = '',
    nums = []
  num = (num || 0).toString()
  nums = num.split('.')
  while (nums[0].length > 3) {
    result = ',' + nums[0].slice(-3) + result
    nums[0] = nums[0].slice(0, nums[0].length - 3)
  }
  if (nums[0]) {
    result = nums[0] + result + (nums[1] ? '.' + nums[1] : '')
  }
  return result
})
```
或者
```
  formatNumber(num){
    num = num.toFixed(2);
    num = parseFloat(num)
    num = num.toLocaleString();
    return num;
  }
```

# 3: isNotZero函数
```
Vue.prototype.isNotZero = value => {
  if ([null, undefined, ''].includes(value)) {
    return false
  } else {
    return true
  }
}
```
# 4: |位运算符
6.66| hundred | priceFixed
```
function(value) {
  return parseFloat(value) * 100
}

 function(value) {
  return (value / 100).toFixed(2)
}
```
# 5：http 协议状态码
200：请求成功
201：请求成功并且服务器创建了新的资源
302：服务器目前从不同位置的网页响应请求，但请求者应继续使用原有位置来响应以后的请求。
304：自从上次请求后，请求的网页未修改过。服务器返回此响应时，不会返回网页内容。
400：服务器不理解请求的语法。
401：未经授权 访问被拒绝。
404：请求的资源（网页等）不存在
500：内部服务器错误
503： 服务器错误

# 6：ios input输入时白屏
在input的父元素添加相对定位就行了 style="position:relative"
# 7：input获取焦点时 页面被放大
设置meta标签
```
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="viewport" content="width=device-width, initial-scale=1.0, minimum-scale=1.0, maximum-scale=1.0, user-scalable=no">
```
# 8：防抖节流
```
const debounce = (fn, delay) => {
  let timer = null;
  return (...args) => {
    clearTimeout(timer);
    timer = setTimeout(() => {
      fn.apply(this, args);
    }, delay);
  };
};
```
```
// 节流函数
const throttle = (fn, delay = 500) => {
  let flag = true;
  return (...args) => {
    if (!flag) return;
    flag = false;
    setTimeout(() => {
      fn.apply(this, args);
      flag = true;
    }, delay);
  };
};
```
# 9instanceof 原理
```
模拟 instanceof
function instance_of(L, R) {
  //L 表示左表达式，R 表示右表达式
  var O = R.prototype; // 取 R 的显示原型
  L = L.__proto__; // 取 L 的隐式原型
  while (true) {
    if (L === null) return false;
    if (O === L)
      // 这里重点：当 O 严格等于 L 时，返回 true
      return true;
    L = L.__proto__;
  }
}

```
# 10mapGetter
mapGetter函数是将store中的getter映射到局部计算属性
# 11 computed和methods
我们可以将同一函数定义为一个方法而不是一个计算属性。两种方式的最终结果确实是完全相同的。然而，不同的是计算属性是基于它们的响应式依赖进行缓存的。只在相关响应式依赖发生改变时它们才会重新求值。这就意味着只要 message 还没有发生改变，多次访问 reversedMessage 计算属性会立即返回之前的计算结果，而不必再次执行函数。
# 12 vue-cloak 
vue-cloak 可以防止页面闪烁
active-class是vue-router模块的router-link组件中的属性，用来做选中样式的切换
# 13elementUI render-header显示问题
```
    renderSequence(h, { column }) {
      return h('span', [
        h('span', {
          domProps: {
            innerHTML: column.label
          }
        }),
        h(
          'el-tooltip',
          {
            props: {
              effect: 'dark',
              placement: 'bottom-start'
            },
            style: {
              position: 'relative',
              left: '5px',
              opacity: 0.6
            }
          },
          [
            h('i', {
              class: 'el-icon-question',
              style: {
                cursor: 'pointer'
              }
            }),
            h('span', {
              domProps: {
                innerHTML: '数字越小，售卖展示越靠前'
              },
              slot: 'content'
            })
          ]
        )
      ])
    }
```
# 14 关于upload action的问题
一般会做一下处理
```
<el-form-item required label="上传图片：">
          <div v-if="form.cardFaceImg" class="card-img">
            <img :src="form.cardFaceImg" alt />
          </div>
          <div style="display: inline-block;">
            <el-upload action="//up.qbox.me/" :show-file-list="true" :file-list="form.fileList" :on-success="AvatarSuccess" :before-upload="AvatarUpload" :data="imgData">
              <el-button :plain="true" type="info">上传图片</el-button>
            </el-upload>
            <p style="color: #9b9b9b;margin-top: 5px;">建议尺寸240*105，不超过500kb</p>
          </div>
        </el-form-item>

```
```
 AvatarSuccess(res, file) {
      this.$set(this.form, 'cardFaceImg', 'https://asset.imuge.net/' + res.key)
      this.form.fileList.push({
        name: 'https://asset.imuge.net/' + res.key
      })
    },
    AvatarUpload(file) {
      let _this = this
      const isLt2M = file.size / 1024 / 1024 < 0.5

      if (!isLt2M) {
        this.$message.error('上传礼品图片大小不能超过 500KB!')
        return false
      }

      return _this.$http.post(apiRoutes.GET_UPLOAD_TOKEN).then(
        res => {
          _this.imgData = {
            token: res.data.uptoken
          }
        },
        res => {}
      )
    }


```
# 15 js 字符串两边截取空白的 trim 的原型方法的实现
```
// 删除左右两端的空格
function trim(str){
 return str.replace(/(^\s*)|(\s*$)/g, "");
}
// 删除左边的空格 /(^\s*)/g
// 删除右边的空格 /(\s*$)/g
```
# 16 vue总结
首先，我们知道vue实现的单页应用中一般不会去刷新页面，因为刷新之后页面中的vuex数据就不见了。
其次，我们也知道一般情况下，url变更的时候，比如指定location.href、history.push、replace等，页面就会刷新。
那么问题来了，vue页面的页面跳转时怎么实现的？没刷新页面么？没刷新页面，又要改变url，加载新内容怎么做的？
vue-router就是利用pushState这个属性，在页面前进的时候动态改变history的内容，添加一条记录，接着location跟着改变。同时根据router前往的路由获取对应的js资源文件并挂载到目标dom上实现页面内容的更新，但是页面本身并没有刷新。

可以在钩子函数 created、beforeMount、mounted 中进行调用，因为在这三个钩子函数中，data 已经创建，可以将服务端端返回的数据进行赋值。但是推荐在 created 钩子函数中调用异步请求，因为在 created 钩子函数中调用异步请求有以下优点：

能更快获取到服务端数据，减少页面 loading 时间；
ssr 不支持 beforeMount 、mounted 钩子函数，所以放在 created 中有助于一致性；

# 17 vue路由的钩子函数
首页可以控制导航跳转，beforeEach，afterEach等，一般用于页面title的修改。一些需要登录才能调整页面的重定向功能。

beforeEach主要有3个参数to，from，next：

to：route即将进入的目标路由对象，

from：route当前导航正要离开的路由

next：function一定要调用该方法resolve这个钩子。执行效果依赖next方法的调用参数。可以控制网页的跳转。
# 18手机号验证
```
function checkPhone(){ 
    var phone = document.getElementById('phone').value;
    if(!(/^1(3|4|5|7|8)\d{9}$/.test(phone))){ 
        alert("手机号码有误，请重填");  
        return false; 
    } 
}
```
^1(3|4|5|7|8)d{9}$，表示以 1 开头，第二位可能是 3/4/5/7/8 等的任意一个，在加上后面的 d 表示数字 [0-9] 的 9 位，总共加起来 11 位结束。

**手机号码格式验证方法(正则表达式验证)支持最新电信 199， 移动 198， 联通 166**

let valid_rule = /^(13[0-9]|14[5-9]|15[012356789]|166|17[0-8]|18[0-9]|19[8-9])[0-9]{8}$/;

if ( ! valid_rule.test(phone_number)) {
     alert('手机号码格式有误');
     return false;
}
# 19  new操作符做了什么
1、创建一个空对象，并且 this 变量引用该对象，// lat target = {};
2、继承了函数的原型。// target.proto = func.prototype;
3、属性和方法被加入到 this 引用的对象中。并执行了该函数func// func.call(target);
4、新创建的对象由 this 所引用，并且最后隐式的返回 this 。// 如果func.call(target)返回的res是个对象或者function 就返回它

```
function new(func) {
	lat target = {};
	target.__proto__ = func.prototype;
	let res = func.call(target);
	if (typeof(res) == "object" || typeof(res) == "function") {
		return res;
	}
	return target;
}

```
# 20 dayJS轻量化工具
当前时间 dayjs()
时间字符串 dayjs('2018-06-03')
时间戳 dayjs(1528361259484)
Date 对象 dayjs(new Date(2018,8,18))
复制 dayjs().clone()
检测当前 Dayjs 对象是否是一个有效的时间 dayjs().isValid()
获取 年 ： dayjs().year() 月 ： dayjs().month() 日 ： dayjs().date() 星期 ： dayjs().day() 时 ： dayjs().hour() 分 ： dayjs().minute() 秒 ： dayjs().second() 毫秒 ： dayjs().millisecond()
设置 dayjs().set('year',2017) dayjs().set('month',9)
增加时间并返回一个新的 Dayjs() 对象 dayjs().add(7, 'day') dayjs().add(7, 'year')
减少时间并返回一个新的 Dayjs() 对象 dayjs().subtract(7, 'year') dayjs().subtract(7, 'month')
返回当前时间的开头时间的 Dayjs() 对象，如月份的第一天。 dayjs().startOf('year') dayjs().startOf('month')
返回当前时间的末尾时间的 Dayjs() 对象，如月份的最后一天。 dayjs().endOf('month') dayjs().endOf('year')
格式化 dayjs().format() dayjs().format('YYYY-MM-DD dddd HH:mm:ss.SSS A')
时间差 dayjs('2018-06-08').diff(dayjs('2017-06-01'),'years') dayjs('2018-06-08').diff(dayjs('2017-06-01'),'day') dayjs('2018-06-08').diff(dayjs('2017-06-01'),'hour')
Unix 时间戳 (毫秒) dayjs().valueOf()
Unix 时间戳 (秒) dayjs().unix()
返回月份的天数 dayjs().daysInMonth()
返回原生的 Date 对象 dayjs().toDate()
返回包含时间数值的数组 dayjs().toArray()
当序列化 Dayjs 对象时，会返回 ISO8601 格式的字符串 dayjs().toJSON() //2018-06-08T02:44:30.599Z
返回 ISO8601 格式的字符串 dayjs().toISOString() //2018-06-08T02:46:06.554Z
返回包含时间数值的对象 dayjs().toObject()
字符串 dayjs().toString()
检查一个 Dayjs 对象是否在另一个 Dayjs 对象时间之前 dayjs('2018-06-01').isBefore(dayjs('2018-06-02'))
检查一个 Dayjs 对象是否和另一个 Dayjs 对象时间相同 dayjs().isSame(dayjs())
检查一个 Dayjs 对象是否在另一个 Dayjs 对象时间之后 dayjs().isAfter(dayjs())

# 21 Array.from() 五个超好用的用途
1：Array.from() 第一个用途：将类数组对象转换成数组。
function sumArguments() {
    return Array.from(arguments).reduce((sum, num) => sum + num);
}
sumArguments(1, 2, 3); // => 6

2：Array.from() 可以很容易的实现数组的浅拷贝。
const numbers = [3, 6, 9];
const numbersCopy = Array.from(numbers);
numbers === numbersCopy; // => false

3：数组去重
Array.from() 的入参是可迭代对象，因而我们可以利用其与 Set 结合来实现快速从数组中删除重复项。
```
function unique(array) {
  return Array.from(new Set(array));
}

unique([1, 1, 2, 3, 3]); // => [1, 2, 3]
```
4：使用值填充数组
如果你需要使用相同的值来初始化数组，那么 Array.from() 将是不错的选择。
```
const length = 3;
const init   = 0;
const result = Array.from({ length }, () => init);
result; // => [0, 0, 0]
```
5：生成数字范围
使用 Array.from() 生成值范围。例如，下面的 range 函数生成一个数组，从0开始到 end - 1。
```
function range(end) {
    return Array.from({ length: end }, (_, index) => index);
}
range(4); // => [0, 1, 2, 3]

```

# 22 this.$router.push
1：query 相当于ajax的get传参 在流浪器地址中会带入参数 而params相当于post的传参  不会在浏览器地址中带参数
2：query 要通过path来引入路由 而 params则是通过name来

# 23 vue中ref的作用
ref除了可以获取本页面的dom元素，还可以拿到子组件中的data和去调用子组件中的方法
**this.$router.back()可以返回上一层**

# 24 border-bottom如何指定它的宽度
利用伪元素after
.type-list-item{
            font-size:26px;
            font-weight:400;
            width:100px;
            height:23px;
            color:rgba(0,0,0,1);
            text-align: center;
            position: relative;
          }
          .active{
            color: #E4262D;
            &:after {
              content: '';
              position: absolute;
              bottom: -15px;
              top: auto;
              left: 40px;
              height: 3px;
              width: 22px;
              background-color: #E4262D;
            }
          }

# 25 Object.keys(),Object.values(),Object.entries()区别
Object.keys方法，返回一个数组，成员是参数对象自身的（不含继承的）所有可遍历（enumerable）属性的键名。
```
let obj = { a: 1, b: 2, c: 3 };
for (let key of Object.keys(obj)) {
  console.log(key); // 'a', 'b', 'c'
}
```
Object.values() 返回数组，成员是参数对象自身的（不含继承的）所有可遍历(enumerable)属性的键值
```
let obj = { a: 1, b: 2, c: 3 };
for (let key of Object.values(obj)) {
  console.log(key); // 1, 2, 3
}
```
entries()函数会将 key 和 value 以数组的形式都返回。这样，使用循环或则将对象转为 Map 就很方便了。
```
let obj = { a: 1, b: 2, c: 3 };
for (let [key, value] of Object.entries(obj)) {
  console.log([key, value]); // ['a', 1], ['b', 2], ['c', 3]
}
```

# 26return和return false的区别
1. return返回null，起到中断方法执行的效果，事件处理函数将会继续执行，表单将提交
2. return false，事件处理函数会取消事件，不再继续向下执行。比如表单将终止提交。

# 27 v-lazy
npm install vue-lazyload 有这个插件

# 28 怎么解决iconfont图标不能为渐变色
用background-clip text可以解决 但是现在只有chrome 支持，所以通常想使用它，需要 -webkit-background-clip:text
```
  background linear-gradient(90deg, rgba(253, 110, 88, 1) 0%, rgba(255, 63, 78, 1) 100%)
  background-clip text
  -webkit-background-clip text
  -webkit-text-fill-color transparent //text-fill-color打造镂空文字
```

# 29 dayjs如何把时间戳转化为标准时间
  引入dayjs dayjs是一个轻量的处理时间和日期的JavaScript 库
    formateTime(time) {
      return dayjs.unix(time).format('YYYY-MM-DD HH:mm:ss')
    }

# 30 对于价格的优化
```
  <span class="integer">{{ (item.price + '').split('.')[0] }}</span> //+ ''是把价格变成字符串
  <span class="decimal" v-if="(item.price + '').split('.').length > 1">.{{ (item.price + '').split('.')[1] }}</span>
 ```

# 31 对于从后端取过来的数据优化
**this.orderList = res.result ? res.result.list : []**


【Vuex】mapGetters 辅助函数
mapGetters 辅助函数仅仅是将 store 中的 getter 映射到局部计算属性：
1：mapState工具函数会将store中的state映射到局部计算属性中
        ...mapState({
          add: state => state.add,
          counts: state => state.counts
        })
2：mapGetters 辅助函数仅仅是将 store 中的 getter 映射到局部计算属性：
 …mapGetters(['userInfo'])
3：mapMutations将 store 中的 action 映射到局部计算属性     
…mapMutations({
        'ADD': 'ADD'
      })
...展开运算符
1.展开函数在多个参数的地方使用 。意指用于函数传参
2.多个元素的地方使用，意指用于数组字面量
3.多个边框的地方使用，意指用于解构赋值
...this.$route.query,

# 32 手机号隐藏中间四位
一般都会用正则表达式
```
  var tel = "13122223333";
  var reg = /^(\d{3})\d{4}(\d{4})$/;
  tel = tel.replace(reg, "$1****$2");
```

# 33 v-model
 如果是输入框 , 可以直接使用 v-model="".
 .trim 表示去除输入内容的收尾空格
 .number 表示将输入的字符串转换为数值类型
 .lazy 表示让数据的更新在输入改变时进行.

# 34 tab点击切换时一大一小？
记得给不是active的盒子一个border就可以了

# 35 substring substr slice splice区别
1:substring(start,stop)表示返回从start开始到stop处之间的新字符串，其长度为stop减 start。包含start，但不包含stop，且不修改原字符串。
2: substr（index，length）index：表示起始位置   length：表示截取内容长度  且不修改原字符串
3:slice可操作数组和字符串，但substring和substr只能操作字符串.
slice(start,stop)表示截取从下标start 到下标stop（不包括stop）的之间的元素，并返回新数组/新字符串，并不修改原数组/原字符串.
4:splice(start,length,items)表示从下标start处截取length长度（与substr有点像）的元素后，在start处为原数组添加items，并返回被截取的新数组，splice会直接修改原数组
[具体可看](https://www.cnblogs.com/echolun/p/7646025.html)

# 36 js中!和!!的区别及用法
js中!的用法是比较灵活的，它除了做逻辑运算常常会用！做类型判断，可以用！与上对象来求得一个布尔值
！可将变量转换成boolean类型，null、undefined和空字符串取反都为false，其余都为true。
!null = true
！！常常用来做类型判断，在第一步!（变量）之后再做逻辑取反运算
不好的写法
var a;
if(a!=null&&typeof(a)!=undefined&&a!=''){
    //a有内容才执行的代码  
}
最佳写法
if(!!a){
    //a有内容才执行的代码...  
}
# 37 padStart padEnd
padStart()用于头部补全，padEnd()用于尾部补全。padStart()和padStart()一共接受两个参数，第一个参数用来指定字符串的最小长度，第二个参数是用来补全的字符串。
x'.padStart(5, 'ab') // 'ababx'
如果原字符串的长度，等于或大于指定的最小长度，则返回原字符串。
'xxx'.padStart(2, 'ab') // 'xxx'

# 38 vuex数据异步造成初始化的时候没值报错
1: 在computed赋值该数据 并且在dom渲染的时候做非空的判断
isShow() {
  return this.customerInfo ? true : false
}
2: 通过watch和mouted监听并且初始化vuex数据
**在mounted的时候初始化该数据 并且watch该数据(不推荐)**
3: 使用拓展运算符
[具体可看](https://blog.csdn.net/ink_if/article/details/86076720)

# 39 flex 布局会让position absolute bottom 0 失效？？？？

# 40 this.nextTick()
this.$nextTick()在页面交互，尤其是从后台获取数据后重新生成dom对象之后的操作有很大的优势，这里只是简单的例子，实际应用中更为好用.
使用this.$nextTick()可以等待dom生成以后再来获取dom对象
# 41 跳转
在vue中使用 this.$router.push（{ path:  '/home' }） 默认是替代本窗口
如果想新开一个窗口，可以使用下面的方式：this.$router.resolve
link to :to={name: cart}
# 42 使用navigator.userAgent.toLowerCase()判断登陆端是pc还是手机
    ```initLogin() {
      let ua = navigator.userAgent.toLowerCase()
      this.timer = setInterval(() => {
        /* global androidToJs,bsg */
        /* eslint no-undef: "error" */
        try {
          if (/iphone|ipad|ipod/.test(ua)) {
            if (bsg) {
              //获取pushId, 用于极光推送
              this.getPushId && this.getPushId()
              //获取app品牌（定制登录）
              this.getAppBrand && this.getAppBrand()
              clearInterval(this.timer)
            }
          } else if (/android/.test(ua)) {
            if (androidToJs) {
              //获取pushId, 用于极光推送
              this.getPushId && this.getPushId()
              //获取app品牌（定制登录）
              this.getAppBrand && this.getAppBrand()
              clearInterval(this.timer)
            }
          }
        } catch (error) {
          return error
        }
      }, 10)
    }
    ```
  # 43 JSON.stringfy()
  undefined、任意的函数以及 symbol 作为对象属性值时 JSON.stringify() 将跳过（忽略）对它们进行序列化
  undefined、任意的函数以及 symbol 作为数组元素值时，JSON.stringify() 会将它们序列化为 null
  undefined、任意的函数以及 symbol 被 JSON.stringify() 作为单独的值进行序列化时都会返回 undefined

  # 44 mixins的特点：

  1、方法和参数在各组件中不共享  比如混入对象中有一个 cont:1 的变量,在组件A中改变cont值为5，这时候在组件B中获取这个值，拿到的还是1，还是混入对象里的初始值，数据不共享

  2、值为对象的选项，如methods,components等，选项会被合并，键冲突的组件会覆盖混入对象的，比如混入对象里有个方法A，组件里也有方法A，这时候在组件里调用的话，执行的是组件里的A方法

  3、值为函数的选项，如created,mounted等，就会被合并调用，混合对象里的钩子函数在组件里的钩子函数之前调用，同一个钩子函数里，会先执行混入对象的东西，再执行本组件的

  3、与vuex的区别

  vuex：用来做状态管理的，里面定义的变量在每个组件中均可以使用和修改，在任一组件中修改此变量的值之后，其他组件中此变量的值也会随之修改。

  Mixins：可以定义共用的变量，在每个组件中使用，引入组件中之后，各个变量是相互独立的，值的修改在组件中不会相互影响。

  4、与公共组件的区别

  组件：在父组件中引入组件，相当于在父组件中给出一片独立的空间供子组件使用，然后根据props来传值，但本质上两者是相对独立的。

  Mixins：则是在引入组件之后与组件中的对象和方法进行合并，相当于扩展了父组件的对象与方法，可以理解为形成了一个新的组件。

# 45 cursor: pointer 
鼠标属性 只在pc端生效 在app 设置了无效。

# 46 link和@import的区别？
1.页面被加载的时，link会同时被加载，而@import引用的CSS会等到页面被加载完再加载。
2.@import是css提供的语法，因此只可以引入css文件。而link是html提供的语法，除了css文件还可以引入其他文件。
3.@import会有兼容性问题，只有IE5+才能使用。而link没有兼容性问题。
4.可以通过 JS 操作DOM，插入link标签来改变样式；由于 DOM 方法是基于文档的，无法使用@import的方式插入样式。
5.link引入的样式权重大于@import引入的样式。

#47 什么时候使用keep-alive元素？
当由于数据属性或其他某种响应状态而动态切换组件时，每次将它们切换到渲染状态时，都会被重新渲染。尽管你可能需要这种行为，但在某些情况下重新渲染可能是不合适的。例如在创建时从 API 调用中引入数据的组件。你可能不希望每次动态切换这个组件进行渲染时都调用此 API。这时你可以将组件包含在 keep-alive 元素中。keep-alive 元素缓存该组件并从那里获取它，而不是每次都重新渲染它

# 48 父元素设置了border-radius，子元素应用了transform，并且父元素设置了overflow:hidden，但是却失效？
// 给父元素设置 
{
  position:relative;
  z-index:1;
}
# 49 content-type
isJson: true  json格式的入参
```
  computed: {
    apiParams () {
      let params = {
      id: '',
      name:'',
      age: '',
      telephone:''
    }
    params.sex = sex
    }
  }
  this.$http.post(api.getUserInfo, this.apiParams, {isJson: true})
```
把isjson 去掉 则格式为form-data

# 50 addEventListener和attachEvent介绍, 原生js和jquery的兼容性写法
```
  this.$nextTick(() => {
    let el = document.getElementById('userAgreement')
    if(el.addEventListener) {
      el.addEventListener('click',() => {
        this.goUserAgreement()
      })
    } else if(el.attachEvent) {
      el.attachEvent('onclick',() => {
        this.goUserAgreement()
      })
    }
  })
```
# 51 如何调用安卓的方法
```
export const closeApp = {
  methods: {
    getCloseApp() {
      let ua = navigator.userAgent.toLowerCase() //判断登录端是pc还是手机端
      try {
        if (/iphone|ipad|ipod/.test(ua)) {
          //closeApp() 就是安卓暴露出来的事件
          bsg.closeApp() 
        } else if (/android/.test(ua)) {
          androidToJs.closeApp()
        }
      } catch (error) {
        return error
      }
    }
  }
}
```
安卓如何调用web的方法。在methods定义
mounted: {
  window.setShowSecret = this.setShowSecret
}
在methods定义setShowSecret方法就可以了。

# 52 对于复杂的表单校验
verifyItemExtConfigList 为数组对象 校验的是goodsId 
```
 <el-form-item
    class="shopGoods"
    :prop="'verifyItemExtConfigList.' + index + '.goodsId'"
    :rules="[
      {
        required: true,
        message: '请选择核销的商品',
        trigger: 'blur'
      }
    ]"
  >
</el=form-item>
```
一定不能忘记callback
```
let validPercent = (rule, value, callback) => {
      if (value === '' || value === undefined || value === null) {
        callback(new Error('输⼊不能为空'))
      } else {
        var reg = /^[1-9]\d*$/g
        if (!reg.test(value)) {
          callback(new Error('请输⼊大于0的正整数'))
        } else {
          callback()  //这个也不能忘记 否则触发不了保存事件
        }
      }
    }
```

# 53 flex 0 0 40% 圣杯布局
```
  .text
    font-size 12px
    color #555
    &:nth-child(1)
      flex 0 0 40% //占总宽度的40%
    &:nth-child(2)
      flex 0 0 40% //占总宽度的40%
    &.operate
      flex 0 0 20% //占总宽度的20%
```
# 54 当后台返回的字段名跟前端一些框架绑定的键名不一致时，需要手动转换一下
JSON.parse(JSON.stringify(data).replace(/keyName/g, 'name'))
data为数组，keyName为修改前的键名，name为修改后的键名
另一种方法
```
let newData = [];
  let obj = {};
  this.data.map(item => {
  	obj = item;
    obj['name'] = item['keyName'];  
    delete obj['keyName'];
    newData.push(obj)   
  })
```
# 55 Vue组件data为什么必须是函数
因为组件是可以复用的,JS里对象是引用关系,如果组件data是一个对象,那么子组件中的 data属性值会互相污染,产生副作用。
所以一个组件的 data 选项必须是一个函数,因此每个实例可以维护一份被返回对象的独立的拷贝。new Vue 的实例是不会被复用的,因此不存在以上问题.
# 56 div内置img元素，底部总有间距
用一个div包裹一个img，会出现img不能完全覆盖div空间，总会在底边留下一点空隙。
解决方法
1:div设置font-size: 0或line-height: 0，进而行高为0；
2:img设置 vertical-align: top 或者 middle/，使其不再以默认基线为对齐方式；
3:img设置 display:block，使其变成块级元素。
推荐用第三种
# 57 什么是 Cookie
HTTP 是无状态的协议（对于事务处理没有记忆能力，每次客户端和服务端会话完成时，服务端不会保存任何会话信息）：每个请求都是完全独立的，服务端无法确认当前访问者的身份信息，无法分辨上一次的请求发送者和这一次的发送者是不是同一个人。所以服务器与浏览器为了进行会话跟踪（知道是谁在访问我），就必须主动的去维护一个状态，这个状态用于告知服务端前后两个请求是否来自同一浏览器。而这个状态需要通过 cookie 或者 session 去实现。
cookie 存储在客户端： cookie 是服务器发送到用户浏览器并保存在本地的一小块数据，它会在浏览器下次向同一服务器再发起请求时被携带并发送到服务器上。
cookie 是不可跨域的： 每个 cookie 都会绑定单一的域名，无法在别的域名下获取使用，一级域名和二级域名之间是允许共享使用的（靠的是 domain）
# 58 环境的切换
我们的项目环境可能有开发环境、测试环境和生产环境。我们通过node的环境变量来匹配我们的默认的接口url前缀。axios.defaults.baseURL可以设置axios的默认请求地址就不多说了。
```
// 环境的切换
if (process.env.NODE_ENV == 'development') {    
  axios.defaults.baseURL = 'https://www.baidu.com';} 
else if (process.env.NODE_ENV == 'debug') {    
  axios.defaults.baseURL = 'https://www.ceshi.com';
} 
else if (process.env.NODE_ENV == 'production') {    
  axios.defaults.baseURL = 'https://www.production.com';
}
```
# 59 有关于部分oppo 华为手机的兼容性问题
部分手机（oppo 华为）span 不给inline-block 大小会出现异常.
# 60 兼容ipad样式
有的页面没有给overflow-y: scroll 导致在ipad样式出现问题
# 61 字符串转数字
parseInt()和parseFloat()两个转换函数，将字符串转换成相应的数字。
利用js变量弱类型转换 比如+ - * /
# 62 vue的注意规范之v-if 与 v-for 一起使用
当它们处于同一dom节点，v-for的优先级比 v-if 更高，这意味着 v-if 将分别重复运行于每个 v-for 循环中。
# 63 vue数据源？怎么操作数据？
不要局限于this.$set()赛 数据，还可以在获取接口数据时进行数据操作。
# 64 如何管理你的路由
使用 vue-router，使用import() 生成异步路由，只有在访问时候才会加载模块。
为什么使用 import() 会异步加载模块？
MDN：在您希望按照一定的条件或者按需加载模块的时候，动态import() 是非常有用的。而静态型的import是初始化加载依赖项的最优选择，使用静态import更容易从代码静态分析工具和 tree shaking 中受益。
# 65 ref 与通过类名来获取dom节点的区别
一般来讲，获取DOM元素，需document.querySelector（".input1"）获取这个dom节点，然后在获取input1的值。
但是用ref绑定之后，我们就不需要在获取dom节点了，直接在上面的input上绑定input1，然后$refs里面调用就行。
然后在javascript里面这样调用：this.$refs.input1  这样就可以减少获取dom节点的消耗了
this.$refs.leftScroll.scrollTo(0, 0)
# 66 input标签
input 标签可以接受的类型button checkbox file hidden image password radio reset submit text
<input type="file" class="file" accept="image/*" v-if="!multiple" :capture="capture" @change="uploadBox" name="imageFile" ref="imageFile" />上传图片
# 67 合理借用dayjs
今天之前的日期置灰
```
  let disabled = false
  let nowTime = dayjs().unix()
  let nowTimeText = dayjs().format('MM.DD') + `${'今天'}`
  if(item.day < nowTime) {
    disabled = true
  }
  if(item.text == nowTimeText) {
    disabled = false
  }
  return disabled
```
优雅写法
```
  return dayjs.unix(item.day).isBefore(dayjs(), 'date')
```
# 68 position sticky 粘性定位
sticky是position:relative和position:fixed的结合体.
特性
1.sticky不会触发BFC，
2.z-index无效，
3.当父元素的height：100%时，页面滑动到一定高度之后sticky属性会失效。
4.父元素不能有overflow:hidden或者overflow:auto属性。
5.父元素高度不能低于sticky高度，必须指定top、bottom、left、right4个值之一。

# 69获取表头数据
```
  for (let dayObj of dayList) {
    if (statusList.includes(dayObj.name)) {
      break
    } else {
      statusList.push(dayObj.name)
    }
  }
```
# 70 日期加星期几 合理利用dayjs
```
export function filterYTT(time, format = "M.DD", isFormatWeek = false) {
  if (
    dayjs()
      .subtract(1, "day")
      .format("YYYY-MM-DD") === dayjs(time).format("YYYY-MM-DD")
  ) {
    return "昨天";
  }
  if (dayjs().format("YYYY-MM-DD") === dayjs(time).format("YYYY-MM-DD")) {
    return "今天";
  }
  if (
    dayjs()
      .add(1, "day")
      .format("YYYY-MM-DD") === dayjs(time).format("YYYY-MM-DD")
  ) {
    return "明天";
  }
  if (isFormatWeek) {
    let weeks = ["日", "一", "二", "三", "四", "五", "六"];
    return "周" + weeks[dayjs(time).day()];
  }

  return dayjs(time).format(format);
}
```
# 71下载报表
在开发后台管理系统的时候，很多地方都要用到导出excel 表格，比如将table中的数据导出到本地，那么实现这种需求往往有两种方案：
一、后端开发一个下载链接，前端将这个链接放到 a 标签的 href 中，一点就能下载。
　　优点：对于前端来说实现简单，不用写过多的代码，也不依赖第三方库，兼容性好
　　缺点：如果前端操作数据更改了，需要发给后端才能导出
```
  downLoadFile(FileName) {
    if (!FileName) {
      return false
    }
    var link = document.createElement('a')
    link.href = window.location.origin + '/beauty/export/download/门店业绩/' + FileName
    link.style = 'visibility:hidden'
    link.download = FileName + '.xls'
    document.body.appendChild(link)
    link.click()
    document.body.removeChild(link)
  }

```
二、前端借助一些第三方库实现。
# 72 el-scrollbar
element-ui官网的文档时，发现其左侧导航和右边的内容超出屏幕时，滚动条的样式比较小巧，通过浏览器审查工具查看，发现它是使用了el-scrollbar的样式，跟element-ui的组件样式命名一致。但文档中并没有关于这个 scrollbar组件的使用文档，搜索一番得知这是一个隐藏组件，官方在 github 的 issues 中表示不会写在文档中，需要用的自己看源码进行调用。
**有时很出现横向滚动条**
得处理一下
  &/deep/
    >.el-scrollbar__wrap
        overflow-x hidden
        overflow-y scroll
# 73 富文本编辑器 vue2editor
```
  <vue-editor
    class="editor"
    :class="{ disabled : form.isDetail }"
    :disabled="form.isDetail"
    useCustomImageHandler
    @imageAdded="addImage"
    v-model="form.detail"
  ></vue-editor>

```
# 74 loacalStorage使用
在HTML5中，新加入了一个localStorage特性，这个特性主要是用来作为本地存储来使用的，解决了cookie存储空间不足的问题(cookie中每条cookie的存储空间为4k)，localStorage中一般浏览器支持的是5M大小，这个在不同的浏览器中localStorage会有所不同。
 storage.setItem("c",3)
 **localStorage的优势**
1、localStorage拓展了cookie的4K限制
2、localStorage会可以将第一次请求的数据直接存储到本地，这个相当于一个5M大小的针对于前端页面的数据库，相比于cookie可以节约带宽，但是这个却是只有在高版本的浏览器中才支持的
localStorage的局限
1、浏览器的大小不统一，并且在IE8以上的IE版本才支持localStorage这个属性
2、目前所有的浏览器中都会把localStorage的值类型限定为string类型，这个在对我们日常比较常见的JSON对象类型需要一些转换
3、localStorage在浏览器的隐私模式下面是不可读取的
4、localStorage本质上是对字符串的读取，如果存储内容多的话会消耗内存空间，会导致页面变卡
5、localStorage不能被爬虫抓取到
localStorage与sessionStorage的唯一一点区别就是localStorage属于永久性存储，而sessionStorage属于当会话结束的时候，sessionStorage中的键值对会被清空
```
/**
 *localStorage 存贮
 * @param { String } key  属性
 * @param { string } value 值
 */
export const localStorageSet = (key, value) => {
    if (typeof (value) === 'object') value = JSON.stringify(value);
    localStorage.setItem(key, value)
};
```
```
/**
 *localStorage 获取
 * @param {String} key  属性
 */
export const localStorageGet = (key) => {
    return localStorage.getItem(key)
};
```
```
/**
 * localStorage 移除ss
 * @param {String} key  属性
 */
export const localStorageRemove = (key) => {
    localStorage.removeItem(key)
};
```
# 75 nuxt
Nuxt.js 是一个基于 Vue.js 的通用应用框架。
通过对客户端/服务端基础架构的抽象组织，Nuxt.js 主要关注的是应用的 UI渲染。
# 76 微信网页扫码登陆

# 77 将base64转换为文件
```
  dataURLtoFile(dataurl, filename) {
      var arr = dataurl.split(','), mime = arr[0].match(/:(.*?);/)[1],
          bstr = atob(arr[1]), n = bstr.length, u8arr = new Uint8Array(n);
      while(n--){
          u8arr[n] = bstr.charCodeAt(n);
      }
      return new File([u8arr], filename, {type:mime});
  }

```
# 78 class
ES6的class可以看作只是一个语法糖。
```
class Polygon {
  constructor(height, width) {
    this.area = height * width;
  }
}
console.log(new Polygon(4,3).area);
// expected output: 12
class 是严格模式(strict)
```
使用的时候，也是直接对类使用new命令，跟构造函数的用法完全一致。
# 79 dialog组件扩展
```
  <div class="title" slot="title">
    <span class="text">按日排班</span>
    <span class="info">选择空白格子进行排班</span>
  </div>
```
# 80 获取url的参数
```
/**
 * @param {*} name
 * @param {*} origin
 */
export function getUrlParams(name, origin = null) {
    let url = location.href;
    let temp1 = url.split('?');
    let pram = temp1[1];
    let keyValue = pram.split('&');
    let obj = {};
    for (let i = 0; i < keyValue.length; i++) {
        let item = keyValue[i].split('=');
        let key = item[0];
        let value = item[1];
        obj[key] = value;
    }
    return obj[name];
}
```
# 81 location 包含当前URL的信息
window.location.reload()重新刷新页面

# 82 vue-cookies
this.$cookie.set(key，value,expireTime)

# 83 vue监听组件生命周期
通常我们监听组件生命周期会使用this.$emit('')来通知父组件。其实还有一种简洁的方法 使用**@hook**即可监听组件生命周期.
```
子组件
export default() {
  mounted( ) {
    this.$emit('listenMounted')
  }
}
父组件
<list @listenMounted="listenMounted"></list>
优雅写法
<list @hook:mounted="listenMounted"></list>
翻了一遍源码 组件调用了$on监听的事件名符合以hook开头，当前实例的vm._hasHookEvent会为true,如果hasHookEvent为true，组件会在对应生命周期的时候 通过$emit触发对应的vm.$emit('hook' + hook)
```

# 84 js eventLoop机制

# 85 elementUI popver组件 v-if 放里面和外面的区别？？
```
  <el-popover placement="top-start" width="200" trigger="hover" :content="scope.row.remark" v-if="scope.row.remark !==null">
    <i slot="reference" class="iconfont icon-xingzhuang2" style="font-size: 13px; color:rgba(221,221,221,1);margin-left: 5px;cursor:pointer"></i>
  </el-popover>
```
如果把v-if放i标签里面 则会导致填完信息之后刷新列表时 弹出框不弹出。

# 86 样式总结
:class="{ disabled: checkDayIsCurrentMonth(item.day), active: index === activeDayIndex }"可用，分开来对应不同的样式。
:style="{color: getTimeBoxStyle(time).iconColor}
设为Flex布局以后，子元素的float、clear和vertical-align属性将失效。
如果发现div的宽度高度没上去，给div设置：display:inline-block。
原因：块级对象元素会单独占一行显示，多个block元素会各自新起一行，并且可以设置width,height属性。
而内联对象元素前后不会产生换行，一系列inline元素都在一行内显示，直到该行排满，对inline元素设置width,height属性无效。

# 87 cancelToken 用来取消ajax请求的
在真实项目中，当路由已经跳转，而上一页的请求还在pending状态，如果数据量小还好，数据量大时，跳到新页面，旧的请求依旧没有停止，这将会十分损耗性能，这时我们应该先取消掉之前还没有获得相应的请求，再跳转页面。这里axios给我们提供了一个方法：cancelToken.
```
   this.$http
        .post(apiRoutes.BEAUTY_GOODS_COMMON_LIST, params, {
          cancelToken: new CancelToken(function executor(c) {
            self.cancel = c
            // 这个参数 c 就是CancelToken构造函数里面自带的取消请求的函数，这里把该函数当参数用
          })
        })
        .then(res => {
          if (res.data.status === 1) {
            let result = res.data.result
            this.goodsList = [...this.goodsList, ...result.data]
            if (this.goodsList.length < result.total) 
              this.busy = false
            } else {
              this.busy = true
            }
          } else {
            this.$message.error(res.data.message)
          }
          this.loading = false
        })
        .catch(err => {
          if (err.constructor && err.constructor.name === 'Cancel') {
            return
          }
          this.loading = false
          console.log(err)
        })
```

# 88 小程序公众号开发
**公众号小程序开发** 使用的是kbone框架
1：为了兼容小程序的开发 style里面不要用scoped 命名的时候注意一下
2：img标签图片要显示圆形的话 除了设置border-radius: 50% 还要用mode="aspectFill"

# 89 今日总结
alert(['10','20'])  '10','20' alert输出的内容都会默认转化为字符串
7.7777toFixed(2) // '7.77' toFixed 最后得到的结果是一个字符串
console.log(NaN==NaN) NaN和任何值都不相等 包括它本身 NaN == NaN  //false
```
  setDayOperateList(list = []) {}
  list = []表示如果opts没传就设为[]，防止报错，ES6语法
```

**为什么computed的数据不能双向绑定？？**
computed里的return,实际上是一个get方法，
就是每次vm.someComputed或者页面相应变化的时候{{someComputed}}，实际上访问的是它的get方法
如果只有return的话，是无法实现双向数据绑定的，因为并没有set数据啊，
这样永远获得的都是return里面的值，如果响应依赖的话就get依赖进行运算的值，如果是个常量就会一直返回常量
我们可以增加一个可以做中转的依赖属性，重写计算属性的set和get方法，这样就能实现计算属性的双向绑定。
# 90 vue 实现图片拖动换顺序
```
  <draggable :list="form.M06.itemList" style="display: flex;">
    <div v-for="(item, index) in form.M06.itemList" :key="index" class="m03-col">
      <div class="img-box">
        <img :src="item.goodsIcon || item.goodsCover" alt />
      </div>
      <i class="iconfont avatar-remove" @click="removeItem('M06', index)">&#xe6b7;</i>
    </div>
  </draggable>

```
# 91 axios封装
axios 中，提供是否允许跨域的属性——withCredentials，以及配置超时时间的属性——timeout，通过这两个属性，可以轻松处理跨域和超时的问题。
axios.defaults.timeout = 10000 
axios.defaults.withCredentials = true
# 92 word-wrap
1、word-wrap对行内元素是没有效果的

2、一般情况下，元素拥有默认的white-space:normal（自动换行，不换行是white-space:nowrap），可能是元素中设置的white-space是norwrap导致，无法换行。所以需要
white-space normal
word-break break-all
3、table里面的td设置word-wrap也是没有效果

# 93 el-table-infinite-scroll
插件是结合了element的infinite-scroll做的滚动加载 踩的坑
1 为什么滚动加载事件没有出来？ 先看看div的高度 有可能是高度影响的

# 94 公众号二维码太密集 放大才能扫码识别？
把二维码携带的信息通过scene转化。

# 95 watch 和computed的区别
computed： 
1：支持缓存，只有依赖数据发生改变，才会重新进行计算。
2：不支持异步，当computed有异步操作时，无法监听数据变化。
watch:
1: 不支持缓存，当数据发生改变，就会重新计算。
2：支持异步。
3：接受二个参数，第一个是最新的值，第二个是之前的值。

# 96 复制到粘贴板
```
  document.execCommand('copy')
  copyinput.select()   点击这个例子的按钮以选中所有在<input>元素中的文字
  document.execCommand('Copy')  复制到粘贴板
```
# 97 非指定结合？？

# 98 DOM0 DOM1 DOM2 DOM3
  DOM0 :未形成标准的实验性质的初级阶段的DOM
  DOM1: DOM1级主要定义了HTML和XML文档的底层结构。
  DOM2:DOM2级在原来DOM的基础上又扩充了鼠标、用户界面事件、范围、遍历等细分模块，而且通过对象接口增加了对CSS的支持。
  DOM3:DOM3进一步扩展了DOM

# 99 今日总结
  offsetParent: 返回一个指向最近的（指包含层级上的最近）包含该元素的定位元素或者最近的 table,td,th,body元素。当元素的 style.display 设置为 "none" 时，offsetParent 返回 null。
  offsetTop： 返回的是当前元素相对于offsetParent元素的顶部内边距距离。

  document.title.Document 对象是 Window 对象的一部分，可通过 window.document 属性对其进行访问

  .popper-class[x-placement^='bottom']有个elementUI一定要这样覆盖样式才有用

  watch() {ss
    value(newVal, oldVal) {
      this.getConfig()
    }
  }
  相当于
  this.$watch('panpel.shopId', (newVal, oldVal) => {
    this.getConfig()
  })


# 100 今日总结
为什么0.1+0.2 !== 0.3? 精确度问题？
那么0.2+0.3 === 0.5呢？

# 110 v-if、v-show、v-html 的原理是什么，它是如何封装的？
v-if会调用addIfCondition方法，生成vnode的时候会忽略对应节点，render的时候就不会渲染； v-show会生成vnode，render的时候也会渲染成真实节点，只是在render过程中会在节点的属性中修改属性display值； v-html会先移除节点下的所有节点，调用html方法，通过addProp添加innerHTML属性，归根结底还是设置innerHTML为v-html的值

# 111 清除form里面的某一项校验
this.$refs['form'].clearValidate('name');

# 112 代码规范
:prop="'verifyItemExtConfigList.' + index + '.isFirstVerify'"
:prop="`verifyItemExtConfigList.${index}.isFirstVerify`"
感觉后面那个更加规范一点

# 113 sort
['安徽','河南','北京'].sort((a,b)=>a.localeCompare(b,'zh-CN'))，汉字，如果直接减法的话，得到的是NaN，对数组不进行任何排序




