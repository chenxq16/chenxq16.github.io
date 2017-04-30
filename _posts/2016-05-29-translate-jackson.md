---
layout: post
title:  android中高效的JSON解析
date:   2016-05-29 13:05:11
tags:
    - translate
---

对大json文件的处理。第一次尝试翻译，多多包涵。

---

**原文地址：**[http://www.donnfelker.com/hi-performance-json-parsing-in-android](http://www.donnfelker.com/hi-performance-json-parsing-in-android)

**温馨提示：英文水平有限，仅供参考，欢迎拍砖！**

在android开发中，我们有时候需要访问一个第三方的API，来请求一大块JSON格式的数据。现在，我其中的一个客户端，有一个用来向设备同步数据的API接口。一旦调用该API接口将会返回一个包含成千上万条JSON数据格式的结果。如果以JSON文件格式保存下来，则该文件大小达到8MB以上。将其完全的加载到内存中去是很痛苦的，并且在很多内存受限的设备上会造成奔溃。然而，使用[Jackson](http://jackson.codehaus.org/)这个JSON解析库，你能够使用流的方式将[http-request](https://github.com/kevinsawicki/http-request)作为一个输入流([reader](http://developer.android.com/intl/zh-cn/reference/java/io/Reader.html))封装到[Jacksons Streaming API](http://wiki.fasterxml.com/JacksonStreamingApi)中。该API能够让你在通信管道中有数据的时候，尽可能快的处理每一个json object对象。这使得你能够在通信管道中分段处理数据而非先把整个JSON文件加载进来。

我在Jackson中使用[ObjectMapper](http://www.codehaus.org/)将JSON数据转换为一个[POJO](https://en.wikipedia.org/wiki/Plain_Old_Java_Object)类。这使得我的APP通过domain objects能够很好的工作，因此我无需担心所有地方的JSON解析操作（或者使用游标将数据移动到本地的数据库当中）。如果你需要将整个JSON文件与对象映射起来，你需要创建一个巨大无比的对象，这样很容易到达内存的边界，直到让你得到一个OutOfMemory的异常。正确的做法是，当数据来了我逐条解析JSON数据，但这也得益于使用POJO。下面是具体例子。

### The ObjectModel
下面我们有一个简单的customer对象需要下载下来。想象一下，一个大型的会计类APP有着上百万的顾客。每一个顾客将会被转换为如下的POJO类。

```
public class Customer {
  String firstName;
  String lastName;
  // 20 Other properties

  public String getFirstName() {
    return firstName;
  }

  public void setFirstName(String firstName) {
    this.firstName = firstName;
  }

  public String getLastName() {
    return lastName;
  }

  public void setLastName(String lastName) {
    this.lastName = lastName;
  }

  // all the other mutators and accessors

}
```

### Connection to the API/Consuming the stream
这实际上相当简单。选择你的网络框架（我使用的是http-request），简单的访问你的API接口。使用HttpRequest的<strong>reader()</strong>方法，然后通过这个输入流转换为JsonPraser（如下）,然后循环创建每一个Customer，最后我就有了每一个customer的实例。可参考注释。

```
// Connect to your API with your http lib, i use http-request: https://github.com/kevinsawicki/http-request
// Then get the reader. The 'request' variable below is just a HttpRequest object
// as shown here: http://kevinsawicki.github.io/http-request/
final Reader reader = request.reader();

final ObjectMapper mapper = new ObjectMapper();
final JsonFactory factory = mapper.getFactory();

// Create a new streaming parser with the reader
JsonParser jp = factory.createParser(reader);

// I'm working with a JSON Array that is returned from the API that I'm connecting to, so I need to advance the
// parser to the start of the array.

// Advance stream to START_ARRAY first:
jp.nextToken();

// Parse each value by looking for each start object. The Mapper will read the object and advance the parser
// until it finds the END_OBJECT at which time the mapper then deserializes into a POJO. Then the loop will
// continue to the next position. This will handle each JSON object as it becomes available and will be very
// light on memory and high in performance.
while(jp.nextToken() == JsonToken.START_OBJECT) {
    // Get customer POJO
    final Customer c = mapper.readValue(jp, Customer.class);

    // Update local db with customer info.
    myDataLayer.updateCustomer(c);
}
```

### 如何使用Jackson
简单的将它作为一个库引用，或者如果你使用的是Maven，往pom文件中添加依赖。现在，你可以处理巨大的json流，而不用再担心你的内存溢出了。
