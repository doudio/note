> ## alibaba fastjson

* 当json(反序列化)对象结构嵌套复杂时

```java
String target = "{'code': 0, 'msg':'success', 'data': [{}....]}";
ApiResult<List<ApiDTO>> apiResult = JSON.parseObject(target, 
				new TypeReference<ApiResult<List<ApiDTO>>>(){});
```

* 当字段在json (序列化&反序列化) 中换名字了

```java
@JSONField(name = "stu_name")
private String name;
```

* 当字段在json(反序列化)中有多个名字时

```java
@JSONField(name = "stu_name", alternateNames = {"a_name", "b_name"})
private String name;
```

* 当字段在json(序列化&反序列化)中需要转换date格式

```java
@JSONField(name = "create_date",format = "yyyy-MM-dd HH:mm:ss")
private Date createDate;
```

* 当数据需要直接转换成数组

```java
String targetr = "['a','b',....]";
List<String> array= JSON.parseArray(target,String.class);
```

* 常见的序列化与反序列化

```java
String targetJSON = JSON.toJSONString(object);//将对对象序列化为JSON
String targetAry = JSONArray.toJSONString(ary);//将数组序列化

JSONObject targetJSONObject = JSON.parseObject(targetJSON);
String value = jsonObject.getString("key");
JSONObject obj = jsonObject.getJSONObject("obj");
JSONArray ary = jsonObject.getJSONArray("ary");
```
