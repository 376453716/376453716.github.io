#### @EnableMvcSecurity
开启spring security，import WebSecurityConfiguration

#### WebSecurityConfiguration
通过WebSecurity配置创建FilterChainProxy securityFilterChains

#### WebSecurity
由WebSecurityConfiguration创建，用于创建Spring Security Filter Chain（FilterChainProxy）.

请求由DelegatingFilterProxy委派给springSecurityFilterChain.

通过实现WebSecurityConfigurer或者继承WebSecurityConfigurerAdapter可以自定义配置WebSecurity

#### HttpSecurity
配置http请求是否需要安全措施，使用RequestMatcher匹配需要安全的请求
```
    @Override
   	protected void configure(HttpSecurity http) throws Exception {
   		http.authorizeRequests().antMatchers("/**").hasRole("USER").and().formLogin();
   	}

   	@Override
   	protected void configure(AuthenticationManagerBuilder auth) throws Exception {
   		auth.inMemoryAuthentication().withUser("user").password("password").roles("USER");
   	}
```

#### AbstractSecurityWebApplicationInitializer
1. servlet3.0+容器初始化时会调用实现了ServletContainerInitializer接口的类
2. Spring WebApplicationInitializer初始化
3. 创建DelegatingFilterProxy，注册SpringSecurityFilterChain到ServletContext中，设置代理filterName-->springSecurityFilterChain

#### DelegatingFilterProxy
1. 根据targetBeanName在spring context中获取目标filter，调用目标filter

#### DefaultSecurityFilterChain
通过SecurityBuilder build后生成springSecurityFilterChain

#### Filter
DelegatingFilterProxy

springSecurityFilterChain

#### filterChain
1. AccessAuthenticationProcessingFilter 访问令牌认证过滤器 
2. CasAuthenticationFilter 处理CAS ticket Ant [pattern='/login/cas']
3. CheckLoginFilter Ant [pattern='/checkLogin']
4. UsernamePasswordAuthenticationFilter

#### AuthenticationManager

#### AccessAuthenticationProvider

#### UserDetailsService


#### cas
![spring_security_cas](img/spring_security/cas.png)

![spring_security](img/spring_security/spring_security.png)

![spring_security_config](img/spring_security/spring_security_config.png)


#### concept
- Authentication: token for 身份验证请求/经过身份验证的主体，请求被认证后，存储在SecurityContext线程变量中，由SecurityContextHolder管理，
- UsernamePasswordAuthenticationToken extends AbstractAuthenticationToken： principal 用户名 credentials 密码