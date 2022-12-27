---
title: "Spring Boot: ignore deserialization errors with Redis cache"
date: 2022-09-14T09:54:29+03:00
summary: "Ignore deserialization errors with Redis cache in Spring"
description: "Ignore deserialization errors with Redis cache in Spring"
icon: /icons/icons8-source-code-50.png
tags:
  - Spring
  - Redis
  - Cache
---

To store java objects in Redis for caching with Srping Boot they need to implement `Serializable` 
and have `serialVersionId`. If you update the object and change its `serialVersionId` Spring will start 
throwing deserialization errors. You can clear the cache during deployment but that is not ideal as it presents 
additional steps especially if you are using CD/CI. The solution I used is to ignore the errors, Spring will
act like there is no cache, and it will override the cache key. To configure it, you need to extend
`CachingConfigurerSupport` and replace `CacheErrorHandler` with custom implementation:

```java
@Configuration
@Slf4j
public class CacheConfiguration extends CachingConfigurerSupport {

    /**
     * ignore cache get errors
     */
    @Slf4j
    private static class RelaxedCacheErrorHandler extends SimpleCacheErrorHandler {
        @Override
        public void handleCacheGetError(RuntimeException exception, Cache cache, Object key) {
            log.warn("Deserialization error on {} cache. Refreshing", cache.getName());
        }
    }

    @Override
    public CacheErrorHandler errorHandler() {
        return new RelaxedCacheErrorHandler();
    }

}
```