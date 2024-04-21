## JS

var定义的全局变量

let定义的局部变量

#### js的三种输出方式

```ja
alert(a)//弹窗
document.write(a)//正文
console.log(a)//控制台
```

#### js的函数

```js
function add(a,b)
{
    // return a+b
}
```

#### js的对象

##### 数组

```js
var arr=new Array(1,2,3,"4");
arr.forEach(function(e)
{
     console.log(e)   
     })//遍历
arr.push("5")//添加
console.log(arr)
arr.splice(4)//删除
console.log(arr)
```

##### 字符串

```js
var a=new String(" hello world ");
console.log(a.length)
console.log(a.charAt(4))//查看第4位字符为
console.log(a.trim())//删除前后的空格
console.log(a.indexOf("wo"))//返回字符串的索引值
console.log(a.substring(0,5))//返回该索引值的字符串 
```

##### json

```js
var User='{"name":"cyz","age":18}'
var UserObject=JSON.parse(User)//json转js对象
document.write(UserObject.name)
var jsObj=JSON.stringify(UserObject)//js对象转json
document.write(jsObj)
```

##### BOM

alect(a)//在提示框打印a

confirm(a)//在对话框打印，返回1，0

setInterval(a,2000)//周期循环a，时间间隔为2s
setTimeout(a,2000)//3s后执行a

##### DOM

可以通过document.getElementById获取相应的资源，并设置或更改

getElementById：通过id索引

getElementsByClassName：通过class索引

getElementsByTagName：通过标签名索引

```js
var img2= document.getElementById("img")
img2.src="images/柴犬2.jpg"

var text=document.getElementsByClassName("show")
for (let index = 0; index < text.length; index++) {
    const element = text[index];
    element.innerHTML+= " <font color='red'> very good</font>"
}
var cb=document.getElementsByTagName("input")
for (let index = 0; index < cb.length; index++) {
    const element = cb[index];
    element.checked=true;
    
}
```

##### 事件绑定

onclick：点击

onfocus：聚焦

onblur：失焦

## VUE



new一个vue对象

el为id

data为数据

methods为函数

```js
<script>
    new Vue({
        el:"#app",
        data:{
            message:"hello vue2",
            url:"https://www.baidu.com"
        },
        methods: {
            handle:function()
            {
                alert("我被点击了")
            }
        }
    })
</script>
```

v-bind是标签内的单向绑定

v-model是标签外的双向绑定

v-on为触发事件

v-if，判断执行，若条件符合即不往下判断

v-else-if，判断执行，若条件符合即不往下判断

v-else，判断执行

```js
    <div id="app">
        <input type="text" v-model="message">{{message}}
        <input type="text" v-model="url">
        <a v-bind:href="url">第一个链接</a>
        <input type="button" v-on:click="handle()">
    </div>
```

案例

```html
<script src="js/vue.js"></script>
<body>
    <div id="app">
    <table border="1" cellspacing="0" width="200px">
        <tr>
            <th>编号</th>
            <th>名字</th>
            <th>年龄</th>
            <th>性别</th>
            <th>成绩</th>
            <th>等级</th>
        </tr>
        <tr v-for="(user,index) in users"  v-model="users">
            <td>{{index+1}}</td>
            <td>{{user.name}}</td>
            <td>{{user.age}}</td>
            <td>
                <span v-if="user.gender==1">男</span>
                <span v-if="user.gender==2">女</span>
            </td>
            <td>{{user.score}}</td>
            <td>
                <span v-if="user.score>=90">优秀</span>
                <span v-else-if="user.score>=60">合格</span>
                <span v-else style="color: red;">不合格</span>
            </td>
        </tr>
    </table>
</div>
</body>
<script>
    new Vue({
        el:"#app",
        data:{
        users:[
            {
                name:"cyz",
                age:18,
                gender:1,
                score:90
            },
            {
                name:"kk",
                age:25,
                gender:2,
                score:66
            },            {
                name:"ff",
                age:21,
                gender:2,
                score:100
            },            {
                name:"hei",
                age:3,
                gender:1,
                score:55
            }
        ]
    }
    })
</script>
```

### Vue前端工程化

#### Vue工程的基础框架

```vue
<template>

</template>    

<script>

</script>

<style>

</style>
```

#### 如何运行不同的VUE

在app.vue中

```vue
<template>
  <div>
    <element-build></element-build>
  </div>
</template>
<script>
import ElementBuild from './views/element/elementBuild.vue'; 
export default {
  components: {ElementBuild},
};
</script>
```

#### Axios

获取服务器数据，并返回给前端

在vue工程的script标签中，import axios from "axios";导入包

用get方法获取数据赋值给前端

```js
axios.get("https://yapi.pro/mock/350548/user/id").then((result) => {
  this.dataBase = result.data;
});
```

#### 

#### 使用elementui组件库

在main.js中，

import ElementUI from 'element-ui'

Vue.use(ElementUI)

**示例：**

```vue
<template>
    <el-table :data="dataBase" border>
      <el-table-column property="name" label="姓名" width="180">
      </el-table-column>
      <el-table-column label="图像" width="180">
          <template slot-scope="scope">
          <img :src="scope.row.image">
        </template>
      </el-table-column>
      <el-table-column label="性别" width="180">
        <template slot-scope="scope">
          {{scope.row.gender==1?'男':'女'}}
        </template>
      </el-table-column>
      <el-table-column prop="job" label="职位" width="180">
      </el-table-column>
      <el-table-column prop="entrydate" label="入职日期" width="180">
      </el-table-column>
      <el-table-column prop="updatatime" label="最后操作时间" width="180">
      </el-table-column>
      <el-table-column label="操作">
        <el-button type="primary" plain>添加按钮</el-button>
        <el-button type="success" plain>删除按钮</el-button>
      </el-table-column>
    </el-table>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      dataBase: [],
      formInline: {
        user: "",
        region: "",
      },
      form: {
        date1: "",
        date2: "",
      },
    };
  },
  methods: {
    onSubmit: function () {
      alert("提交成功");
    },
  },
  mounted() {
    axios.get("https://yapi.pro/mock/350548/user/id").then((result) => {
      this.dataBase = result.data;
    });
  },
};
</script>
```

#### Vue路由

在router文件夹下的index声明

```js
const routes = [
  {
    path: '/emp',
    name: 'emp',
    component: () => import( '../views/element/elementBuild.vue')
  },
  {
    path: '/table',
    name: 'table',
    component: () => import( '../views/element/ElementVue.vue')
  },
  {
    path: '/',
    redirect: '/emp'
  }
]
```

## WEB的HTTP协议

##### HTTP请求

格式：get方法的请求体放在请求行上，post则有单独的请求体

![image-20240417090931270](C:\Users\0311cyz\AppData\Roaming\Typora\typora-user-images\image-20240417090931270.png)

请求行：请求方法 请求路径 协议的版本

请求头：

![image-20240417091206409](C:\Users\0311cyz\AppData\Roaming\Typora\typora-user-images\image-20240417091206409.png)



##### HTTP请求响应

常见的http协议响应状态码

![image-20240417090303930](C:\Users\0311cyz\AppData\Roaming\Typora\typora-user-images\image-20240417090303930.png)

格式

![](C:\Users\0311cyz\AppData\Roaming\Typora\typora-user-images\image-20240417090536915.png)

常见的响应头

![image-20240417090758628](C:\Users\0311cyz\AppData\Roaming\Typora\typora-user-images\image-20240417090758628.png)

## 案例

### 案例1：智能学习辅助系统

#### 部门管理（增删查改）

#### 查

```java
@GetMapping("/depts")
public Result list()
{
    log.info("查询全部");
    List<Dept> list= deptService.list();

    return Result.success(list);

}
```

分页查询

```java
    @Override
    public PageBean pageSelect(int page, int pageSize, String name, Short gender,
                               LocalDate begin, LocalDate end) {
/*        Integer total =empMapper.count();
        Integer start=page-1;
        List<Emp> emps=empMapper.pageSelect(start,pageSize);
        PageBean pageBean=new PageBean(total,emps);

        return pageBean;*/


        PageHelper.startPage(page,pageSize);
        List<Emp> list=empMapper.pageHelpSelect(name,gender,begin,end);
        Page<Emp> page1= (Page<Emp>) list;

        PageBean pageBean =new PageBean(page1.getTotal(),page1.getResult());
        return pageBean;

    }
```

```java
public List<Emp> pageHelpSelect(String name, Short gender,
                                LocalDate begin, LocalDate end);
```



#### 增



#### 删



#### 改



#### 员工管理（增删查改）

#### 登陆校验

```java
@PostMapping("/login")
public Result login(@RequestBody Emp emp){
    Emp emp1= empService.login(emp);
    if(emp1!=null)
    {
        Map<String,Object> claim=new HashMap<>();
        claim.put("id",emp1.getId());
        claim.put("name",emp1.getName());
        claim.put("username",emp1.getUsername());
        String s = JwtUtils.generateJwt(claim);
        return Result.success(s);
    }
    return Result.error("用户名或密码错误");
```

#### JWT令牌

生成令牌

```java
    public void j1()
    {
        Map<String,Object> claim=new HashMap();
        claim.put("name","killer");
        claim.put("id","05");
        String cyzyzc = Jwts.builder()
                .signWith(SignatureAlgorithm.HS256, "cyzhskkss")
                .addClaims(claim)
                .setExpiration(new Date(System.currentTimeMillis() + 3600 * 100))
                .compact();
        System.out.println(cyzyzc);
    }
```

获取令牌

```java
@Test
public void j2()
{
    Claims claims= Jwts.parser()
            .setSigningKey("cyzhskkss")
            .parseClaimsJws("eyJhbGciOiJIUzI1NiJ9.eyJuYW1lIjoia2lsbGVyIiwiaWQiOiIwNSIsImV4cCI6MTcxMzQzNTE2M30.swFsclLjzj533hHwmdbagiVKl5hX7k35aLiqprHQEAQ")
            .getBody();
    System.out.println(claims);
}
```

#### Intercrptor拦截器

1，配置

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Autowired
    private LoginInterceptor loginInterceptor;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(loginInterceptor).addPathPatterns("/**").excludePathPatterns("/login");
    }
}
```

2，定义拦截器继承

（1），判断访问路径

（2），判断有没有令牌

（3），判断令牌的token是否正确

（4），放行

拦截器类

```java
@Component
@Slf4j
public class LoginInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {

        //1,判断是否为login,是即放行
        String requestURI = request.getRequestURI().toString();
        if(requestURI.contains("login"))
        {
            return true;
        }
        //2,判断jwt是否存在
        String jwt = request.getHeader("token");
        if(!StringUtils.hasLength(jwt))
        {
            log.info("token中无值");
            Result error=Result.error("NOT_LOGIN");
            String jsonString = JSONObject.toJSONString(error);
            response.getWriter().write(jsonString);
            return false;
        }
        //3,判断token是否正确
        try {
            JwtUtils.parseJWT(jwt);
        }catch (Exception e)
        {
            e.printStackTrace();
            Result error=Result.error("NOT_LOGIN");
            String jsonString = JSONObject.toJSONString(error);
            response.getWriter().write(jsonString);
            log.info("jwt令牌不正确");
            return false;
        }
        //4,放行
        return true;

    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        HandlerInterceptor.super.postHandle(request, response, handler, modelAndView);
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        HandlerInterceptor.super.afterCompletion(request, response, handler, ex);
    }
}
```

工具类

```java
public class JwtUtils {

    private static String signKey = "itheima";
    private static Long expire = 43200000L;

    /**
     * 生成JWT令牌
     * @param claims JWT第二部分负载 payload 中存储的内容
     * @return
     */
    public static String generateJwt(Map<String, Object> claims){


        String jwt = Jwts.builder()
                .addClaims(claims)
                .signWith(SignatureAlgorithm.HS256, signKey)
                .setExpiration(new Date(System.currentTimeMillis() + expire))
                .compact();
        return jwt;
    }

    /**
     * 解析JWT令牌
     * @param jwt JWT令牌
     * @return JWT第二部分负载 payload 中存储的内容
     */
    public static Claims parseJWT(String jwt){
        Claims claims = Jwts.parser()
                .setSigningKey(signKey)
                .parseClaimsJws(jwt)
                .getBody();
        return claims;
    }
```

#### 异常处理

```java
@Slf4j
@RestControllerAdvice
public class GlobalException {


    @ExceptionHandler(Exception.class)
    public Result ex(Exception ex)
    {
        ex.printStackTrace();
        return Result.error("操作失败，请联系管理员");
    }
}
```

#### 事务

```java
@Transactional//事务
@Override
public void delete(Integer id) {
    deptMapper.delete(id);
    //int i=1/0;
    empMapper.deleteByDeptId(id);
}
```

```java
@Transactional(rollbackFor = Exception.class)//指定因为什么异常而回滚数据，若为Exception则为全部
```

![image-20240420145714429](C:\Users\0311cyz\AppData\Roaming\Typora\typora-user-images\image-20240420145714429.png)

```
propagation用于a事务中调用b事务，若为REQUIRES_NEW，则a事务回滚但b不回滚

@Transactional(propagation = REQUIRED)
@Transactional(propagation = REQUIRES_NEW)
```

日志事务管理开关，在application.properties中配置

![image-20240420150040158](C:\Users\0311cyz\AppData\Roaming\Typora\typora-user-images\image-20240420150040158.png)

#### springboot原理
