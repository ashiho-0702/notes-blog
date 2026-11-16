Redis实战篇

# **实战**
## **导入项目**
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">修改配置文件application.yaml</font>

比如数据库密码

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">修改Java版本</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785232324762-b8edf183-156e-46a0-8992-f6e1d4f4fa8d.png" width="272" title="" crop="0,0,1,1" id="u4612864e" class="ne-image" style="font-size: 14px">

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785232324839-67c94137-b0dd-4bdc-91bc-a82484a39f8c.png" width="292" title="" crop="0,0,1,1" id="u5403b0ca" class="ne-image" style="font-size: 14px">

报错：java: 源值 8 已过时，将在未来发行版中删除

原因：项目编译指定的 Java 源码版本为 8，但你当前使用的 JDK 版本较高（JDK11+、JDK17、JDK21）

解决：我把1.8改为17

报错：java: java.lang.ExceptionInInitializerErrorcom.sun.tools.javac.code.TypeTag :: UNKNOWN

解决：修改lombok版本

```xml
<dependency>
  <groupId>org.projectlombok</groupId>
  <artifactId>lombok</artifactId>
  <version>1.18.30</version>
  <scope>provided</scope>
</dependency>
```

修改springboot版本

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.7.18</version>
    <relativePath/> <!-- lookup parent from repository -->
</parent>
```

修改MySQL驱动版本

```xml
<dependency>
    <groupId>com.mysql</groupId>
    <artifactId>mysql-connector-j</artifactId>
    <scope>runtime</scope>
    <version>8.0.33</version>
</dependency>
```

报错：Loading class `com.mysql.jdbc.Driver'. This is deprecated. The new driver class is `com.mysql.cj.jdbc.Driver'. The driver is automatically registered via the SPI and manual loading of the driver class is generally unnecessary.

解决：把application.yaml的 driver-class-name: com.mysql.jdbc.Driver 删掉

### **启动前端**
注意nginx要放在没有中文目录的地方

在浏览器访问 localhost:8080，打开F12，选择手机模式

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785232326074-65eb536f-ed8f-4318-92fe-6dd864e6ecf0.png" width="637" title="" crop="0,0,1,1" id="u748b14f3" class="ne-image" style="font-size: 14px">



## **短信登录**
### **基于Session实现登录**
发送验证码：

用户在提交手机号后，会校验手机号是否合法，如果不合法，则要求用户重新输入手机号。

如果手机号合法，后台此时生成对应的验证码，同时将验证码进行保存，然后再通过短信的方式将验证码发送给用户。



短信验证码登录、注册：

用户将验证码和手机号进行输入，后台从session中拿到当前验证码，然后和用户输入的验证码进行校验，如果不一致，则无法通过校验，如果一致，则后台根据手机号查询用户，如果用户不存在，则为用户创建账号信息，保存到数据库，无论是否存在，都会将用户信息保存到session中，方便后续获得当前登录信息。



校验登录状态：

用户在请求时候，会从 cookie 中携带者 JsessionId 到后台，后台通过 JsessionId 从 session 中拿到用户信息，如果没有 session信息，则进行拦截，如果有 session 信息，则将用户信息保存到 threadLocal 中，并且放行。

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785232352269-fffee400-b06b-4457-882f-da4d31af6539.png" width="703" title="" crop="0,0,1,1" id="u3c43c959" class="ne-image" style="font-size: 14px">

### **发送短信验证码**
**<font style="color:#333333;">页面流程</font>**

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785232352585-0cc5d205-fbed-47b5-9d72-90029691cefa.png" width="698" title="" crop="0,0,1,1" id="u36cb90dd" class="ne-image" style="font-size: 14px">

**<font style="color:#333333;">具体代码如下</font>**

**<font style="color:#333333;">贴心小提示：</font>**

<font style="color:#333333;">具体逻辑上文已经分析，我们仅仅只需要按照提示的逻辑写出代码即可。</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">发送验证码</font>

在 UserInfoServiceImpl 类里：

```java
@Override
public Result sendCode(String phone, HttpSession session) {
    // 1.校验手机号
    if (RegexUtils.isPhoneInvalid(phone)) {
        // 2.如果不符合，返回错误信息
        return Result.fail("手机号格式错误！");
    }
    // 3.符合，生成验证码
    //RandomUtil为HuTool工具类的方法
    String code = RandomUtil.randomNumbers(6);

    // 4.保存验证码到 session
    session.setAttribute("code",code);
    // 5.发送验证码（假装）
    log.debug("发送短信验证码成功，验证码：{}", code);
    // 返回ok
    return Result.ok();
}
```

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">登录</font>

```java
@Override
public Result login(LoginFormDTO loginForm, HttpSession session) {
    // 1.校验手机号
    String phone = loginForm.getPhone();
    if (RegexUtils.isPhoneInvalid(phone)) {
        // 2.如果不符合，返回错误信息
        return Result.fail("手机号格式错误！");
    }
    // 3.校验验证码
    Object cacheCode = session.getAttribute("code");
    String code = loginForm.getCode();
    if(cacheCode == null || !cacheCode.toString().equals(code)){
         //3.不一致，报错
        return Result.fail("验证码错误");
    }
    //一致，根据手机号查询用户
    User user = query().eq("phone", phone).one();

    //5.判断用户是否存在
    if(user == null){
        //不存在，则创建
        user =  createUserWithPhone(phone);
    }
    //7.保存用户信息到session中
    session.setAttribute("user",user);

    return Result.ok();
}
```

```java
private User createUserWithPhone(String phone) {
    User user = new User()
            .setPhone(phone)
            .setNickName(SystemConstants.USER_NICK_NAME_PREFIX + RandomUtil.randomString(10));
    save(user);
    return user;
}
```

会话只保存了验证码，怎么知道验证码是该手机号的？

用户携带的 cookie 有 sessionId，就可以验证 点击发送验证码的会话，和点击登录按钮的会话，是同一个会话。

### **实现登录拦截功能**
所有业务（除登陆注册）都需要校验登录态，如果每个方法都写一遍登录校验逻辑就会很麻烦，所以要有统一登录拦截器。

但后续业务需要用户信息，拦截器能拿到，后面的 Conroller 层拿不到。

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785232387163-d20cac3b-1b5a-4755-a0ad-fe997cea4b95.png" width="671" title="" crop="0,0,1,1" id="u694586df" class="ne-image" style="font-size: 14px">



+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">值的存入方式</font>
1. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">存入 request 域</font>

```java
// 存入request域，Controller中直接取用
request.setAttribute("currentUserId",userId);
request.setAttribute("currentUsername",username);
```

2. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">存入 ThreadLocal</font>

创建用户上下文工具类 UserContext

```java
public class UserContext {
    private static final ThreadLocal<Long> USER_ID = new ThreadLocal<>();
    private static final ThreadLocal<Integer> ROLE = new ThreadLocal<>();

    public static void setUserId(Long userId) {
        USER_ID.set(userId);
    }
    public static Long getUserId() {
        return USER_ID.get();
    }

    public static void setRole(Integer role) {
        ROLE.set(role);
    }
    public static Integer getRole() {
        return ROLE.get();
    }

    public static void remove() {
        USER_ID.remove();
        ROLE.remove();
    }
}
```

**<font style="color:#333333;">温馨小贴士：tomcat的运行原理</font>**

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785232407914-1e4c63f9-518c-4912-af31-02baba8a49fa.png" width="591" title="" crop="0,0,1,1" id="u0b9bcc02" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">当用户发起请求时，会访问我们 tomcat 注册的端口，任何程序想要运行，都需要有一个线程对当前端口号进行监听，tomcat 也不例外，当监听线程知道用户想要和 tomcat 连接连接时，那会由监听线程创建 socket 连接，socket 都是成对出现的，用户通过 socket 互相传递数据，当 tomcat 端的 socket 接受到数据后，此时监听线程会从 tomcat 的线程池中取出一个线程执行用户请求，在我们的服务部署到 tomcat 后，线程会找到用户想要访问的工程，然后用这个线程转发到工程中的 controller，service，dao中，并且访问对应的DB，在用户执行完请求后，再统一返回，再找到 tomcat 端的 socket，再将数据写回到用户端的 socket，完成请求和响应。</font>

<font style="color:#333333;">通过以上讲解，我们可以得知 每个用户其实对应都是去找 tomcat 线程池中的一个线程来完成工作的， 使用完成后再进行回收，既然每个请求都是独立的，所以在每个用户去访问我们的工程时，我们可以使用 threadlocal 来做到线程隔离，每个线程操作自己的一份数据。</font>

**<font style="color:#333333;">温馨小贴士：关于threadlocal</font>**

<font style="color:#333333;">如果小伙伴们看过threadLocal的源码，你会发现在threadLocal中，无论是他的put方法和他的get方法， 都是先从获得当前用户的线程，然后从线程中取出线程的成员变量map，只要线程不一样，map就不一样，所以可以通过这种方式来做到线程隔离</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785232427022-ccdf45b5-1918-4ceb-84f1-4141219cab6a.png" width="743" title="" crop="0,0,1,1" id="ubb91a483" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">拦截器代码</font>

<font style="color:#333333;">实现 HandlerInterceptor 接口，按 CTRL + i，选择要实现的方法。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785232426903-69d245fc-460f-4d2f-98e9-ffeb702ec148.png" width="383" title="" crop="0,0,1,1" id="u86e8b2f1" class="ne-image" style="font-size: 14px">



```java
public class LoginInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        // 获取session
        HttpSession session = request.getSession();
        // 获取用户
        Object userObj = session.getAttribute("user");
        // 用户是否存在
        if (!(userObj instanceof UserDTO userDTO)){
            // 不存在，拦截，返回401状态码
            response.setStatus(401);
            return false;
        }
        // 存在，存入ThreadLocal
        UserHolder.saveUser(userDTO);
        // 放行
        return true;
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        UserHolder.removeUser();
    }
}
```

```java
//用户上下文
public class UserHolder {
    private static final ThreadLocal<UserDTO> tl = new ThreadLocal<>();

    public static void saveUser(UserDTO user){
        tl.set(user);
    }

    public static UserDTO getUser(){
        return tl.get();
    }

    public static void removeUser(){
        tl.remove();
    }
}
```

<font style="color:#333333;">让拦截器生效</font>

```java
@Configuration
public class MvcConfig implements WebMvcConfigurer {

    @Resource
    private StringRedisTemplate stringRedisTemplate;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        // 登录拦截器
        registry.addInterceptor(new LoginInterceptor())
                .excludePathPatterns( //不需要拦截的路径
                        "/shop/**",
                        "/voucher/**",
                        "/shop-type/**",
                        "/upload/**",
                        "/blog/hot",
                        "/user/code",
                        "/user/login"
                ).order(1);
        // token刷新的拦截器
        registry.addInterceptor(new RefreshTokenInterceptor(stringRedisTemplate)).addPathPatterns("/**").order(0);
    }
}
```

### **集群的session共享问题**
核心思路分析：

每个 tomcat 中都有一份属于自己的 session，假设用户第一次访问第一台 tomcat，并且把自己的信息存放到第一台服务器的session 中，但是第二次这个用户访问到了第二台 tomcat，那么在第二台服务器上，肯定没有第一台服务器存放的 session，所以此时整个登录拦截功能就会出现问题，我们能如何解决这个问题呢？

早期的方案是** session 拷贝**，就是说虽然每个 tomcat 上都有不同的 session，但是每当任意一台服务器的 session 修改时，都会**同步**给其他的 Tomcat 服务器的 session，这样的话，就可以实现 session 的共享了。

但是这种方案具有两个大问题：

1. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">每台服务器中都有完整的一份 session 数据，服务器压力过大。</font>
2. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">session 拷贝数据时，可能会出现延迟。</font>

所以咱们后来采用的方案都是基于redis来完成，我们把session换成redis，redis数据本身就是共享的，就可以避免session共享的问题了。

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785232352777-689b5458-05c1-4e6c-9131-d354a1bd8089.png" width="1624" title="" crop="0,0,1,1" id="u6aca7cb1" class="ne-image" style="font-size: 14px">

### **基于Redis实现共享session登录**
#### **设计key的结构**
首先我们要思考一下利用redis来存储数据，那么到底使用哪种结构呢？

由于存入的数据比较简单，我们可以考虑使用String，或者是哈希。

如果使用String，value 会用多占用一点空间。

如果使用哈希，value 中只会存储它数据本身。

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785232448671-3149db5f-b418-4e5e-a468-5cf5532f463f.png" width="642" title="" crop="0,0,1,1" id="u4f458ffd" class="ne-image" style="font-size: 14px">

#### **设计key的具体细节**
关于 key 的处理：

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">Session 是用户隔离的：每个浏览器用户对应唯一JSESSIONID，服务器内部自动以 JSESSIONID 作为隔离标识；我们直接写 key="code" 没问题：不同用户，各自 Session 空间相互隔离，同一个 key 名称互不干扰。用户 A 拿自己 session 的 code、用户 B 拿自己 session 的 code，不会冲突。</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">但是 redis 的 key 是共享的，用户 A 获取验证码，Redis 设置 code=123456；</font>

用户 B 紧接着获取验证码，Redis 覆盖 code=666666。数据相互覆盖冲突，所以不能用 code。

在设计这个 key 的时候，我们之前讲过需要满足两点

1. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">key要具有唯一性</font>
2. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">key要方便携带</font>

如果我们采用phone：这样的敏感数据存储到 redis 中，返回前端保存到浏览器，会有泄露风险。

所以我们在后台生成一个随机串 token，然后让前端带来这个 token 就能完成我们的整体逻辑了。

#### **整体访问流程**
当注册完成后，用户去登录会去校验用户提交的手机号和验证码 是否一致。如果一致，则根据手机号查询用户信息，不存在则新建，最后将用户数据保存到redis，并且生成 token 作为 redis 的 key，当我们校验用户是否登录时，会去携带着token进行访问，从redis中取出token对应的value，判断是否存在这个数据，如果没有则拦截，如果存在则将其保存到 threadLocal 中，并且放行。

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785232449054-3b5d4dc8-2ca5-4a38-83fd-b8bbda90010a.png" width="755" title="" crop="0,0,1,1" id="u0fc32ed1" class="ne-image" style="font-size: 14px">

### **基于Redis实现短信登录**
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">UserInfoServiceImpl 代码</font>

```java
@Autowired
private StringRedisTemplate stringRedisTemplate;

@Override
public Result sendCode(String phone, HttpSession session) {
    // 校验手机号
    if (RegexUtils.isPhoneInvalid(phone)) {
        // 不符合格式，返回错误信息
        return Result.fail("手机号格式错误！");
    }
    // 生成验证码
    String code = RandomUtil.randomNumbers(6);
    // 保存验证码
    stringRedisTemplate.opsForValue().set(RedisConstants.LOGIN_CODE_KEY + phone, code);
    // 设置验证码有效期，LOGIN_CODE_TTL = 2L，有效期为2分钟
    stringRedisTemplate.expire(RedisConstants.LOGIN_CODE_KEY + phone,RedisConstants.LOGIN_CODE_TTL, TimeUnit.MINUTES);
    // 发送验证码
    log.debug("发送短信验证码成功，验证码：{}", code);
    return Result.ok();
}
```

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">UserServiceImpl 代码</font>

```java
@Autowired
private StringRedisTemplate stringRedisTemplate;

@Override
public Result login(LoginFormDTO loginForm, HttpSession session) {
    // 1.校验手机号
    String phone = loginForm.getPhone();
    if (RegexUtils.isPhoneInvalid(phone)) {
        // 2.如果不符合，返回错误信息
        return Result.fail("手机号格式错误！");
    }
    // 3.从redis获取验证码并校验
    String cacheCode = stringRedisTemplate.opsForValue().get(LOGIN_CODE_KEY + phone);
    // 存手机号为key的作用是登录之前核对短信验证码
    // 登录后存token为key，传给前端也是token
    String code = loginForm.getCode();
    if (cacheCode == null || !cacheCode.equals(code)) {
        // a.equals(b) 执行逻辑：如果 a == null，直接抛出空指针
        // 因为条件前面判断了cacheCode是否为空，不为空才会走!cacheCode.equals(code)，所以不会报错
        // 如果写成!code.equals (cacheCode)，就可能有NPE
        // 不一致，报错
        return Result.fail("验证码错误");
    }

    // 4.一致，根据手机号查询用户 select * from tb_user where phone = ?
    User user = query().eq("phone", phone).one();

    // 5.判断用户是否存在
    if (user == null) {
        // 6.不存在，创建新用户并保存
        user = createUserWithPhone(phone);
    }

    // 7.保存用户信息到 redis中
    // 7.1.随机生成token，作为登录令牌
    // 00-8cf0-11，true的意思是：把字符串里所有 - 横杠全部删除
    String token = UUID.randomUUID().toString(true);
    // 脱敏
    UserDTO userDTO = BeanUtil.copyProperties(user, UserDTO.class);
    // 7.2.将User对象转为HashMap存储
    Map<String, Object> userMap = BeanUtil.beanToMap(userDTO, new HashMap<>(), //对象转 Map
            CopyOptions.create()
                    .setIgnoreNullValue(true) //属性为 null 时，不要存入 map
                    .setFieldValueEditor((fieldName, fieldValue) -> fieldValue.toString()));//所有value统一转为 String
    // 7.3.存储
    String tokenKey = LOGIN_USER_KEY + token;
    stringRedisTemplate.opsForHash().putAll(tokenKey, userMap);
    // 7.4.设置token有效期
    // LOGIN_USER_TTL = 30L，有效期为30分钟
    stringRedisTemplate.expire(tokenKey, LOGIN_USER_TTL, TimeUnit.MINUTES);

    // 8.返回token
    return Result.ok(token);
}
```

此时我希望用户是三十分钟没有请求，token 就过期。如果有请求，token 有效期就重新刷新为三十分钟；

而现在的情况是无论用户有无请求，三十分钟后必过期。所以要修改拦截器代码。

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">MvcConfig 类</font>

```java
@Configuration
public class MvcConfig implements WebMvcConfigurer {
    @Autowired
    private StringRedisTemplate stringRedisTemplate;
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        // 把已经注入完成的 stringRedisTemplate 对象，通过构造方法传递给拦截器
        registry.addInterceptor(new LoginInterceptor(stringRedisTemplate))
                .excludePathPatterns( // 不需要拦截的路径
                        "/user/code",
                        "/user/login",
                        "/blog/hot",
                        "/shop/**",
                        "/shop-type/**",
                        "/upload/**",
                        "/voucher/**"
                );
    }
}
```

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">LoginInterceptor 类</font>

```java
// 这里不能用@Autowired注解来注入对象，只能用构造器
// 因为LoginInterceptor对象是我们手动new的，不是 Spring IOC 容器帮我们创建的
private StringRedisTemplate stringRedisTemplate;

public LoginInterceptor(StringRedisTemplate stringRedisTemplate) {
    this.stringRedisTemplate = stringRedisTemplate;
}

@Override
public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
    // 获取请求头中的token
    String token = request.getHeader("authorization");
    if (StrUtil.isBlank(token)) { // 与token.isBlank()的区别是，如果字符串为null，原生方法会报NPE，而该方法不会
        response.setStatus(401);
        return false;
    }
    // 获取用户
    String tokenKey = RedisConstants.LOGIN_USER_KEY + token;
    Map<Object, Object> userMap = stringRedisTemplate.opsForHash()
            .entries(tokenKey);
    // 用户是否存在
    if (userMap.isEmpty()){ //不需要判null，因为如果为null，entries()方法会返回空集合
        // 不存在，拦截，返回401状态码
        response.setStatus(401);
        return false;
    }
    // 存在，转为UserDTO对象
    UserDTO userDTO = BeanUtil.fillBeanWithMap(userMap, new UserDTO(), false);// 是否忽略转换的错误
    // 存入ThreadLocal
    UserHolder.saveUser(userDTO);
    // 刷新token有效期
    stringRedisTemplate.expire(tokenKey,RedisConstants.LOGIN_USER_TTL, TimeUnit.MINUTES);
    // 放行
    return true;
}
```



### **状态登录刷新问题**
在这个方案中确实可以使用对应路径的拦截，同时刷新登录 token 令牌的存活时间。

但是现在这个拦截器只是拦截需要被拦截的路径，假设当前用户访问了一些不需要拦截的路径，那么这个拦截器就不会生效，所以此时令牌刷新的动作实际上就不会执行，所以这个方案是存在问题的。

如图，拦截登录路径，如用户不存在则拦截，存在则保存。

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785232481027-86a2a0d1-c4ad-4c00-bc97-94845beee5d6.png" width="651" title="" crop="0,0,1,1" id="ue2e0cab1" class="ne-image" style="font-size: 14px">

修改为下图，第一个拦截器拦截所有路径，但用户不存在也放行，用户存在则保存；

第二个拦截器拦截需要登录的路径，用户不存在则拦截，查看ThreadLocal中是否有用户，用户存在则放行。

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785232481202-44ff0ce4-8ae5-43b1-855f-4d9284264cb1.png" width="736" title="" crop="0,0,1,1" id="u809c42cd" class="ne-image" style="font-size: 14px">

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">RefreshTokenInterceptor 类</font>

```java
public class RefreshTokenInterceptor implements HandlerInterceptor {

    private StringRedisTemplate stringRedisTemplate;

    public RefreshTokenInterceptor(StringRedisTemplate stringRedisTemplate) {
        this.stringRedisTemplate = stringRedisTemplate;
    }

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        // 1.获取请求头中的token
        String token = request.getHeader("authorization");
        if (StrUtil.isBlank(token)) {
            return true;
        }
        // 2.基于TOKEN获取redis中的用户
        String key  = LOGIN_USER_KEY + token;
        Map<Object, Object> userMap = stringRedisTemplate.opsForHash().entries(key);
        // 3.判断用户是否存在
        if (userMap.isEmpty()) {
            return true;
        }
        // 5.将查询到的hash数据转为UserDTO
        UserDTO userDTO = BeanUtil.fillBeanWithMap(userMap, new UserDTO(), false);
        // 6.存在，保存用户信息到 ThreadLocal
        UserHolder.saveUser(userDTO);
        // 7.刷新token有效期
        stringRedisTemplate.expire(key, LOGIN_USER_TTL, TimeUnit.MINUTES);
        // 8.放行
        return true;
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        // 移除用户
        UserHolder.removeUser();
    }
}
    
```

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">LoginInterceptor 类</font>

```java
public class LoginInterceptor implements HandlerInterceptor {

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        // 1.判断是否需要拦截（ThreadLocal中是否有用户）
        if (UserHolder.getUser() == null) {
            // 没有，需要拦截，设置状态码
            response.setStatus(401);
            // 拦截
            return false;
        }
        // 有用户，则放行
        return true;
    }
}
```

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">MvcConfig 类</font>

```java
@Configuration
public class MvcConfig implements WebMvcConfigurer {
    @Autowired
    private StringRedisTemplate stringRedisTemplate;
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        // 登录拦截器
        registry.addInterceptor(new LoginInterceptor())
                .excludePathPatterns( // 不需要拦截的路径
                        "/user/code",
                        "/user/login",
                        "/blog/hot",
                        "/shop/**",
                        "/shop-type/**",
                        "/upload/**",
                        "/voucher/**"
                ).order(1);
        // 虽然两个拦截器都写好了，但是希望RefreshTokenInterceptor拦截器要先执行
        // 所以添加order()，数字小的先执行
        // 刷新拦截器
        registry.addInterceptor(new RefreshTokenInterceptor(stringRedisTemplate)).addPathPatterns("/**").order(0);
    }
}
```



## **商户查询缓存**
### **缓存是什么**
缓存(Cache)，就是数据交换的缓冲区，俗称的缓存就是缓冲区内的数据，一般从数据库中获取，存储于本地代码

```java
例1:Static final ConcurrentHashMap<K,V> map = new ConcurrentHashMap<>(); 本地用于高并发

例2:static final Cache<K,V> USER_CACHE = CacheBuilder.newBuilder().build(); 用于redis等缓存

例3:Static final Map<K,V> map =  new HashMap(); 本地缓存
```

由于其被 Static 修饰，所以随着类的加载而被加载到内存之中，作为本地缓存，由于其又被final修饰，所以其引用(例3:map)和对象(例3:new HashMap())之间的关系是固定的，不能改变，因此不用担心赋值导致缓存失效;

#### **为什么要使用缓存**
一句话：因为速度快，好用

缓存数据存储于代码中，而代码运行在内存中，内存的读写性能远高于磁盘，缓存可以大大降低用户访问并发量带来的服务器读写压力。

实际开发过程中，企业的数据量，少则几十万，多则几千万，这么大数据量，如果没有缓存来作为"避震器"，系统是几乎撑不住的，所以企业会大量运用到缓存技术。

但是缓存也会增加代码复杂度和运营的成本。

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785232498001-a37d7d63-2a9e-424b-b439-8799969347b0.png" width="682" title="" crop="0,0,1,1" id="ucef0871a" class="ne-image" style="font-size: 14px">

#### **如何使用缓存**
实际开发中，会构筑多级缓存来使系统运行速度进一步提升，例如：本地缓存与redis中的缓存并发使用

浏览器缓存：静态资源，缓存未命中，就用下面的缓存

应用层缓存：可以分为 tomcat 本地缓存，从数据库查询数据存到map里，再请求直接从map读取，或者是使用redis作为缓存。

数据库缓存：在数据库中有一片空间是 buffer pool，增改查数据都会先加载到mysql的缓存中

CPU缓存：当代计算机最大的问题是 cpu性能提升了，但内存读写速度没有跟上，所以为了适应当下的情况，增加了cpu的L1，L2，L3级的缓存

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785232497892-8ebe8bb6-fc05-4a57-92ca-8c4b203711c8.png" width="655" title="" crop="0,0,1,1" id="uc79ed4c8" class="ne-image" style="font-size: 14px">

### **添加商户缓存**
在我们查询商户信息时，我们是直接操作从数据库中去进行查询的，大致逻辑是这样，直接查询数据库那肯定慢咯，所以我们需要增加缓存。

```java
@GetMapping("/{id}")
public Result queryShopById(@PathVariable("id") Long id) {
    //这里是直接查询数据库
    return shopService.queryById(id);
}
```

#### **缓存模型和思路**
标准的操作方式就是查询数据库之前先查询缓存，如果缓存数据存在，则直接从缓存中返回，如果缓存数据不存在，再查询数据库，然后将数据存入redis。

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785232498094-dc654f8b-aaec-43a7-b483-38678cd56a4f.png" width="709" title="" crop="0,0,1,1" id="ucd057e78" class="ne-image" style="font-size: 14px">

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">ShopController 类</font>

```java
/**
 * 根据id查询商铺信息
 * @param id 商铺id
 * @return 商铺详情数据
 */
@GetMapping("/{id}")
public Result queryShopById(@PathVariable("id") Long id) {
    return shopService.queryById(id);
}
```

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">ShopServiceImpl 类</font>

```java
@Autowired
private StringRedisTemplate stringRedisTemplate;

@Override
public Result queryById(Long id) {
    String key = RedisConstants.CACHE_SHOP_KEY + id;
    // 从redis查询商户缓存
    String shopJson = stringRedisTemplate.opsForValue().get(key);
    // 判断是否存在
    if (!StrUtil.isBlank(shopJson)) {
        // 转成对象
        Shop shop = JSONUtil.toBean(shopJson, Shop.class);
        // 存在则直接返回
        return Result.ok(shop);
    }
    // 从数据库查询商户
    Shop shop = baseMapper.selectById(id);
    // 判断是否存在
    if (shop == null){
        // 不存在，返回错误
        return Result.fail("商户不存在！");
    }
    // 存在，存入缓存中
    stringRedisTemplate.opsForValue().set(key,JSONUtil.toJsonStr(shop)
                            ,RedisConstants.CACHE_SHOP_TTL, TimeUnit.MINUTES);
    // 返回
    return Result.ok(shop);
}
```

### **添加商户类型缓存**
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">ShopTypeController 类</font>

```java
@RestController
@RequestMapping("/shop-type")
public class ShopTypeController {
    @Resource
    private IShopTypeService typeService;

    @GetMapping("list")
    public Result queryTypeList() {
        return typeService.queryTypeList();
    }
}
```

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">ShopTypeServiceImpl 类</font>

```java
@Service
public class ShopTypeServiceImpl extends ServiceImpl<ShopTypeMapper, ShopType> implements IShopTypeService {
    @Autowired
    private StringRedisTemplate stringRedisTemplate;

    @Override
    public Result queryTypeList() {
        String key = RedisConstants.CACHE_SHOP_TYPE_KEY;
        // 查询商户类型缓存
        // Json的集合，一个Json为一个类型
        List<String> shopTypeJson = stringRedisTemplate.opsForList().range(key, 0, -1);
        // 判断缓存是否存在
        if (CollUtil.isNotEmpty(shopTypeJson)){
            // 缓存命中，直接返回
            // 转成对象
            List<ShopType> shopTypes = JSONUtil.toList(shopTypeJson.toString(), ShopType.class);
            // 按顺序排序
            Collections.sort(shopTypes,(o1, o2) -> o1.getSort()- o2.getSort());
            return Result.ok(shopTypes);
        }
        // 不存在，查询数据库
        List<ShopType> shopTypes = query().orderByAsc("sort").list();
        // 判断是否存在
        if (CollUtil.isEmpty(shopTypes)) {
            // 不存在，返回错误
            return Result.fail("商户类型不存在！");
        }
        // 对象转成Json
        List<String> shopTypesJson = shopTypes.stream()
                .map(shopType -> JSONUtil.toJsonStr(shopType))
                .collect(Collectors.toList());
        // 添加缓存
        stringRedisTemplate.opsForList().rightPushAll(key,shopTypesJson);
        stringRedisTemplate.expire(key,RedisConstants.CACHE_SHOP_TYPE_TTL, TimeUnit.MINUTES);
        // 返回
        return Result.ok(shopTypes);
    }
}
```

### **缓存更新策略**
<font style="color:#333333;">缓存更新是 redis 为了节约内存而设计出来的一个东西，主要是因为内存数据宝贵，当我们向redis插入太多数据，此时就可能会导致缓存中的数据过多，所以redis会对部分数据进行更新，或者把他叫为淘汰更合适。</font>

**<font style="color:#333333;">内存淘汰：</font>**<font style="color:#333333;">redis自动进行，当 redis 内存达到咱们设定的 max-memery 的时候，会自动触发淘汰机制，淘汰掉一些不重要的数据(可以自己设置策略方式)</font>

**<font style="color:#333333;">超时剔除：</font>**<font style="color:#333333;">当我们给redis设置了过期时间 ttl 之后，redis 会将超时的数据进行删除，方便咱们继续使用缓存</font>

**<font style="color:#333333;">主动更新：</font>**<font style="color:#333333;">我们可以手动调用方法把缓存删掉，通常用于解决</font>**<font style="color:#333333;">缓存和数据库不一致</font>**<font style="color:#333333;">问题</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785232516988-bdfabcd6-5d3a-4be4-9cbe-d8d904b133fc.png" width="739" title="" crop="0,0,1,1" id="u87c2081f" class="ne-image" style="font-size: 14px">

#### **数据库缓存不一致解决方案**
<font style="color:#333333;">由于我们的</font>**<font style="color:#333333;">缓存的数据源来自于数据库</font>**<font style="color:#333333;">，而数据库的</font>**<font style="color:#333333;">数据是会发生变化的</font>**<font style="color:#333333;">，因此，如果当数据库中</font>**<font style="color:#333333;">数据发生变化</font>**<font style="color:#333333;">，</font>**<font style="color:#333333;">而缓存却没有同步</font>**<font style="color:#333333;">，此时就会有</font>**<font style="color:#333333;">一致性问题存在</font>**<font style="color:#333333;">，其后果是：</font>

<font style="color:#333333;">用户使用缓存中的过时数据，就会产生类似多线程数据安全问题，从而影响业务，产品口碑等；</font>

**<font style="color:#333333;">主动更新</font>**<font style="color:#333333;">有如下几种方案：</font>

<font style="color:#333333;">Cache Aside Pattern 人工编码方式：缓存调用者在更新完数据库后再去更新缓存，也称之为双写方案。</font>

<font style="color:#333333;">Read/Write Through Pattern：由系统本身完成，数据库与缓存的问题交由系统本身去处理。但维护此服务很复杂，开发成本高。</font>

<font style="color:#333333;">Write Behind Caching Pattern：调用者只操作缓存，其他线程去异步处理数据库，实现最终一致。但如果缓存出现了宕机，缓存由内存存储，宕机时内存数据会丢失。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785232516962-dd6f70dd-dbd1-44da-b412-5e9e6face486.png" width="1547" title="" crop="0,0,1,1" id="u7ec54dee" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">所以综合考虑使用方案一，但是方案一调用者如何处理呢？这里有几个问题</font>

<font style="color:#333333;">操作缓存和数据库时有三个问题需要考虑：</font>

<font style="color:#333333;">如果采用第一个方案，那么假设我们每次操作数据库后，都操作缓存，但是中间如果没有人查询，那么这个更新动作实际上只有最后一次生效，中间的更新动作意义并不大，我们可以把缓存删除，等待再次查询时，将缓存中的数据加载出来。</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">删除缓存还是更新缓存？</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">更新缓存：每次更新数据库都更新缓存，无效写操作较多</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">删除缓存：更新数据库时让缓存失效，查询时再更新缓存</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">如何保证缓存与数据库的操作的同时成功或失败？</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">单体系统，将缓存与数据库操作放在一个事务</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">分布式系统，利用TCC等分布式事务方案</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">先操作缓存还是先操作数据库？</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">先删除缓存，再操作数据库</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">先操作数据库，再删除缓存</font>

<font style="color:#333333;">先删除缓存的正常情况：</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785232516952-33899ff1-a9de-4592-ae40-dc737529f55f.png" width="266" title="" crop="0,0,1,1" id="u8fb4cfee" class="ne-image" style="font-size: 14px">

先操作数据库正常情况：

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785232517395-300f3c11-cd84-4571-bfcb-83f90d894629.png" width="277" title="" crop="0,0,1,1" id="uf88ac4a1" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">应该具体操作缓存还是操作数据库，我们应当是先操作数据库，再删除缓存。</font>

<font style="color:#333333;">原因在于：如果先删缓存，两个线程为两个用户，左图线程一执行修改操作，线程二执行查询操作；右图则反过来。</font>

+ <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">左图：两个用户并发来访问时，假设线程1 先来，他先把缓存删了，此时线程2 过来，他查询缓存数据并不存在，此时他写入缓存，当他写入缓存后，线程1再执行更新动作时，实际上写入缓存的是旧数据，新的数据被旧数据覆盖了。</font>
    - <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">线程一的更新数据库较慢，所以线程二有可能乘虚而入。发生概率较高。</font>
+ <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">右图：假设查询缓存时，缓存数据失效了。线程一查询数据库时，数据未更新，所以查到的是旧数据10，写入缓存也是旧数据。</font>
    - <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">恰好缓存失效，且线程一的写入缓存较快，在这点时间内线程二既更新数据库又要删除缓存。发生概率较低。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785232517377-f5137c99-3e51-46eb-8173-99a456066b32.png" width="727" title="" crop="0,0,1,1" id="u950172a8" class="ne-image" style="font-size: 14px">

##### **总结**
缓存更新策略的最佳实践方案：

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">低一致性需求：使用Redis自带的内存淘汰机制</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">高一致性需求：主动更新，并以超时剔除作为兜底方案</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">读操作</font>
        * <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">缓存命中则直接返回；</font>
        * <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">缓存未命中则查询数据库，并写入缓存，设定超时时间。</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">写操作</font>
        * <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">先写数据库，然后再删除缓存；</font>
        * <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">要确保数据库与缓存操作的原子性。</font>

<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);"></font>

### **商铺缓存与数据库双写一致**
<font style="color:#333333;">修改ShopController中的业务逻辑，满足下面的需求：</font>

<font style="color:#333333;">根据 id 查询店铺时，如果缓存未命中，则查询数据库，将数据库结果写入缓存，并</font>**<font style="color:#333333;">设置超时时间</font>**<font style="color:#333333;">（之前写了）。</font>

<font style="color:#333333;">根据 id 修改店铺时，先修改数据库，再删除缓存。</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">ShopServiceImpl 类</font>

```java
@Override
@Transactional
public Result updateShop(Shop shop) {
    // 判断商铺ID
    Long id = shop.getId();
    if (id == null){
        return Result.fail("商户ID不得为空");
        // 如果ID为空，下面的代码就执行不了
    }
    // 先修改数据库
    updateById(shop);
    // 再删缓存
    String key = RedisConstants.CACHE_SHOP_KEY + id;
    stringRedisTemplate.delete(key);
    return Result.ok();
}
```

### **缓存穿透问题**
<font style="color:#333333;">缓存穿透 ：缓存穿透是指客户端请求的数据在 缓存 和 数据库中都不存在，这样缓存永远不会生效，这些请求都会打到数据库。</font>

<font style="color:#333333;">常见的解决方案有两种：</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">缓存空对象</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">优点：实现简单，维护方便</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">缺点：</font>
        * <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">额外的内存消耗</font>
        * <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">可能造成短期的不一致。假设请求的数据不存在，缓存了null，此时又插入了真数据，那用户只能查到null而不是真数据。</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">布隆过滤</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">优点：内存占用较少，没有多余key</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">缺点：</font>
        * <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">实现复杂</font>
        * <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">存在误判可能</font>

**<font style="color:#333333;">缓存空对象思路分析：</font>**<font style="color:#333333;">当我们客户端访问不存在的数据时，先请求redis，但是此时redis中没有数据，此时会访问到数据库，但是数据库中也没有数据，这个数据穿透了缓存，直击数据库，我们都知道数据库能够承载的并发不如 redis 这么高，如果大量的请求同时过来访问这种不存在的数据，这些请求就都会访问到数据库，简单的解决方案就是哪怕这个数据在数据库中也不存在，我们也把这个数据存入到redis中去，这样，下次用户过来访问这个不存在的数据，那么在redis中也能找到这个数据就不会进入到缓存了</font>

**<font style="color:#333333;">布隆过滤：</font>**<font style="color:#333333;">布隆过滤器其实采用的是哈希思想来解决这个问题，通过一个庞大的二进制数组，数据基于哈希算法计算出哈希值，将哈希值转为二进制，再保存到数组里。所以走哈希思想去判断当前这个要查询的这个数据是否存在，如果布隆过滤器判断存在，则放行，这个请求会去访问redis，哪怕此时redis中的数据过期了，但是数据库中一定存在这个数据，在数据库中查询出来这个数据后，再将其放入到redis中。</font>

<font style="color:#333333;">假设布隆过滤器判断这个数据不存在，那数据是真的不存在；如果判断存在，不一定存在，因为只要哈希思想，就可能存在哈希冲突。数据不存在还是会有缓存穿透问题。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785232537732-43bbddad-a875-4d66-8e7d-c2db723cab1a.png" width="591" title="" crop="0,0,1,1" id="u7f1245c0" class="ne-image" style="font-size: 14px">

#### **商品查询的缓存穿透问题**
在原来的逻辑中，我们如果发现这个数据在mysql中不存在，直接就返回404了，这样是会存在缓存穿透问题的。

现在的逻辑中：如果这个数据不存在，我们不会返回404 ，还是会把这个数据写入到Redis中，并且将value设置为空字符串，设置短期TTL。

当再次发起查询时，我们如果发现命中之后，判断这个value是否是null，如果是null，则是之前写入的数据，证明是缓存穿透数据，如果不是，则直接返回数据。

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785232537843-468f59c5-baf9-4886-87db-fc161c67867b.png" width="747" title="" crop="0,0,1,1" id="u747a26d6" class="ne-image" style="font-size: 14px">

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">ShopServiceImpl 类</font>

```java
@Override
public Result queryById(Long id) {
    String key = RedisConstants.CACHE_SHOP_KEY + id;
    String shopJson = stringRedisTemplate.opsForValue().get(key);
    // 判断是否为空值
    if (StrUtil.isNotBlank(shopJson)) {
        // 不是空值直接返回店铺信息
        Shop shop = JSONUtil.toBean(shopJson, Shop.class);
        return Result.ok(shop);
    }
    // 判断空值
    if (shopJson != null){ // 不等于null就是空字符串
        return Result.fail("店铺不存在！");
    }
    // 从数据库查询商铺
    Shop shop = baseMapper.selectById(id);
    // 判断是否存在
    if (shop == null) {
        // 不存在，将空值存入redis，设置短期TTL
        stringRedisTemplate.opsForValue().set(key,"",RedisConstants.CACHE_NULL_TTL,TimeUnit.MINUTES);
        // 返回错误
        return Result.fail("商户不存在！");
    }
    // 存在，存入缓存中
    stringRedisTemplate.opsForValue().set(key, JSONUtil.toJsonStr(shop), RedisConstants.CACHE_SHOP_TTL, TimeUnit.MINUTES);
    // 返回
    return Result.ok(shop);
}
```

**<font style="color:#333333;">小总结：</font>**

<font style="color:#333333;">缓存穿透产生的原因是什么？</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">用户请求的数据在缓存中和数据库中都不存在，不断发起这样的请求，给数据库带来巨大压力</font>

<font style="color:#333333;">缓存穿透的解决方案有哪些？</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">缓存null值</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">布隆过滤</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">增强 id 的复杂度，避免被猜测 id 规律</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">做好数据的基础格式校验</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">加强用户权限校验</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">做好热点参数的限流</font>

<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);"></font>

### **缓存雪崩问题及解决思路**
<font style="color:#333333;">缓存雪崩是指在同一时段大量的缓存 key 同时失效（因为用户就算不断请求某数据，该数据的缓存有效期也不会更新）或者 Redis 服务宕机，导致大量请求到达数据库，带来巨大压力。</font>

<font style="color:#333333;">解决方案：</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">给不同的Key的TTL添加随机值。比如30分钟加上1~5分钟的随机值</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">利用Redis集群提高服务的可用性。一台Redis宕机，还有另外几台顶着。(集群在高级篇)</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">给缓存业务添加降级限流策略。机房挂了，所有Redis都挂了，服务降级，拒绝服务。(降级在微服务)</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">给业务添加多级缓存。nginx做缓存，jvm做缓存等等。(微服务)</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785232556160-93dd4f0d-0588-4ba9-917c-b1f0b0422da1.png" width="562" title="" crop="0,0,1,1" id="u43b87f8d" class="ne-image" style="font-size: 14px">

### **缓存击穿问题**
<font style="color:#333333;">缓存击穿问题也叫热点Key问题，就是一个被高并发访问并且缓存重建业务较复杂的key突然失效了，无数的请求访问会在瞬间给数据库带来巨大的冲击。</font>

<font style="color:#333333;">常见的解决方案有两种：</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">互斥锁</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">逻辑过期</font>

<font style="color:#333333;">逻辑分析：假设线程1在查询缓存之后，本来应该去查询数据库，然后把这个数据重新加载到缓存的，此时只要线程1走完这个逻辑，其他线程就都能从缓存中加载这些数据了，但是假设在线程1没有走完的时候，后续的线程2，线程3，线程4同时过来访问当前这个方法， 那么这些线程都不能从缓存中查询到数据，那么他们就会同一时刻来访问查询缓存，都没查到，接着同一时间去访问数据库，同时的去执行数据库代码，对数据库访问压力过大。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785232556168-bc7fa8d8-2181-41d1-a058-6716c2b47e7d.png" width="604" title="" crop="0,0,1,1" id="ua498e476" class="ne-image" style="font-size: 14px">

+ <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">方案一，使用锁来解决</font>

<font style="color:#333333;">因为锁能实现互斥性。假设线程过来，只能一个人一个人的来访问数据库，从而避免对于数据库访问压力过大，但这也会影响查询的性能，因为此时会让查询的性能从并行变成了串行，我们可以采用 tryLock 方法 + double check来解决这样的问题。</font>

<font style="color:#333333;">假设现在线程1过来访问，他查询缓存没有命中，但是此时他获得到了锁的资源，那么线程1就会一个人去执行逻辑，假设现在线程2过来，线程2在执行过程中，并没有获得到锁，那么线程2就可以进行到休眠，直到线程1把锁释放后，线程2获得到锁，然后再来执行逻辑，此时就能够从缓存中拿到数据了。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785232556228-455370d4-9fb7-47fd-8659-3230f7e55001.png" width="398" title="" crop="0,0,1,1" id="u63a497de" class="ne-image" style="font-size: 14px">

+ <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">方案二，逻辑过期方案</font>

<font style="color:#333333;">方案分析：我们之所以会出现这个缓存击穿问题，主要原因是在于我们对key设置了过期时间，假设我们不设置过期时间，其实就不会有缓存击穿的问题，但是不设置过期时间，这样数据不就一直占用我们内存了吗，我们可以采用逻辑过期方案。</font>

<font style="color:#333333;">我们把过期时间设置在 redis的 value 中，注意：这个过期时间并不会直接作用于 redis，而是我们后续通过逻辑去处理。</font>

<font style="color:#333333;">假设线程1去查询缓存，然后从value中判断出来当前的数据已经过期了，此时线程1去获得互斥锁，那么其他线程会进行阻塞，获得了锁的线程他会开启一个线程去进行以前的重构数据的逻辑，直到新开的线程完成这个逻辑后，才释放锁， 而线程1直接进行返回，假设现在线程3过来访问，由于线程线程2持有着锁，所以线程3无法获得锁，线程3也直接返回数据，只有等到新开的线程2把重建数据构建完后，其他线程才能走返回正确的数据。</font>

<font style="color:#333333;">这种方案巧妙在于，异步的构建缓存，缺点在于在构建完缓存之前，返回的都是脏数据。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785232556272-964e4ef1-b3c4-466c-bb5f-afbc39eecfce.png" width="505" title="" crop="0,0,1,1" id="u39f1004f" class="ne-image" style="font-size: 14px">

+ <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">方案对比</font>

**<font style="color:#333333;">互斥锁方案：</font>**<font style="color:#333333;">由于保证了互斥性，所以数据一致，且实现简单，因为仅仅只需要加一把锁而已，也没其他的事情需要操心，所以没有额外的内存消耗，缺点在于有锁就有死锁问题的发生，且只能串行执行性能肯定受到影响</font>

**<font style="color:#333333;">逻辑过期方案：</font>**<font style="color:#333333;"> 线程读取过程中不需要等待，性能好，有一个额外的线程持有锁去进行重构数据，但是在重构数据完成前，其他的线程只能返回之前的数据，且实现起来麻烦</font>

<font style="color:#333333;">一致性与可用性的抉择</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785232556103-b3425acb-8d9b-4e53-9b50-416f71f2fbe7.png" width="674" title="" crop="0,0,1,1" id="u167dae68" class="ne-image" style="font-size: 14px">



#### **互斥锁解决缓存击穿问题**
<font style="color:#333333;">核心思路：相较于原来从缓存中查询不到数据后直接查询数据库而言，现在的方案是 进行查询之后，如果从缓存没有查询到数据，则进行互斥锁的获取，获取互斥锁后，判断是否获得到了锁，如果没有获得到，则休眠，过一会再进行尝试，直到获取到锁为止，才能进行查询。</font>

<font style="color:#333333;">如果获取到了锁的线程，再去进行查询，查询后将数据写入redis，再释放锁，返回数据，利用互斥锁就能保证只有一个线程去执行操作数据库的逻辑，防止缓存击穿。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785232570180-3e912ca4-fe1c-4774-9b2c-a32f312b7844.png" width="597" title="" crop="0,0,1,1" id="u8d1e5c5f" class="ne-image" style="font-size: 14px">

**<font style="color:#333333;">操作锁的代码：</font>**

<font style="color:#333333;">核心思路就是利用 redis 的 setnx 方法来表示获取锁。</font>

<font style="color:#333333;">该方法含义是redis中如果没有这个 key，则插入成功，返回1，在stringRedisTemplate中返回 true。</font>

<font style="color:#333333;">如果有这个 key 则插入失败，则返回0，在stringRedisTemplate返回 false。</font>

<font style="color:#333333;">我们可以通过 true 和 false，判断线程是否获取到锁。</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">ShopServiceImpl 类</font>

```java
// 获取锁
private boolean tryLock(String key) {
    Boolean flag = stringRedisTemplate.opsForValue().setIfAbsent(key, "1", 10, TimeUnit.SECONDS);
    return BooleanUtil.isTrue(flag); // 避免NPE
}
// 释放锁
private void unlock(String key) {
    stringRedisTemplate.delete(key);
}
```

```java
@Override
public Result queryById(Long id) {
    // 互斥锁解决缓存击穿问题
    Shop shop = queryWithMutex(id);
    if (shop == null) {
        return Result.fail("店铺不存在！");
    }
    return Result.ok(shop);
}
```

```java
private Shop queryWithMutex(Long id) throws InterruptedException {
    // 自旋循环，替代递归，防止栈溢出
    while (true) {
        String key = RedisConstants.CACHE_SHOP_KEY + id;
        String shopJson = stringRedisTemplate.opsForValue().get(key);
        // 判断缓存是否命中
        if (StrUtil.isNotBlank(shopJson)) {
            // 命中，直接返回
            return JSONUtil.toBean(shopJson, Shop.class);
        }
        // 判断空值（缓存穿透）
        if (shopJson != null) {
            return null;
        }
        // 尝试获取锁
        String lockKey = RedisConstants.LOCK_SHOP_KEY + id;
        Shop shop = null;
        if (tryLock(lockKey)) {
            try {
                // 线程抢到锁之后，要二次检查缓存(Double Check)
                shopJson = stringRedisTemplate.opsForValue().get(key);
                if (StrUtil.isNotBlank(shopJson)) {
                    // 命中，直接返回
                    return JSONUtil.toBean(shopJson, Shop.class);
                }
                // 判断空值（缓存穿透）
                if (shopJson != null) {
                    return null;
                }
                // 获取锁成功，从数据库查询商户
                shop = getById(id);
                // 判断是否存在
                if (shop == null) {
                    // 不存在，将空值存入redis，设置短期TTL（缓存穿透）
                    stringRedisTemplate.opsForValue()
                            .set(key, "", RedisConstants.CACHE_NULL_TTL, TimeUnit.MINUTES);
                    // 返回错误
                    return null;
                }
                // 存在，存入缓存中
                stringRedisTemplate.opsForValue()
                        .set(key, JSONUtil.toJsonStr(shop), RedisConstants.CACHE_SHOP_TTL, TimeUnit.MINUTES);
                // 返回
                return shop;
            } finally {
                // 释放互斥锁
                unlock(lockKey);
            }
        }
        try {
            // 获取失败，休眠
            // 30~80毫秒的随机值，避免大量线程休眠固定时间后同一时间同时发起抢锁请求
            Thread.sleep(new Random().nextLong(30,80));
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        }
    }
}
```



#### **逻辑过期解决缓存击穿问题**
<font style="color:rgb(51, 51, 51);">热点key会提前加入缓存，然后添加逻辑过期时间。如果用户查询商品没有命中缓存，说明不是热点key，则直接返回空数据，不需要查询数据库。</font>

<font style="color:rgb(51, 51, 51);">而一旦命中后，将value取出，判断value中的过期时间是否满足，如果没有过期，则直接返回redis中的数据，如果过期，则直接返回之前的数据，返回数据前开一个独立线程去重构数据，重构完成后释放互斥锁。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785232587927-d719a69a-aac7-44fd-981f-7dcc809accfb.png" width="667" title="" crop="0,0,1,1" id="ue5df970c" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">如果封装数据：因为现在redis中存储的数据的value需要带上过期时间，可以修改原来的实体类，或者新建一个实体类。</font>

**<font style="color:#333333;">步骤一</font>**

<font style="color:#333333;">选择新建一个实体类，对原来代码没有侵入性。</font>

<font style="color:#333333;">新建后，如果让Shop继承该类，还是对代码有侵入性；所以可以添加一个 Object的变量，来接收Shop对象。</font>

```java
@Data
public class RedisData {
    private LocalDateTime expireTime;
    private Object data;
}
```

**<font style="color:#333333;">步骤二</font>**

<font style="color:#333333;">在</font>**<font style="color:#333333;">ShopServiceImpl</font>**<font style="color:#333333;"> 新增此方法，利用单元测试把热点key提前加到缓存中，进行缓存预热。</font>

```java
public void saveShop2Redis(Long id,Long expireSeconds){
    // 查询店铺
    Shop shop = getById(id);
    // 封装逻辑过期时间
    RedisData redisData = new RedisData();
    redisData.setData(shop);
    redisData.setExpireTime(LocalDateTime.now().plusSeconds(expireSeconds));
    // 写入缓存
    stringRedisTemplate.opsForValue()
            .set(RedisConstants.CACHE_SHOP_KEY+id,JSONUtil.toJsonStr(redisData));
}
```

<font style="color:#333333;">在测试类中</font>

```java
@SpringBootTest
class ShopServiceImplTest {
    @Autowired
    private IShopService shopService;
    @Test
    void testSaveShop2Redis(){
        shopService.saveShop2Redis(1L,10L);
    }

}
```

**<font style="color:#333333;">步骤三</font>**<font style="color:#333333;">：正式代码</font>

**<font style="color:#333333;">ShopServiceImpl 类</font>**

```java
// 建议不要用JUC提供的线程池 自己实现线程池是阿里推荐的
private static final ExecutorService CACHE_REBUILD_EXECUTOR = Executors.newFixedThreadPool(10);
public Shop queryWithLogicalExpire(Long id) {
    String key = CACHE_SHOP_KEY + id;
    // 1.从redis查询商铺缓存
    String json = stringRedisTemplate.opsForValue().get(key);
    // 2.判断是否存在
    if (StrUtil.isBlank(json)) {
        // 3.不存在，直接返回
        return null;
    }
    // 4.命中，转为对象
    RedisData redisData = JSONUtil.toBean(json, RedisData.class);
    Shop shop = JSONUtil.toBean((JSONObject) redisData.getData(), Shop.class);
    LocalDateTime expireTime = redisData.getExpireTime();
    // 5.判断是否过期
    if(expireTime.isAfter(LocalDateTime.now())) {
        // 5.1.未过期，直接返回店铺信息
        return shop;
    }
    // 5.2.已过期，需要缓存重建
    // 6.缓存重建
    // 6.1.获取互斥锁
    String lockKey = LOCK_SHOP_KEY + id;
    boolean isLock = tryLock(lockKey);
    // 6.2.判断是否获取锁成功
    if (isLock){
        // 线程抢到锁之后，要二次检查缓存是否过期(Double Check)
        shopJson = stringRedisTemplate.opsForValue().get(key);
        redisData = JSONUtil.toBean(shopJson, RedisData.class);
        shop = JSONUtil.toBean((JSONObject) redisData.getData(), Shop.class);
        if (redisData.getExpireTime().isAfter(LocalDateTime.now())){
            // 未过期，直接返回
            return shop;
        }
        // 自定义线程池，向线程池提交一个异步任务
        CACHE_REBUILD_EXECUTOR.submit( ()->{
            try{
                //重建缓存
                this.saveShop2Redis(id,20L);
            }catch (Exception e){
                throw new RuntimeException(e);
            }finally {
                unlock(lockKey);
            }
        });
    }
    // 6.4.返回过期的商铺信息
    return shop;
}
```

### **封装Redis工具类**
<font style="color:#333333;">基于 StringRedisTemplate 封装一个缓存工具类，满足下列需求：</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">方法1：将任意Java对象序列化为json并存储在String类型的key中，并且可以设置TTL过期时间</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">方法2：将任意Java对象序列化为json并存储在String类型的key中，并且可以设置逻辑过期时间，用于处理缓</font>

<font style="color:#333333;">存击穿问题</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">方法3：根据指定的key查询缓存，并反序列化为指定类型，利用缓存空值的方式解决缓存穿透问题</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">方法4：根据指定的key查询缓存，并反序列化为指定类型，需要利用逻辑过期解决缓存击穿问题</font>

<font style="color:#333333;">将逻辑进行封装</font>

```java
@Slf4j
@Component
public class CacheClient {
    @Autowired
    private final StringRedisTemplate stringRedisTemplate;
    
    private static final ExecutorService CACHE_REBUILD_EXECUTOR = Executors.newFixedThreadPool(10);

    /**
     * 设置缓存
     * @param value
     * @param key
     * @param time
     * @param unit
     */
    public void set(String key, Object value, Long time, TimeUnit unit) {
        stringRedisTemplate.opsForValue().set(key, JSONUtil.toJsonStr(value), time, unit);
    }
    
    /**
     * 逻辑过期，设置缓存
     * @param value
     * @param key
     * @param time
     * @param unit
     */
    public void setWithLogicalExpire(String key, Object value, Long time, TimeUnit unit) {
        // 设置逻辑过期
        RedisData redisData = new RedisData();
        redisData.setData(value);
        redisData.setExpireTime(LocalDateTime.now().plusSeconds(unit.toSeconds(time)));
        // 写入Redis
        stringRedisTemplate.opsForValue().set(key, JSONUtil.toJsonStr(redisData));
    }
    
    /**
     * 缓存空值
     * @param id
     * @param keyPrefix
     * @param type
     * @param time
     */
    // 参考ShopServiceImpl类里的queryWithPassThrough()方法
    public <R,ID> R queryWithPassThrough(String keyPrefix, ID id, Class<R> type, 
            Function<ID, R> dbFallback, Long time, TimeUnit unit){
        String key = keyPrefix + id;
        // 1.从redis查询商铺缓存
        String json = stringRedisTemplate.opsForValue().get(key);
        // 2.判断是否存在
        if (StrUtil.isNotBlank(json)) {
            // 3.存在，直接返回
            return JSONUtil.toBean(json, type);
        }
        // 判断命中的是否是空值
        if (json != null) {
            // 返回一个错误信息
            return null;
        }

        // 4.不存在，根据id查询数据库
        R r = dbFallback.apply(id);
        // 5.不存在，返回错误
        if (r == null) {
            // 将空值写入redis
            stringRedisTemplate.opsForValue().set(key, "", CACHE_NULL_TTL, TimeUnit.MINUTES);
            // 返回错误信息
            return null;
        }
        // 6.存在，写入redis
        this.set(key, r, time, unit);
        return r;
    }
    
    /**
     * 逻辑过期，查询缓存
     * @param id
     * @return
     */
    public <R,ID> R queryWithLogicalExpire(String keyPrefix,String lockPrefix,ID id,Class<R> type,
                                           Long time,TimeUnit unit, Function<ID,R> dbFallBack) {
        String key = keyPrefix + id;
        // 从redis查询商户缓存
        String jsonStr = stringRedisTemplate.opsForValue().get(key);
        // 判断是否存在
        if (StrUtil.isBlank(jsonStr)) {
            // 不存在直接返回
            return null;
        }
        // 缓存命中，转为对象
        RedisData redisData = JSONUtil.toBean(jsonStr, RedisData.class);
        R r = JSONUtil.toBean((JSONObject) redisData.getData(), type);
        // 判断是否过期
        if (redisData.getExpireTime().isAfter(LocalDateTime.now())){
            // 未过期，直接返回
            return r;
        }
        // 过期，尝试获取锁
        String lockKey = lockPrefix + id;
        if (tryLock(lockKey)) {
            // 获取成功
            // 线程抢到锁之后，要二次检查缓存是否过期(Double Check)
            jsonStr = stringRedisTemplate.opsForValue().get(key);
            redisData = JSONUtil.toBean(jsonStr, RedisData.class);
            r = JSONUtil.toBean((JSONObject) redisData.getData(), type);
            if (redisData.getExpireTime().isAfter(LocalDateTime.now())){
                // 未过期，直接返回
                return r;
            }
            CACHE_REBUILD_EXECUTOR.submit(()->{
                try {
                    // 查询数据库
                    R newR = dbFallBack.apply(id);
                    // 重建缓存
                    setWithLogicalExpire(newR,key,time,unit);
                } finally {
                    unlock(lockKey);
                }
            });
        }
        // 返回过期数据
        return r;
    }
    
    /**
     * 互斥锁，查询缓存
     * @param id
     * @return
     */
    public <R,ID> R queryWithMutex(ID id,String lockPrefix,String keyPrefix,Class<R> type,
                                   Long time,TimeUnit unit,Function<ID,R> dbFallBack) {
        // 自旋循环，替代递归，防止栈溢出
        while (true) {
            String key = keyPrefix + id;
            String jsonStr = stringRedisTemplate.opsForValue().get(key);
            // 判断缓存是否命中
            if (StrUtil.isNotBlank(jsonStr)) {
                // 命中，直接返回
                return JSONUtil.toBean(jsonStr, type);
            }
            // 判断空值（缓存穿透）
            if (jsonStr != null) {
                return null;
            }
            // 尝试获取锁
            String lockKey = lockPrefix + id;
            R r = null;
            if (tryLock(lockKey)) {
                try {
                    // 线程抢到锁之后，要二次检查缓存(Double Check)
                    jsonStr = stringRedisTemplate.opsForValue().get(key);
                    if (StrUtil.isNotBlank(jsonStr)) {
                        // 命中，直接返回
                        return JSONUtil.toBean(jsonStr, type);
                    }
                    // 判断空值（缓存穿透）
                    if (jsonStr != null) {
                        return null;
                    }
                    // 获取锁成功，从数据库查询商户
                    r = dbFallBack.apply(id);
                    // 判断是否存在
                    if (r == null) {
                        // 不存在，将空值存入redis，设置短期TTL（缓存穿透）
                        stringRedisTemplate.opsForValue()
                                .set(key, "", RedisConstants.CACHE_NULL_TTL, TimeUnit.MINUTES);
                        // 返回错误
                        return null;
                    }
                    // 存在，存入缓存中
                    set(r,key,time,unit);
                    // 返回
                    return r;
                } finally {
                    // 释放互斥锁
                    unlock(lockKey);
                }
            }
            try {
                // 获取失败，休眠
                // 30~80毫秒的随机值，避免大量线程休眠固定时间后同一时间同时发起抢锁请求
                Thread.sleep(new Random().nextLong(30, 80));
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            }
        }
    }

    private boolean tryLock(String key) {
        Boolean flag = stringRedisTemplate.opsForValue().setIfAbsent(key, "1", 10, TimeUnit.SECONDS);
        return BooleanUtil.isTrue(flag);
    }

    private void unlock(String key) {
        stringRedisTemplate.delete(key);
    }
}
```

+ <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">ShopServiceImpl 类</font>

```java
@Resource
private CacheClient cacheClient;

 @Override
    public Result queryById(Long id) {
        // 解决缓存穿透
        Shop shop = cacheClient
                .queryWithPassThrough(CACHE_SHOP_KEY, id, Shop.class, 
                        this::getById, CACHE_SHOP_TTL, TimeUnit.MINUTES);

        // 互斥锁解决缓存击穿
        // Shop shop = cacheClient
        //         .queryWithMutex(CACHE_SHOP_KEY, id, Shop.class, this::getById, CACHE_SHOP_TTL, TimeUnit.MINUTES);

        // 逻辑过期解决缓存击穿
        // Shop shop = cacheClient
        //         .queryWithLogicalExpire(CACHE_SHOP_KEY, id, Shop.class, this::getById, 20L, TimeUnit.SECONDS);

        if (shop == null) {
            return Result.fail("店铺不存在！");
        }
        // 7.返回
        return Result.ok(shop);
    }
```



## **优惠券秒杀**
### **全局唯一ID**
<font style="color:#333333;">每个店铺都可以发布优惠券：</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785593827183-376d6a33-9718-402c-98a5-973bc42e5296.png" width="376" title="" crop="0,0,1,1" id="u98fd6435" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">当用户抢购时，就会生成订单并保存到 tb_voucher_order 这张表中，而订单表如果使用数据库自增ID就存在一些问题：</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">id 的规律性太明显</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">受单表数据量的限制</font>

<font style="color:#333333;">场景分析一：如果我们的 id 具有太明显的规则，用户或者说商业对手很容易猜测出来我们的一些敏感信息，比如商城在一天时间内，卖出了多少单，这明显不合适。</font>

<font style="color:#333333;">场景分析二：随着我们商城规模越来越大，mysql 的单表的容量不宜超过500W，数据量过大之后，我们要进行拆库拆表，但拆分表了之后，它们从逻辑上讲它们是同一张表，所以他们的 id 是不能一样的， 于是乎我们需要保证 id 的唯一性。</font>

**<font style="color:#333333;">全局ID生成器</font>**<font style="color:#333333;">，是一种在分布式系统下用来生成全局唯一ID的工具，一般要满足下列特性：</font>

<font style="color:#333333;">高可用：不容易挂；高性能：快</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785593827227-c6af7fff-7a5a-4c92-a99d-1becfc58484c.png" width="563" title="" crop="0,0,1,1" id="uc067cf86" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">为了增加ID的安全性，我们可以不直接使用Redis自增的数值，而是拼接一些其它信息：</font>

<font style="color:#333333;">ID的组成部分：Long 为8个字节，则为64个 bit</font>

<font style="color:#333333;">符号位：1bit，永远为0，保证数字永远正数</font>

<font style="color:#333333;">时间戳：31bit，以秒为单位，当前秒 减 基准起始时间BEGIN_TIMESTAMP，可以使用约 69 年</font>

<font style="color:#333333;">序列号：32bit，秒内的计数器，支持每秒产生 2^32 个不同ID</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785593827484-a78c14d4-aab5-4540-b07f-f51e40a748f6.png" width="668" title="" crop="0,0,1,1" id="udfeedef5" class="ne-image" style="font-size: 14px">



#### **Redis实现全局唯一Id**
```java
@Component
public class RedisIdWorker {
    /**
     * 开始时间戳
     */
    private static final long BEGIN_TIMESTAMP = 1640995200L;
    /**
     * 序列号的位数
     */
    private static final int COUNT_BITS = 32;
    
    @Autowired
    private StringRedisTemplate stringRedisTemplate;


    public long nextId(String keyPrefix) {
        // 1.生成时间戳
        LocalDateTime now = LocalDateTime.now();
        long nowSecond = now.toEpochSecond(ZoneOffset.UTC);
        // 相对时间戳 = 当前秒数 - 基准起始秒数
        long timestamp = nowSecond - BEGIN_TIMESTAMP;

        // 2.生成序列号
        // 2.1.获取当前日期，精确到天
        String date = now.format(DateTimeFormatter.ofPattern("yyyy:MM:dd"));
        // 2.2.自增长
        // 时间用 : 冒号分隔，在Redis方便统计
        long count = stringRedisTemplate.opsForValue().increment("icr:" + keyPrefix + ":" + date);
        
        // 3.拼接并返回
        // 时间戳在低位，让它左移 32 位，把时间戳整体挪到 64 位 long 的高 32 位，低 32 位全部补 0
        // | count 按位或，count 只占用低 32 位，高 32 位都是 0；
        // 或运算后，低 32 位填充序列号 count，高 32 位保留时间戳
        return timestamp << COUNT_BITS | count;
    }
}
```

<font style="color:#333333;">测试类</font>

<font style="color:#333333;">知识小贴士：关于countdownlatch</font>

<font style="color:#333333;">countdownlatch名为信号枪：主要的作用是同步协调在多线程的等待于唤醒问题</font>

<font style="color:#333333;">我们如果没有CountDownLatch ，那么由于程序是异步的，当异步程序没有执行完时，主线程就已经执行完了，然后我们期望的是分线程全部走完之后，主线程再走，所以我们此时需要使用到CountDownLatch</font>

<font style="color:#333333;">CountDownLatch 中有两个最重要的方法</font>

<font style="color:#333333;">1、countDown</font>

<font style="color:#333333;">2、await</font>

<font style="color:#333333;">await 方法 是阻塞方法，我们担心分线程没有执行完时，main线程就先执行，所以使用await可以让main线程阻塞，那么什么时候main线程不再阻塞呢？</font>

<font style="color:#333333;">当CountDownLatch 内部维护的 变量变为0时，就不再阻塞，直接放行，那么什么时候CountDownLatch 维护的变量变为0 呢。</font>

<font style="color:#333333;">我们只需要调用一次 countDown ，内部变量就减少1，我们让分线程和变量绑定， 执行完一个分线程就减少一个变量，当分线程全部走完，CountDownLatch 维护的变量就是0，此时await就不再阻塞，统计出来的时间也就是所有分线程执行完后的时间。</font>

```java
@Test
void testIdWorker() throws InterruptedException {
    CountDownLatch latch = new CountDownLatch(300);

    Runnable task = () -> {
        for (int i = 0; i < 100; i++) {
            long id = redisIdWorker.nextId("order");
            System.out.println("id = " + id);
        }
        latch.countDown();
    };
    long begin = System.currentTimeMillis();
    for (int i = 0; i < 300; i++) {
        es.submit(task);
    }
    latch.await();
    long end = System.currentTimeMillis();
    System.out.println("time = " + (end - begin));
}
```

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">全局唯一D生成策略</font>

UUID

Redis自增

snowflake算法

数据库自增

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">Redis自增ID策略</font>

每天一个key，方便统计订单量

ID构造是时间戳 + 计数器



### **添加优惠卷**
<font style="color:#333333;">每个店铺都可以发布优惠券，分为平价券和特价券。平价券可以任意购买，而特价券需要秒杀抢购：</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785593864328-72c5ecfb-b112-4b4a-9389-d912b098a113.png" width="636" title="" crop="0,0,1,1" id="uea07c58d" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">tb_voucher：优惠券的基本信息，优惠金额、使用规则等</font>

<font style="color:#333333;"> tb_seckill_voucher：优惠券的库存、开始抢购时间，结束抢购时间。特价优惠券才需要填写这些信息。</font>

<font style="color:#333333;">平价卷由于优惠力度并不是很大，所以是可以任意领取。</font>

<font style="color:#333333;">而代金券由于优惠力度大，所以像第二种卷，就得限制数量，从表结构上也能看出，特价卷除了具有优惠卷的基本信息以外，还具有库存，抢购时间，结束时间等等字段。</font>

**<font style="color:#333333;">新增普通卷代码： </font>**

+ <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">VoucherController 类</font>

```java
/**
 * 新增普通券
 * @param voucher 优惠券信息
 * @return 优惠券id
 */
@PostMapping
public Result addVoucher(@RequestBody Voucher voucher) {
    voucherService.save(voucher);
    return Result.ok(voucher.getId());
}
```

**<font style="color:#333333;">新增秒杀卷代码：</font>**

+ **<font style="color:#333333;background-color:rgba(0, 0, 0, 0);">VoucherController 类</font>**

```java
/**
 * 新增秒杀券
 * @param voucher 优惠券信息，包含秒杀信息
 * @return 优惠券id
 */
@PostMapping("/seckill")
public Result addSeckillVoucher(@RequestBody Voucher voucher) {
    voucherService.addSeckillVoucher(voucher);
    return Result.ok(voucher.getId());
}
```

+ **<font style="color:#333333;background-color:rgba(0, 0, 0, 0);">VoucherServiceImpl 类</font>**

```java
/**
 * 新增秒杀卷
 * @param voucher
 */
@Override
@Transactional
public void addSeckillVoucher(Voucher voucher) {
    // 保存优惠券
    save(voucher);
    // 保存秒杀信息
    SeckillVoucher seckillVoucher = new SeckillVoucher();
    seckillVoucher.setVoucherId(voucher.getId());
    seckillVoucher.setStock(voucher.getStock());
    seckillVoucher.setBeginTime(voucher.getBeginTime());
    seckillVoucher.setEndTime(voucher.getEndTime());
    seckillVoucherService.save(seckillVoucher);
    // 保存秒杀库存到Redis中
    stringRedisTemplate.opsForValue().set(SECKILL_STOCK_KEY + voucher.getId(), voucher.getStock().toString());
}
```



### **实现秒杀下单**
<font style="color:#333333;">下单核心思路：当我们点击抢购时，会触发右侧的请求，我们只需要编写对应的controller即可</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785593880244-fd13f652-293e-4e66-a4e9-63625b70557d.png" width="718" title="" crop="0,0,1,1" id="ua0a0c5c2" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">秒杀下单应该思考的内容：</font>

<font style="color:#333333;">下单时需要判断两点：</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">秒杀是否开始或结束，如果尚未开始或已经结束则无法下单</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">库存是否充足，不足则无法下单</font>

<font style="color:#333333;">下单核心逻辑分析：</font>

<font style="color:#333333;">当用户开始进行下单，我们应当去查询优惠卷信息，查询到优惠卷信息，判断是否满足秒杀条件</font>

<font style="color:#333333;">比如时间是否充足，如果时间充足，则进一步判断库存是否足够，如果两者都满足，则扣减库存，创建订单，然后返回订单id，如果有一个条件不满足则直接结束。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785593879715-4022b77d-9057-4581-b170-8c3fc932149f.png" width="647" title="" crop="0,0,1,1" id="ud3e6dee5" class="ne-image" style="font-size: 14px">

+ <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">VoucherOrderServiceImpl 类</font>

```java
@Autowired
private ISeckillVoucherService seckillVoucherService;

@Autowired
private RedisIdWorker redisIdWorker;

/**
 * 秒杀下单
 *
 * @param voucherId
 * @return
 */
@Override
@Transactional
public Result seckillVoucher(Long voucherId) {
    // 1.查询优惠券
    SeckillVoucher voucher = seckillVoucherService.getById(voucherId);
    // 2.判断秒杀是否开始
    if (voucher.getBeginTime().isAfter(LocalDateTime.now())) {
        // 尚未开始
        return Result.fail("秒杀尚未开始！");
    }
    // 3.判断秒杀是否已经结束
    if (voucher.getEndTime().isBefore(LocalDateTime.now())) {
        // 已结束
        return Result.fail("秒杀已经结束！");
    }
    // 4.判断库存是否充足
    if (voucher.getStock() < 1) {
        // 库存不足
        return Result.fail("库存不足！");
    }
    //5，扣减库存
    boolean success = seckillVoucherService.update()
            .setSql("stock = stock -1")
            .eq("voucher_id", voucherId).update();
    if (!success) {
        //扣减库存
        return Result.fail("库存不足！");
    }
    //6.创建订单
    VoucherOrder voucherOrder = new VoucherOrder();
    // 6.1.订单id
    long orderId = redisIdWorker.nextId("order");
    voucherOrder.setId(orderId);
    // 6.2.用户id
    Long userId = UserHolder.getUser().getId();
    voucherOrder.setUserId(userId);
    // 6.3.代金券id
    voucherOrder.setVoucherId(voucherId);
    // 保存订单
    save(voucherOrder);
    // 返回订单
    return Result.ok(orderId);
}
```



### **库存超卖问题**
<font style="color:#333333;">有关超卖问题分析：在我们原有代码中是这么写的</font>

```java
 if (voucher.getStock() < 1) {
    // 库存不足
    return Result.fail("库存不足！");
}
//5，扣减库存
boolean success = seckillVoucherService.update()
        .setSql("stock= stock -1")
        .eq("voucher_id", voucherId).update();
if (!success) {
    //扣减库存
    return Result.fail("库存不足！");
}
```

<font style="color:#333333;">假设线程1过来查询库存，判断出来库存大于1，线程一在扣减之前，线程2也去查询库存，发现这个数量一定也大于1，那么这两个线程都会去扣减库存，最终多个线程相当于一起去扣减库存，此时就会出现库存的超卖问题。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785593893986-7a84e3bf-418b-418d-830c-2928bd9d391b.png" width="662" title="" crop="0,0,1,1" id="u51bf3b08" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">超卖问题是典型的多线程安全问题，针对这一问题的常见解决方案就是加锁。</font>

<font style="color:#333333;">而对于加锁，我们通常有两种解决方案：</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785593893677-3b2de4f5-34cd-4380-9fd8-2aa430926852.png" width="682" title="" crop="0,0,1,1" id="udce54793" class="ne-image" style="font-size: 14px">

+ **<font style="color:#333333;background-color:rgba(0, 0, 0, 0);">悲观锁</font>**

<font style="color:#333333;">悲观锁可以实现对于数据的串行化执行，比如 syn，和 lock 都是悲观锁的代表，同时，悲观锁中又可以再细分为公平锁，非公平锁，可重入锁，等等。</font>

<font style="color:#333333;">但悲观锁是一个一个执行，性能较差，不适合高并发场景。</font>

+ **<font style="color:#333333;background-color:rgba(0, 0, 0, 0);">乐观锁</font>**

<font style="color:#333333;">乐观锁：会有一个版本号，每次操作数据会对版本号+1，再提交回数据时，会去校验是否比之前的版本大1 ，如果大1 ，则进行操作成功，这套机制的核心逻辑在于，如果在操作过程中，版本号只比原来大1 ，那么就意味着操作过程中没有人对它进行过修改，它的操作就是安全的，如果不大1，则数据被修改过，当然乐观锁还有一些变种的处理方式比如cas</font>

<font style="color:#333333;">乐观锁的典型代表：就是cas，利用cas进行无锁化机制加锁，var5 是操作前读取的内存值，while中的var1+var2 是预估值，如果预估值 == 内存值，则代表中间没有被人修改过，此时就将新值去替换 内存值。</font>

<font style="color:#333333;">其中 do while 是为了在操作失败时，再次进行自旋操作，即把之前的逻辑再操作一次。</font>

```java
int var5;
do {
    var5 = this.getIntVolatile(var1, var2);
} while(!this.compareAndSwapInt(var1, var2, var5, var5 + var4));

return var5;
```

乐观锁的关键是判断之前查询得到的数据是否有被修改过，常见的方式有两种：

+ <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">版本号法</font>

<font style="color:#333333;">它的操作逻辑是在操作时，对版本号进行+1 操作，然后要求 version 如果是1 的情况下，才能操作，那么第一个线程在操作后，数据库中的version变成了2，但是它自己满足version=1 ，所以没有问题。</font>

<font style="color:#333333;">此时线程2执行，线程2 最后也需要加上条件version =1 ，但是现在由于线程1已经操作过了，此时 version = 2。所以线程2 操作时就不满足version=1 的条件了，所以线程2无法执行成功。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785593893988-eba0c5ea-092c-486e-9cf1-0a7630dfadd1.png" width="1598" title="" crop="0,0,1,1" id="ucb61ac66" class="ne-image" style="font-size: 14px">

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">CAS法</font>

对比版本号是否为之前查询到的版本号，那改为对比库存是否为之前查询到的库存也可以。

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785593894084-84c01e43-0c0f-48da-96bc-9d23fcf54efe.png" width="1964" title="" crop="0,0,1,1" id="u2e4c56bd" class="ne-image" style="font-size: 14px">

#### **乐观锁解决超卖问题**
+ **<font style="color:#333333;background-color:rgba(0, 0, 0, 0);">方案一</font>**

<font style="color:#333333;">VoucherOrderServiceImpl 在扣减库存时，改为：</font>

```java
boolean success = seckillVoucherService.update()
            .setSql("stock= stock -1") //set stock = stock -1
            .eq("voucher_id", voucherId).eq("stock",voucher.getStock()).update(); //where id = ？ and stock = ?
```



<font style="color:#333333;">以上逻辑的核心含义是：只要我扣减库存时的库存和之前我查询到的库存是一样的，就意味着没有人在中间修改过库存，那么此时就是安全的。</font>

<font style="color:#333333;">但是以上这种方式通过测试发现会有很多失败的情况，失败的原因在于：在使用乐观锁过程中假设100个线程同时都拿到了100的库存，然后大家一起去进行扣减，但是100个人中只有1个人能扣减成功，其他的人在处理时，他们在扣减时，库存已经被修改过了，所以此时其他线程都会失败。</font>

<font style="color:#333333;">安全性能保证，但失败率太高。</font>

+ **<font style="color:#333333;background-color:rgba(0, 0, 0, 0);">方案二</font>**

<font style="color:#333333;">之前的方式要修改前后都保持一致，但是这样我们分析过，成功的概率太低，所以我们的乐观锁需要变一下，改成stock大于0 即可</font>

```java
boolean success = seckillVoucherService.update()
            .setSql("stock= stock -1")
            .eq("voucher_id", voucherId)
            .gt("stock",0)
            .update(); //where id = ? and stock > 0
```

**<font style="color:#333333;">知识小扩展：</font>**

<font style="color:#333333;">针对cas中的自旋压力过大，我们可以使用 Longaddr 这个类去解决</font>

<font style="color:#333333;">Java8 提供的一个对 AtomicLong 改进后的一个类：LongAdder</font>

<font style="color:#333333;">大量线程并发更新一个原子性的时候，天然的问题就是自旋，会导致并发性问题，当然这也比我们直接使用syn 好。</font>

<font style="color:#333333;">所以利用这么一个类 LongAdder来进行优化</font>

<font style="color:#333333;">如果获取某个值，则会对 cell 和 base 的值进行递增，最后返回一个完整的值。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785593933732-c3e2d3ba-56fb-491f-9920-6750543a270f.png" width="687" title="" crop="0,0,1,1" id="u9660cdef" class="ne-image" style="font-size: 14px">

### **优惠券秒杀一人一单**
<font style="color:#333333;">需求：修改秒杀业务，要求同一个优惠券，一个用户只能下一单</font>

**<font style="color:#333333;">现在的问题在于：</font>**

<font style="color:#333333;">优惠卷是为了引流，但是目前的情况是，一个人可以无限制的抢这个优惠卷，所以我们应当增加一层逻辑，让一个用户只能下一个单，而不是让一个用户下多个单。</font>

<font style="color:#333333;">具体操作逻辑如下：比如时间是否充足，如果时间充足，则进一步判断库存是否足够，然后再根据优惠卷id和用户id查询是否已经下过这个订单，如果下过这个订单，则不再下单，否则进行下单。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785593933842-499a0327-b039-4dab-a364-a17f41f7f9b4.png" width="636" title="" crop="0,0,1,1" id="u7a6d9ecf" class="ne-image" style="font-size: 14px">

+ <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">VoucherOrderServiceImpl 类</font>

**<font style="color:#333333;">初步代码：增加一人一单逻辑</font>**

```java
@Override
public Result seckillVoucher(Long voucherId) {
    // 1.查询优惠券
    SeckillVoucher voucher = seckillVoucherService.getById(voucherId);
    // 2.判断秒杀是否开始
    if (voucher.getBeginTime().isAfter(LocalDateTime.now())) {
        // 尚未开始
        return Result.fail("秒杀尚未开始！");
    }
    // 3.判断秒杀是否已经结束
    if (voucher.getEndTime().isBefore(LocalDateTime.now())) {
        // 尚未开始
        return Result.fail("秒杀已经结束！");
    }
    // 4.判断库存是否充足
    if (voucher.getStock() < 1) {
        // 库存不足
        return Result.fail("库存不足！");
    }
    // 5.一人一单逻辑
    // 5.1.用户id
    Long userId = UserHolder.getUser().getId();
    int count = query().eq("user_id", userId).eq("voucher_id", voucherId).count();
    // 5.2.判断是否存在
    if (count > 0) {
        // 用户已经购买过了
        return Result.fail("用户已经购买过一次！");
    }

    //6，扣减库存
    boolean success = seckillVoucherService.update()
            .setSql("stock= stock -1")
            .eq("voucher_id", voucherId)
            .update();
    if (!success) {
        //扣减库存
        return Result.fail("库存不足！");
    }
    //7.创建订单
    VoucherOrder voucherOrder = new VoucherOrder();
    // 7.1.订单id
    long orderId = redisIdWorker.nextId("order");
    voucherOrder.setId(orderId);

    voucherOrder.setUserId(userId);
    // 7.3.代金券id
    voucherOrder.setVoucherId(voucherId);
    save(voucherOrder);

    return Result.ok(orderId);

}
```

**<font style="color:#333333;">存在问题：</font>**<font style="color:#333333;">现在的问题还是和之前一样，并发过来，查询数据库，都不存在订单，存在线程安全问题。</font>

<font style="color:#333333;">所以我们还是需要加锁，但是乐观锁比较适合更新数据，而现在是插入数据，所以我们需要使用悲观锁操作。</font>

**<font style="color:#333333;">注意：</font>**<font style="color:#333333;">在这里提到了非常多的问题，首先我们的初始方案是封装了一个createVoucherOrder方法，同时为了确保它线程安全，在方法上添加了一把synchronized 锁，其余代码不变。</font>

```java
@Transactional
public synchronized Result createVoucherOrder(Long voucherId) {
    Long userId = UserHolder.getUser().getId();
         // 5.1.查询订单
        int count = query().eq("user_id", userId).eq("voucher_id", voucherId).count();
        // 5.2.判断是否存在
        if (count > 0) {
            // 用户已经购买过了
            return Result.fail("用户已经购买过一次！");
        }

        // 6.扣减库存
        boolean success = seckillVoucherService.update()
                .setSql("stock = stock - 1") // set stock = stock - 1
                .eq("voucher_id", voucherId).gt("stock", 0) // where id = ? and stock > 0
                .update();
        if (!success) {
            // 扣减失败
            return Result.fail("库存不足！");
        }

        // 7.创建订单
        VoucherOrder voucherOrder = new VoucherOrder();
        // 7.1.订单id
        long orderId = redisIdWorker.nextId("order");
        voucherOrder.setId(orderId);
        // 7.2.用户id
        voucherOrder.setUserId(userId);
        // 7.3.代金券id
        voucherOrder.setVoucherId(voucherId);
        save(voucherOrder);

        // 7.返回订单id
        return Result.ok(orderId);
}
```

<font style="color:#333333;">但是这样添加锁，锁的粒度太粗了，在使用锁过程中，控制 </font>**<font style="color:#333333;">锁粒度</font>**<font style="color:#333333;"> 是一个非常重要的事情，因为如果锁的粒度太大，会导致每个线程进来都会锁住。因为一人一单，同一个用户来了才去判断并发安全问题，对于其他用户不用去加锁。</font>

<font style="color:#333333;">所以我们需要去控制锁的粒度，以下这段代码需要修改为：以用户ID去加锁，其他用户不受影响。</font>

<font style="color:#333333;">但如果我们直接使用 userId.toString() 它拿到的对象实际上是不同的对象，Long和String都是对象，即使id数字没变，但会new出来新对象，所以不是同一个值。</font>

<font style="color:#333333;">我们使用锁必须保证锁必须是同一把，所以我们需要使用 intern() 方法，intern() 这个方法是从常量池中拿到数据。</font>

```java
@Transactional
public  Result createVoucherOrder(Long voucherId) {
    Long userId = UserHolder.getUser().getId();
    synchronized(userId.toString().intern()){
         // 5.1.查询订单
        int count = query().eq("user_id", userId).eq("voucher_id", voucherId).count();
        // 5.2.判断是否存在
        if (count > 0) {
            // 用户已经购买过了
            return Result.fail("用户已经购买过一次！");
        }

        // 6.扣减库存
        boolean success = seckillVoucherService.update()
                .setSql("stock = stock - 1") // set stock = stock - 1
                .eq("voucher_id", voucherId).gt("stock", 0) // where id = ? and stock > 0
                .update();
        if (!success) {
            // 扣减失败
            return Result.fail("库存不足！");
        }

        // 7.创建订单
        VoucherOrder voucherOrder = new VoucherOrder();
        // 7.1.订单id
        long orderId = redisIdWorker.nextId("order");
        voucherOrder.setId(orderId);
        // 7.2.用户id
        voucherOrder.setUserId(userId);
        // 7.3.代金券id
        voucherOrder.setVoucherId(voucherId);
        save(voucherOrder);

        // 7.返回订单id
        return Result.ok(orderId);
    }
}
```

<font style="color:#333333;">但是以上代码还是存在问题，synchronized 代码块执行完毕立刻释放锁，但方法的事务要等到整个方法全部执行完毕才会提交。</font>

<font style="color:#333333;">所以如果你在方法内部加锁，可能会导致锁已经释放，但当前方法事务还没有提交，其他进程进来还是查询不到订单。</font>

<font style="color:#333333;">所以应该将整个方法都锁起来，这样事务提交之后，才去释放锁，确保事务不会出现问题。</font>

```java
/**
 * 秒杀下单
 *
 * @param voucherId
 * @return
 */
@Override
@Transactional
public Result seckillVoucher(Long voucherId) {
    // 查询优惠券
    SeckillVoucher voucher = seckillVoucherService.getById(voucherId);
    // 秒杀是否开始
    if (voucher.getBeginTime().isAfter(LocalDateTime.now())) {
        // 还没开始
        return Result.fail("秒杀还没开始！");
    }
    // 秒杀是否结束
    if (voucher.getEndTime().isBefore(LocalDateTime.now())) {
        // 已结束
        return Result.fail("秒杀已结束！");
    }
    // 库存是否充足
    Integer stock = voucher.getStock();
    if (stock <= 0) {
        // 库存不足
        return Result.fail("库存不足！");
    }
    Long userId = UserHolder.getUser().getId();
    synchronized (userId.toString().intern()) {
        // 解决一人一单问题
        return createVoucherOrder(voucherId);
    }
}

@Transactional
public Result createVoucherOrder(Long voucherId) {
    // 一人一单
    Long userId = UserHolder.getUser().getId();
    QueryWrapper<VoucherOrder> wrapper = new QueryWrapper<VoucherOrder>()
            .eq("user_id", userId);
    VoucherOrder voucherOrder = getOne(wrapper);
    if (voucherOrder != null) {
        return Result.fail("用户无法重复下单！");
    }
    // 扣减库存
    boolean flag = seckillVoucherService.update()
            .setSql("stock = stock -1")
            .eq("voucher_id", voucherId)
            .gt("stock", 0)
            .update();
    if (!flag) {
        // 二次检查库存
        return Result.fail("库存不足！");
    }
    // 创建订单ID
    long orderID = redisIdWorker.nextId("order");
    // 创建订单
    VoucherOrder order = new VoucherOrder()
            .setVoucherId(voucherId)
            .setUserId(userId)
            .setId(orderID);
    // 保存订单
    save(order);
    // 返回订单
    return Result.ok(orderID);

}
```

<font style="color:#333333;">但是以上做法依然有问题，createVoucherOrder(voucherId) 本质是 this.createVoucherOrder(voucherId) 的方式调用的，this 是</font>**<font style="color:#333333;">原始目标对象</font>**<font style="color:#333333;">，不是 Spring </font>**<font style="color:#333333;">事务代理对象</font>**<font style="color:#333333;">，导致内层 @Transactional 完全失效。</font>

<font style="color:#333333;">事务想要生效，还得利用代理来生效，所以这个地方，我们需要获得原始的事务对象， 来操作事务</font>

<font style="color:#333333;">添加依赖：</font>

```xml
<dependency>
  <groupId>org.aspectj</groupId>
  <artifactId>aspectjweaver</artifactId>
</dependency>
```

```java
@MapperScan("com.hmdp.mapper")
@SpringBootApplication
// 暴露当前代理到上下文
@EnableAspectJAutoProxy(exposeProxy = true)
public class HmDianPingApplication {
    public static void main(String[] args) {
        SpringApplication.run(HmDianPingApplication.class, args);
    }
}
```

```java
synchronized (userId.toString().intern()) {
    // 解决一人一单问题
    IVoucherOrderService proxy = (IVoucherOrderService) AopContext.currentProxy();
    return proxy.createVoucherOrder(voucherId);
}
```



### **集群环境下的并发问题**
<font style="color:#333333;">通过加锁可以解决在单机情况下的一人一单安全问题，但是在集群模式下就不行了。</font>

+ <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">模拟集群</font>

<font style="color:#333333;">1、我们将服务启动两份，端口分别为8081和8082。</font>

步骤：

点击下面该图标

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785593972078-d58eb37e-ed71-4a6b-859a-785e61d3561b.png" width="509" title="" crop="0,0,1,1" id="u891ab995" class="ne-image" style="font-size: 14px">

添加SpringBoot服务

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785593971952-169ac302-e5b4-4896-8f1e-144cb6fb40e1.png" width="357" title="" crop="0,0,1,1" id="uf9b7ce7d" class="ne-image" style="font-size: 14px">

按 CTRL+D，复制出下面的启动类

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785593971961-7623ae5c-20b2-411b-a9b6-5ff5eacea81c.png" width="317" title="" crop="0,0,1,1" id="u3fa0f381" class="ne-image" style="font-size: 14px">

点击修改选项

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785593972080-fc5a3383-38cf-4739-bb11-f4528eea35da.png" width="510" title="" crop="0,0,1,1" id="uf8d72737" class="ne-image" style="font-size: 14px">

选择添加虚拟机

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785593972191-a72edce6-8fde-4eca-80ee-a75025f72218.png" width="325" title="" crop="0,0,1,1" id="uc7fb22c5" class="ne-image" style="font-size: 14px">

在VM options中输入：-Dserver.port=8082

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785593972711-bdf95472-b858-4a79-8aeb-98734c1ee22a.png" width="400" title="" crop="0,0,1,1" id="u6fb36f65" class="ne-image" style="font-size: 14px">

再把两个服务都启动了，就可以了。

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785593972754-69f62cbc-c6aa-4963-950e-ac8a1c679b4e.png" width="365" title="" crop="0,0,1,1" id="u9b48de3a" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">2、然后修改nginx的conf目录下的 nginx.conf 文件，配置反向代理和负载均衡：</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785593973034-1457deb0-516f-4312-bcdc-3188073d835b.png" width="505" title="" crop="0,0,1,1" id="u6dd1c08c" class="ne-image" style="font-size: 14px">

文件修改完后，在任务管理器把nginx关闭了，再启动。

<font style="color:#333333;">在此处打上断点（注意要启动调试）</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785593973383-f563b570-35a4-49ca-957a-f14e86fd9312.png" width="720" title="" crop="0,0,1,1" id="u5d3a71f2" class="ne-image" style="font-size: 14px">

token后的值就是 authorization 值

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785593973331-10fdcf5f-75d9-4d1e-a835-d18d8217aa1d.png" width="456" title="" crop="0,0,1,1" id="uf6621585" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">模拟同一个用户，authorization 值都填一样的</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785593973665-b608fa9d-5174-4605-926f-a3256cb3a948.png" width="584" title="" crop="0,0,1,1" id="u6e12a063" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">两个都发起请求，查看订单表，同一个用户买了两张券</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785593973890-a4bc4c91-9148-4436-9325-2d77ddbe5294.png" width="584" title="" crop="0,0,1,1" id="u2aa611c2" class="ne-image" style="font-size: 14px">

**<font style="color:#333333;">有关锁失效原因分析</font>**

<font style="color:#333333;">由于现在我们部署了多个tomcat，每个tomcat都有一个属于自己的 jvm，那么假设在服务器A的tomcat内部，有几个线程它们的锁对象是同一个，一个锁只能让一个线程执行。</font>

<font style="color:#333333;">但是如果现在是服务器B的tomcat内部，又有几个线程，但是它们的锁对象与服务器A的锁对象不是同一个。所以线程3和线程4可以实现互斥，但是却无法和线程1和线程2实现互斥。</font>

<font style="color:#333333;">一个锁有一个线程执行，那么多个锁就有多个线程执行。</font>

<font style="color:#333333;">这就是 集群环境下，syn 锁失效的原因，在这种情况下，我们就需要使用分布式锁来解决这个问题。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785593974416-95e9b6f1-3c7e-4556-9507-1e956c097ce7.png" width="741" title="" crop="0,0,1,1" id="uc3bdbe9d" class="ne-image" style="font-size: 14px">



## **分布式锁**
### **基本原理**
<font style="color:#333333;">分布式锁：满足分布式系统或集群模式下多进程可见并且互斥的锁。</font>

<font style="color:#333333;">分布式锁的核心思想就是让大家都使用同一把锁，只要大家使用的是同一把锁，那么我们就能锁住线程，不让线程进行，让程序串行执行，这就是分布式锁的核心思路。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785594004378-5d1078d0-b2dc-4290-a464-62b03d371f3e.png" width="702" title="" crop="0,0,1,1" id="u37815ff7" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">分布式锁满足什么条件呢？</font>

<font style="color:#333333;">可见性：多个线程都能看到相同的结果，注意：这个地方说的可见性并不是并发编程中指的内存可见性，只是说多个进程之间都能感知到变化的意思。</font>

<font style="color:#333333;">互斥：互斥是分布式锁的最基本的条件，使得程序串行执行</font>

<font style="color:#333333;">高可用：程序不易崩溃，时时刻刻都保证较高的可用性</font>

<font style="color:#333333;">高性能：由于加锁本身就让性能降低，所有对于分布式锁本身需要较高的</font>**<font style="color:#333333;">加锁性能和释放锁性能</font>**

<font style="color:#333333;">安全性：有异常情况，锁能及时释放，不然其他线程拿不到锁，出现死锁问题。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785594004031-cfc0b8b6-981d-4e0b-9d96-410974caacd8.png" width="693" title="" crop="0,0,1,1" id="u1788e7bd" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">常见的分布式锁有三种</font>

<font style="color:#333333;">Mysql：mysql本身就带有锁机制，但是由于mysql性能一般，所以采用分布式锁的情况下，其实使用mysql作为分布式锁比较少见。</font>

<font style="color:#333333;">Redis：redis作为分布式锁是非常常见的一种使用方式，现在企业级开发中基本都使用redis或者zookeeper作为分布式锁，利用setnx这个方法，如果插入key成功，则表示获得到了锁，如果有人插入成功，其他人插入失败则表示无法获得到锁，利用这套逻辑来实现分布式锁。</font>

<font style="color:#333333;">Zookeeper：zookeeper也是企业级开发中较好的一个实现分布式锁的方案，由于本套视频并不讲解zookeeper的原理和分布式锁的实现，所以不过多阐述。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785594004137-08c3fe68-2f97-483c-b329-8febdfbbf7da.png" width="707" title="" crop="0,0,1,1" id="u93eb8225" class="ne-image" style="font-size: 14px">

### **实现思路**
<font style="color:#333333;">实现分布式锁时需要实现的两个基本方法：</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">获取锁：</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">互斥：确保只能有一个线程获取锁</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">非阻塞：尝试一次，获取锁失败直接返回旧数据。阻塞式会浪费CPU，且实现起来会麻烦。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785594004108-b2a04bad-3b16-4596-8030-694925c912ee.png" width="463" title="" crop="0,0,1,1" id="ud8c1aa1e" class="ne-image" style="font-size: 14px">

避免了添加锁，还没添加锁过期时间，服务就宕机了。把命令写在一起。

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785594003924-4b7938da-f700-4acd-b3cb-d2acd3694185.png" width="463" title="" crop="0,0,1,1" id="u92d1ebe1" class="ne-image" style="font-size: 14px">

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">释放锁：</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">手动释放</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">超时释放：获取锁时添加一个超时时间</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785594004467-b0678f8f-5047-493d-b5a9-8b4b8965a680.png" width="550" title="" crop="0,0,1,1" id="u6b506f87" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">核心思路：</font>

<font style="color:#333333;">我们利用redis 的 setNx 方法，当有多个线程进入时，我们就利用该方法，第一个线程进入时，redis 中就有这个key 了，返回了1，如果结果是1，则表示它抢到了锁，那么它去执行业务，然后再删除锁，退出锁逻辑，没有抢到锁的，等待一定时间后重试即可。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785594004839-4e0967dd-f81e-41eb-ac15-3263be38f577.png" width="418" title="" crop="0,0,1,1" id="uad4dbe40" class="ne-image" style="font-size: 14px">



### **分布式锁初级版本**
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">加锁逻辑</font>

**<font style="color:#333333;">锁的基本接口</font>**

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785594023090-a2dbdb80-0422-4b8a-b9a7-138d84cfcc5b.png" width="648" title="" crop="0,0,1,1" id="ua102daea" class="ne-image" style="font-size: 14px">

**<font style="color:#333333;">SimpleRedisLock</font>**

<font style="color:#333333;">利用 setnx 方法进行加锁，同时增加过期时间，防止死锁，此方法可以保证加锁和增加过期时间具有原子性。</font>

```java
public class SimpleRedisLock implements ILock {
    // 锁名
    private String name;
    private StringRedisTemplate stringRedisTemplate;

    public SimpleRedisLock(StringRedisTemplate stringRedisTemplate, String name) {
        this.stringRedisTemplate = stringRedisTemplate;
        this.name = name;
    }

    // 锁前缀
    private static final String KEY_PREFIX = "lock:";

    /**
     * 获取锁
     *
     * @param timeoutSec
     * @return
     */
    @Override
    public boolean tryLock(long timeoutSec) {
        // 获取线程id，唯一标识锁
        String threadId = Thread.currentThread().getId();
        // 获取锁
        Boolean flag = stringRedisTemplate.opsForValue()
                .setIfAbsent(KEY_PREFIX + name, threadId + "", timeoutSec, TimeUnit.SECONDS);
        // 防止自动拆箱空指针
        return Boolean.TRUE.equals(flag);
    }

    /**
     * 释放锁
     */
    @Override
    public void unlock() {
        stringRedisTemplate.delete(KEY_PREFIX + name);
    }
}
```

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">修改业务代码</font>

```java
@Override
public Result seckillVoucher(Long voucherId) {
    // 1.查询优惠券
    SeckillVoucher voucher = seckillVoucherService.getById(voucherId);
    // 2.判断秒杀是否开始
    if (voucher.getBeginTime().isAfter(LocalDateTime.now())) {
        // 尚未开始
        return Result.fail("秒杀尚未开始！");
    }
    // 3.判断秒杀是否已经结束
    if (voucher.getEndTime().isBefore(LocalDateTime.now())) {
        // 尚未开始
        return Result.fail("秒杀已经结束！");
    }
    // 4.判断库存是否充足
    if (voucher.getStock() < 1) {
        // 库存不足
        return Result.fail("库存不足！");
    }
    Long userId = UserHolder.getUser().getId();
    //创建锁对象(新增代码)
    SimpleRedisLock lock = new SimpleRedisLock("order:" + userId, stringRedisTemplate);
    //获取锁对象
    boolean isLock = lock.tryLock(1200);
    //加锁失败
    if (!isLock) {
        return Result.fail("不允许重复下单");
    }
    try {
        //获取代理对象(事务)
        IVoucherOrderService proxy = (IVoucherOrderService) AopContext.currentProxy();
        return proxy.createVoucherOrder(voucherId);
    } finally {
        //释放锁
        lock.unlock();
    }
}
```

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785594022929-4b78e69b-63cb-4817-b580-4a51636a83ab.png" width="466" title="" crop="0,0,1,1" id="udf2db948" class="ne-image" style="font-size: 14px">

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785594022998-9e883522-605a-4ff6-9d8b-8eff2b65ddbf.png" width="463" title="" crop="0,0,1,1" id="u42767421" class="ne-image" style="font-size: 14px">

### **Redis分布式锁误删情况**
<font style="color:#333333;">逻辑说明：</font>

<font style="color:#333333;">持有锁的线程在锁的内部出现了阻塞，导致它的锁自动释放。这时其他线程，线程2来尝试获得锁，就拿到了这把锁。然后线程2在持有锁执行过程中，线程1反应过来，继续执行，而线程1执行过程中，走到了删除锁逻辑，此时就会把本应该属于线程2的锁进行删除，这就是误删别人锁的情况说明。</font>

<font style="color:#333333;">解决方案：解决方案就是在每个线程释放锁的时候，去判断一下当前这把锁是否属于自己，如果属于自己，则不进行锁的删除，假设还是上边的情况，线程1卡顿，锁自动释放，线程2进入到锁的内部执行逻辑，此时线程1反应过来，然后删除锁，但是线程1，一看当前这把锁不是属于自己，于是不进行删除锁逻辑，当线程2走到删除锁逻辑时，如果没有卡过自动释放锁的时间点，则判断当前这把锁是属于自己的，于是删除这把锁。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785594023313-c6a3481c-4108-4808-aa2b-83041f24f213.png" width="679" title="" crop="0,0,1,1" id="ubf310042" class="ne-image" style="font-size: 14px">

#### **解决锁误删**
<font style="color:#333333;">需求：修改之前的分布式锁实现，满足：在获取锁时存入线程标示（可以用UUID表示）在释放锁时先获取锁中的线程标示，判断是否与当前线程标示一致。</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">如果一致则释放锁</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">如果不一致则不释放锁</font>

<font style="color:#333333;">核心逻辑：在存入锁时，放入自己线程的标识，在删除锁时，判断当前这把锁的标识是不是自己存入的，如果是，则进行删除，如果不是，则不进行删除。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785594023307-a2ffcbe2-bc00-4b09-a58b-3f13b9d7ee9f.png" width="384" title="" crop="0,0,1,1" id="u41e80050" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">具体代码如下：加锁</font>

```java
// 这里的UUID是hutool包的
private static final String ID_PREFIX = UUID.randomUUID().toString(true) + "-";

@Override
public boolean tryLock(long timeoutSec) {
   // 获取线程标示
   // 多服务集群部署时，不同机器的线程 ID 可能重复，所以不能单加线程Id，还要加UUID
   String threadId = ID_PREFIX + Thread.currentThread().getId();
   // 获取锁
   Boolean success = stringRedisTemplate.opsForValue()
                .setIfAbsent(KEY_PREFIX + name, threadId, timeoutSec, TimeUnit.SECONDS);
   return Boolean.TRUE.equals(success);
}
```

<font style="color:#333333;">释放锁</font>

```java
public void unlock() {
    // 获取线程标示
    String threadId = ID_PREFIX + Thread.currentThread().getId();
    // 获取锁中的标示
    String id = stringRedisTemplate.opsForValue().get(KEY_PREFIX + name);
    // 判断标示是否一致
    if(threadId.equals(id)) {
        // 释放锁
        stringRedisTemplate.delete(KEY_PREFIX + name);
    }
}
```

**<font style="color:#333333;">有关代码实操说明：</font>**

<font style="color:#333333;">在我们修改完此处代码后，我们重启工程，然后启动两个线程，第一个线程持有锁后，手动释放锁，第二个线程 此时进入到锁内部，再放行第一个线程，此时第一个线程由于锁的value值并非是自己，所以不能释放锁，也就无法删除别人的锁，此时第二个线程能够正确释放锁，通过这个案例初步说明我们解决了锁误删的问题。</font>

<font style="color:#333333;"></font>

### **分布式锁的原子性问题**
<font style="color:#333333;">更为极端的误删逻辑说明：</font>

<font style="color:#333333;">线程1现在持有锁之后，在执行业务逻辑过程中，它正准备删除锁，而且已经走到了条件判断的过程中，比如它拿到的当前这把锁确实是属于它自己的，正准备删除锁，线程一卡顿了，但是此时它的锁到期了，那么此时线程2进来，但是线程1会接着往后执行，当它卡顿结束后，它直接就会执行释放锁，把线程2的锁给释放了，相当于条件判断并没有起到作用，这就是删锁时的原子性问题。之所以有这个问题，是因为线程1的拿锁、判断锁、删锁，实际上并不是原子性的，我们要防止刚才的情况发生。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785594048103-6f927116-448d-4253-b99e-962078423852.png" width="760" title="" crop="0,0,1,1" id="u445e334b" class="ne-image" style="font-size: 14px">

#### **Lua脚本解决多条命令原子性问题**
<font style="color:#333333;">Redis提供了Lua脚本功能，在一个脚本中编写多条Redis命令，确保多条命令执行时的原子性。</font>

<font style="color:#333333;">Lua是一种编程语言，它的基本语法大家可以参考网站：https://www.runoob.com/lua/lua-tutorial.html，这里重点介绍Redis提供的调用函数，我们可以使用Lua去操作redis，又能保证它的原子性，这样就可以实现拿锁、比锁、删锁是一个原子性动作了，作为Java程序员这一块并不作一个简单要求，并不需要大家过于精通，只需要知道它有什么作用即可。</font>

<font style="color:#333333;">这里重点介绍Redis提供的调用函数，语法如下：</font>

```lua
redis.call('命令名称', 'key', '其它参数', ...)
```

<font style="color:#333333;"></font>

<font style="color:#333333;">例如，我们要执行set name jack，则脚本是这样：</font>

```lua
# 执行 set name jack
redis.call('set', 'name', 'jack')
```

<font style="color:#333333;">例如，我们要先执行set name Rose，再执行get name，则脚本如下：</font>

```lua
# 先执行 set name jack
redis.call('set', 'name', 'Rose')
# 再执行 get name
local name = redis.call('get', 'name')
# 返回
return name
```

<font style="color:#333333;">写好脚本以后，需要用Redis命令来执行脚本，执行脚本的常见命令如下：</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785594047712-4fce9514-43a0-4bda-a517-25c1bed11758.png" width="559" title="" crop="0,0,1,1" id="uffcd02c6" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">例如，我们要执行 redis.call('set', 'name', 'jack') 这个脚本，语法如下：</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785594047998-04e220cd-7a30-4180-811a-37379d376520.png" width="569" title="" crop="0,0,1,1" id="u6276eafb" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">如果脚本中的key、value不想写死，可以作为参数传递。key类型参数会放入KEYS数组，其它参数会放入ARGV数组，在脚本中可以从KEYS和ARGV数组获取这些参数：</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785594048186-2811a43e-1bec-46b5-bc80-d13ea2b318df.png" width="625" title="" crop="0,0,1,1" id="ud5d922d6" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">接下来我们来回一下我们释放锁的逻辑：</font>

<font style="color:#333333;">释放锁的业务流程是这样的</font>

<font style="color:#333333;">	1、获取锁中的线程标示</font>

<font style="color:#333333;">	2、判断是否与指定的标示（当前线程标示）一致</font>

<font style="color:#333333;">	3、如果一致则释放锁（删除）</font>

<font style="color:#333333;">	4、如果不一致则什么都不做</font>

<font style="color:#333333;">如果用Lua脚本来表示则是这样的：</font>

<font style="color:#333333;">最终我们操作redis的拿锁比锁删锁的lua脚本就会变成这样</font>

```lua
-- 这里的 KEYS[1] 就是锁的key，这里的ARGV[1] 就是当前线程标示threadId
-- 获取锁中的标示，判断是否与当前线程标示一致
if (redis.call('GET', KEYS[1]) == ARGV[1]) then
  -- 一致，则删除锁
  return redis.call('DEL', KEYS[1])
end
-- 不一致，则直接返回
return 0
```

#### **Java代码调用Lua脚本改造分布式锁**
<font style="color:#333333;">Lua脚本本身并不需要大家花费太多时间去研究，只需要知道如何调用，大致是什么意思即可，所以在笔记中并不会详细的去解释这些Lua表达式的含义。</font>

<font style="color:#333333;">我们的RedisTemplate中，可以利用execute方法去执行Lua脚本，参数对应关系就如下图：</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785594048123-2100411e-6b1d-444c-8ddc-3081199f5056.png" width="685" title="" crop="0,0,1,1" id="u5750aa73" class="ne-image" style="font-size: 14px">

**<font style="color:#333333;">Java代码</font>**

```java
private static final DefaultRedisScript<Long> UNLOCK_SCRIPT;
static {
    UNLOCK_SCRIPT = new DefaultRedisScript<>();
    UNLOCK_SCRIPT.setLocation(new ClassPathResource("unlock.lua"));
    UNLOCK_SCRIPT.setResultType(Long.class);
}

public void unlock() {
    // 调用lua脚本
    stringRedisTemplate.execute(
        UNLOCK_SCRIPT,
        Collections.singletonList(KEY_PREFIX + name),
        ID_PREFIX + Thread.currentThread().getId());
}
// 经过以上代码改造后，我们就能够实现 拿锁、比锁、删锁的原子性动作了~
```

+ <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">小总结</font>

<font style="color:#333333;">基于Redis的分布式锁实现思路：</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">利用set nx ex获取锁，并设置过期时间，保存线程标示</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">释放锁时先判断线程标示是否与自己一致，一致则删除锁</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">特性：</font>
        * <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">利用set nx满足互斥性</font>
        * <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">利用set ex保证故障时锁依然能释放，避免死锁，提高安全性</font>
        * <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">利用Redis集群保证高可用和高并发特性</font>

<font style="color:#333333;">笔者总结：我们一路走来，利用添加过期时间，防止死锁问题的发生，但是有了过期时间之后，可能出现误删别人锁的问题，这个问题我们开始是利用删之前 通过拿锁，比锁，删锁这个逻辑来解决的，也就是删之前判断一下当前这把锁是否是属于自己的，但是现在还有原子性问题，也就是我们没法保证拿锁比锁删锁是一个原子性的动作，最后通过lua表达式来解决这个问题</font>

<font style="color:#333333;">但是目前还剩下一个问题：</font>**<font style="color:#333333;">锁不住</font>**<font style="color:#333333;">，即业务代码还没执行完，Redis 里的分布式锁提前过期自动消失了，就会出现一人一单失效问题。</font>

<font style="color:#333333;">提前过期，说明过期时间设置的太短，容易锁不住；但过期时间太长影响性能。</font>

<font style="color:#333333;">所以当过期时间到了之后，我们可以给它续期一下，比如续个30s，是不是后边的问题都不会发生了，那么续期问题怎么解决呢，可以依赖于我们接下来要学习redisson啦。</font>

## **Redisson**
### **功能介绍**
<font style="color:#333333;">基于setnx实现的分布式锁存在下面的问题：</font>

**<font style="color:#333333;">重入问题</font>**<font style="color:#333333;">：同一个线程，拿到锁之后，还能再次获取同一把锁，不会自己把自己卡死，这就叫可重入。</font>

同一个线程持有锁时，再次申请同一把锁会失败，造成死锁，这就叫不可重入。

<font style="color:#333333;">可重入锁的意义在于防止死锁，比如HashTable这样的代码中，它的方法全都是使用synchronized修饰的。假如它在一个方法内，调用另一个方法，那么此时如果是不可重入的，不就死锁了吗？所以可重入锁的主要意义是防止死锁，我们的synchronized和Lock锁都是可重入的。</font>

```java
// Hashtable 所有方法都加了 synchronized
// 方法1
public synchronized void put() {
    // 内部调用get方法
    get(key);
}
// 方法2
public synchronized Object get() {
}
// 线程执行 put，获取对象锁；put 内部调用 get，又要申请同一把锁；
// 如果锁不可重入：线程自己持有锁，还在等锁释放，
// 但锁无法释放因为方法一还没执行完，方法一还在调用方法二，于是出现死锁。
```

**<font style="color:#333333;">不可重试</font>**<font style="color:#333333;">：是指目前的分布式只能尝试一次，我们认为合理的情况是：当线程在获得锁失败后，它应该能再次尝试获得锁。</font>

**<font style="color:#333333;">超时释放：</font>**<font style="color:#333333;">我们在加锁时增加了过期时间，这样的我们可以防止死锁，但是如果卡顿的时间超长，虽然我们采用了lua表达式防止删锁的时候，误删别人的锁，但是毕竟没有锁住，有安全隐患。</font>

**<font style="color:#333333;">主从一致性：</font>**<font style="color:#333333;"> 如果Redis提供了主从集群，当我们向集群写数据时，主机需要异步的将数据同步给从机，而万一在同步过去之前，主机宕机了，从机没有锁，其他线程又获取锁，那就多个线程获取锁。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785594080664-50a16209-6c02-4ef1-bb27-97654fd7b914.png" width="727" title="" crop="0,0,1,1" id="u1a172e4f" class="ne-image" style="font-size: 14px">

+ <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">Redisson</font>

<font style="color:#333333;">Redisson是一个在Redis的基础上实现的Java驻内存数据网格（In-Memory Data Grid）。它不仅提供了一系列的分布式的Java常用对象，还提供了许多分布式服务，其中就包含了各种分布式锁的实现。</font>

<font style="color:#333333;">Redisson提供了分布式锁的多种多样的功能</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785594080435-2ac48c85-f631-4e11-b9ed-6c55042d9742.png" width="469" title="" crop="0,0,1,1" id="u9313d513" class="ne-image" style="font-size: 14px">

### **Redisson快速入门**
<font style="color:#333333;">引入依赖：</font>

```xml
<dependency>
  <groupId>org.redisson</groupId>
  <artifactId>redisson</artifactId>
  <version>3.13.6</version>
</dependency>
<!--不推荐使用下面依赖，与springboot整合实现-->
<dependency>
  <groupId>org.redisson</groupId>
  <artifactId>redisson-spring-boot-starter</artifactId>
  <version>3.23.5</version>
</dependency>
```

<font style="color:#333333;">配置Redisson客户端：</font>

```java
@Configuration
public class RedissonConfig {
    
    @Bean
    public RedissonClient redissonClient(){
        // 配置
        Config config = new Config();
        // 也可以使用config.useclusterServers()添加集群地址
        config.useSingleServer().setAddress("redis://192.168.88.130:6379")
        .setPassword("1234");
        // 创建RedissonClient对象
        return Redisson.create(config);
    }
}

```

<font style="color:#333333;">如何使用Redisson的分布式锁</font>

```java
@Resource
private RedissonClient redissonClient;

@Test
void testRedisson() throws Exception{
    //获取锁(可重入)，指定锁的名称
    RLock lock = redissonClient.getLock("anyLock");
    //尝试获取锁，参数分别是：获取锁的最大等待时间(期间会重试)，锁自动释放时间，时间单位
    boolean isLock = lock.tryLock(1,10,TimeUnit.SECONDS);
    //判断获取锁成功
    if(isLock){
        try{
            System.out.println("执行业务");          
        }finally{
            //释放锁
            lock.unlock();
        }
        
    }
}
```

+ <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">VoucherOrderServiceImpl 类</font>

<font style="color:#333333;">注入RedissonClient，修改秒杀代码</font>

```java
@Resource
private RedissonClient redissonClient;

@Override
public Result seckillVoucher(Long voucherId) {
    // 1.查询优惠券
    SeckillVoucher voucher = seckillVoucherService.getById(voucherId);
    // 2.判断秒杀是否开始
    if (voucher.getBeginTime().isAfter(LocalDateTime.now())) {
        // 尚未开始
        return Result.fail("秒杀尚未开始！");
    }
    // 3.判断秒杀是否已经结束
    if (voucher.getEndTime().isBefore(LocalDateTime.now())) {
        // 尚未开始
        return Result.fail("秒杀已经结束！");
    }
    // 4.判断库存是否充足
    if (voucher.getStock() < 1) {
        // 库存不足
        return Result.fail("库存不足！");
    }
    Long userId = UserHolder.getUser().getId();
    //创建锁对象 这个代码不用了，因为我们现在要使用分布式锁
    //SimpleRedisLock lock = new SimpleRedisLock("order:" + userId, stringRedisTemplate);
    RLock lock = redissonClient.getLock("lock:order:" + userId);
    //获取锁对象，无参即为不等待立刻返回
    boolean isLock = lock.tryLock();
   
    //加锁失败
    if (!isLock) {
        return Result.fail("不允许重复下单");
    }
    try {
        //获取代理对象(事务)
        IVoucherOrderService proxy = (IVoucherOrderService) AopContext.currentProxy();
        return proxy.createVoucherOrder(voucherId);
    } finally {
        //释放锁
        lock.unlock();
    }
 }
```

### **可重入锁原理**
<font style="color:#333333;">在 Lock、synchronized 锁中，它是借助于底层的一个 volatile 的一个state变量来记录重入的状态的，比如当前没有人持有这把锁，那么state=0，假如有人持有这把锁，那么 state=1。如果持有这把锁的人再次持有这把锁，那么state就会+1 ，如果是对于synchronized 而言，它在C语言代码中会有一个 count，原理和state类似，也是重入一次就加一，释放一次就-1 ，直到减少成0 时，表示当前这把锁没有被人持有。 </font>

<font style="color:#333333;">在 redisson 中，采用 hash 结构用来存储锁，其中大key表示这把锁是否存在，用小key表示当前这把锁被哪个线程持有。</font>

```java
大key（KEYS[1]）：lock:voucher:1  锁的全局名称
Hash内部：
    field（ARGV[2]）：UUID-线程ID（唯一标识当前哪个服务哪个线程持有）
    value：数字（可重入计数器，对应本地锁的state）

示例：
lock:voucher:1 = {
    "9f2d3478xxx-15" : 2
}
含义：这把锁被机器 UUID、线程 15 持有，重入了 2 次
```

+ <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">获取锁</font>

```lua
-- KEYS[1]：锁的大key，全局唯一锁标识，示例：lock:seckill:1
local key = KEYS[1];
-- ARGV[1]：线程全局唯一标识 = UUID + ":" + 当前线程ID，用来区分锁持有者，防止跨机器/线程误删锁
local threadId = ARGV[1];
-- ARGV[2]：锁过期时间，单位 秒，用来兜底防止服务宕机造成死锁
local releaseTime = ARGV[2];

-- 分支一：判断锁key是否不存在（当前无任何线程持有这把锁）
if(redis.call('exists', key) == 0) then
  -- hset 创建Hash结构：大key=锁名，field=线程标识，value=重入次数，首次加锁次数为1
  redis.call('hset', key, threadId, '1');
  -- 给整个Hash锁设置过期时间，兜底死锁
  redis.call('expire', key, releaseTime);
  -- 返回1：加锁成功
  return 1;
end;

-- 分支二：锁已经存在，判断当前持有锁的线程是不是自己
-- hexists：判断Hash内是否存在当前线程标识的field，存在代表锁是本线程持有
if(redis.call('hexists', key, threadId) == 1) then
  -- hincrby：对应本地锁state+1，重入次数自增1，实现可重入
  redis.call('hincrby', key, threadId, '1');
  -- 每次重入都刷新锁过期时间，等效看门狗续期，避免业务没执行完锁提前过期（锁不住）
  redis.call('expire', key, releaseTime);
  -- 返回1：重入加锁成功
  return 1;
end;

-- 分支三：前两个条件都不满足，说明锁存在且持有者不是当前线程，抢锁失败
-- 返回0：获取锁失败，客户端会自旋循环重试抢锁
return 0;
```

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">释放锁</font>

```lua
-- 接收外部传入参数，定义本地变量
-- KEYS[1]：锁的全局大key，例如 lock:voucher:1
local key = KEYS[1];
-- ARGV[1]：线程全局唯一标识（机器UUID-线程ID），用来校验锁持有者
local threadId = ARGV[1];
-- ARGV[2]：锁的过期时长（单位：秒），重入未完全释放时刷新有效期
local releaseTime = ARGV[2];

-- 分支1：校验当前锁是否属于本线程
-- HEXISTS：判断Hash中是否存在当前线程标识的field
if (redis.call('HEXISTS', key, threadId) == 0) then
    -- 不存在该线程记录，说明锁不是自己持有的，禁止解锁，直接返回nil
    return nil;
end;

-- 分支2：锁属于当前线程，重入计数器自减1（对应加锁时+1，实现可重入配对释放）
local count = redis.call('HINCRBY', key, threadId, -1);

-- 分支3：判断减完后的重入次数
if (count > 0) then
    -- 计数器大于0：说明还有嵌套的锁未释放，只刷新过期时间，不删除锁
    redis.call('EXPIRE', key, releaseTime);
    return nil;
else
    -- 计数器等于0：所有嵌套层级全部释放，直接删除整个锁Hash
    redis.call('DEL', key);
    return nil;
end;
```

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785594080991-4799af8c-1df4-4699-a06c-83146b8ad5b0.png" width="1655" title="" crop="0,0,1,1" id="ude72d59f" class="ne-image" style="font-size: 14px">



### **redisson锁重试和WatchDog机制**
```java
RLock lock = redissonClient.getLock("lock:order:" + userId);
// 获取锁
lock.tryLock();
// 无参：
// 无等待时间：抢锁一次，抢到返回true，抢不到直接返回false，不会自旋阻塞
// 无自定义过期时间：锁默认有效期 30 秒
// 内置看门狗（WatchDog）自动续期
// 加锁成功后，启动看门狗后台定时线程：
// 每 30/3=10秒 执行一次续期 Lua 脚本
// 只要线程没释放锁、程序没宕机，持续把锁过期时间刷新回 30 秒，解决「锁不住」
// 加锁失败，直接返回false，不会循环重试

tryLock(10, TimeUnit.SECONDS);
// 带等待时间：
// waitTime：自旋等待最长时间，期间会反复尝试抢锁，超过10秒仍没拿到返回 false
// 无自定义过期时间：锁默认有效期 30 秒
// 仍保留看门狗自动续期

tryLock(10,15,TimeUnit.SECONDS);
// 完整三参：
// 传入自定义过期时间15秒，看门狗机制直接失效，不会自动续期
```

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785594101246-c37c5599-a8e8-4dfa-9f1c-4e81a77fc6ee.png" width="1886" title="" crop="0,0,1,1" id="uc858d449" class="ne-image" style="font-size: 14px">

**<font style="color:#333333;">说明</font>**<font style="color:#333333;">：由于课程中已经说明了有关tryLock的源码解析以及其看门狗原理，所以笔者在这里给大家分析lock()方法的源码解析，希望大家在学习过程中，能够掌握更多的知识</font>

<font style="color:#333333;">抢锁过程中，获得当前线程，通过tryAcquire进行抢锁，该抢锁逻辑和之前逻辑相同</font>

<font style="color:#333333;">1、先判断当前这把锁是否存在，如果不存在，插入一把锁，返回null</font>

<font style="color:#333333;">2、判断当前这把锁是否是属于当前线程，如果是，则返回null</font>

<font style="color:#333333;">所以如果返回是null，则代表着当前这哥们已经抢锁完毕，或者可重入完毕，但是如果以上两个条件都不满足，则进入到第三个条件，返回的是锁的失效时间，同学们可以自行往下翻一点点，你能发现有个while( true) 再次进行tryAcquire进行抢锁</font>

```java
long threadId = Thread.currentThread().getId();
Long ttl = tryAcquire(-1, leaseTime, unit, threadId);
// lock acquired
if (ttl == null) {
    return;
}
```

<font style="color:#333333;">接下来会有一个条件分支，因为lock方法有重载方法，一个是带参数，一个是不带参数，如果带带参数传入的值是-1，如果传入参数，则 leaseTime是他本身，所以如果传入了参数，此时leaseTime != -1 则会进去抢锁，抢锁的逻辑就是之前说的那三个逻辑</font>

```java
if (leaseTime != -1) {
    return tryLockInnerAsync(waitTime, leaseTime, unit, threadId, RedisCommands.EVAL_LONG);
}
```

<font style="color:#333333;">如果是没有传入时间，则此时也会进行抢锁， 而且抢锁时间是默认看门狗时间 commandExecutor.getConnectionManager().getCfg().getLockWatchdogTimeout()</font>

<font style="color:#333333;">ttlRemainingFuture.onComplete((ttlRemaining, e) 这句话相当于对以上抢锁进行了监听，也就是说当上边抢锁完毕后，此方法会被调用，具体调用的逻辑就是去后台开启一个线程，进行续约逻辑，也就是看门狗线程</font>

```java
RFuture<Long> ttlRemainingFuture = tryLockInnerAsync(waitTime,
                    commandExecutor.getConnectionManager().getCfg().getLockWatchdogTimeout(),
                    TimeUnit.MILLISECONDS, threadId, RedisCommands.EVAL_LONG);
ttlRemainingFuture.onComplete((ttlRemaining, e) -> {
    if (e != null) {
        return;
    }

    // lock acquired
    if (ttlRemaining == null) {
        scheduleExpirationRenewal(threadId);
    }
});
return ttlRemainingFuture;
```

<font style="color:#333333;">此逻辑就是续约逻辑，注意看commandExecutor.getConnectionManager().newTimeout（） 此方法</font>

<font style="color:#333333;">Method( </font>**<font style="color:#333333;">new</font>**<font style="color:#333333;"> TimerTask() {},参数2 ，参数3 )</font>

<font style="color:#333333;">指的是：通过参数2，参数3 去描述什么时候去做参数1的事情，现在的情况是：10s之后去做参数一的事情</font>

<font style="color:#333333;">因为锁的失效时间是30s，当10s之后，此时这个timeTask 就触发了，他就去进行续约，把当前这把锁续约成30s，如果操作成功，那么此时就会递归调用自己，再重新设置一个timeTask()，于是再过10s后又再设置一个timerTask，完成不停的续约</font>

<font style="color:#333333;">那么大家可以想一想，假设我们的线程出现了宕机他还会续约吗？当然不会，因为没有人再去调用renewExpiration这个方法，所以等到时间之后自然就释放了。</font>

```java
private void renewExpiration() {
    ExpirationEntry ee = EXPIRATION_RENEWAL_MAP.get(getEntryName());
    if (ee == null) {
        return;
    }
    
    Timeout task = commandExecutor.getConnectionManager().newTimeout(new TimerTask() {
        @Override
        public void run(Timeout timeout) throws Exception {
            ExpirationEntry ent = EXPIRATION_RENEWAL_MAP.get(getEntryName());
            if (ent == null) {
                return;
            }
            Long threadId = ent.getFirstThreadId();
            if (threadId == null) {
                return;
            }
            
            RFuture<Boolean> future = renewExpirationAsync(threadId);
            future.onComplete((res, e) -> {
                if (e != null) {
                    log.error("Can't update lock " + getName() + " expiration", e);
                    return;
                }
                
                if (res) {
                    // reschedule itself
                    renewExpiration();
                }
            });
        }
    }, internalLockLeaseTime / 3, TimeUnit.MILLISECONDS);
    
    ee.setTimeout(task);
}
```

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">总结</font>

Redisson分布式锁原理：

可重入：利用hash结构记录线程id和重入次数

可重试：利用信号量和PubSub功能实现等待、唤醒，获取锁失败的重试机制

超时续约：利用watchDog，每隔一段时间（releaseTime/3)，重置超时时间

### **MutiLock原理**
<font style="color:#333333;">为了提高 redis 的可用性，我们会搭建集群或者主从，现在以主从为例。</font>

<font style="color:#333333;">此时我们去写命令，写在主机上， 主机会将数据同步给从机，但是假设在主机还没有来得及把数据写入到从机去的时候，此时主机宕机，哨兵会发现主机宕机，并且选举一个slave变成master，而此时新的master中实际上并没有锁信息，此时锁信息就已经丢掉了。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785594100830-fe8e07f3-acee-4811-b4d4-bb1bb386bc0d.png" width="663" title="" crop="0,0,1,1" id="uff6ad1dc" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">为了解决这个问题，redisson提出来了MutiLock锁（联锁），使用这把锁就不使用主从了，每个节点的地位都是一样的， 这把锁加锁的逻辑需要写入到每一个主从节点上，</font>**<font style="color:#333333;">只有所有的服务器都写入成功，此时才是加锁成功</font>**<font style="color:#333333;">。假设现在某个节点挂了，那么它去获得锁的时候，只要有一个节点拿不到，都不能算是加锁成功，就保证了加锁的可靠性。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785594101094-cbc44da7-afe4-4b5d-8a51-c34c859eaa52.png" width="654" title="" crop="0,0,1,1" id="u74d0cd32" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">那么MutiLock 加锁原理是什么呢？</font>

<font style="color:#333333;">当我们去设置了多个锁时，redisson会将多个锁添加到一个集合中，然后用while循环去不停去尝试拿锁，但是会有一个总共的加锁时间，这个时间是用 需要加锁的个数 * 1500ms ，假设有3个锁，那么时间就是4500ms，假设在这4500ms内，所有的子锁都加锁成功， 那么此时才算是加锁成功。如果在4500ms有任意一把子锁加锁失败，已拿到的全部子锁立刻释放，重新重试。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785594100661-76a7e2f2-4dbf-4866-844c-9ddbbd0dad2d.png" width="1411" title="" crop="0,0,1,1" id="u8522dc19" class="ne-image" style="font-size: 14px">

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">总结</font>
1. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">不可重入Redis分布式锁</font>

原理：利用setnx的互斥性；利用ex避免死锁；释放锁时判断线程标示

缺陷：不可重入、无法重试、锁超时失效

2. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">可重入的Redis分布式锁</font>

原理：利用hash结构，记录线程标示和重入次数；利用 watchDog延续锁时间；利用信号量控制锁重试等待

缺陷：redis宕机引起锁失效问题

3. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">RedissonmultiLock</font>

原理：多个独立的Redis节点，必须在所有节点都获取重入锁，才算获取锁成功

缺陷：运维成本高、实现复杂

## **秒杀优化**
### **异步秒杀思路**
<font style="color:#333333;">我们来回顾一下下单流程：</font>

<font style="color:#333333;">1、查询优惠卷（查DB）</font>

<font style="color:#333333;">2、判断秒杀库存是否足够（查DB）</font>

<font style="color:#333333;">3、查询订单（查DB）</font>

<font style="color:#333333;">4、校验是否是一人一单（查DB）</font>

<font style="color:#333333;">5、扣减库存（改DB）</font>

<font style="color:#333333;">6、创建订单（插入DB）</font>

串行执行逻辑：必须等第 1 步数据库查询完成，才能走第 2 步；第 2 步结束才能走第 3 步……

异步并行逻辑：不同线程同时执行，不用排队等待。

<font style="color:#333333;">在这六步操作中，很多操作是要去操作数据库的，而且还是一个线程串行执行， 这样就会导致我们的程序执行的很慢，所以我们需要异步程序执行，那么如何加速呢？</font>

<font style="color:#333333;">有没有小伙伴这么想，比如，我们可以不可以使用异步编排来做，或者说我开启N多个线程，N多个线程，一个线程执行查询优惠卷，一个线程执行判断扣减库存，一个去创建订单等等，然后再统一做返回。这种做法和课程中有哪种好呢？</font>

<font style="color:#333333;">答案是课程中的好，因为如果你采用我刚说的方式，如果访问的人很多，每一条请求都要拆分多个异步任务，那么线程池中的线程可能一下子就被消耗完了，系统雪崩。上述方案的特点是 必须同步走完所有流程，立刻给用户最终结果。</font>

<font style="color:#333333;">但只需要快速告诉用户 “下单成功”，复杂写库逻辑后台慢慢异步消化。因此高并发秒杀场景，优先使用消息队列异步，而非本地线程异步。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785594101090-2823f26b-2012-4a44-8b18-1ccc8d412231.png" width="717" title="" crop="0,0,1,1" id="u38b9caf4" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">优化方案：我们将快DB的校验逻辑放入到redis中，比如是否库存足够，比如是否一人一单。我们只需要进行快速的逻辑判断，根本就不用等下单逻辑走完，我们直接给用户返回成功， 再在后台开一个线程，把 建订单、扣数据库库存等慢 DB 操作交给消息队列，后台线程慢慢的去执行queue里边的消息，这样程序不就超级快了吗？而且也不用担心线程池消耗殆尽的问题，因为这里我们的程序中并没有手动使用任何线程池。</font>

<font style="color:#333333;">当然这里边有两个难点：</font>

<font style="color:#333333;">第一个难点是我们怎么在redis中去快速校验一人一单，还有库存判断。</font>

<font style="color:#333333;">第二个难点是由于我们校验和tomcat下单是两个线程，那么我们如何知道到底哪个单下单完成，为了完成这件事我们在redis操作完之后，我们会将一些信息返回给前端，同时也会把这些信息丢到异步queue中去，后续操作中，可以通过这个id来查询我们tomcat中的下单逻辑是否完成了。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785594102007-4933cc2f-8aeb-42f8-a236-df428dce5804.png" width="752" title="" crop="0,0,1,1" id="u9e83d597" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">我们现在来看看整体思路：当用户下单之后，判断库存是否充足只需要到 redis中去根据key找对应的value是否大于0即可，如果不充足，则直接结束；如果充足，继续在redis中判断用户是否重复下单，如果 set 集合中没有这条数据，说明可以下单，再将userId和优惠卷存入到 redis 的 set 集合中，并且返回0，整个过程需要保证是原子性的，我们可以使用 Lua 来操作。</font>

<font style="color:#333333;">当以上判断逻辑走完之后，我们可以判断当前redis中返回的结果是否是0 ，如果是0，则表示可以下单，则将之前说的信息存入到到queue中去，然后返回，然后再来个线程异步的下单，前端可以通过返回的订单id来判断是否下单成功。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785594102208-ce3d4ca7-b3c7-4593-acd9-660ce4ce37a4.png" width="1606" title="" crop="0,0,1,1" id="ua74c5120" class="ne-image" style="font-size: 14px">



### **Redis完成秒杀资格判断**
<font style="color:#333333;">需求：</font>

1. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">新增秒杀优惠券的同时，将优惠券信息保存到Redis中</font>
2. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">基于Lua脚本，判断秒杀库存、一人一单，决定用户是否抢购成功</font>
3. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">如果抢购成功，将优惠券 id 和用户 id 封装后存入阻塞队列</font>
4. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">开启线程任务，不断从阻塞队列中获取信息，实现异步下单功能</font>
+ <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">VoucherServiceImpl 类</font>

完成第一步

```java
@Override
@Transactional
public void addSeckillVoucher(Voucher voucher) {
    // 保存优惠券
    save(voucher);
    // 保存秒杀信息
    SeckillVoucher seckillVoucher = new SeckillVoucher();
    seckillVoucher.setVoucherId(voucher.getId());
    seckillVoucher.setStock(voucher.getStock());
    seckillVoucher.setBeginTime(voucher.getBeginTime());
    seckillVoucher.setEndTime(voucher.getEndTime());
    seckillVoucherService.save(seckillVoucher);
    // 保存秒杀库存到Redis中（新增代码）
    //private static final String SECKILL_STOCK_KEY ="seckill:stock:"
    stringRedisTemplate.opsForValue()
            .set(SECKILL_STOCK_KEY + voucher.getId(), voucher.getStock().toString());
}
```

<font style="color:#333333;">完整Lua表达式</font>

<font style="color:#333333;">完成第二步</font>

```lua
-- 1.参数列表
-- 1.1.优惠券id
local voucherId = ARGV[1]
-- 1.2.用户id
local userId = ARGV[2]
-- 1.3.订单id
local orderId = ARGV[3]

-- 2.数据key
-- 2.1.库存key
local stockKey = 'seckill:stock:' .. voucherId
-- 2.2.订单key
local orderKey = 'seckill:order:' .. voucherId

-- 3.脚本业务
-- 3.1.判断库存是否充足 get stockKey
if(tonumber(redis.call('get', stockKey)) <= 0) then
  -- 3.2.库存不足，返回1
  return 1
end
-- 3.2.判断用户是否下单 SISMEMBER orderKey userId
if(redis.call('sismember', orderKey, userId) == 1) then
  -- 3.3.存在，说明是重复下单，返回2
  return 2
end
-- 3.4.扣库存 incrby stockKey -1
redis.call('incrby', stockKey, -1)
-- 3.5.下单（保存用户）sadd orderKey userId
redis.call('sadd', orderKey, userId)
-- 3.6.发送消息到队列中， XADD stream.orders * k1 v1 k2 v2 ...
redis.call('xadd', 'stream.orders', '*', 'userId', userId, 'voucherId', voucherId, 'id', orderId)
return 0
```

+ <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">VoucherOrderServiceImpl 类</font>

继续第二步

```java
// 静态代码块：类加载时仅初始化一次
static {
    // 实例化脚本对象
    SECKILL_SCRIPT = new DefaultRedisScript<>();
    // 指定lua脚本文件路径：resources根目录下的seckill.lua
    SECKILL_SCRIPT.setLocation(new ClassPathResource("seckill.lua"));
    // 指定脚本执行后的返回值类型为Long
    SECKILL_SCRIPT.setResultType(Long.class);
}

@Override
public Result seckillVoucher(Long voucherId) {
//获取用户
Long userId = UserHolder.getUser().getId();
long orderId = redisIdWorker.nextId("order");
// 1.执行lua脚本
Long result = stringRedisTemplate.execute(
    SECKILL_SCRIPT,
    Collections.emptyList(),
    voucherId.toString(), userId.toString(), String.valueOf(orderId)
);
int r = result.intValue();
// 2.判断结果是否为0
if (r != 0) {
    // 2.1.不为0 ，代表没有购买资格
    return Result.fail(r == 1 ? "库存不足" : "不能重复下单");
}
//TODO 保存阻塞队列
// 3.返回订单id
return Result.ok(orderId);
}
```

### **完成阻塞队列**
+ <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">VoucherOrderServiceImpl 类</font>

<font style="color:#333333;">判断结果如果是0，则把下单的逻辑保存到队列中去，然后异步执行</font>

```java
//异步处理线程池
private static final ExecutorService SECKILL_ORDER_EXECUTOR = Executors.newSingleThreadExecutor();
IVoucherOrderService proxy;
//在类初始化后立刻执行init()方法，提前创建消费线程
//单线程池直接启动一条后台死循环线程，卡在 orderTasks.take() 阻塞等待订单
@PostConstruct
private void init() {
   SECKILL_ORDER_EXECUTOR.submit(new VoucherOrderHandler());
}
// 用于线程池处理的任务
// 当初始化完毕后，就会去从队列中去拿信息
private class VoucherOrderHandler implements Runnable{
    @Override
    public void run() {
        while (true){
            try {
                // 1.获取队列中的订单信息
                VoucherOrder voucherOrder = orderTasks.take();
                // 2.创建订单
                handleVoucherOrder(voucherOrder);
            } catch (Exception e) {
                log.error("处理订单异常", e);
            }
         }
    }
     
    private void handleVoucherOrder(VoucherOrder voucherOrder) {
        //1.获取用户
        Long userId = voucherOrder.getUserId();
        // 2.创建锁对象
        RLock redisLock = redissonClient.getLock("lock:order:" + userId);
        // 3.尝试获取锁
        boolean isLock = redisLock.lock();
        // 4.判断是否获得锁成功
        if (!isLock) {
            // 获取锁失败，直接返回失败或者重试
            log.error("不允许重复下单！");
            return;
        }
        try {
            //注意：由于是spring的事务是放在threadLocal主线程中，但现在是子线程，事务会失效
            //直接放订单而不是放订单id
            proxy.createVoucherOrder(voucherOrder);
        } finally {
            // 释放锁
            redisLock.unlock();
        }
    }
}

//阻塞队列，队列容量为1024*1024，最多缓存 104 万条订单任务
private BlockingQueue<VoucherOrder> orderTasks = new ArrayBlockingQueue<>(1024 * 1024);

@Override
public Result seckillVoucher(Long voucherId) {
    Long userId = UserHolder.getUser().getId();
    long orderId = redisIdWorker.nextId("order");
    // 1.执行lua脚本
    Long result = stringRedisTemplate.execute(
            SECKILL_SCRIPT,
            Collections.emptyList(),
            voucherId.toString(), userId.toString(), String.valueOf(orderId)
    );
    int r = result.intValue();
    // 2.判断结果是否为0
    if (r != 0) {
        // 2.1.不为0 ，代表没有购买资格
        return Result.fail(r == 1 ? "库存不足" : "不能重复下单");
    }
    VoucherOrder voucherOrder = new VoucherOrder();
    // 2.3.订单id（新增代码）
    long orderId = redisIdWorker.nextId("order");
    voucherOrder.setId(orderId);
    // 2.4.用户id
    voucherOrder.setUserId(userId);
    // 2.5.代金券id
    voucherOrder.setVoucherId(voucherId);
    // 2.6.放入阻塞队列
    orderTasks.add(voucherOrder);
    //3.帮子线程获取代理对象
    proxy = (IVoucherOrderService)AopContext.currentProxy();
    //4.返回订单id
    return Result.ok(orderId);
}
 
@Transactional
public void createVoucherOrder(VoucherOrder voucherOrder) {
    //子线程执行该方法，所以不能用UserHolder获取用户id
    //获取订单id同理
    Long userId = voucherOrder.getUserId();
    // 5.1.查询订单
    int count = query().eq("user_id", userId)
            .eq("voucher_id", voucherOrder.getVoucherId()).count();
    // 5.2.判断是否存在
    if (count > 0) {
        // 用户已经购买过了
       log.error("用户已经购买过了");
       return ;
    }

    // 6.扣减库存
    boolean success = seckillVoucherService.update()
            .setSql("stock = stock - 1") // set stock = stock - 1
            .eq("voucher_id", voucherOrder.getVoucherId()).gt("stock", 0)
            .update();
    if (!success) {
        // 扣减失败
        log.error("库存不足");
        return ;
    }
    save(voucherOrder);
}
```

**<font style="color:#333333;">小总结：</font>**

<font style="color:#333333;">秒杀业务的优化思路是什么？</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">先利用Redis完成库存余量、一人一单判断，完成抢单业务</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">再将下单业务放入阻塞队列，利用独立线程异步下单</font>

现在的 jvm的阻塞队列的异步秒杀存在哪些问题？

    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">jvm 内存限制问题</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">数据安全问题</font>

<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);"></font>

## **消息队列**
### **介绍**
<font style="color:#333333;">什么是消息队列：字面意思就是存放消息的队列。最简单的消息队列模型包括3个角色：</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">消息队列：存储和管理消息，也被称为消息代理（Message Broker）</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">生产者：发送消息到消息队列</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">消费者：从消息队列获取消息并处理消息</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785595419819-075c31c5-f28e-4e95-948f-f6329c73cb88.png" width="661" title="" crop="0,0,1,1" id="u56734b90" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">使用队列的好处在于 </font>**<font style="color:#333333;">解耦：</font>**<font style="color:#333333;">所谓解耦，举一个生活中的例子就是：快递员(生产者)把快递放到快递柜里边(Message Queue)去，我们(消费者)从快递柜里边去拿东西，这就是一个异步，如果耦合，那么这个快递员相当于直接把快递交给你，这事固然好，但是万一你不在家，那么快递员就会一直等你，这就浪费了快递员的时间，所以这种思想在我们日常开发中，是非常有必要的。</font>

<font style="color:#333333;">这种场景在我们秒杀中就变成了：我们下单之后，利用redis去进行校验下单条件，再通过队列把消息发送出去，然后再启动一个线程去消费这个消息，完成解耦，同时也加快我们的响应速度。</font>

<font style="color:#333333;">这里我们可以使用一些现成的mq，比如kafka，rabbitmq等等，但是呢，如果没有安装mq，我们也可以直接使用redis提供的mq方案，降低我们的部署和学习成本。</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">Redis提供了三种不同的方式来实现消息队列</font>

List结构：基于List结构模拟消息队列

PubSub：基本的点对点消息模型

Stream：比较完善的消息队列模型

### **基于List实现消息队列**
<font style="color:#333333;">消息队列（Message Queue），字面意思就是存放消息的队列。而Redis的list数据结构是一个双向链表，很容易模拟出队列效果。</font>

<font style="color:#333333;">队列是入口和出口不在一边，因此我们可以利用：LPUSH 结合 RPOP、或者 RPUSH 结合 LPOP来实现。不过要注意的是，当队列中没有消息时RPOP或LPOP操作会返回null，并不像JVM的阻塞队列那样会阻塞并等待消息。</font>

<font style="color:#333333;">因此这里应该使用BRPOP或者BLPOP来实现阻塞效果。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785595418550-57852fa5-89fa-4e45-a358-43a62b34df30.png" width="1332" title="" crop="0,0,1,1" id="u1b75ff3d" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">基于List的消息队列有哪些优缺点？</font>

<font style="color:#333333;">优点：</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">利用Redis存储，不受限于JVM内存上限</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">基于Redis的持久化机制，数据安全性有保证</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">可以满足消息有序性</font>

<font style="color:#333333;">缺点：</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">无法避免消息丢失：从队列里取出消息，还没来得及处理就宕机了，队列中已经没有这条消息了，就丢失了。</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">只支持单消费者：一个消费者拿完消息，队列就没有这条消息了，其他消费者拿不到。</font>

### **基于PubSub的消息队列**
<font style="color:#333333;">PubSub（发布订阅）是Redis2.0版本引入的消息传递模型。顾名思义，消费者可以订阅一个或多个channel，生产者向对应channel发送消息后，所有订阅者都能收到相关消息。</font>

<font style="color:#333333;">SUBSCRIBE channel [channel] ：订阅一个或多个频道</font>

<font style="color:#333333;">PUBLISH channel msg ：向一个频道发送消息</font>

<font style="color:#333333;">PSUBSCRIBE pattern [pattern] ：订阅与 pattern 格式匹配的所有频道，填通配符</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785595418558-c681bb3d-01ee-4f5b-96aa-df0a8e16a74d.png" width="1377" title="" crop="0,0,1,1" id="ub0281988" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">基于PubSub的消息队列有哪些优缺点？</font>

<font style="color:#333333;">优点：</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">采用发布订阅模型，支持多生产、多消费</font>

<font style="color:#333333;">缺点：</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">不支持数据持久化</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">无法避免消息丢失</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">消费者的消息堆积有上限，超出时数据丢失</font>

### **基于Stream的消息队列**
<font style="color:#333333;">Stream 是 Redis 5.0 引入的一种新</font>**<font style="color:#333333;">数据类型</font>**<font style="color:#333333;">，可以实现一个功能非常完善的消息队列。</font>

<font style="color:#333333;">发送消息的命令：</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785595420641-18c929e3-7ecf-48b0-bb58-f09e34e1b642.png" width="1507" title="" crop="0,0,1,1" id="uf4d74355" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">例如：</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785595420014-e14d291f-5d19-47d6-a55b-670d2d1e475c.png" width="745" title="" crop="0,0,1,1" id="u753410c0" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">读取消息的方式之一：XREAD</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785595420985-b4900106-064f-4077-9f16-b2a488fec4f7.png" width="696" title="" crop="0,0,1,1" id="u322f284c" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">例如，使用XREAD读取第一个消息：</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785595420229-5f83e8a2-3df4-4b5b-bdba-f81ffe24fff9.png" width="613" title="" crop="0,0,1,1" id="u90a18f8e" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">XREAD阻塞方式，读取最新的消息：</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785595420589-1b7b2675-85d7-4694-8bbc-7ae6348e9dfb.png" width="687" title="" crop="0,0,1,1" id="u9b341233" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">在业务开发中，我们可以循环的调用XREAD阻塞方式来查询最新消息，从而实现持续监听队列的效果，伪代码如下</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785595421057-ece69aa4-200b-40a3-9f53-f056b3aecec0.png" width="683" title="" crop="0,0,1,1" id="u93ab6e68" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">注意：当我们指定起始ID为 $ 时，代表读取最新的消息，如果我们处理一条消息的过程中，又有超过1条以上的消息到达队列，则下次获取时也只能获取到最新的一条，会出现漏读消息的问题。</font>

<font style="color:#333333;">STREAM类型消息队列的XREAD命令特点：</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">消息可回溯</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">一个消息可以被多个消费者读取</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">可以阻塞读取</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">有消息漏读的风险</font>

<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);"></font>

#### **消费者组**
<font style="color:#333333;">消费者组（Consumer Group）：将多个消费者划分到一个组中，监听同一个队列。具备下列特点：</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785595437793-be89167b-29fd-4a00-a66e-955cf5d98955.png" width="664" title="" crop="0,0,1,1" id="u0f4b1a3f" class="ne-image" style="font-size: 14px">

| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">维度</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);"> 消息标示（全局 offset 位点）</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);"> 消息确认（pending-list + XACK）</font> |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">归属对象</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">整个消费组（组共用）</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">单个消费者（每个消费者独立）</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">核心用途</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">获取从</font>**<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">未分配过的全新消息</font>**<br/><font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">，断点续读防漏新消息</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">管理</font>**<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">已分配但未处理完</font>**<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">的滞留消息，失败消息重试</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">读取参数对应</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">XREADGROUP ... ></font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">XREADGROUP ... 0</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">数据是否自动清除</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">只会向前递增，不会删除历史记录</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">XACK 执行后，消息立刻从 pending 列表移除</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">宕机影响</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">重启后从位点后继续读，不会漏新消息</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">未 ACK 消息留在 pending，重启可重试</font> |
| <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">作用</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">用来抓新消息的游标</font> | <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">用来兜底重试失败消息的缓冲区</font> |


**<font style="color:#333333;">创建消费者组</font>**

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785595437675-f6f5bcf3-ce58-46e6-88e6-3c055b57336e.png" width="937" title="" crop="0,0,1,1" id="u5e5148e0" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">key：队列名称</font>

<font style="color:#333333;">groupName：消费者组名称</font>

<font style="color:#333333;">ID：起始ID标示，$代表队列中最后一个消息，0则代表队列中第一个消息</font>

<font style="color:#333333;">MKSTREAM：队列不存在时自动创建队列</font>

<font style="color:#333333;">其它常见命令：</font>

**<font style="color:#333333;">删除指定的消费者组</font>**

```java
XGROUP DESTORY key groupName
```

**<font style="color:#333333;">给指定的消费者组添加消费者</font>**

```java
XGROUP CREATECONSUMER key groupname consumername
```

**<font style="color:#333333;">删除消费者组中的指定消费者</font>**

```java
XGROUP DELCONSUMER key groupname consumername
```

<font style="color:#333333;">从消费者组读取消息：</font>

```java
XREADGROUP GROUP group consumer [COUNT count] [BLOCK milliseconds] [NOACK] STREAMS key [key ...] ID [ID ...]
```

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">group：消费组名称</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">consumer：消费者名称，如果消费者不存在，会自动创建一个消费者</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">count：本次查询的最大数量</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">BLOCK milliseconds：当没有消息时最长等待时间</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">NOACK：无需手动ACK，获取到消息后自动确认，即不会进pending-list</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">STREAMS key：指定队列名称</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">ID：获取消息的起始ID</font>
    - <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">">"：从下一个未消费的消息开始</font>
    - <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">其它：根据指定id从pending-list中获取已消费但未确认的消息，例如0，是从pending-list中的第一个消息开始</font>

<font style="color:#333333;">消息确认：</font>

```java
XACK key group ID [ID ...]
```

<font style="color:#333333;">消费者监听消息的基本思路：</font>

```java
while(true){
    // 阻塞拉取全新消息（> 只拿新消息，无阻塞2000ms返回null）
    Object msg = redis.call("XREADGROUP GROUP g1 c1 COUNT 1 BLOCK 2000 STREAMS s1 >");
    if(msg == null){
        continue; // 超时无新消息，重新循环监听
    }
    try {
        handleMessage(msg); // 正常处理消息，内部必须执行XACK确认
    } catch(Exception e) {
        // 业务处理报错，进入内层死循环重试pending消息
        while(true){
            // 读取所有未ACK的失败消息（0代表pending队列起点）
            Object errMsg = redis.call("XREADGROUP GROUP g1 c1 COUNT 1 STREAMS s1 0");
            if(errMsg == null){
                break; // 没有残留失败消息，退出重试循环，回到外层监听新消息
            }
            try {
                handleMessage(errMsg); // 重试处理失败消息
            } catch(Exception e2) {
                // 重试依然失败，继续循环下一条pending消息
                continue;
            }
        }
    }
}
```

<font style="color:#333333;">STREAM类型消息队列的XREADGROUP命令特点：</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">消息可回溯</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">可以多消费者争抢消息，加快消费速度</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">可以阻塞读取</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">没有消息漏读的风险</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">有消息确认机制，保证消息至少被消费一次</font>

<font style="color:#333333;">最后我们来个小对比</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785595437882-7085ed5d-2f28-4af3-880b-8ba2e41f2aa0.png" width="635" title="" crop="0,0,1,1" id="u9898ded1" class="ne-image" style="font-size: 14px">

#### **实现异步秒杀下单**
<font style="color:#333333;">需求：</font>

1. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">创建一个Stream类型的消息队列，名为 stream.orders</font>
2. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">修改之前的秒杀下单Lua脚本，在认定有抢购资格后，直接向stream.orders中添加消息，内容包含voucherId、userId、orderId</font>
3. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">项目启动时，开启一个线程任务，尝试获取stream.orders中的消息，完成下单</font>
+ <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">修改Lua表达式，新增3.6</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785595438047-d24ccd9f-3385-4eee-a7fd-071002dfb742.png" width="1236" title="" crop="0,0,1,1" id="ub73d9e9d" class="ne-image" style="font-size: 14px">

+ <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">VoucherOrderServiceImpl 类</font>

```java
private class VoucherOrderHandler implements Runnable {
    @Override
    public void run() {
        while (true) {
            try {
                // 1.获取消息队列中的订单信息 XREADGROUP GROUP g1 c1 COUNT 1 BLOCK 2000 STREAMS s1 >
                List<MapRecord<String, Object, Object>> list = stringRedisTemplate.opsForStream().read(
                    Consumer.from("g1", "c1"),
                    StreamReadOptions.empty().count(1).block(Duration.ofSeconds(2)),
                    StreamOffset.create("stream.orders", ReadOffset.lastConsumed())
                );
                // 2.判断订单信息是否为空
                if (list == null || list.isEmpty()) {
                    // 如果为null，说明没有消息，继续下一次循环
                    continue;
                }
                // 解析数据
                MapRecord<String, Object, Object> record = list.get(0);
                Map<Object, Object> value = record.getValue();
                VoucherOrder voucherOrder = BeanUtil.fillBeanWithMap(value, new VoucherOrder(), true);
                // 3.创建订单
                createVoucherOrder(voucherOrder);
                // 4.确认消息 XACK,消息从 pending-list 中移除
                stringRedisTemplate.opsForStream().acknowledge("s1", "g1", record.getId());
            } catch (Exception e) {
                log.error("处理订单异常", e);
                //处理异常消息
                handlePendingList();
            }
        }
    }

    private void handlePendingList() {
    // 初始化消费者组
        try {
            // 创建消费者组，如果组已存在则忽略错误
            stringRedisTemplate.opsForStream().createGroup("stream.orders", "g1");
        } catch (Exception e) {
            log.warn("消费者组 g1 已存在，跳过创建");
        }
        while (true) {
            try {
                // 1.获取pending-list中的订单信息 XREADGROUP GROUP g1 c1 COUNT 1 BLOCK 2000 STREAMS s1 0
                List<MapRecord<String, Object, Object>> list = stringRedisTemplate.opsForStream().read(
                    Consumer.from("g1", "c1"),
                    StreamReadOptions.empty().count(1),
                    StreamOffset.create("stream.orders", ReadOffset.from("0"))
                );
                // 2.判断订单信息是否为空
                if (list == null || list.isEmpty()) {
                    // 如果为null，说明没有异常消息，结束循环
                    break;
                }
                // 解析数据
                MapRecord<String, Object, Object> record = list.get(0);
                Map<Object, Object> value = record.getValue();
                VoucherOrder voucherOrder = BeanUtil.fillBeanWithMap(value, new VoucherOrder(), true);
                // 3.创建订单
                createVoucherOrder(voucherOrder);
                // 4.确认消息 XACK
                stringRedisTemplate.opsForStream().acknowledge("s1", "g1", record.getId());
            } catch (Exception e) {
                log.error("处理pending订单异常", e);
                try{
                    Thread.sleep(20);
                }catch(Exception e){
                    e.printStackTrace();
                }
            }
        }
    }
}
/**
 * 秒杀下单stream队列
 * @param voucherId
 * @return
 */
@Override
@Transactional
public Result seckillVoucher(Long voucherId) {
    Long userId = UserHolder.getUser().getId();
    long orderId = redisIdWorker.nextId("order");

    //执行lua脚本
    Long result = stringRedisTemplate.execute(
            SECKILL_SCRIPT,
            Collections.emptyList(),
            voucherId.toString(),
            voucherId.toString(),userId.toString(), String.valueOf(orderId));
    //把Long类型转为int
    int r = result.intValue();
    //判断结果
    if (r != 0) {
        return Result.fail(r == 1 ? "库存不足" : "不能重复下单");
    }
    //获取代理对象
    proxy = (IVoucherOrderService) AopContext.currentProxy();
    return Result.ok(orderId);
}
```

<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);"></font>

## **达人探店**
### **发布探店笔记**
<font style="color:#333333;">探店笔记类似点评网站的评价，往往是图文结合。对应的表有两个：</font>

<font style="color:#333333;">tb_blog：探店笔记表，包含笔记中的标题、文字、图片等</font>

<font style="color:#333333;">tb_blog_comments：其他用户对探店笔记的评价</font>

**<font style="color:#333333;">具体发布流程</font>**

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785595474826-ffe42861-6796-4325-909f-5c867bd71a91.png" width="728" title="" crop="0,0,1,1" id="uc17c0c25" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">上传接口</font>

```java
@Slf4j
@RestController
@RequestMapping("upload")
public class UploadController {

    @PostMapping("blog")
    public Result uploadImage(@RequestParam("file") MultipartFile image) {
        try {
            // 获取原始文件名称
            String originalFilename = image.getOriginalFilename();
            // 生成新文件名
            String fileName = createNewFileName(originalFilename);
            // 保存文件
            image.transferTo(new File(SystemConstants.IMAGE_UPLOAD_DIR, fileName));
            // 返回结果
            log.debug("文件上传成功，{}", fileName);
            return Result.ok(fileName);
        } catch (IOException e) {
            throw new RuntimeException("文件上传失败", e);
        }
    }
    
    private String createNewFileName(String originalFilename) {
        // 获取后缀
        String suffix = StrUtil.subAfter(originalFilename, ".", true);
        // 生成目录
        String name = UUID.randomUUID().toString();
        int hash = name.hashCode();
        int d1 = hash & 0xF;
        int d2 = (hash >> 4) & 0xF;
        // 判断目录是否存在
        File dir = new File(SystemConstants.IMAGE_UPLOAD_DIR, StrUtil.format("/blogs/{}/{}", d1, d2));
        if (!dir.exists()) {
            dir.mkdirs();
        }
        // 生成文件名
        return StrUtil.format("/blogs/{}/{}/{}.{}", d1, d2, name, suffix);
    }
}
```

<font style="color:#333333;">注意：同学们在操作时，需要修改SystemConstants.IMAGE_UPLOAD_DIR 自己图片所在的地址，在实际开发中图片一般会放在nginx上或者是云存储上。</font>

+ <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">BlogController 类</font>

```java
@RestController
@RequestMapping("/blog")
public class BlogController {

    @Resource
    private IBlogService blogService;

    @PostMapping
    public Result saveBlog(@RequestBody Blog blog) {
        //获取登录用户
        UserDTO user = UserHolder.getUser();
        blog.setUpdateTime(user.getId());
        //保存探店博文
        blogService.saveBlog(blog);
        //返回id
        return Result.ok(blog.getId());
    }
}
```

### **查看探店笔记**
<font style="color:#333333;">实现查看发布探店笔记的接口</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785595474373-8f01d00c-4545-4ec4-9f44-b2e725aa77c0.png" width="699" title="" crop="0,0,1,1" id="ud716b3bf" class="ne-image" style="font-size: 14px">

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">BlogController 类</font>

```java
@GetMapping("/{id}")
public Result queryBlogById(@PathVariable("id") Long blogId){
    return blogService.queryBlogById(blogId);
}
```

+ <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">BlogServiceImpl 类</font>

```java
@Resource
private IUserService userService;
@Override
public Result queryBlogById(Long blogId) {
    Blog blog = getById(blogId);
    if (blog == null) {
        return Result.fail("该笔记不存在！");
    }
    Long userId = blog.getUserId();
    User user = userService.getById(userId);
    blog.setIcon(user.getIcon());
    blog.setName(user.getNickName());
    return Result.ok(blog);
}
```

### **点赞功能**
<font style="color:#333333;">初始代码</font>

```java
@GetMapping("/likes/{id}")
public Result queryBlogLikes(@PathVariable("id") Long id) {
    //修改点赞数量
    blogService.update().setSql("liked = liked + 1").eq("id",id).update();
    return Result.ok();
}
```

<font style="color:#333333;">问题分析：这种方式会导致一个用户无限点赞，明显是不合理的</font>

<font style="color:#333333;">造成这个问题的原因是，我们现在的逻辑，发起请求只是给数据库 +1，所以才会出现这个问题</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785595475583-946de98d-402f-49df-a95d-c4411ff4b938.png" width="698" title="" crop="0,0,1,1" id="uf3c4258e" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">完善点赞功能</font>

<font style="color:#333333;">需求：</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">同一个用户只能点赞一次，再次点击则取消点赞</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">如果当前用户已经点赞，则点赞按钮高亮显示（前端已实现，判断字段Blog类的isLike属性）</font>

<font style="color:#333333;">实现步骤：</font>

1. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">给Blog类中添加一个isLike字段，标示是否被当前用户点赞</font>
2. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">修改点赞功能，利用Redis的set集合判断是否点赞过，未点赞过则点赞数+1，已点赞过则点赞数-1</font>
3. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">修改根据id查询Blog的业务，判断当前登录用户是否点赞过，赋值给isLike字段</font>
4. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">修改分页查询Blog业务，判断当前登录用户是否点赞过，赋值给isLike字段</font>

<font style="color:#333333;">具体步骤：</font>

<font style="color:#333333;">1、在Blog 添加一个字段</font>

```java
@TableField(exist = false)
private Boolean isLike;
```

<font style="color:#333333;">2、修改代码</font>

```java
@Override
public Result likeBlog(Long id){
    // 1.获取登录用户
    Long userId = UserHolder.getUser().getId();
    // 2.判断当前登录用户是否已经点赞
    String key = BLOG_LIKED_KEY + id;
    Boolean isMember = stringRedisTemplate.opsForSet().isMember(key, userId.toString());
    if(BooleanUtil.isFalse(isMember)){
        //3.如果未点赞，可以点赞
        //3.1 数据库点赞数+1
        boolean isSuccess = update().setSql("liked = liked + 1").eq("id", id).update();
        //3.2 保存用户到Redis的set集合
        if(isSuccess){
            stringRedisTemplate.opsForSet().add(key,userId.toString());
        }
    }else{
         //4.如果已点赞，取消点赞
        //4.1 数据库点赞数-1
        boolean isSuccess = update().setSql("liked = liked - 1").eq("id", id).update();
        //4.2 把用户从Redis的set集合移除
        if(isSuccess){
            stringRedisTemplate.opsForSet().remove(key,userId.toString());
        }
    }
    return Result.ok();
}
```

<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);"></font>

### **点赞排行榜**
<font style="color:#333333;">在探店笔记的详情页面，应该把给该笔记点赞的人显示出来，比如最早点赞的TOP5，形成点赞排行榜：</font>

<font style="color:#333333;">之前的点赞是放到set集合，但是set集合是不能排序的，所以这个时候，咱们可以采用一个可以排序的set集合，就是咱们的sortedSet</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785595496886-4760017a-4c53-4bde-b1ab-296365fe682f.png" width="683" title="" crop="0,0,1,1" id="uef641660" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">我们接下来来对比一下这些集合的区别是什么</font>

<font style="color:#333333;">所有点赞的人，需要是唯一的，所以我们应当使用set或者是sortedSet</font>

<font style="color:#333333;">其次我们需要排序，就可以直接锁定使用sortedSet啦</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785595495920-d0431cf7-ee28-4a05-8d0e-de92873935cd.png" width="658" title="" crop="0,0,1,1" id="ue5fab8c2" class="ne-image" style="font-size: 14px">

+ <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">BlogServiceImpl 类</font>

<font style="color:#333333;">点赞逻辑代码</font>

```java
@Override
public Result likeBlog(Long id) {
    // 1.获取登录用户
    Long userId = UserHolder.getUser().getId();
    // 2.判断当前登录用户是否已经点赞
    String key = BLOG_LIKED_KEY + id;
    //（修改代码）
    Double score = stringRedisTemplate.opsForZSet().score(key, userId.toString());
    if (score == null) {
        // 3.如果未点赞，可以点赞
        // 3.1.数据库点赞数 + 1
        boolean isSuccess = update().setSql("liked = liked + 1").eq("id", id).update();
        // 3.2.保存用户到Redis的set集合  zadd key value score
        if (isSuccess) {
            //（修改代码）
            stringRedisTemplate.opsForZSet().add(key, userId.toString(), System.currentTimeMillis());
        }
    } else {
        // 4.如果已点赞，取消点赞
        // 4.1.数据库点赞数 -1
        boolean isSuccess = update().setSql("liked = liked - 1").eq("id", id).update();
        // 4.2.把用户从Redis的set集合移除
        if (isSuccess) {
            stringRedisTemplate.opsForZSet().remove(key, userId.toString());
        }
    }
    return Result.ok();
}


private void isBlogLiked(Blog blog) {
    // 1.获取登录用户
    UserDTO user = UserHolder.getUser();
    if (user == null) {
        // 用户未登录，无需查询是否点赞
        return;
    }
    Long userId = user.getId();
    // 2.判断当前登录用户是否已经点赞
    String key = "blog:liked:" + blog.getId();
    Double score = stringRedisTemplate.opsForZSet().score(key, userId.toString());
    blog.setIsLike(score != null);
}
```

<font style="color:#333333;">查询点赞列表</font>

+ <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">BlogController 类</font>

```java
@GetMapping("/likes/{id}")
public Result queryBlogLikes(@PathVariable("id") Long id) {
    return blogService.queryBlogLikes(id);
}
```

+ <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">BlogServiceImpl 类</font>

```java
@Override
public Result queryBlogLikes(Long id) {
    String key = BLOG_LIKED_KEY + id;
    // 1.查询top5的点赞用户 zrange key 0 4
    Set<String> top5 = stringRedisTemplate.opsForZSet().range(key, 0, 4);
    if (top5 == null || top5.isEmpty()) {
        return Result.ok(Collections.emptyList());
    }
    // 2.解析出其中的用户id，把id从字符串转成Long类型
    List<Long> ids = top5.stream().map(str -> Long.valueOf(str)).collect(Collectors.toList());
    String idStr = StrUtil.join(",", ids);
    // 3.根据用户id查询用户 WHERE id IN ( 5 , 1 ) ORDER BY FIELD(id, 5, 1)
    List<UserDTO> userDTOS = userService.query()
            .in("id", ids).last("ORDER BY FIELD(id," + idStr + ")").list()
            .stream()
            .map(user -> BeanUtil.copyProperties(user, UserDTO.class))
            .collect(Collectors.toList());
    // 4.返回
    return Result.ok(userDTOS);
}
```

## **好友关注**
### **关注和取消关注**
<font style="color:#333333;">针对用户的操作：可以对用户进行关注和取消关注功能。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785595497181-5d0c42f8-f32e-4ad6-a84f-2e42f73e336b.png" width="698" title="" crop="0,0,1,1" id="ucaa5a382" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">实现思路：</font>

<font style="color:#333333;">需求：基于该表数据结构，实现两个接口：</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">关注和取关接口</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">判断是否关注的接口</font>

<font style="color:#333333;">关注是User之间的关系，是博主与粉丝的关系，数据库中有一张tb_follow表来标示：</font>

<font style="color:#333333;">注意：这里id没有设置自增长，需要把主键修改为自增长，简化开发。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785595496314-67c9a271-85b7-423c-b458-98b5ff9feb57.png" width="685" title="" crop="0,0,1,1" id="u5fc367b3" class="ne-image" style="font-size: 14px">

+ <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">FollowController 类</font>

```java
//关注
@PutMapping("/{id}/{isFollow}")
public Result follow(@PathVariable("id") Long followUserId, @PathVariable("isFollow") Boolean isFollow) {
    return followService.follow(followUserId, isFollow);
}
//是否关注
@GetMapping("/or/not/{id}")
public Result isFollow(@PathVariable("id") Long followUserId) {
      return followService.isFollow(followUserId);
}
```

+ <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">FollowService 类</font>

```java
//是否关注
@Override
public Result isFollow(Long followUserId) {
    // 1.获取登录用户
    Long userId = UserHolder.getUser().getId();
    // 2.查询是否关注 select count(*) from tb_follow where user_id = ? and follow_user_id = ?
    Integer count = query().eq("user_id", userId).eq("follow_user_id", followUserId).count();
    // 3.判断
    return Result.ok(count > 0);
}

//关注
@Override
public Result follow(Long followUserId, Boolean isFollow) {
    // 1.获取登录用户
    Long userId = UserHolder.getUser().getId();
    String key = "follows:" + userId;
    // 1.判断到底是关注还是取关
    if (isFollow) {
        // 2.关注，新增数据
        Follow follow = new Follow();
        follow.setUserId(userId);
        follow.setFollowUserId(followUserId);
        boolean isSuccess = save(follow);
    } else {
        // 3.取关，删除 delete from tb_follow where user_id = ? and follow_user_id = ?
        remove(new QueryWrapper<Follow>()
                .eq("user_id", userId).eq("follow_user_id", followUserId));
    }
    return Result.ok();
}
```

### **共同关注**
<font style="color:#333333;">想要去看共同关注的好友，需要首先进入到这个页面，这个页面会发起两个请求</font>

<font style="color:#333333;">1、去查询用户的详情</font>

<font style="color:#333333;">2、去查询用户的笔记</font>

<font style="color:#333333;">以上两个功能和共同关注没有什么关系，大家可以自行将笔记中的代码拷贝到idea中就可以实现这两个功能了，我们的重点在于共同关注功能。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785595497847-31abe27d-f269-4f22-a914-3e36e69ba022.png" width="1602" title="" crop="0,0,1,1" id="u59c2f7e2" class="ne-image" style="font-size: 14px">

```java
// UserController 根据id查询用户
@GetMapping("/{id}")
public Result queryUserById(@PathVariable("id") Long userId){
    // 查询详情
    User user = userService.getById(userId);
    if (user == null) {
        return Result.ok();
    }
    UserDTO userDTO = BeanUtil.copyProperties(user, UserDTO.class);
    // 返回
    return Result.ok(userDTO);
}

// BlogController  根据id查询博主的探店笔记
@GetMapping("/of/user")
public Result queryBlogByUserId(
    //current为页数
        @RequestParam(value = "current", defaultValue = "1") Integer current,
        @RequestParam("id") Long id) {
    // 根据用户查询
    Page<Blog> page = blogService.query()
            .eq("user_id", id).page(new Page<>(current, SystemConstants.MAX_PAGE_SIZE));
    // 获取当前页数据
    List<Blog> records = page.getRecords();
    return Result.ok(records);
}
```

<font style="color:#333333;">接下来我们来看看共同关注如何实现：</font>

<font style="color:#333333;">需求：利用Redis中恰当的数据结构，实现共同关注功能。在博主个人页面展示出当前用户与博主的共同关注呢。</font>

<font style="color:#333333;">当然是使用我们之前学习过的 set 集合咯，在set集合中，有交集并集补集的api，我们可以把两人的关注的人分别放入到一个set集合中，然后再通过api去查看这两个 set 集合中的交集数据。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785595497344-f9a90126-b36a-482b-9fef-5a67ddcd909b.png" width="1259" title="" crop="0,0,1,1" id="uffab0e53" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">我们先来改造当前的关注列表</font>

<font style="color:#333333;">改造原因是因为我们需要在用户关注了某位用户后，需要将数据放入到set集合中，方便后续进行共同关注，同时当取消关注时，也需要从set集合中进行删除</font>

+ <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">FollowServiceImpl 类</font>

```java
@Override
public Result follow(Long followUserId, Boolean isFollow) {
    // 1.获取登录用户
    Long userId = UserHolder.getUser().getId();
    String key = "follows:" + userId;
    // 1.判断到底是关注还是取关
    if (isFollow) {
        // 2.关注，新增数据
        Follow follow = new Follow();
        follow.setUserId(userId);
        follow.setFollowUserId(followUserId);
        boolean isSuccess = save(follow);
        if (isSuccess) {
            // 把关注用户的id，放入redis的set集合 sadd userId followerUserId
            stringRedisTemplate.opsForSet().add(key, followUserId.toString());
        }
    } else {
        // 3.取关，删除 delete from tb_follow where user_id = ? and follow_user_id = ?
        boolean isSuccess = remove(new QueryWrapper<Follow>()
                .eq("user_id", userId).eq("follow_user_id", followUserId));
        if (isSuccess) {
            // 把关注用户的id从Redis集合中移除
            stringRedisTemplate.opsForSet().remove(key, followUserId.toString());
        }
    }
    return Result.ok();
}
```

**<font style="color:#333333;">具体的关注代码：</font>**

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">FollowController 类</font>

```java
@GetMapping("/common/{id}")
public Result followCommons(@PathVariable Long id){
    return followService.followCommons(id);
}
```

+ <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">FollowServiceImpl 类</font>

```java
@Resource
private IUserService userService;

@Override
public Result followCommons(Long id) {
    // 1.获取当前用户
    Long userId = UserHolder.getUser().getId();
    String key = "follows:" + userId;
    // 2.求交集
    String key2 = "follows:" + id;
    Set<String> intersect = stringRedisTemplate.opsForSet().intersect(key, key2);
    if (intersect == null || intersect.isEmpty()) {
        // 无交集
        return Result.ok(Collections.emptyList());
    }
    // 3.解析id集合
    List<Long> ids = intersect.stream().map(Long::valueOf).collect(Collectors.toList());
    // 4.查询用户，注意这里要用userService
    List<UserDTO> users = userService.listByIds(ids)
            .stream()
            .map(user -> BeanUtil.copyProperties(user, UserDTO.class))
            .collect(Collectors.toList());
    return Result.ok(users);
}
```

<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);"></font>

### **Feed流实现方案**
<font style="color:#333333;">当我们关注了用户后，这个用户发了动态，那么我们应该把这些数据推送给用户。关注推送也叫做Feed流，直译为投喂。为用户持续的提供“沉浸式”的体验，通过无限下拉刷新获取新的信息。</font>

<font style="color:#333333;">对于传统的模式的内容解锁：我们是需要用户去通过搜索引擎或者是其他的方式去解锁想要看的内容</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785595518348-8c5d6ebd-6ce0-4d6a-8fb9-44c5b50b7eec.png" width="671" title="" crop="0,0,1,1" id="u5dd35f4b" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">对于新型的Feed流的的效果：不需要我们用户再去推送信息，而是系统分析用户到底想要什么，然后直接把内容推送给用户，从而使用户能够更加的节约时间，不用主动去寻找。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785595518410-8c0dbbcf-fa7e-4bfd-859c-579f01b768e0.png" width="698" title="" crop="0,0,1,1" id="uf47f4fc6" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">Feed流的实现有两种模式：</font>

<font style="color:#333333;">Timeline：不做内容筛选，简单的按照内容发布时间排序，常用于好友或关注。例如朋友圈</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">优点：信息全面，不会有缺失。并且实现也相对简单</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">缺点：信息噪音较多，用户不一定感兴趣，内容获取效率低</font>

<font style="color:#333333;">智能排序：利用智能算法屏蔽掉违规的、用户不感兴趣的内容。推送用户感兴趣信息来吸引用户</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">优点：投喂用户感兴趣信息，用户粘度很高，容易沉迷</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">缺点：如果算法不精准，可能起到反作用</font>

<font style="color:#333333;">我们本次针对好友的操作，采用的就是Timeline的方式，只需要拿到我们关注用户的信息，然后按照时间排序即可，因此采用Timeline的模式。该模式的实现方案有三种：</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">拉模式</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">推模式</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">推拉结合</font>

**<font style="color:#333333;">拉模式</font>**<font style="color:#333333;">：也叫做读扩散</font>

<font style="color:#333333;">该模式的核心含义就是：当张三和李四和王五发了消息后，都会保存在自己的邮箱中，假设赵六要读取信息，那么他会从读取他自己的收件箱，此时系统会从他关注的人群中，把他关注人的信息全部都进行拉取，然后在进行排序</font>

<font style="color:#333333;">优点：比较节约空间，因为赵六在读信息时，并没有重复读取，而且读取完之后可以把他的收件箱进行清楚。</font>

<font style="color:#333333;">缺点：比较延迟，当用户读取数据时才去关注的人里边去读取数据，假设用户关注了大量的用户，那么此时就会拉取海量的内容，对服务器压力巨大。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785595518690-792d10e3-04ce-45a1-a3eb-9df5a836b9a8.png" width="661" title="" crop="0,0,1,1" id="u93e4acb6" class="ne-image" style="font-size: 14px">

**<font style="color:#333333;">推模式</font>**<font style="color:#333333;">：也叫做写扩散。</font>

<font style="color:#333333;">推模式是没有写邮箱的，当张三写了一个内容，此时会主动的把张三写的内容发送到他的粉丝收件箱中去，假设此时李四再来读取，就不用再去临时拉取了</font>

<font style="color:#333333;">优点：时效快，不用临时拉取</font>

<font style="color:#333333;">缺点：内存压力大，假设一个大V写信息，很多人关注他， 就会写很多分数据到粉丝那边去</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785595518690-76a2e17c-6c30-44a7-b689-3c56ba04b986.png" width="510" title="" crop="0,0,1,1" id="ucb0abe66" class="ne-image" style="font-size: 14px">

**<font style="color:#333333;">推拉结合模式</font>**<font style="color:#333333;">：也叫做读写混合，兼具推和拉两种模式的优点。</font>

<font style="color:#333333;">推拉模式是一个折中的方案，站在发件人这端：如果是个普通的人，那么我们采用写扩散的方式，直接把数据写入到他的粉丝中去，因为普通的人他的粉丝关注量比较小，所以这样做没有压力。如果是大V，那么他是直接将数据先写入到一份到发件箱里边去，然后再直接写一份到活跃粉丝收件箱里边去。</font>

<font style="color:#333333;">现在站在收件人这端：如果是活跃粉丝，那么大V和普通的人发的都会直接写入到自己收件箱里边来，而如果是普通粉丝，由于他们上线不是很频繁，所以等他们上线时，再从发件箱里边去拉信息。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785595518764-6055e00d-3b96-4116-a950-e817a14b3b14.png" width="663" title="" crop="0,0,1,1" id="u26575755" class="ne-image" style="font-size: 14px">

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785595520165-2686c8be-eed9-4958-9728-207fb2c73ca9.png" width="717" title="" crop="0,0,1,1" id="u7376db3b" class="ne-image" style="font-size: 14px">

### **推送到粉丝收件箱**
<font style="color:#333333;">需求：</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">修改新增探店笔记的业务，在保存blog到数据库的同时，推送到粉丝的收件箱</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">收件箱满足可以根据时间戳排序，必须用Redis的数据结构实现</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">查询收件箱数据时，可以实现分页查询</font>

<font style="color:#333333;">传统分页在feed流是不适用的，因为我们的数据会随时发生变化。</font>

<font style="color:#333333;">假设在t1 时刻，我们去读取第一页，此时page = 1 ，size = 5 ，那么我们拿到的就是10~6 这几条记录，假设现在t2时候又发布了一条记录，此时t3 时刻，我们来读取第二页，读取第二页传入的参数是page=2 ，size=5 ，那么此时读取到的第二页实际上是从6 开始，然后是6~2 ，那么我们就读取到了重复的数据，所以feed流的分页，不能采用原始方案来做。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785595520632-cbcca68b-7b2f-4759-a46a-ad77e93b7c10.png" width="611" title="" crop="0,0,1,1" id="u26409b30" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">Feed流的滚动分页</font>

<font style="color:#333333;">我们需要记录每次操作的最后一条，然后从这个位置开始去读取数据</font>

<font style="color:#333333;">举个例子：我们从 t1 时刻开始，拿第一页数据，拿到了 10~6，然后记录下当前最后一次拿取的记录，就是6，t2时刻发布了新的记录，此时这个11放到最顶上，但是不会影响我们之前记录的6，此时t3时刻来拿第二页，第二页这个时候拿数据，还是从6后一点的5去拿，就拿到了 5~1 的记录。我们这个地方可以采用 sortedSet 来做，可以进行范围查询，并且还可以记录当前获取数据时间戳最小值，就可以实现滚动分页了。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785595520981-a1b87974-7dea-4c7c-9b58-3ef9cb82eaea.png" width="580" title="" crop="0,0,1,1" id="u33f93345" class="ne-image" style="font-size: 14px">

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">传统分页：zrange key start stop：按照score排序后，返回有序集合中指定 排名范围内的成员（按下标索引分页）</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">传统分页之所以不行，是因为数据改变，索引也会改变；但分值不会改变。</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">滚动分页：zrangebyscore key min max：按照score排序后，获取指定 score 范围内的元素（按score 分值分页）</font>

注意：所有的排名默认都是升序，如果要降序则在命令的 z 后面添加 rev 即可

<font style="color:#333333;">核心的意思：就是我们在保存完探店笔记后，获得当前笔记的粉丝，然后把数据推送到粉丝的redis中去。</font>

```java
@Override
public Result saveBlog(Blog blog) {
    // 1.获取登录用户
    UserDTO user = UserHolder.getUser();
    blog.setUserId(user.getId());
    // 2.保存探店笔记
    boolean isSuccess = save(blog);
    if(!isSuccess){
        return Result.fail("新增笔记失败!");
    }
    // 3.查询笔记作者的所有粉丝 select * from tb_follow where follow_user_id = ?
    List<Follow> follows = followService.query().eq("follow_user_id", user.getId()).list();
    // 4.推送笔记id给所有粉丝
    for (Follow follow : follows) {
        // 4.1.获取粉丝id
        Long userId = follow.getUserId();
        // 4.2.推送
        String key = FEED_KEY + userId;
        stringRedisTemplate.opsForZSet().add(key, blog.getId().toString(), System.currentTimeMillis());
    }
    // 5.返回id
    return Result.ok(blog.getId());
}
```

### **分页查询收邮箱**
<font style="color:#333333;">需求：在个人主页的 “关注” 卡片中，查询并展示推送的Blog信息：</font>

<font style="color:#333333;">具体操作如下：</font>

<font style="color:#333333;">1、每次查询完成后，我们要分析出查询出数据的最小时间戳，这个值会作为下一次查询的条件(滚动分页)</font>

<font style="color:#333333;">2、需要找到与上一次查询的最小值 相同的元素的个数作为偏移量，下次查询时，跳过这些查询过的数据，拿到我们需要的数据。</font>

<font style="color:#333333;">综上：我们的请求参数中就需要携带 lastId(maxTime)：上一次查询的最小时间戳 和偏移量这两个参数。</font>

<font style="color:#333333;">这两个参数第一次会由前端来指定，以后的查询就根据后台结果作为条件，再次传递到后台。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785595521252-2a003507-8c38-405b-8684-197c706cfac7.png" width="706" title="" crop="0,0,1,1" id="u0172829d" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">一、定义出来具体的返回值实体类</font>

```java
@Data
public class ScrollResult {
    private List<?> list;
    private Long minTime;
    private Integer offset;
}
```

+ <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">BlogController 类</font>

<font style="color:#333333;">注意：RequestParam 表示接受 url 地址栏传参的注解，当方法上参数的名称和 url 地址栏不相同时，可以通过 RequestParam 来进行指定</font>

```java
@GetMapping("/of/follow")
public Result queryBlogOfFollow(
    @RequestParam("lastId") Long max, @RequestParam(value = "offset", defaultValue = "0") Integer offset){
    return blogService.queryBlogOfFollow(max, offset);
}
```

+ <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">BlogServiceImpl 类</font>

```java
@Override
public Result queryBlogOfFollow(Long max, Integer offset) {
    // 1.获取当前用户
    Long userId = UserHolder.getUser().getId();
    // 2.查询收件箱 ZREVRANGEBYSCORE key Max Min LIMIT offset count
    String key = FEED_KEY + userId;
    Set<ZSetOperations.TypedTuple<String>> typedTuples = stringRedisTemplate.opsForZSet()
        .reverseRangeByScoreWithScores(key, 0, max, offset, 2);
    //reverseRangeByScoreWithScores：倒序查询（score 从大→小，新笔记在前）
    //区间 0,max：只取 0 ≤ score ≤ max 的数据。0这个数字不重要，重要的是起始位置(max)和查几条(2)
    //offset：同 score 下跳过多少条数据
    //2：每页固定取 2 条笔记
    
    // 3.非空判断
    if (typedTuples == null || typedTuples.isEmpty()) {
        return Result.ok();
    }
    // 4.解析数据：blogId、minTime（时间戳）、offset
    List<Long> ids = new ArrayList<>(typedTuples.size());
    long minTime = 0; // 本页最小时间戳，下一次下拉作为 max 入参
    int os = 1; // 当前最小时间戳对应的笔记条数
    for (ZSetOperations.TypedTuple<String> tuple : typedTuples) {
        // 4.1.获取博客id
        ids.add(Long.valueOf(tuple.getValue()));
        // 4.2.获取分数，即获取本条笔记发布时间戳
        long time = tuple.getScore().longValue();
        if(time == minTime){
            // 和当前最小时间戳相同，同毫秒笔记计数+1
            os++;
        }else{
            // 遇到更小的时间戳，更新minTime，重置计数为1
            minTime = time;
            os = 1;
        }
    }
    // 计算下一次请求需要传递的offset
    os = minTime == max ? os : os + offset;
    // 5.根据id查询blog
    String idStr = StrUtil.join(",", ids);
    List<Blog> blogs = query().in("id", ids).last("ORDER BY FIELD(id," + idStr + ")").list();

    for (Blog blog : blogs) {
        // 5.1.查询blog有关的用户
        queryBlogUser(blog);
        // 5.2.查询blog是否被点赞
        isBlogLiked(blog);
    }

    // 6.封装并返回
    ScrollResult r = new ScrollResult();
    r.setList(blogs);
    r.setOffset(os);
    r.setMinTime(minTime);

    return Result.ok(r);
}
```

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">首次下拉（前端传 max = 当前时间戳，offset=0）</font>
1. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">查询 ZSet，取出最新 2 条笔记，时间戳 1000、900；</font>
2. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">遍历：minTime=900，os=1；minTime != max → os = 1+0=1；</font>
3. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">返回 minTime=900，offset=1，两条最新笔记。</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">第二次下拉（前端传 max=900，offset=1）</font>
1. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">查询 score ≤900、跳过前 1 条，取出 800、700；</font>
2. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">返回 minTime=700，offset = 1；</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">极端场景：两条笔记同一毫秒 score = 500</font>
1. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">第一页：max=500 offset=0，取出 2 条 score = 500；</font>
    - <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">minTime=500，os=2；minTime == max → os=2；</font>
2. <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">第二页：max=500 offset=2，跳过前 2 条同毫秒数据，读取更早笔记，不会重复。</font>

注意：翻页翻不了可以改变一下手机型号



## **附近的商户**
### **GEO数据结构**
<font style="color:#333333;">GEO就是 Geolocation 的简写形式，代表地理坐标。Redis在3.2版本中加入了对GEO的支持，允许存储地理坐标信息，帮助我们根据经纬度来检索数据。常见的命令有：</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">GEOADD：添加一个地理空间信息，包含：经度（longitude）、纬度（latitude）、值（member）</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">GEODIST：计算指定的两个点之间的距离并返回</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">GEOHASH：将指定member的坐标转为hash字符串形式并返回</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">GEOPOS：返回指定member的坐标</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">GEORADIUS：指定圆心、半径，找到该圆内包含的所有member，并按照与圆心之间的距离排序后返回。6.以后已废弃</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">GEOSEARCH：在指定范围内搜索member，并按照与指定点之间的距离排序后返回。范围可以是圆形或矩形。6.2.新功能</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">GEOSEARCHSTORE：与GEOSEARCH功能一致，不过可以把结果存储到一个指定的key。 6.2.新功能</font>

### **导入店铺数据到GEO**
<font style="color:#333333;">具体场景说明：</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785595538618-8b637932-3967-4a49-a2a8-c95777751cb6.png" width="665" title="" crop="0,0,1,1" id="u78aaef02" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">当我们点击美食之后，会出现一系列的商家，商家中可以按照多种排序方式，我们此时关注的是距离，这个地方就需要使用到我们的GEO，向后台传入当前app收集的地址(我们此处是写死的) ，以当前坐标作为圆心，同时绑定相同的店家类型type，以及分页信息，把这几个条件传入后台，后台查询出对应的数据再返回。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785595538452-cda0b72f-9c19-454b-ba9d-c5e19a442ff5.png" width="484" title="" crop="0,0,1,1" id="uf007b708" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">我们要做的事情是：将数据库表中的数据导入到redis中去，redis中的GEO，GEO在redis中就一个member和一个经纬度，我们把x和y轴传入到redis做的经纬度位置去，但我们不能把所有的数据都放入到member中去，毕竟作为redis是一个内存级数据库，如果存海量数据，redis 还是力不从心，所以我们在这个地方存储店铺的 id 即可。</font>

<font style="color:#333333;">但是这个时候还有一个问题，就是在redis中并没有存储type，所以我们无法根据type来对数据进行筛选，所以我们可以按照商户类型做分组，类型相同的商户作为同一组，以 typeId 为 key 存入同一个GEO集合中即可。</font>

+ <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">HmDianPingApplicationTests 类</font>

```java
@Test
void loadShopData() {
    // 1.查询店铺信息
    List<Shop> list = shopService.list();
    // 2.把店铺分组，按照typeId分组，typeId一致的放到一个集合
    Map<Long, List<Shop>> map = list.stream().collect(Collectors.groupingBy(Shop::getTypeId));
    // 3.分批完成写入Redis
    for (Map.Entry<Long, List<Shop>> entry : map.entrySet()) {
        // 3.1.获取类型id
        Long typeId = entry.getKey();
        String key = SHOP_GEO_KEY + typeId;
        // 3.2.获取同类型的店铺的集合
        List<Shop> value = entry.getValue();
        List<RedisGeoCommands.GeoLocation<String>> locations = new ArrayList<>(value.size());
        // 3.3.写入redis GEOADD key 经度 纬度 member
        for (Shop shop : value) {
            // stringRedisTemplate.opsForGeo().add(key, new Point(shop.getX(), shop.getY()), shop.getId().toString());
            locations.add(new RedisGeoCommands.GeoLocation<>(
                    shop.getId().toString(),
                    new Point(shop.getX(), shop.getY())
            ));
        }
        stringRedisTemplate.opsForGeo().add(key, locations);
    }
}
```

### **实现附近商户功能**
<font style="color:#333333;">SpringDataRedis的2.3.9版本并不支持Redis 6.2提供的GEOSEARCH命令，因此我们需要提示其版本，修改自己的POM</font>

<font style="color:#333333;">第一步：导入pom</font>

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-data-redis</artifactId>
  <exclusions>
    <exclusion>
      <artifactId>spring-data-redis</artifactId>
      <groupId>org.springframework.data</groupId>
    </exclusion>
    <exclusion>
      <artifactId>lettuce-core</artifactId>
      <groupId>io.lettuce</groupId>
    </exclusion>
  </exclusions>
</dependency>
<dependency>
  <groupId>org.springframework.data</groupId>
  <artifactId>spring-data-redis</artifactId>
  <version>2.6.2</version>
</dependency>
<dependency>
  <groupId>io.lettuce</groupId>
  <artifactId>lettuce-core</artifactId>
  <version>6.1.6.RELEASE</version>
</dependency>
```

<font style="color:#333333;">第二步：</font>

+ <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">ShopController 类</font>

```java
@GetMapping("/of/type")
public Result queryShopByType(
    @RequestParam("typeId") Integer typeId,
    @RequestParam(value = "current", defaultValue = "1") Integer current,
    @RequestParam(value = "x", required = false) Double x,
    @RequestParam(value = "y", required = false) Double y
    //required=false前端不传该参数不会报错
) {
    return shopService.queryShopByType(typeId, current, x, y);
}
```

+ <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">ShopServiceImpl 类</font>

```java
@Override
public Result queryShopByType(Integer typeId, Integer current, Double x, Double y) {
    // 1.判断是否需要根据坐标查询
    if (x == null || y == null) {
        // 不需要坐标查询，按数据库查询
        Page<Shop> page = query()
                .eq("type_id", typeId)
                .page(new Page<>(current, SystemConstants.DEFAULT_PAGE_SIZE));
        // 返回数据
        return Result.ok(page.getRecords());
    }

    // 2.计算下标分页的参数
    int from = (current - 1) * SystemConstants.DEFAULT_PAGE_SIZE;
    int end = current * SystemConstants.DEFAULT_PAGE_SIZE;

    // 3.查询redis、按照距离排序、分页。结果：shopId、distance
    String key = SHOP_GEO_KEY + typeId;
    // GEOSEARCH key BYLONLAT x y BYRADIUS 5000 WITHDISTANCE
    //0~end
    GeoResults<RedisGeoCommands.GeoLocation<String>> results = stringRedisTemplate.opsForGeo()
            .search(
                    key,
                    GeoReference.fromCoordinate(x, y), // 对应 BYLONLAT x y
                    new Distance(5000), // 对应 BYRADIUS 5000（单位默认米）
                    RedisGeoCommands.GeoSearchCommandArgs.newGeoSearchArgs()
                    .includeDistance() // 对应 WITHDISTANCE
                    .limit(end)        // 对应 LIMIT end
            );
    // 4.解析出id
    if (results == null) {
        return Result.ok(Collections.emptyList());
    }
    //content为member(商铺id)和score(经纬度)
    List<GeoResult<RedisGeoCommands.GeoLocation<String>>> list = results.getContent();
    if (list.size() <= from) {
        // 没有下一页了，结束
        return Result.ok(Collections.emptyList());
    }
    // 4.1.截取 from ~ end的部分
    List<Long> ids = new ArrayList<>(list.size());
    Map<String, Distance> distanceMap = new HashMap<>(list.size());
    list.stream().skip(from).forEach(result -> {
        // 4.2.获取店铺id
        String shopIdStr = result.getContent().getName();
        ids.add(Long.valueOf(shopIdStr));
        // 4.3.获取距离
        //对应 Redis 参数 WITHDISTANCE，不加 .includeDistance() 该值为 null。
        Distance distance = result.getDistance();
        distanceMap.put(shopIdStr, distance);
    });
    // 5.根据id查询Shop
    String idStr = StrUtil.join(",", ids);
    List<Shop> shops = query().in("id", ids).last("ORDER BY FIELD(id," + idStr + ")").list();
    for (Shop shop : shops) {
        shop.setDistance(distanceMap.get(shop.getId().toString()).getValue());
    }
    // 6.返回
    return Result.ok(shops);
}
```



## **用户签到**
### **BitMap功能**
<font style="color:#333333;">我们针对签到功能完全可以通过mysql来完成，比如说以下这张表</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785595568576-c19236a8-f7b6-4fb8-8f1c-cf54ed7993c4.png" width="624" title="" crop="0,0,1,1" id="ufff5ac65" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">用户一次签到，就是一条记录，假如有1000万用户，平均每人每年签到次数为10次，则这张表一年的数据量为 1亿条</font>

<font style="color:#333333;">每签到一次需要使用（8 + 8 + 1 + 1 + 3 + 1）共22 字节的内存，一个月则最多需要600多字节</font>

<font style="color:#333333;">我们如何能够简化一点呢？其实可以考虑小时候一个挺常见的方案，就是小时候，咱们准备一张小小的卡片，你只要签到就打上一个勾，我最后判断你是否签到，其实只需要到小卡片上看一看就知道了。</font>

<font style="color:#333333;">我们可以采用类似这样的方案来实现我们的签到需求。</font>

<font style="color:#333333;">我们按月来统计用户签到信息，签到记录为1，未签到则记录为0。</font>

<font style="color:#333333;">把每一个 bit 位对应当月的每一天，形成了映射关系。用0和1标示业务状态，这种思路就称为位图（BitMap）。这样我们就用极小的空间，来实现了大量数据的表示</font>

<font style="color:#333333;">Redis中是利用string类型数据结构实现BitMap，单个 String 最大存储容量 = 512MB。因此最大上限是512M，转换为bit则是 2^32个bit位。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785595568692-ad7a0a1f-bbeb-4f4e-810f-aeca6af50d0e.png" width="607" title="" crop="0,0,1,1" id="u66e90df7" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">BitMap的操作命令有：</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">SETBIT：向指定位置（offset）存入一个0或1</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">GETBIT ：获取指定位置（offset）的bit值</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">BITCOUNT ：统计BitMap中值为1的bit位的数量</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">BITFIELD ：操作（查询、修改、自增）BitMap中bit数组中的指定位置（offset）的值</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">BITFIELD_RO ：获取BitMap中 bit 数组，并以十进制形式返回</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">BITOP ：将多个BitMap的结果做位运算（与 、或、异或）</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">BITPOS ：查找bit数组中指定范围内第一个0或1出现的位置</font>

### **实现签到功能**
<font style="color:#333333;">需求：实现签到接口，将当前用户当天签到信息保存到Redis中</font>

<font style="color:#333333;">思路：我们可以把年和月作为 bitMap 的 key，然后保存到一个 bitMap 中，每次签到就到对应的位上把数字从0变成1，只要对应是1，就表明说明这一天已经签到了，反之则没有签到。</font>

<font style="color:#333333;">我们通过接口文档发现，此接口并没有传递任何的参数，没有参数怎么确实是哪一天签到呢？</font>

<font style="color:#333333;">因为当前用户和当天都可以通过后台代码直接获取，然后到对应的地址上去修改 bitMap。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785595571071-6552b939-4a56-45ab-a7f7-7c417ed9ce3a.png" width="583" title="" crop="0,0,1,1" id="ub7069427" class="ne-image" style="font-size: 14px">

+ <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">UserController 类</font>

```java
 @PostMapping("/sign")
 public Result sign(){
    return userService.sign();
 }
```

+ <font style="color:#333333;background-color:rgba(0, 0, 0, 0);">UserServiceImpl 类</font>

```java
@Override
public Result sign() {
    // 1.获取当前登录用户
    Long userId = UserHolder.getUser().getId();
    // 2.获取日期
    LocalDateTime now = LocalDateTime.now();
    // 3.拼接key
    //格式化结果为:202607
    String keySuffix = now.format(DateTimeFormatter.ofPattern(":yyyyMM"));
    String key = USER_SIGN_KEY + userId + keySuffix;
    // 4.获取今天是本月的第几天
    int dayOfMonth = now.getDayOfMonth();
    // 5.写入Redis SETBIT key offset 1
    // true = 将这一天对应的比特位设为 1，代表已签到
    stringRedisTemplate.opsForValue().setBit(key, dayOfMonth - 1, true);
    return Result.ok();
}
```

### **签到统计**
**<font style="color:#333333;">问题1：</font>**<font style="color:#333333;">什么叫做连续签到天数？从最后一次签到开始向前统计，直到遇到第一次未签到为止，计算总的签到次数，就是连续签到天数。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785595569811-18d11f8d-3a28-4059-a95d-75bc78dcf042.png" width="1028" title="" crop="0,0,1,1" id="u3654c5be" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">Java 逻辑代码：获得当前这个月的最后一次签到数据，定义一个计数器，然后不停的向前统计，直到获得第一个非0的数字即可，每得到一个非0的数字计数器+1，直到遍历完所有的数据，就可以获得当前月的签到总天数了</font>

**<font style="color:#333333;">问题2：</font>**<font style="color:#333333;">如何得到本月到今天为止的所有签到数据？</font>

<font style="color:#333333;">BITFIELD key GET u[dayOfMonth] 0</font>

<font style="color:#333333;">假设今天是10号，那么我们就可以从当前月的第一天开始，获得到当前这一天的位数，是10号，那么就是10位，去拿这段时间的数据，就能拿到所有的数据了，那么这10天里边签到了多少次呢？统计有多少个1即可。</font>

**<font style="color:#333333;">问题3：如何从后向前遍历每个bit位？</font>**

<font style="color:#333333;">注意：bitMap 返回的数据是10进制，</font>

<font style="color:#333333;">假如说返回一个数字8，那么我哪儿知道到底哪些是0，哪些是1呢？我们只需要让得到的10进制数字和1做与运算就可以了，因为1只有遇见1 才是1，其他数字都是0 ，我们把签到结果和1进行与操作，每与一次，就把签到结果向右移动一位，依次内推，我们就能完成逐个遍历的效果了。</font>

<font style="color:#333333;">需求：实现下面接口，统计当前用户截止当前时间在本月的连续签到天数</font>

<font style="color:#333333;">有用户有时间我们就可以组织出对应的key，此时就能找到这个用户截止这天的所有签到记录，再根据这套算法，就能统计出来他连续签到的次数了。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785595568949-adebb6aa-8954-4561-8299-68992a4bdfac.png" width="458" title="" crop="0,0,1,1" id="uf8f9413c" class="ne-image" style="font-size: 14px">

+ **<font style="color:#333333;background-color:rgba(0, 0, 0, 0);">UserController 类</font>**

```java
@GetMapping("/sign/count")
public Result signCount(){
    return userService.signCount();
}
```

+ **<font style="color:#333333;background-color:rgba(0, 0, 0, 0);">UserServiceImpl 类</font>**

```java
@Override
public Result signCount() {
    // 1.获取当前登录用户
    Long userId = UserHolder.getUser().getId();
    // 2.获取日期
    LocalDateTime now = LocalDateTime.now();
    // 3.拼接key
    String keySuffix = now.format(DateTimeFormatter.ofPattern(":yyyyMM"));
    String key = USER_SIGN_KEY + userId + keySuffix;
    // 4.获取今天是本月的第几天
    int dayOfMonth = now.getDayOfMonth();
    // 5.获取本月截止今天为止的所有的签到记录，返回的是一个十进制的数字
    //BITFIELD sign:5:202203 GET u14 0，从下标0开始，截取14 个连续无符号 bit
    List<Long> result = stringRedisTemplate.opsForValue().bitField(
            key,
            BitFieldSubCommands.create()
                    .get(BitFieldSubCommands.BitFieldType.unsigned(dayOfMonth)).valueAt(0)
    );
    if (result == null || result.isEmpty()) {
        // 没有任何签到结果
        return Result.ok(0);
    }
    //获得十进制
    Long num = result.get(0);
    if (num == null || num == 0) {
        return Result.ok(0);
    }
    // 6.循环遍历
    int count = 0;
    while (true) {
        // 6.1.让这个数字与1做与运算，得到数字的最后一个bit位  // 判断这个bit位是否为0
        if ((num & 1) == 0) {
            // 如果为0，说明未签到，结束
            break;
        }else {
            // 如果不为0，说明已签到，计数器+1
            count++;
        }
        // 把数字右移一位，抛弃最后一个bit位，继续下一个bit位
        num >>>= 1;
    }
    return Result.ok(count);
}
```

### **bitmap解决缓存穿透的方案**
<font style="color:#333333;">回顾</font>**<font style="color:#333333;">缓存穿透</font>**<font style="color:#333333;">：</font>

<font style="color:#333333;">发起了一个数据库不存在的，redis里边也不存在的数据，通常你可以把他看成一个攻击</font>

<font style="color:#333333;">解决方案：</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">判断id<0</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">如果数据库是空，那么就可以直接往redis里边把这个空数据缓存起来</font>

<font style="color:#333333;">第一种解决方案：遇到的问题是如果用户访问的是id不存在的数据，则此时就无法生效</font>

<font style="color:#333333;">第二种解决方案：遇到的问题是：如果是不同的id那就可以防止下次过来直击数据</font>

<font style="color:#333333;">所以我们如何解决呢？</font>

<font style="color:#333333;">我们可以将数据库的数据，所对应的id写入到一个list集合中，当用户过来访问的时候，我们直接去判断list中是否包含当前的要查询的数据，如果说用户要查询的id数据并不在list集合中，则直接返回，如果list中包含对应查询的id数据，则说明不是一次缓存穿透数据，则直接放行。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785595571065-175a1ccc-155f-4e30-b260-c86e181af252.png" width="717" title="" crop="0,0,1,1" id="ud4d32369" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">现在的问题是这个主键其实并没有那么短，而是很长的一个 主键</font>

<font style="color:#333333;">哪怕你单独去提取这个主键，但是在11年左右，淘宝的商品总量就已经超过10亿个</font>

<font style="color:#333333;">所以如果采用以上方案，这个list也会很大，所以我们可以使用bitmap来减少list的存储空间</font>

<font style="color:#333333;">我们可以把list数据抽象成一个非常大的bitmap，我们不再使用list，而是将db中的id数据利用哈希思想，比如：</font>

<font style="color:#333333;">id % bitmap.size = 算出当前这个id对应应该落在bitmap的哪个索引上，然后将这个值从0变成1，然后当用户来查询数据时，此时已经没有了list，让用户用他查询的id去用相同的哈希算法， 算出来当前这个id应当落在bitmap的哪一位，然后判断这一位是0，还是1，如果是0则表明这一位上的数据一定不存在， 采用这种方式来处理，需要重点考虑一个事情，就是误差率，所谓的误差率就是指当发生哈希冲突的时候，产生的误差。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785595572743-4a7c1d1a-223f-4c7b-9a56-2f60740ff4b6.png" width="659" title="" crop="0,0,1,1" id="uf519d20a" class="ne-image" style="font-size: 14px">



## **UV统计**
### **HyperLogLog**
<font style="color:#333333;">首先我们搞懂两个概念：</font>

+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">UV：全称Unique Visitor，也叫独立访客量，是指通过互联网访问、浏览这个网页的自然人。1天内同一个用户多次访问该网站，只记录1次。</font>
+ <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">PV：全称Page View，也叫页面访问量或点击量，用户每访问网站的一个页面，记录1次PV，用户多次打开页面，则记录多次PV。往往用来衡量网站的流量。</font>

<font style="color:#333333;">通常来说PV会比UV大很多，所以衡量同一个网站的访问量，我们需要综合考虑很多因素，所以我们只是单纯的把这两个值作为一个参考值。</font>

<font style="color:#333333;">UV统计在服务端做会比较麻烦，因为要判断该用户是否已经统计过了，需要将统计过的用户信息保存。但是如果每个访问的用户都保存到Redis中，数据量会非常恐怖，那怎么处理呢？</font>

<font style="color:#333333;">Hyperloglog(HLL)是从Loglog算法派生的概率算法，专门用来统计基数（去重数量） 的数据结构。而不需要存储其所有值。相关算法原理大家可以参考：</font>[<u><font style="color:rgb(3, 102, 214);">https://juejin.cn/post/6844903785744056333#heading-0Redis</font></u>](https://juejin.cn/post/6844903785744056333#heading-0Redis)

<font style="color:#333333;">Redis中的HLL是基于string结构实现的，单个HLL的内存</font>**<font style="color:#333333;">永远小于16kb</font>**<font style="color:#333333;">，</font>**<font style="color:#333333;">内存占用低</font>**<font style="color:#333333;">的令人发指！作为代价，其测量结果是概率性的，</font>**<font style="color:#333333;">有小于0.81％的误差</font>**<font style="color:#333333;">。不过对于UV统计来说，这完全可以忽略。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785595585543-e6db580b-da89-4d54-86e0-3ae06dd05819.png" width="639" title="" crop="0,0,1,1" id="u24bb52aa" class="ne-image" style="font-size: 14px">

### **测试百万数据的统计**
<font style="color:#333333;">测试思路：我们直接利用单元测试，向HyperLogLog中添加100万条数据，看看内存占用和统计效果如何。</font>

<img src="https://ashiho-blog.oss-cn-shenzhen.aliyuncs.com/blogPic/1785595585756-595932ba-65b9-4cbe-9cf8-eff33b8b3f5e.png" width="612" title="" crop="0,0,1,1" id="u56662ebc" class="ne-image" style="font-size: 14px">

<font style="color:#333333;">经过测试：我们会发生他的误差是在允许范围内，并且内存占用极小。</font>