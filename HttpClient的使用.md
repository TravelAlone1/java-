# HttpClient的使用

### 常用方法

客户端注入，模拟请求

```java
	@Autowired
	private RestTemplate restTemplate;

	//参数，url和响应类型
	restTemplate.getForEntity(url,ResponseType(例如自定义WeatherResponse.class))
        
    //一般不能转为自定义响应对象，向转为String类型
    ResponseEntity<String> forEntity = restTemplate.getForEntity(url, String.class);

	String body=null;
    if (forEntity.getStatusCodeValue()==200){
    	body = forEntity.getBody();
    }

 	Gson gson = new Gson();
    response = gson.fromJson(body, WeatherResponse.class);
```

