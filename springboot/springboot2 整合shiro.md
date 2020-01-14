[TOC]

# springboot2 整合shiro

- 1、完全使用注解来实现登陆身份验证和访问权限控制
- 2、完全使用URL配置来实现登陆身份验证和访问权限控制
- 3、使用URL配置来实现登陆身份验证、使用注解来实现访问权限控制

#### 用户登录配置步骤

1.提供一个Realm实例

2.配置一个SecurityManage，在Security中配置Realm。

3.配置一个shiroFilterFactoryBean，在shiroFilterFactoryBean指定拦截规则

4.配置登录和测试接口



其中，ShiroFilterFactoryBean 的配置稍微多一些，配置含义如下

- setSecurityManager 表示指定 SecurityManager。
- setLoginUrl表示指定登录页面。
- setSuccessUrl表示指定登录成功页面
- 接下来的Map中配置了路径拦截规则，注意，要有序。



1. 创建工程，导入shiro相关依赖，完整的pom.xml依赖如下：

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.apache.shiro</groupId>
        <artifactId>shiro-web</artifactId>
        <version>1.4.0</version>
    </dependency>
    <dependency>
        <groupId>org.apache.shiro</groupId>
        <artifactId>shiro-spring</artifactId>
        <version>1.4.0</version>
    </dependency>
</dependencies>
```

2.  创建realm

```java
public class CustomRealm extends AuthorizingRealm {

    @Autowired
    private UserInfoService userService;
    @Autowired
    private UserRoleService userRoleService;
    @Autowired
    private RolePermService rolePermService;

    /**
     * 告诉shiro如何根据获取到的用户信息中的密码和盐值来校验密码
     */
    {
        //设置用于匹配密码的CredentialsMatcher
        HashedCredentialsMatcher hashMatcher = new HashedCredentialsMatcher();
        hashMatcher.setHashAlgorithmName("md5");
        hashMatcher.setStoredCredentialsHexEncoded(true);
        //加密的次数
        hashMatcher.setHashIterations(1024);
        this.setCredentialsMatcher(hashMatcher);
    }


    /**
     *  定义如何获取用户的角色和权限的逻辑，给shiro做权限判断
     */
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principals) {
        System.out.println("权限配置-->MyShiroRealm.doGetAuthorizationInfo()");
        UserInfo userInfo = (UserInfo) principals.getPrimaryPrincipal();
        SimpleAuthorizationInfo simpleAuthorizationInfo = new SimpleAuthorizationInfo();
        //获取当前用户的角色与权限，让simpleAuthorizationInfo去验证
        for (SysRole sysRole : userInfo.getRoleList()) {
            simpleAuthorizationInfo.addRole(sysRole.getRoleName());
            for (SysPerm sysPermission : sysRole.getSysPermList()) {
                simpleAuthorizationInfo.addStringPermission(sysPermission.getPermName());
            }
        }
        return simpleAuthorizationInfo;
    }


    /**
     * 定义如何获取用户信息的业务逻辑，给shiro做登录
     */
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken token) throws AuthenticationException {
        String username = (String) token.getPrincipal();
        //String password = new String((char[]) token.getCredentials());
        UserInfo user = userService.selectByName(username);
        if (user == null) {
            throw new UnknownAccountException("用户名或密码错误！");
        }

        //UserInfo userDB = userService.selectByName(username);

        //查询用户的角色和权限存到SimpleAuthenticationInfo中，这样在其它地方
//        //SecurityUtils.getSubject().getPrincipal()就能拿出用户的所有信息，包括角色和权限
//        List<String> permList = rolePermService.getPermsByUserId(userDB.getId());
//        List<String> roleList = userRoleService.getRolesByUserId(userDB.getId());
//        Set<String> roles = new HashSet(roleList);
//        Set<String> perms = new HashSet(permList);
//        userDB.setRoles(roles);
//        userDB.setPerms(perms);

//        SimpleAuthenticationInfo info = new SimpleAuthenticationInfo(userDB, userDB.getPassword(), getName());
//        info.setCredentialsSalt(ByteSource.Util.bytes(userDB.getSalt()));
        SimpleAuthenticationInfo info = new SimpleAuthenticationInfo(user, user.getPassword(), ByteSource.Util.bytes(user.getCredentialsSalt()),getName());
        return info;

    }


}
```



shiroConfig：过滤器的规则

![](https://note.youdao.com/yws/public/resource/ed3ed7bda9e698e627156617e6359390/xmlnote/253E5D31D5914F9EA2FE2C30AF1AC277/9434)

注意：anon, authc, authcBasic, user 是第一组认证过滤器，perms, port, rest, roles, ssl 是第二组授权过滤器，要通过授权过滤器，就先要完成登陆认证操作（即先要完成认证才能前去寻找授权) 才能走第二组授权器（例如访问需要 roles 权限的 url，如果还没有登陆的话，会直接跳转到 shiroFilterFactoryBean.setLoginUrl(); 设置的 url ）





再说下 UsernamePasswordToken 类，我们可以从该对象拿到登陆时的用户名和密码（登陆时会使用 new UsernamePasswordToken(username, password);），而 get 用户名或密码有以下几个方法：

```java
token.getUsername()  //获得用户名 String
token.getPrincipal() //获得用户名 Object 
token.getPassword()  //获得密码 char[]
token.getCredentials() //获得密码 Object
```

配置shiro易错点：

- 配置过滤器时需要配置登录的Url为anon;

- mybatis 在配置数据库字段和实体类字段映射时不能用int

- 今天下午写代码时发现一直报错，找了半天都没找到错误原因。

  最后才发现原来是XML配置错误，某条属性的JdbcType我按照以前ibatis的习惯写的int，但是Mybatis不识别的。

  上Mybatis官网翻了翻才发现原来Mybatis的JdbcType全是大写，而且没有INT，只有**INTEGER**