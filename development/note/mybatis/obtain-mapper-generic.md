---
title: 【mybatis随手记】-获取Mapper接口上的泛型
description: 获取Mapper接口上的泛型
published: true
date: 2023-10-16T09:16:58.871Z
tags: mybatis, note
editor: markdown
dateCreated: 2023-10-16T09:08:12.634Z
---

# 【mybatis随手记】获取Mapper接口上的泛型

## 一、背景说明
1. 项目中的代码通过代码生成器统一生成
2. mybatis父接口中定义了一些统一的方法，并限定了Model泛型
```java
public interface BaseMapper<T> {
   /**
     * 查询列表
     * @param lookup query condition
     * @return list
     */
    List<T> list(Lookup lookup);
    // 其他一些统一的方法 略
}
```

2. 在运行时时候，期望通过Model class调用mapper的一些通用方法
3. 所以需要在运行时候获取Mapper接口上的泛型类型，反向获取到Mapper

## 实现代码
1. 通过spring注入全部的BaseMapper的子接口列表
2. 获取接口上的泛型，放如Map中，key为Model class，value为mapper
3. 注意事项：
  - 3.1 注入spring中的Mapper，本质上为一个MapperProxy，而不是我们的原始Mapper
  - 3.2 MapperProxy上的泛型`<T> `才是我们真正的Mapper接口，
  - 3.3 所以需要获取到我们的真实Mapper后，再获取Mapper上的泛型即可。

```java
/**
 * 描述: 所有mapper的通用查询汇总：可以根据model class 获取对应的mapper，然后调用BaseMapper的通用数据库操作
 * @author Vic.xu
 * @date 2023-10-16 14:55
 */
public class CommonBaseMapperService implements InitializingBean {

    private static final Logger LOGGER = LoggerFactory.getLogger(CommonBaseMapperService.class);
    /**
     * 注入全部的BaseMapper 的子接口
     */
    @Autowired
    private List<BaseMapper<?>> mappers;

    private Map<Class<?>, BaseMapper<?>> mapperMap;

    @Override
    public void afterPropertiesSet() throws Exception {
        LOGGER.info("all BaseMapper size is {}", mappers.size());
        mapperMap = new HashMap<>();
        for (BaseMapper<?> mapper : mappers) {
            Class<?> modelGeneric = getModelGenericFromMapper(mapper);
            if (mapperMap.containsKey(modelGeneric)) {
                LOGGER.warn("习总中包含关于{}对象的BaseMapper超过一个，请注意检查", modelGeneric.getName());
                continue;
            }
            mapperMap.putIfAbsent(modelGeneric, mapper);
        }
        LOGGER.info("all BaseMapper no duplicate size is {}", mapperMap.size());
    }

    /**
     *  通过mapper获取mapper上的model泛型
     * @param mapper spring里注入的mapper，实际上为 MapperProxy,需要通过它获取到真实的mapper接口类，然后获取mapper上的泛型
     * @see MapperProxy
     * @return model  generic
     */
    private Class<?> getModelGenericFromMapper(BaseMapper<?> mapper) {
        Type[] genericInterfaces = mapper.getClass().getGenericInterfaces();
        //MapperProxy 上的泛型记为mapper 接口类型
        Class<?> realMapperClass = (Class<?>) genericInterfaces[0];
        Type[] types = realMapperClass.getGenericInterfaces();
        ParameterizedType parameterizedType = (ParameterizedType) types[0];
        Type type = parameterizedType.getActualTypeArguments()[0];
        return (Class<?>) type;
    }

    @SuppressWarnings("unchecked")
    public <T> BaseMapper<T> getMapper(Class<T> modelClass) {
        if (!mapperMap.containsKey(modelClass)) {
            throw new CommonException("系统中不存在" + modelClass.getName() + "对象队形的Mapper!");
        }
        return (BaseMapper<T>) mapperMap.get(modelClass);
    }

    /* ****************** 重写BaseMapper中的相关方法***************************************/

    /**
     * 查询列表
     * @param lookup query condition
     * @return list
     */
    public <T> List<T> list(Lookup lookup, Class<T> modelClass) {
        return getMapper(modelClass).list(lookup);
    }

  // 其他方法略

}

```

代码较为简单，参见[gitee](https://gitee.com/xuqiudong/boot-support/blob/master/lcxm-common-base/src/main/java/cn/xuqiudong/common/base/service/CommonBaseMapperService.java).