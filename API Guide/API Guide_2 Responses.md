												Translated by Emma LIU
#Responses#

REST架构提供了一个Response类，允许你根据客户端请求来返回可以被渲染成多种类型的内容。 

<font color=#FF0000>`Response`</font> 类是Django<font color=#FF0000>`SimpleTemplateResponse`</font>的子类。 <font color=#FF0000>`Response`</font>对象由包含的Python基本数据类型的数据初始化。REST架构使用标准的HTTP内容协商来决定怎样渲染出最终的response内容。

没有强制要求来使用<font color=#FF0000>`Response`</font>类，根据需要你的view也可以返回正规的<font color=#FF0000>`HttpResponse`</font>或者<font color=#FF0000>`StreamingHttpResponse`</font>对象。使用<font color=#FF0000>`Reponse`</font>类只是简单地为返回内容协商相关的Web API response提供了一个更友好的接口，因为他们可以被渲染成多种格式。

除非因为某些原因你想要笨重的定制化REST架构，一般来讲，在view中你应该使用<font color=#FF0000>`APIView`</font>类或者<font color=#FF0000>`@api_view`</font>功能来返回<font color=#FF0000>`Response`</font>对象。这样做可以确保视图可以正确的表达内容协商，为response选择正确的渲染器。

--

###Creating responses 创建responses
####Response()
**Signature**: <font color=#FF0000>`Response(data, status=None, template_name=None, headers=None, content_type=None)`</font>

不像一般的<font color=#FF0000>`HttpResponse`</font>对象，你不需要用渲染过的内容来实例化<font color=#FF0000>`Response`</font>对象。你只需要传未经渲染的任何类型的Python数据即可。

<font color=#FF0000>`Response`</font>类使用的渲染器本身无法处理复杂的数据类型比如Django的model实例，因此在创建<font color=#FF0000>`Response`</font>对象之前，你需要序列化数据使之成为基础的数据类型。

你可以使用REST架构的<font color=#FF0000>`Serializer`</font>类来执行数据序列化，或者使用你自己的序列函数。


参数：

- <font color=#FF0000>`data`</font>: response中序列化了的数据。
- <font color=#FF0000>`status`</font>: response状态码。默认是200。详见状态码说明</font>。
- <font color=#FF0000>`template_name`</font>: 如果选择了<font color=#FF0000>`HTMLRenderer`</font>需要提供模板名称。
- <font color=#FF0000>`headers`</font>: reponse中使用的HTTP headers字典。
- <font color=#FF0000>`content_type`</font>: response的内容类型。一般来讲，根据内容协商，渲染器会自动设置内容类型，如果有需要你也可以通过该参数来设定内容类型。

--

###Attributes 属性
####.data
<font color=#FF0000>`Request`</font>对象未经渲染过的内容。

####.status_code
HTTP response的数字状态码。

####.content
response里渲染过的内容。访问<font color=#FF0000>`.content`</font>之前需要调用<font color=#FF0000>`.render()`</font>方法。

####.template_name
<font color=#FF0000>`.template_name`</font>，如果有的话。只有在选定了<font color=#FF0000>`HTMLRender`</font>或者其他定制的模板渲染器时才需要模板名称。

####.accepted_renderer
用来渲染response的渲染器实例。

在response从view中返回之前被<font color=#FF0000>`APIView`</font>或<font color=#FF0000>`@api_view`</font>自动设置。

####.accepted_media_type
内容协商阶段确定的媒体类型。

在response从view中返回之前被<font color=#FF0000>`APIView`</font>或<font color=#FF0000>`@api_view`</font>自动设置。

####.renderer_context
传入渲染器<font color=#FF0000>`.render()`</font>方法的包含额外内容信息的字典。

在response从view中返回之前被<font color=#FF0000>`APIView`</font>或<font color=#FF0000>`@api_view`</font>自动设置。

--

###Standard HttpResponse attributes 标准HttpResponse属性
<font color=#FF0000>`Response`</font>类扩展<font color=#FF0000>`SimpleTemplateResponse`</font>，所有一般的属性和方法在response都可使用。例如你可以用标准方式这是headers:

```
response = Response()
response['Cache-Control'] = 'no-cache'
```

####.render()
**Signature:**  <font color=#FF0000>`.render()`</font>

与其他<font color=#FF0000>`TemplateResponse`</font>类似，这个方法用来渲染reponse里序列化了的数据到最终的response内容里。当调用<font color=#FF0000>`.render()`</font>方法，<font color=#FF0000>`accepted_renderer`</font>实例里的<font color=#FF0000>`.render(data, accepted_media_type, renderer_context)`</font>方法返回的结果就是response的内容。

一般来说你不需要自己来调用<font color=#FF0000>`.render()`</font>，它是在Django标准的response周期中被自动处理的。
