# java
1. java类中获取resources下资源文件：
```
File file = new File("src/main/resources/properties/basecom.properties");
```
2. 当工程部署到Tomcat中时，按照上边方式，则会出现找不到该文件路径的异常。经搜索资料知道，Java工程打包部署到Tomcat中时，properties的路径变到顶层（classes下），这是由Maven工程结构决定的。由Maven构建的web工程，主代码放在src/main/java路径下，资源放在src/main/resources路径下，当构建为war包的时候，会将主代码和资源文件放置classes文件夹下：  
```
 InputStream in = this.getClass().getResourceAsStream("/properties/basecom.properties");
```

2. 使用`@Deprecated`来标明该方法已经失效，使用`@see #`来标明替代方法
3. 将json字符串转换为JSON对象
- 使用Jackson Json
```java	
	public void testJackson() throws IOException {  
    ObjectMapper mapper = new ObjectMapper(); 
    File from = new File("albumnList.txt"); 
    TypeReference<HashMap<String,Object>> typeRef 
            = new TypeReference<HashMap<String,Object>>() {};

    HashMap<String,Object> o = mapper.readValue(from, typeRef); 
    System.out.println("Got " + o); 
}   
```
- 使用fastJson
```java
 //body is Json String
 JSONObject jsonObject = JSONObject.parseObject(body);
```