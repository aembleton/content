Managing your application....with JMX
#####################################
:date: 2010-04-05 04:28
:author: Laurence Noton
:category: Development

Many times you will want to obtain access to your running application to
change something on the fly. Typically this might be changing a runtime
property which alters the behaviour of the system, such as the location
of an external service, or the location of a file prehaps.

Recently we needed to reload a simple cache of data in a Java
application which was read-only. The actual data source was managed by
another set of .NET applications which was responsible for updating the
information. When updates happended, we needed a way to ensure our cache
was synchronised. Of course there are quite a few ways to do this such
as having expirying caches etc, however we chose to expose a method via
the Java Management API JMX to resolve this problem.

Imagine a basic cache (using the singleton pattern) :

package com.laurencenoton.jmx;

``import java.util.HashMap;``

import java.util.Map;

``public class MyCache {``

private Map cache = new HashMap();

private static MyCache singleton = new MyCache();

public Object getValue(String key) {

return cache.get(key);

}

public void refreshCache() {

// go to database and refresh cache

}

public static MyCache getInstance() {

return singleton;

}

private MyCache() {

}

}

JMX Interface:

``package com.laurencenoton.jmx;``

``public interface MyCacheManagerMBean {``

boolean refreshCache();

}

JMX Implementation

package com.laurencenoton.jmx;

``public class MyCacheManager implements MyCacheManagerMBean {``

``public boolean refreshCache() {``

MyCache cache = MyCache.getInstance();

cache.refreshCache();

return true;

}

``}``

Registering the JMX Bean - best way is through a Servlet Listener:

package com.laurencenoton.jmx.web;

``import javax.management.MBeanServer;``

import javax.management.MalformedObjectNameException;

import javax.management.ObjectName;

import javax.naming.InitialContext;

import javax.servlet.ServletContextEvent;

import javax.servlet.ServletContextListener;

``import com.laurencenoton.jmx.MyCacheManager;``

public class JmxServletContextListener implements ServletContextListener
{

private InitialContext ctx;

private MBeanServer server;

{

try {

ctx = new InitialContext();

server =
MBeanServer.class.cast(ctx.lookup("java:comp/env/jmx/runtime"));

} catch (Exception e) {

}

}

public void contextInitialized(ServletContextEvent sce) {

try {

server.registerMBean(new MyCacheManager(), getObjectName(sce));

} catch (Exception e) {

}

}

public void contextDestroyed(ServletContextEvent sce) {

try {

if (server.isRegistered(getObjectName(sce))) {

server.unregisterMBean(getObjectName(sce));

}

} catch (Exception e) {

}

}

`` private ObjectName getObjectName(ServletContextEvent sce) throws MalformedObjectNameException, NullPointerException {``

String appName = sce.getServletContext().getContextPath().substring(1);

return new ObjectName("com.laurencenoton.jmx:type=MyCacheManager,name="
+ appName + "CacheManager");

}

}


