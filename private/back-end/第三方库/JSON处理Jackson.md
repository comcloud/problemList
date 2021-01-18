# Jackson读写文件问题

## 一：引言

> Jackson处理json类库是全世界使用量最多，同时我们尽量不要使用阿里巴巴的faskjson，阿里这个的确fast，但是是翻船fast
>
> - **JsonFactory**：这个类是Jackson主要的工厂方法，主要用于配置和构建解析器(JsonParser)和生成器(如JsonGenerator)，这个工厂实例是线程安全的，如果有配置的话，可以重复使用。
> - **JsonGenerator**：这个类主要是用来生成Json格式的内容的，我们可以使用JsonFactory 的方法生成一个实例。
> - **JsonParser**：这个主要是用来读取Json格式的内容，并且完成解析操作的，我们可以使用JsonFactory的方法生成一个实例。
> - **ObjectMapper**：这个类提供了Java对象和Json之间的转化，主要通过JsonParser和JsonGenerator实例来完成实际的对Json数据的读写操作。
> - **JsonNode**：这个就好像是一个json对象图，我们在读入Json时候获取一个JsonNode对象，这个对象不可变，所以如果我们想要去构建一个json使用jackson需要用到下个类
> - **JsonNodeFactory**：创建JsonNode的工厂，可以使用他来创建ObjectNode或者ArrayNode来动态构建Json串

## 二：使用方式

### 1. 读

~~~java
public void readJson(){
	String json = "{
          "data": {
            "route": {
              "destination": "115.104645,35.79215",
              "origin": "115.064320,35.78661",
              "paths": [

              ]
            },
            "count": 1
          },
          "errcode": 0,
          "errdetail": null,
          "errmsg": "OK",
          "ext": null
        }
        ";
	//创建一个对象映射器
    ObjectMapper mapper = new ObjectMapper();
	//根据映射器读取json串，他会返回一个JsonNode，这个对象提供了丰富的方法来获取json串中的信息
	//当然readTree方法提供了很多重载方法，不仅可以读入String类型，也可以根据URL读取或者File读取
	JsonNode node = mapper.readTree(json);
	//这里使用一些常用的方法
	//直接根据Key名来获取，如果有多个，只会获取第一个，实际上源码中遍历到一个之后会直接return
	JsonNode data = node.findPath("data");
	//通过索引获取
	JsonNode paths = data.findPath("route").get(2);
	//深拷贝
	JsonNode newNode = paths.deepCopy();
	//获取field的迭代器，也就是key的集合
	Iterator<String> iterator = node.fieldNames();
	//其他方法就是1.判断是否属于什么类型，2.强转为某个类型,比如：asBoolean();
}

~~~



### 2. 写

~~~java
public void write(){
    	//这里只是做演示，将输出流选为了System.err,实际上这个地方参数只要是OuputStream类型或者Writer类型都可以
	   JsonGenerator generator = 
           new JsonFactory().createGenerator(System.err, JsonEncoding.UTF8);
		//开始写入对象
        generator.writeStartObject();
		
        generator.writeStringField("name", "lisa");
        generator.writeNumberField("age", 22);
        generator.writeObjectField("sex", "man");

        generator.writeArrayFieldStart("likes");
        generator.writeString("apple");
        generator.writeString("banner");
        generator.writeStartObject();
        generator.writeNumberField("index", 1);
        generator.writeStringField("color", "red");
        generator.writeEndObject();

        generator.writeStartObject();
        generator.writeNumberField("index", 2);
        generator.writeStringField("color", "green");
        generator.writeEndObject();

        generator.writeEndArray();

        generator.writeEndObject();

        generator.flush();
        generator.close();
}
~~~

## 总结

> 使用Jackson写出文件感觉比较鸡肋，每次写出一段数据时候都需要我们自己start以及end，这个地方感觉
>
> 可以有更好的方式，同时使用Jackson可以对对象操作，我们可以将json直接解析为对象`new ObjectMapper().readValue(String content,Class<?> type)`，这个就会帮我们进行转换
>
> `new ObjectMapper().writeValueAsString()`,给定对象转换为字符串或者也可以转换为字节数组方法

