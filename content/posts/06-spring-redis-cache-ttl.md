---
title: "Spring Boot: set separate TTL for each Redis cache"
date: 2022-09-14T09:54:29+03:00
summary: "Set separate Time-to-live for each Redis cache in Spring"
description: "Set separate Time-to-live for each Redis cache in Spring"
icon: /icons/icons8-source-code-50.png
tags:
  - Spring
  - Redis
  - Cache
---

I was working on setting up Redis cache for our application at work. Each cache collection had to have
separate Time-to-live (the keys had to expire at different times). We use the standard Spring Boot caching 
mechanism with `@Cachable`. In order to configure it, register new `RedisCacheManager` bean and when creating
the instance provide a map with separate configuration for each cache, and optionally a default TTL configuration 
for each that is not set explicitly. Here is a code sample:

```java
@Bean
public RedisCacheManager redisCacheManager(RedisConnectionFactory connectionFactory) {
    log.info("Creating custom cache configuration");
    Map<String, RedisCacheConfiguration> cacheNamesConfigurationMap = new HashMap<>();
    //set cache with name "cache name 1" to expire after 100 seconds
    cacheNamesConfigurationMap.put("cache name 1",
            RedisCacheConfiguration.defaultCacheConfig().entryTtl(Duration.ofSeconds(100)));
    //set cache with name "cache name 2" to expire after 200 seconds
    cacheNamesConfigurationMap.put("cache name 2",
            RedisCacheConfiguration.defaultCacheConfig().entryTtl(Duration.ofSeconds(200)));

    return new RedisCacheManager(RedisCacheWriter.lockingRedisCacheWriter(connectionFactory),
            //set default expiration for all other caches to 300 seconds (optional)
            RedisCacheConfiguration.defaultCacheConfig().entryTtl(Duration.ofSeconds(300)),
            cacheNamesConfigurationMap);
}
```