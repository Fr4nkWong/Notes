https://github.com/zhuifengshaonianhanlu/pikachu

# 暴力破解

```
- 查看表单登录的验证流程
  - 有无无验证码
  - 有无败次数限制
  - 密码复杂度
- 缩小爆破空间
  - 常见的后台账号密码，可进一步结合社工
```

**1 基于表单的暴力破解**

- 常见的后台账号密码，可进一步结合社工

**2 验证码绕过(on server)**

- 有验证码；验证码仅通过点击图片或提交表单的方式刷新；验证码无期限

**3 验证码绕过(on client)**

- 验证码仅在前端做校验，直接模拟请求可绕过浏览器

**4 token防爆破**

- 有隐藏参数(eg:token)，每次请求后服务端会更新token，每次请求后动态更新token值即可

# XSS

```
过滤输入、输出转义
```

**1 反射型xss(get)**

- 分析业务流程
  - 存在表单提交功能 => 页面刷新 => 页面展示表单提交内容
  - 提交input框有长度的限制：20 => 前端限制
- 表单提交`<script>...</script>` => 页面刷新，浏览器执行页面代码 => 表单提交的代码也会执行
- form默认为method=get => 提交内容会显示在url上 => 通过修改url参数，绕过长度限制进行提交
- 通过浏览器修改\<input>长度限制，再通过\<input>提交

**2 反射型xss(post)**

- 登录表单：之前已经拿到了平台的账号密码，这里的为何不在试一下呢？

**3 存储型xss**

- 分析业务流程
  - 表单提交留言 => ajax请求并响应，动态生成留言
  - 页面刷新，以前的留言会展示在页面上 => 留言存储在数据库
- 表单提交的留言是代码`<script>...</script>`  => 代码存储在数据库 => 页面刷新，浏览器执行代码(留言)

**4 dom型xss**

- 分析业务流程：输入框输入 => 点击按钮 => 弹出一个`<a href="...">/<a>`，href属性的值为输入框的输入
- dom型xss是纯前端漏洞，用户在界面的操作会影响dom，如何让dom在受操作影响时执行代码是关键！
  - `#' onclick='alert("fuck");`
  - `javascript:alert("fuck")`

**5 dom型xss-x**

- 分析业务流程：输入框输入并提交表单(get请求)
  - 输入改变了url
  - 输入被填充到了`<a href="...">/<a>`
- 利用代码同上。相比上一个，本漏洞更有实战价值
  - url发给目标，目标点开url，其页面具有构造好漏洞的页面

**6 xss盲打**

- 分析业务流程：输入框输入并提交表单 => 提交成功
- 提交完了，内容呢？=> 存到数据库里了，应该在后台显示
- 后台的地址呢？=> 都刷到这里了，看看这个系统的url命名习惯，admin_login?
- 后台怎么登录进去？=> 那之前怎么登录进平台的？
- 登进去后，发现之前的留言展示在页面上。那如果留言是可执行代码呢？后台只要打开查看便会执行代码

**7 xss过滤**

- 分析业务流程：输入框输入并提交表单(get请求) => 页面刷新，下方显示的字符串中插有刚才的输入
- 尝试输入框输入`<script>...</script>`并提交，无法正常显示输入的内容 => 过滤了标签符号内的内容
  - 关键字过滤
  - 大小写过滤 `<ScrIpt>...</ScriPt>`
  - 符号过滤

**8 xss之htmlspecialchars**

- 分析业务流程：输入框输入并提交表单(get请求) => 页面刷新，提交的内容插入`<a>...</a>`的href属性和标签内
- htsmlspecialchars()是php的函数，将html标签转义为普通的字符串，从而浏览器不会解析只会原样显示
- 利用href 属性的插入
  - `javascript:alert("fuck")`
  - `#' onclick='alert("fuck");`

**9 xss之href输出**

- `javascript:alert("fuck")`
- `#' onclick='alert("fuck");`

**10 xss之js输出**

- 分析业务流程：输入框输入提交表单(get请求) => 页面刷新，输入放入页面js变量，并按逻辑进行输出
- 输出的内容无法通过输入去执行代码 => 注入点不在输出
- 输入放入页面js变量中，如果输入是代码，提交页面刷新后，代码会自动执行
  - `';</script><script>alert("fuck");</script>`

# CSRF

```
csrf只是借用用户的权限去做事，并没有获取用户的权限
```

**1 CSRF(get)**

- 分析业务流程：登录进个人页面，上面显示了用户的个人信息 => 点击“修改个人信息“，进入信息修改页面 => 按照要求修改信息并点击提交表单，信息修改完成并跳转至个人页面
- 信息修改表单是个get请求，那么可以构造一个url让目标点击链接并触发操作

**2 CSRF(post)**

- 业务流程同上，修改信息的表单改为post提交
- 无法像上一题那样构造url引诱用户点击进行攻击，不过可以构造一个站点伪造表单让用户点击提交 `<form action="目标站点路径">`

**3 CSRF token**



# SQL-Inject

```
后端的报错信息可被利用!
在UpdateXML()、ExtractValue()函数中，当参数xpath_expr路径语法错误时会报错，将xpath_expr中内容当作sql语句执行后结果和报错结果一同返回.
floor()报错，需要count()、rand()、group by，三者缺一不可.
```

**1 数字型注入(post)**

- 分析业务流程：单选框选择，点击查询(post请求) => 页面刷新，查询出的内容显示在页面上
- 通过分析http请求，可发现浏览器端传了一个名为id的值至后端，后端根据此id值进行查询并返回结果到页面上，猜测id在数据库里以数字形式存储
- 猜测SQL语句为`select 字段名 from 表名 where id = ?;`
  - `select 字段名 from 表名 where id = ? or 1 = 1;`
- 注入方式
  - burp拦截post请求，将携带的id值进行修改
  - 浏览器控制台修改页面单选框的value值

**2 字符型注入(get)**

- 分析业务流程：输入框输入，点击查询(get请求) => 页面刷新，查询出的内容显示在页面上
- 通过分析http请求，可发现浏览器端传了一个名为name的值至后端，后端根据此name值进行查询并返回结果到页面上，猜测name在数据库里以字符串形式存储
- 猜测SQL语句为`select 字段名 from 表名 where name= '?';`
  - `select 字段名 from 表名 where id = '?' or 1 = 1 or 1 = '';`
- 注入方式
  - burp拦截post请求，将携带的name值进行修改
  - 在输入框输入可拼接成可执行sql语句的内容

**3 搜索型注入**

- 分析业务流程：输入框输入，点击查询(get请求) => 页面刷新，查询出的内容显示在页面
- 前端发送的一个带有name字段的get请求，后端根据此字段的值进行查询并返回结果至页面上，猜测name在数据库里以字符串形式存储
- 输入`'`，报错提示%号附近语法错误！结合页面功能支持模糊搜索，故猜测SQL语句为`select 字段名 from 表名 where name like '%?%';`
- 注入方式
  - `select 字段名 from 表名 where name like '%' or 1 = '%';`
  - `select 字段名 from 表名 where name like '%%%';`

**4 xx型注入**

- 前端发送的一个带有name字段的get请求，后端根据此字段的值进行查询并返回结果至页面上，猜测name在数据库里以字符串形式存储
- 输入`'`，报错提示`)`附近语法错误！故猜测SQL语句为`select 字段名 from 表名 where name = ('?');`
- 注入方式
  - `select 字段名 from 表名 where name = ('?');` => ` ') or 1=1 or 1= ('`

**5 insert/update注入**

- 分析业务流程：登录页面 => 注册用户 => 登录进用户个人界面 => 修改信息
- `UpdateXML()` `ExtractXML()` `floor()`
- 注册页面
  - 单引号测试是否存在注入点 => 后端报错，根据错误信息猜测sql语句为`insert into member values ('?', md5('?'), '?', '?', '?', '?');`
  - 查数据库名 `insert into member values ('' or updatexml(1, concat(0x7e, database()), 0) or '', md5('?'));` 
  - 查版本 `insert into member values ('' or updatexml(1, concat(0x7e, version()), 0) or '', md5('?'));`
  - 查表名 `insert into member values ('' or updatexml(1,concat(0x7e,(select concat(table_name) from information_schema.tables where table_schema=database() limit 0,1),0x7e),0) or '', md5('?');`
- 修改信息页面
  - 单引号测试是否存在注入点 => 后端报错，根据错误信息猜测sql语句为`update member set username='?', password= '?' where ...; `
  - 查数据库名 `update member set username='' or updatexml(1, concat(0x7e, database()), 0) or '', password = '?' where ...;`

**6 delete注入**

- 先测试是否存在注入点，留言栏输入引号测试发现该处不是注入点. 页面上还有一处可能的注入点便是留言的删除按钮
- 查看版本信息 `delete from message where id = ? and (select 2 from (select count(*),concat(version(),floor(rand(0)*2)) x from information_schema.tables group by x) a);`
  - 经过测试，该注入方法在mysql 8.0+已失效

**7 http header注入**

- 业务逻辑：登录表单 => 后台页面展示登录用户的`ip, user agent, http accept, port`
- 可用单引号测试注入点`'`，发现存在可疑注入点
- 查看数据库名 `' or updatexml(1, concat(0x7e, database()), 0) or '`

**8 盲注base on boolean**

- `select * from member where username = '?';`

**9 盲注base on time**

-   

**10 宽字节注入**

- `select * from member where username = '?';`

# RCE



# File Inclusion



# Unsafe Filedownload



# Unsafe Fileupload



# Over Permission



# 敏感信息泄漏



# PHP反序列化



# XXE



# URL重定向



# SSRF



# 管理工具



