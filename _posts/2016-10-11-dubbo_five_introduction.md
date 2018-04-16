---
layout:     post
title:      "分布式框架Tudou-Dubbo(五)"
subtitle:   "分布式框架Tudou(五) Shiro 和 Session 管理"
date:       2016-10-11 7:00:00
author:     "DavidWang"
header-img: "img/post-bg-e2e-ux.jpg"
catalog: true
tags:
    - Dubbo
    - Java
--- 

## Dubbo Shiro 管理

### Dubbo Shiro 配置

**此工程是使用Shiro 作为安全框架，使用Shiro来管理路由的访问权限和作用域**

![img](/img/in-post/java_introduction/dubbo_introduction_19.png)

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:util="http://www.springframework.org/schema/util"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
	    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

    <description>tudou-upms</description>

    <!-- Shiro的Web过滤器 -->
    <bean id="shiroFilter" class="org.apache.shiro.spring.web.ShiroFilterFactoryBean">
        <property name="securityManager" ref="securityManager"/>
        <!-- Shiro 认证中心地址 -->
        <property name="loginUrl" value="${tudou.sso.server.url}"/>
        <!-- Shiro 登录成功回调-->
        <property name="successUrl" value="${tudou.upms.successUrl}"/>
        <!-- Shiro 未授权回调地址-->
        <property name="unauthorizedUrl" value="${tudou.upms.unauthorizedUrl}"/>
        <property name="filters">
            <util:map>
            	<!-- 自定义Shiro 过滤器-->
                <entry key="authc" value-ref="upmsAuthenticationFilter"/>
            </util:map>
        </property>
        <property name="filterChainDefinitions">
            <value>
            	<!-- 过滤路由地址-和过滤器 -->
                /manage/** = upmsSessionForceLogout,authc
                /druid/** = user
                /act/** = user
                /sso/set_password = user
                <!--/swagger-ui.html = user-->
                /** = anon
            </value>
        </property>
    </bean>

    <!-- 重写authc过滤器 -->
    <bean id="upmsAuthenticationFilter" class="com.tudou.upms.client.shiro.filter.UpmsAuthenticationFilter"/>

    <!-- 强制退出会话过滤器 -->
    <bean id="upmsSessionForceLogout" class="com.tudou.upms.client.shiro.filter.UpmsSessionForceLogoutFilter"/>

    <!-- 安全管理器 -->
    <bean id="securityManager" class="org.apache.shiro.web.mgt.DefaultWebSecurityManager">
        <property name="realms">
            <list><ref bean="upmsRealm"/></list>
        </property>
        <property name="sessionManager" ref="sessionManager"/>
        <property name="rememberMeManager" ref="rememberMeManager"/>
    </bean>

    <!-- realm实现，继承自AuthorizingRealm -->
    <bean id="upmsRealm" class="com.tudou.upms.client.shiro.realm.UpmsRealm"></bean>

    <!-- 会话管理器 -->
    <bean id="sessionManager" class="org.apache.shiro.web.session.mgt.DefaultWebSessionManager">
        <!-- 全局session超时时间 -->
        <property name="globalSessionTimeout" value="${tudou.upms.session.timeout}"/>
        <!-- sessionDAO -->
        <property name="sessionDAO" ref="sessionDAO"/>
        <!-- Session Id Cookie 是否启动 -->
        <property name="sessionIdCookieEnabled" value="true"/>
        <!-- Session Id Cookie 配置 -->
        <property name="sessionIdCookie" ref="sessionIdCookie"/>
        <!-- 是否开启会话验证器 -->
        <property name="sessionValidationSchedulerEnabled" value="false"/>
        <property name="sessionListeners">
            <list><ref bean="sessionListener"/></list>
        </property>
        <property name="sessionFactory" ref="sessionFactory"/>
    </bean>

    <!-- 会话DAO，可重写，持久化session -->
    <bean id="sessionDAO" class="com.tudou.upms.client.shiro.session.UpmsSessionDao"/>

    <!-- 会话Cookie模板 -->
    <bean id="sessionIdCookie" class="org.apache.shiro.web.servlet.SimpleCookie">
        <!-- 不会暴露给客户端 -->
        <property name="httpOnly" value="true"/>
        <!-- 设置Cookie的过期时间，秒为单位，默认-1表示关闭浏览器时过期Cookie -->
        <property name="maxAge" value="-1"/>
        <!-- Cookie名称 -->
        <property name="name" value="${tudou.upms.session.id}"/>
    </bean>

    <!-- 会话监听器 -->
    <bean id="sessionListener" class="com.tudou.upms.client.shiro.listener.UpmsSessionListener"/>

    <!-- session工厂 -->
    <bean id="sessionFactory" class="com.tudou.upms.client.shiro.session.UpmsSessionFactory"/>

    <!-- rememberMe管理器 -->
    <bean id="rememberMeManager" class="org.apache.shiro.web.mgt.CookieRememberMeManager">
        <!-- rememberMe cookie加密的密钥 建议每个项目都不一样 默认AES算法 密钥长度（128 256 512 位）-->
        <property name="cipherKey" value="#{T(org.apache.shiro.codec.Base64).decode('4AvVhmFLUs0KTA3Kprsdag==')}"/>
        <property name="cookie" ref="rememberMeCookie"/>
    </bean>

    <!-- rememberMe缓存cookie -->
    <bean id="rememberMeCookie" class="org.apache.shiro.web.servlet.SimpleCookie">
        <constructor-arg value="rememberMe"/>
        <!-- 不会暴露给客户端 -->
        <property name="httpOnly" value="true"/>
        <!-- 记住我cookie生效时间 -->
        <property name="maxAge" value="${tudou.upms.rememberMe.timeout}"/>
    </bean>

    <!-- 设置SecurityUtils，相当于调用SecurityUtils.setSecurityManager(securityManager) -->
    <bean class="org.springframework.beans.factory.config.MethodInvokingFactoryBean">
        <property name="staticMethod" value="org.apache.shiro.SecurityUtils.setSecurityManager"/>
        <property name="arguments" ref="securityManager"/>
    </bean>

    <!-- 开启Shiro Spring AOP权限注解@RequiresPermissions的支持 -->
    <bean class="org.springframework.aop.framework.autoproxy.DefaultAdvisorAutoProxyCreator" depends-on="lifecycleBeanPostProcessor"/>
    <bean class="org.apache.shiro.spring.security.interceptor.AuthorizationAttributeSourceAdvisor">
        <property name="securityManager" ref="securityManager"/>
    </bean>

    <!-- Shiro生命周期处理器-->
    <bean id="lifecycleBeanPostProcessor" class="org.apache.shiro.spring.LifecycleBeanPostProcessor"/>

</beans>
```

**主要看一下SeesionDao是如何存储和读取seesion**

![img](/img/in-post/java_introduction/dubbo_introduction_21.png)

```
    /**
     * 登录时创建Session
     * @param session
     * @return
     */
	@Override
    protected Serializable doCreate(Session session) {
        Serializable sessionId = generateSessionId(session);
        assignSessionId(session, sessionId);
        RedisUtil.set(TUDOU_UPMS_SHIRO_SESSION_ID + "_" + sessionId, SerializableUtil.serialize(session), (int) session.getTimeout() / 1000);
        _log.debug("doCreate >>>>> sessionId={}", sessionId);
        return sessionId;
    }

    /**
     * 读取Session
     * @param session
     * @return
     */
    @Override
    protected Session doReadSession(Serializable sessionId) {
        String session = RedisUtil.get(TUDOU_UPMS_SHIRO_SESSION_ID + "_" + sessionId);
        _log.debug("doReadSession >>>>> sessionId={}", sessionId);
        return SerializableUtil.deserialize(session);
    }

    /**
     * 更新Session
     * @param session
     * @return
     */
    @Override
    protected void doUpdate(Session session) {
        // 如果会话过期/停止 没必要再更新了
        if(session instanceof ValidatingSession && !((ValidatingSession)session).isValid()) {
            return;
        }
        // 更新session的最后一次访问时间
        UpmsSession upmsSession = (UpmsSession) session;
        UpmsSession cacheUpmsSession = (UpmsSession) doReadSession(session.getId());
        if (null != cacheUpmsSession) {
            upmsSession.setStatus(cacheUpmsSession.getStatus());
            upmsSession.setAttribute("FORCE_LOGOUT", cacheUpmsSession.getAttribute("FORCE_LOGOUT"));
        }
        //如果需要持续操作可以在线不需要屏蔽，否则屏蔽
        RedisUtil.set(TUDOU_UPMS_SHIRO_SESSION_ID + "_" + session.getId(), SerializableUtil.serialize(session), (int) session.getTimeout() / 1000);
        // 更新TUDOU_UPMS_SERVER_SESSION_ID、TUDOU_UPMS_SERVER_CODE过期时间 TODO
        _log.debug("doUpdate >>>>> sessionId={}", session.getId());
    }

    
    /**
     * 删除Session
     * @param session
     * @return
     */
    @Override
    protected void doDelete(Session session) {
        String sessionId = session.getId().toString();
        String upmsType = ObjectUtils.toString(session.getAttribute(UpmsConstant.UPMS_TYPE));
        if ("client".equals(upmsType)) {
            // 删除局部会话和同一code注册的局部会话
            String code = RedisUtil.get(TUDOU_UPMS_CLIENT_SESSION_ID + "_" + sessionId);
            Jedis jedis = RedisUtil.getJedis();
            jedis.del(TUDOU_UPMS_CLIENT_SESSION_ID + "_" + sessionId);
            jedis.srem(TUDOU_UPMS_CLIENT_SESSION_IDS + "_" + code, sessionId);
            jedis.close();
        }
        if ("server".equals(upmsType)) {
            // 当前全局会话code
            String code = RedisUtil.get(TUDOU_UPMS_SERVER_SESSION_ID + "_" + sessionId);
            // 清除全局会话
            RedisUtil.remove(TUDOU_UPMS_SERVER_SESSION_ID + "_" + sessionId);
            // 清除code校验值
            RedisUtil.remove(TUDOU_UPMS_SERVER_CODE + "_" + code);
            // 清除所有局部会话
            Jedis jedis = RedisUtil.getJedis();
            Set<String> clientSessionIds = jedis.smembers(TUDOU_UPMS_CLIENT_SESSION_IDS + "_" + code);
            for (String clientSessionId : clientSessionIds) {
                jedis.del(TUDOU_UPMS_CLIENT_SESSION_ID + "_" + clientSessionId);
                jedis.srem(TUDOU_UPMS_CLIENT_SESSION_IDS + "_" + code, clientSessionId);
            }
            _log.debug("当前code={}，对应的注册系统个数：{}个", code, jedis.scard(TUDOU_UPMS_CLIENT_SESSION_IDS + "_" + code));
            jedis.close();
            // 维护会话id列表，提供会话分页管理
            RedisUtil.lrem(TUDOU_UPMS_SERVER_SESSION_IDS, 1, sessionId);
        }
        // 删除session
        RedisUtil.remove(TUDOU_UPMS_SHIRO_SESSION_ID + "_" + sessionId);
        _log.debug("doUpdate >>>>> sessionId={}", sessionId);
    }

    /**
     * 获取会话列表
     * @param offset
     * @param limit
     * @return
     */
    public Map getActiveSessions(int offset, int limit) {
        Map sessions = new HashMap();
        Jedis jedis = RedisUtil.getJedis();
        // 获取在线会话总数
        long total = jedis.llen(TUDOU_UPMS_SERVER_SESSION_IDS);
        // 获取当前页会话详情
        List<String> ids = jedis.lrange(TUDOU_UPMS_SERVER_SESSION_IDS, offset, (offset + limit - 1));
        List<Session> rows = new ArrayList<>();
        for (String id : ids) {
            String session = RedisUtil.get(TUDOU_UPMS_SHIRO_SESSION_ID + "_" + id);
            // 过滤redis过期session
            if (null == session) {
                RedisUtil.lrem(TUDOU_UPMS_SERVER_SESSION_IDS, 1, id);
                total = total - 1;
                continue;
            }
             rows.add(SerializableUtil.deserialize(session));
        }
        jedis.close();
        sessions.put("total", total);
        sessions.put("data", rows);
        return sessions;
    }

    /**
     * 强制退出
     * @param ids
     * @return
     */
    public int forceout(String ids) {
        String[] sessionIds = ids.split(",");
        for (String sessionId : sessionIds) {
            // 会话增加强制退出属性标识，当此会话访问系统时，判断有该标识，则退出登录
            String session = RedisUtil.get(TUDOU_UPMS_SHIRO_SESSION_ID + "_" + sessionId);
            UpmsSession upmsSession = (UpmsSession) SerializableUtil.deserialize(session);
            upmsSession.setStatus(UpmsSession.OnlineStatus.force_logout);
            upmsSession.setAttribute("FORCE_LOGOUT", "FORCE_LOGOUT");
            RedisUtil.set(TUDOU_UPMS_SHIRO_SESSION_ID + "_" + sessionId, SerializableUtil.serialize(upmsSession), (int) upmsSession.getTimeout() / 1000);
        }
        return sessionIds.length;
    }

    /**
     * 更改在线状态
     *
     * @param sessionId
     * @param onlineStatus
     */
    public void updateStatus(Serializable sessionId, UpmsSession.OnlineStatus onlineStatus) {
        UpmsSession session = (UpmsSession) doReadSession(sessionId);
        if (null == session) {
            return;
        }
        session.setStatus(onlineStatus);
        RedisUtil.set(TUDOU_UPMS_SHIRO_SESSION_ID + "_" + session.getId(), SerializableUtil.serialize(session), (int) session.getTimeout() / 1000);
    }

```

### Dubbo Shiro Session 授权方式

**系统和授权中心的数据权限方式-单点登录**

![img](/img/in-post/java_introduction/dubbo_introduction_23.png)

![img](/img/in-post/java_introduction/dubbo_introduction_22.png)

![img](/img/in-post/java_introduction/dubbo_introduction_24.png)
