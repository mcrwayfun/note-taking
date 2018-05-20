# spring
1. springmvc使用post方法传参时报错，	
```
com.fasterxml.jackson.databind.exc.InvalidDefinitionException: 
Cannot construct instance of xxx(no Creators, like default construct, exist): 
cannot deserialize from Object value (no delegate- or property-based Creator)
```
提示无默认构造函数，为需要反序列化的pojo添加无参构造函数即可。
