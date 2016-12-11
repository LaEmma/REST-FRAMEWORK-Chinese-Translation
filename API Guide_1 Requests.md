#Requests#


REST架构的Request类扩展了标准的HttpRequest，支持REST架构的更为灵活的请求解析以及请求验证。

--
###Request parsing 请求解析

REST framework's Request objects provide flexible request parsing that allows you to treat requests with JSON data or other media types in the same way that you would normally deal with form data.

REST架构的Request对象提供了非常灵活的请求解析功能，它允许你使用JSON或其他类型的数据来提供requests请求（与form类型的数据处理方式相同）

####.data

<font color=#FF0000>`request.data`</font>返回请求正文已解析过的内容。这与标准的<font color=#FF0000>`request.POST`</font>和<font color=#FF0000>`request.FILES`</font>属性基本相似，不同之处包涵下面几点：
 
 * 它包含所有解析过的内容，包括*file*和*non-file*输入。
 * 它可以解析除了POST之外的其他HTTP方法的内容，意味着你可以访问PUT和PATCH请求的内容。
 * 它支持REST架构里多种数据来源的请求解析，而不仅仅只是支持form数据。例如与处理form数据一样，你也可以处理JSON数据。

更多信息参见<font color=#FF0000>解析器文档</font>。

####.query_params

<font color=#FF0000>`request.query_params`</font>是<font color=#FF0000>`request.GET`</font>的同义词。

为了保持你的代码的清除简洁，我们建议使用<font color=#FF0000>`request.query_params`</font>来代替Django标准的<font color=#FF0000>`request.GET`</font>。这样做可以让你的代码库更准确更清晰 - 任何HTTP方法类型都有可能包含查询参数(query parameters)，不仅仅只有GET请求。

####.parsers

根据设置在view中的<font color=#FF0000>`parser_classes`</font>或者给予是setting里的<font color=#FF0000>`DEFAULT_PARSER_CLASSES`</font> 设置，<font color=#FF0000>`APIView`</font>类或者<font color=#FF0000>`@api\_view`</font>装饰器可以确保这一属性正确的设置到一列<font color=#FF0000>`Parser`</font>实例上。

一般来讲你不需要访问这一属性。

--
**备注：**如果客户端发送了一个错误格式的内容，那么访问request.data可能会抛出<font color=#FF0000>`ParserError`</font>。默认情况下REST架构的<font color=#FF0000>`APIView`</font>类或者<font color=#FF0000>`@api_view`</font>装饰器会捕获这个错误并且返回<font color=#FF0000>`400 Bad Request`</font>。

如果客户端发送的请求中包含了一个无法解析的内容类型，那么服务器会抛出<font color=#FF0000>`UnsupportedMediaType`</font>异常，默认情况下也会被捕获并且返回<font color=#FF0000>`415 Unsupported Media Type`</font>。

--

###Content negotiation 内容协商

Request放开了一些属性来允许你决定内容协商阶段的结果。也就是允许你对于不同媒体类型选择不同的序列化方案。

####.accepted_renderer
内容协商阶段选择的渲染器实例

####.accepted\_media\_type
内容协商阶段接受的用来表示媒体类型的字符串

--
###Authentication 校验

REST架构提供了灵活的，针对每一个请求的校验，让你可以：

* 针对你的API的不同部分使用不同验证方式。
* 支持使用多种验证方式。
* 在请求中提供了同时用户和token信息。

####.user

<font color=#FF0000>`request.user`</font>通常返回一个<font color=#FF0000>`django.contrib.auth.models.User`</font>实例，尽管这一行为有使用的验证方式决定。

如果请求未验证，那么<font color=#FF0000>`request.user`</font>默认返回的是一个<font color=#FF0000>`django.contrib.auth.models.AnonymousUser`</font>实例。

更多信息参见<font color=#FF0000>校验文档</font>。

####.auth

<font color=#FF0000>`request.auth`</font>返回任何额外的验证内容。 The exact behavior of <font color=#FF0000>`request.auth`</font> depends on the authentication policy being used, but it may typically be an instance of the token that the request was authenticated against.

If the request is unauthenticated, or if no additional context is present, the default value of <font color=#FF0000>`request.auth`</font> is <font color=#FF0000>`None`</font>.

更多信息参见<font color=#FF0000>校验文档</font>。

####.authenticators
The APIView class or @api_view decorator will ensure that this property is automatically set to a list of Authentication instances, based on the authentication_classes set on the view or based on the DEFAULT_AUTHENTICATORS setting.

You won't typically need to access this property.

--