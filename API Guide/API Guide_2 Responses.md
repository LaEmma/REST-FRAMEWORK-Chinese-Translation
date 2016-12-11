												Translated by Emma LIU
#Responses#
<font color=#FF0000>`  `</font>

REST framework supports HTTP content negotiation by providing a Response class which allows you to return content that can be rendered into multiple content types, depending on the client request.

The <font color=#FF0000>`Response`</font> class subclasses Django's SimpleTemplateResponse. Response objects are initialised with data, which should consist of native Python primitives. REST framework then uses standard HTTP content negotiation to determine how it should render the final response content.

There's no requirement for you to use the Response class, you can also return regular HttpResponse or StreamingHttpResponse objects from your views if required. Using the Response class simply provides a nicer interface for returning content-negotiated Web API responses, that can be rendered to multiple formats.

Unless you want to heavily customize REST framework for some reason, you should always use an APIView class or @api_view function for views that return Response objects. Doing so ensures that the view can perform content negotiation and select the appropriate renderer for the response, before it is returned from the view.

--


