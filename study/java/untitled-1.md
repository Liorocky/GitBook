# RestTemplate

```java
HashMap<String, Object> paramMap = new HashMap<>();
paramMap.put("signdate", signdate);
paramMap.put("secret", secret);
paramMap.put("param", "{\"mobile\":\"13109595651\",\"brand\":\"\"}");
log.info(paramMap.toString());
String url = "http://crm.crmqas.lining.com/lining-integration-api/integration/isMember?signdate={signdate}&secret={secret}&param={param}";
```

