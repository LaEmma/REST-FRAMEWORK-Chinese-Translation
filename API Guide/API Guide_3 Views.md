												Translated by Emma LIU
#Class-based Views#

REST架构提供了<font color=#FF0000>`APIView`</font>类，它继承自Django的<font color=#FF0000>`View`</font>类。

<font color=#FF0000>`APIView`</font>类与普通的<font color=#FF0000>`View`</font>类有以下不同之处：

- 传给处理函数的requests是REST架构的<font color=#FF0000>`Request`</font>实例，而不是Django的<font color=#FF0000>`HttpRequest`</font>实例。
- 处理函数返回的是REST架构的<font color=#FF0000>`Response`</font>，而不是Django的<font color=#FF0000>`HttpResponse`</font>。view会管理内容协商并且给response设定正确的渲染器。
- 任何<font color=#FF0000>`APIException`</font>异常都会被捕捉，处理成正确的responses。
- 进来的requests都会被验证，在把请求分派给处理函数之前会运行正确的权限和/或节流检查。

使用<font color=#FF0000>`APIView`</font>类就跟使用普通的<font color=#FF0000>`View`</font>类一样，进来的请求会被分派到相应的处理函数例如<font color=#FF0000>`/get()`</font>或者<font color=#FF0000>`.post()`</font>。除此之外，类里包含一些属性来控制API政策中的不同方面。

例如：

```
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import authentication, permissions

class ListUsers(APIView):
    """
    列出系统中所有用户的视图。

    * 要求token验证。
    * 只有amdin用户才有权限访问该视图。
    """
    authentication_classes = (authentication.TokenAuthentication,)
    permission_classes = (permissions.IsAdminUser,)

    def get(self, request, format=None):
        """
        Return a list of all users.
        """
        usernames = [user.username for user in User.objects.all()]
        return Response(usernames)
```

###API policy attributes API政策属性
以下属性控制着API view中的可插拔部分。

####.renderer_classes
####.parser_classes
####.authentication_classes
####.throttle_classes
####.permission_classes
####.content_negotiation_class

###API policy instantiation methods API政策实例化方法
以下属性在REST架构中用来实例化不同的可插拔API政策。通常你不需要重写以下方法。
####.get_renderers(self)
####.get_parsers(self)
####.get_authenticators(self)
####.get_throttles(self)
####.get_permissions(self)
####.get_content_negotiator(self)

###API policy implementation methods API政策应用方法
一下函数在分派给处理函数之前会被调用。
####.check_permissions(self, request)
####.check_throttles(self, request)
####.perform_content_negotiation(self, request, force=False)

###Dispatch methods
以下方法会被view的<font color=#FF0000>`.dispatch()`</font>函数直接调用。这些函数是用来进行调用<font color=#FF0000>`.get()`</font>，<font color=#FF0000>`.post()`</font>，<font color=#FF0000>`.patch()`</font>和<font color=#FF0000>`.delete()`</font>之前或之后的各种操作。

####.initial(self, request, *args, **kwargs)
在处理函数被调用之前进行各种操作。这个方法用来加强权限验证，节流处理和进行内容协商。

一般来讲不需要重写该函数。

####.handle_exception(self, exc)
处理函数抛出的任何异常都会被传到该函数，它会要么返回一个<font color=#FF0000>`Response`</font>实例要么重新抛出异常。

默认会处理<font color=#FF0000>`rest_framework.exceptions.APIException`</font>的任何子类，以及Django的<font color=#FF0000>`Http404`</font>和<font color=#FF0000>`PermissionDenied`</font>异常，并且返回适当的错误回应。

如果你想要定制你的API返回的错误回应，你需要继承该函数。

####.initialize_request(self, request, *args, **kwargs)
确保传递给处理函数的是<font color=#FF0000>`Request`</font>实例，而不是Django <font color=#FF0000>`HttpRequest`</font>。

一般来讲不需要重写该函数。

####.finalize_response(self, request, response, *args, **kwargs)
确保从处理函数返回的<font color=#FF0000>`Response`</font>对象可以被渲染成正确的内容类型。

一般来讲不需要重写该函数。

--

#Function Based Views#

REST架构中也可以使用基于函数的view。它提供了一系列简单的装饰器封装你的基于函数的view，来确保接受的是<font color=#FF0000>`Request`</font>实例（而不是Django <font color=#FF0000>`HttpRequest`</font>）并且返回<font color=#FF0000>`Response`</font>（而不是Django <font color=#FF0000>`HttpResponse`</font>），同时允许你配置处理请求的过程。

###@api_view()
**Signature:** <font color=#FF0000>`@api_view(http_method_names=['GET'], exclude_from_schema=False)`</font>


此功能的核心就是<font color=#FF0000>`api_view`</font> 装饰器，它接收一列你的视图应该回应的HTTP方法。例如，下面就是一个简单地返回一些数据的视图函数：

```
from rest_framework.decorators import api_view

@api_view()
def hello_world(request):
    return Response({"message": "Hello, world!"})
```

这个视图使用<font color=#FF0000>settings</font>中设定的默认渲染器，解析器，验证类。

默认只有<font color=#FF0000>`GET`</font>方法会被接受。其他方法会被返回“405 Method Not Allowed”。可以定义允许的方法来改变这一默认，如下：

```
@api_view(['GET', 'POST'])
def hello_world(request):
    if request.method == 'POST':
        return Response({"message": "Got some data!", "data": request.data})
    return Response({"message": "Hello, world!"})
```
你也可以标记API view使之被任何<font color=#FF0000>自动生成模式</font>忽略，可以使用<font color=#FF0000>`exclude_from_schema`</font>属性：

```
@api_view(['GET'], exclude_from_schema=True)
def api_docs(request):
    ...
```

###API policy decorators API政策装饰器

为了重写默认设置，REST架构提供了一系列额外的装饰器，他们都可以加到你的视图上。但是他们必须放在<font color=#FF0000>`@api_view`</font>装饰器下面。例如，创建一个节流视图只允许一个特定用户每天访问一次，可以使用<font color=#FF0000>`@throttle_classes`</font>装饰器，传给它一列节流类：

```
from rest_framework.decorators import api_view, throttle_classes
from rest_framework.throttling import UserRateThrottle

class OncePerDayUserThrottle(UserRateThrottle):
        rate = '1/day'

@api_view(['GET'])
@throttle_classes([OncePerDayUserThrottle])
def view(request):
    return Response({"message": "Hello for today! See you tomorrow!"})
```

上面讲的是对应<font color=#FF0000>`APIView`</font>子类中的属性的一些装饰器。

可用的装饰器有：

- @renderer_classes(...)
- @parser_classes(...)
- @authentication_classes(...)
- @throttle_classes(...)
- @permission_classes(...)

每个装饰器都只接受一个参数，该参数必须是类的list或者是tuple。