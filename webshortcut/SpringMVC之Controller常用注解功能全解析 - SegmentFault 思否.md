# SpringMVC之Controller常用注解功能全解析 - SegmentFault 思否
## 一、简介

> 在 SpringMVC 中，控制器 Controller 负责处理由 DispatcherServlet 分发的请求，它把用户请求的数据经过业务处理层处理之后封装成一个 Model ，然后再把该 Model 返回给对应的 View 进行展示。在 SpringMVC 中提供了一个非常简便的定义 Controller 的方法，你无需继承特定的类或实现特定的接口，只需使用[@Controller](https://segmentfault.com/u/controller) 标记一个类是 Controller ，然后使用 @RequestMapping 和 @RequestParam 等一些注解用以定义 URL 请求和 Controller 方法之间的映射，这样的 Controller 就能被外界访问到。此外 Controller 不会直接依赖于 HttpServletRequest 和 HttpServletResponse 等 HttpServlet 对象，它们可以通过 Controller 的方法参数灵活的获取到。

为了先对 Controller 有一个初步的印象，以下先定义一个简单的 Controller ：

    @Controller
    public class MyController {
     
        @RequestMapping ( "/showView" )
        public ModelAndView showView() {
           ModelAndView modelAndView \= new ModelAndView();
           modelAndView.setViewName( "viewName" );
           modelAndView.addObject( " 需要放到 model 中的属性名称 " , " 对应的属性值，它是一个对象 " );
           return modelAndView;
        }
     
    }

在上面的示例中，[@Controller](https://segmentfault.com/u/controller) 是标记在类 MyController 上面的，所以类 MyController 就是一个 SpringMVC Controller 对象了，然后使用 @RequestMapping(“/showView”) 标记在 Controller 方法上，表示当请求 / showView.do 的时候访问的是 MyController 的 showView 方法，该方法返回了一个包括 Model 和 View 的 ModelAndView 对象。这些在后续都将会详细介绍。

## 二、使用 [@Controller](https://segmentfault.com/u/controller) 定义一个 Controller 控制器

[@Controller](https://segmentfault.com/u/controller) 用于标记在一个类上，使用它标记的类就是一个 SpringMVC Controller 对象。分发处理器将会扫描使用了该注解的类的方法，并检测该方法是否使用了 @RequestMapping 注解。[@Controller](https://segmentfault.com/u/controller) 只是定义了一个控制器类，而使用 @RequestMapping 注解的方法才是真正处理请求的处理器，这个接下来就会讲到。  
单单使用[@Controller](https://segmentfault.com/u/controller) 标记在一个类上还不能真正意义上的说它就是 SpringMVC 的一个控制器类，因为这个时候 Spring 还不认识它。那么要如何做 Spring 才能认识它呢？这个时候就需要我们把这个控制器类交给 Spring 来管理。拿 MyController 来举一个例子

    @Controller
    public class MyController {
        @RequestMapping ( "/showView" )
        public ModelAndView showView() {
           ModelAndView modelAndView \= new ModelAndView();
           modelAndView.setViewName( "viewName" );
           modelAndView.addObject( " 需要放到 model 中的属性名称 " , " 对应的属性值，它是一个对象 " );
           return modelAndView;
        }
     
    }

这个时候有两种方式可以把 MyController 交给 Spring 管理，好让它能够识别我们标记的[@Controller](https://segmentfault.com/u/controller) 。  
第一种方式是在 SpringMVC 的配置文件中定义 MyController 的 bean 对象。  
<bean class="com.host.app.web.controller.MyController"/>  
第二种方式是在 SpringMVC 的配置文件中告诉 Spring 该到哪里去找标记为[@Controller](https://segmentfault.com/u/controller) 的 Controller 控制器。

        < context:component-scan base-package = "com.host.app.web.controller" >
           < context:exclude-filter type = "annotation"
               expression = "org.springframework.stereotype.Service" />
        </ context:component-scan >

> 注：上面 context:exclude-filter 标注的是不扫描 @Service 标注的类

## 三、使用 @RequestMapping 来映射 Request 请求与处理器

可以使用 @RequestMapping 来映射 URL 到控制器类，或者是到 Controller 控制器的处理方法上。当 @RequestMapping 标记在 Controller 类上的时候，里面使用 @RequestMapping 标记的方法的请求地址都是相对于类上的 @RequestMapping 而言的；当 Controller 类上没有标记 @RequestMapping 注解时，方法上的 @RequestMapping 都是绝对路径。这种绝对路径和相对路径所组合成的最终路径都是相对于根路径 “/” 而言的。

    @Controller
    public class MyController {
        @RequestMapping ( "/showView" )
        public ModelAndView showView() {
           ModelAndView modelAndView \= new ModelAndView();
           modelAndView.setViewName( "viewName" );
           modelAndView.addObject( " 需要放到 model 中的属性名称 " , " 对应的属性值，它是一个对象 " );
           return modelAndView;
        }
     
    }

在这个控制器中，因为 MyController 没有被 @RequestMapping 标记，所以当需要访问到里面使用了 @RequestMapping 标记的 showView 方法时，就是使用的绝对路径 / showView.do 请求就可以了。

    @Controller
    @RequestMapping ( "/test" )
    public class MyController {
        @RequestMapping ( "/showView" )
        public ModelAndView showView() {
           ModelAndView modelAndView \= new ModelAndView();
           modelAndView.setViewName( "viewName" );
           modelAndView.addObject( " 需要放到 model 中的属性名称 " , " 对应的属性值，它是一个对象 " );
           return modelAndView;
        }
     
    }

这种情况是在控制器上加了 @RequestMapping 注解，所以当需要访问到里面使用了 @RequestMapping 标记的方法 showView() 的时候就需要使用 showView 方法上 @RequestMapping 相对于控制器 MyController 上 @RequestMapping 的地址，即 / test/showView.do 。

### （一）使用 URI 模板

URI 模板就是在 URI 中给定一个变量，然后在映射的时候动态的给该变量赋值。如 URI 模板[http://localhost/app/](https://link.segmentfault.com/?url=http%3A%2F%2Flocalhost%2Fapp%2F){variable1}/index.html ，这个模板里面包含一个变量 variable1 ，那么当我们请求[http://localhost/app/hello/index.html](https://link.segmentfault.com/?url=http%3A%2F%2Flocalhost%2Fapp%2Fhello%2Findex.html) 的时候，该 URL 就跟模板相匹配，只是把模板中的 variable1 用 hello 来取代。在 SpringMVC 中，这种取代模板中定义的变量的值也可以给处理器方法使用，这样我们就可以非常方便的实现 URL 的 RestFul 风格。这个变量在 SpringMVC 中是使用 @PathVariable 来标记的。  
在 SpringMVC 中，我们可以使用 @PathVariable 来标记一个 Controller 的处理方法参数，表示该参数的值将使用 URI 模板中对应的变量的值来赋值。

    @Controller
    @RequestMapping ( "/test/{variable1}" )
    public class MyController {
     
        @RequestMapping ( "/showView/{variable2}" )
        public ModelAndView showView( @PathVariable String variable1, @PathVariable ( "variable2" ) int variable2) {
           ModelAndView modelAndView \= new ModelAndView();
           modelAndView.setViewName( "viewName" );
           modelAndView.addObject( " 需要放到 model 中的属性名称 " , " 对应的属性值，它是一个对象 " );
           return modelAndView;
        }
    }

在上面的代码中我们定义了两个 URI 变量，一个是控制器类上的 variable1 ，一个是 showView 方法上的 variable2 ，然后在 showView 方法的参数里面使用 @PathVariable 标记使用了这两个变量。所以当我们使用 / test/hello/showView/2.do 来请求的时候就可以访问到 MyController 的 showView 方法，这个时候 variable1 就被赋予值 hello ，variable2 就被赋予值 2 ，然后我们在 showView 方法参数里面标注了参数 variable1 和 variable2 是来自访问路径的 path 变量，这样方法参数 variable1 和 variable2 就被分别赋予 hello 和 2 。方法参数 variable1 是定义为 String 类型，variable2 是定义为 int 类型，像这种简单类型在进行赋值的时候 Spring 是会帮我们自动转换的，关于复杂类型该如何来转换在后续内容中将会讲到。  
在上面的代码中我们可以看到在标记 variable1 为 path 变量的时候我们使用的是 @PathVariable ，而在标记 variable2 的时候使用的是 @PathVariable(“variable2”) 。这两者有什么区别呢？第一种情况就默认去 URI 模板中找跟参数名相同的变量，但是这种情况只有在使用 debug 模式进行编译的时候才可以，而第二种情况是明确规定使用的就是 URI 模板中的 variable2 变量。当不是使用 debug 模式进行编译，或者是所需要使用的变量名跟参数名不相同的时候，就要使用第二种方式明确指出使用的是 URI 模板中的哪个变量。  
除了在请求路径中使用 URI 模板，定义变量之外，@RequestMapping 中还支持通配符 “\* ”。如下面的代码我就可以使用 / myTest/whatever/wildcard.do 访问到 Controller 的 testWildcard 方法。

    @Controller
    @RequestMapping ( "/myTest" )
    public class MyController {
        @RequestMapping ( "\*/wildcard" )
        public String testWildcard() {
           System. out .println( "wildcard------------" );
           return "wildcard" ;
        } 
    }

### （二）使用 @RequestParam 绑定 HttpServletRequest 请求参数到控制器方法参数

        @RequestMapping ( "requestParam" )
       public String testRequestParam( @RequestParam(required=false) String name, @RequestParam ( "age" ) int age) {
           return "requestParam" ;
        }

在上面代码中利用 @RequestParam 从 HttpServletRequest 中绑定了参数 name 到控制器方法参数 name ，绑定了参数 age 到控制器方法参数 age 。值得注意的是和 @PathVariable 一样，当你没有明确指定从 request 中取哪个参数时，Spring 在代码是 debug 编译的情况下会默认取更方法参数同名的参数，如果不是 debug 编译的就会报错。此外，当需要从 request 中绑定的参数和方法的参数名不相同的时候，也需要在 @RequestParam 中明确指出是要绑定哪个参数。在上面的代码中如果我访问 / requestParam.do?name=hello&age=1 则 Spring 将会把 request 请求参数 name 的值 hello 赋给对应的处理方法参数 name ，把参数 age 的值 1 赋给对应的处理方法参数 age 。  
在 @RequestParam 中除了指定绑定哪个参数的属性 value 之外，还有一个属性 required ，它表示所指定的参数是否必须在 request 属性中存在，默认是 true ，表示必须存在，当不存在时就会报错。在上面代码中我们指定了参数 name 的 required 的属性为 false ，而没有指定 age 的 required 属性，这时候如果我们访问 / requestParam.do 而没有传递参数的时候，系统就会抛出异常，因为 age 参数是必须存在的，而我们没有指定。而如果我们访问 / requestParam.do?age=1 的时候就可以正常访问，因为我们传递了必须的参数 age ，而参数 name 是非必须的，不传递也可以。

### （三）使用 @CookieValue 绑定 cookie 的值到 Controller 方法参数

        @RequestMapping ( "cookieValue" )
        public String testCookieValue( @CookieValue ( "hello" ) String cookieValue, @CookieValue String hello) {
           System. out .println(cookieValue + "-----------" + hello);
           return "cookieValue" ;
        }

在上面的代码中我们使用 @CookieValue 绑定了 cookie 的值到方法参数上。上面一共绑定了两个参数，一个是明确指定要绑定的是名称为 hello 的 cookie 的值，一个是没有指定。使用没有指定的形式的规则和 @PathVariable 、@RequestParam 的规则是一样的，即在 debug 编译模式下将自动获取跟方法参数名同名的 cookie 值。

### （四）使用 @RequestHeader 注解绑定 HttpServletRequest 头信息到 Controller 方法参数

    @RequestMapping ( "testRequestHeader" )
    public String testRequestHeader( @RequestHeader ( "Host" ) String hostAddr, @RequestHeader String Host, @RequestHeader String host ) {
        System. out .println(hostAddr + "-----" + Host + "-----" + host );
        return "requestHeader" ;
    }

在上面的代码中我们使用了 @RequestHeader 绑定了 HttpServletRequest 请求头 host 到 Controller 的方法参数。上面方法的三个参数都将会赋予同一个值，由此我们可以知道在绑定请求头参数到方法参数的时候规则和 @PathVariable 、 @RequestParam 以及 @CookieValue 是一样的，即没有指定绑定哪个参数到方法参数的时候，在 debug 编译模式下将使用方法参数名作为需要绑定的参数。但是有一点 @RequestHeader 跟另外三种绑定方式是不一样的，那就是在使用 @RequestHeader 的时候是大小写不敏感的，即 @RequestHeader(“Host”) 和 @RequestHeader(“host”) 绑定的都是 Host 头信息。记住在 @PathVariable 、 @RequestParam 和 @CookieValue 中都是大小写敏感的。

### （五） @RequestMapping 的一些高级应用

在 RequestMapping 中除了指定请求路径 value 属性外，还有其他的属性可以指定，如 params 、method 和 headers 。这样属性都可以用于缩小请求的映射范围。

#### 1.params 属性

params 属性用于指定请求参数的，先看以下代码。

        @RequestMapping (value= "testParams" , params={ "param1=value1" , "param2" , "!param3" })
        public String testParams() {
           System. out .println( "test Params..........." );
           return "testParams" ;
        }

在上面的代码中我们用 @RequestMapping 的 params 属性指定了三个参数，这些参数都是针对请求参数而言的，它们分别表示参数 param1 的值必须等于 value1 ，参数 param2 必须存在，值无所谓，参数 param3 必须不存在，只有当请求 / testParams.do 并且满足指定的三个参数条件的时候才能访问到该方法。所以当请求 / testParams.do?param1=value1¶m2=value2 的时候能够正确访问到该 testParams 方法，当请求 / testParams.do?param1=value1¶m2=value2¶m3=value3 的时候就不能够正常的访问到该方法，因为在 @RequestMapping 的 params 参数里面指定了参数 param3 是不能存在的。

#### 2.method 属性

method 属性主要是用于限制能够访问的方法类型的。

        @RequestMapping (value= "testMethod" , method={RequestMethod. GET , RequestMethod. DELETE })
        public String testMethod() {
           return "method" ;
        }

在上面的代码中就使用 method 参数限制了以 GET 或 DELETE 方法请求 / testMethod.do 的时候才能访问到该 Controller 的 testMethod 方法。

#### 3.headers 属性

使用 headers 属性可以通过请求头信息来缩小 @RequestMapping 的映射范围。

        @RequestMapping (value= "testHeaders" , headers={ "host=localhost" , "Accept" })
        public String testHeaders() {
           return "headers" ;
        }

headers 属性的用法和功能与 params 属性相似。在上面的代码中当请求 / testHeaders.do 的时候只有当请求头包含 Accept 信息，且请求的 host 为 localhost 的时候才能正确的访问到 testHeaders 方法。

### （六）以 @RequestMapping 标记的处理器方法支持的方法参数和返回类型

1.  支持的方法参数类型

> （1 ）HttpServlet 对象，主要包括 HttpServletRequest 、HttpServletResponse 和 HttpSession 对象。 这些参数 Spring 在调用处理器方法的时候会自动给它们赋值，所以当在处理器方法中需要使用到这些对象的时候，可以直接在方法上给定一个方法参数的申明，然后在方法体里面直接用就可以了。但是有一点需要注意的是在使用 HttpSession 对象的时候，如果此时 HttpSession 对象还没有建立起来的话就会有问题。  
> （2 ）Spring 自己的 WebRequest 对象。 使用该对象可以访问到存放在 HttpServletRequest 和 HttpSession 中的属性值。

（3 ）InputStream 、OutputStream 、Reader 和 Writer 。 InputStream 和 Reader 是针对 HttpServletRequest 而言的，可以从里面取数据；OutputStream 和 Writer 是针对 HttpServletResponse 而言的，可以往里面写数据。  
（4 ）使用 @PathVariable 、@RequestParam 、@CookieValue 和 @RequestHeader 标记的参数。  
（5 ）使用 @ModelAttribute 标记的参数。  
（6 ）java.util.Map 、Spring 封装的 Model 和 ModelMap 。 这些都可以用来封装模型数据，用来给视图做展示。  
（7 ）实体类。 可以用来接收上传的参数。  
（8 ）Spring 封装的 MultipartFile 。 用来接收上传文件的。  
（9 ）Spring 封装的 Errors 和 BindingResult 对象。 这两个对象参数必须紧接在需要验证的实体对象参数之后，它里面包含了实体对象的验证结果。

1.  支持的返回类型

> （1 ）一个包含模型和视图的 ModelAndView 对象。  
> （2 ）一个模型对象，这主要包括 Spring 封装好的 Model 和 ModelMap ，以及 java.util.Map ，当没有视图返回的时候视图名称将由 RequestToViewNameTranslator 来决定。

（3 ）一个 View 对象。这个时候如果在渲染视图的过程中模型的话就可以给处理器方法定义一个模型参数，然后在方法体里面往模型中添加值。  
（4 ）一个 String 字符串。这往往代表的是一个视图名称。这个时候如果需要在渲染视图的过程中需要模型的话就可以给处理器方法一个模型参数，然后在方法体里面往模型中添加值就可以了。  
（5 ）返回值是 void 。这种情况一般是我们直接把返回结果写到 HttpServletResponse 中了，如果没有写的话，那么 Spring 将会利用 RequestToViewNameTranslator 来返回一个对应的视图名称。如果视图中需要模型的话，处理方法与返回字符串的情况相同。  
（6 ）如果处理器方法被注解 @ResponseBody 标记的话，那么处理器方法的任何返回类型都会通过 HttpMessageConverters 转换之后写到 HttpServletResponse 中，而不会像上面的那些情况一样当做视图或者模型来处理。  
（7 ）除以上几种情况之外的其他任何返回类型都会被当做模型中的一个属性来处理，而返回的视图还是由 RequestToViewNameTranslator 来决定，添加到模型中的属性名称可以在该方法上用 @ModelAttribute(“attributeName”) 来定义，否则将使用返回类型的类名称的首字母小写形式来表示。使用 @ModelAttribute 标记的方法会在 @RequestMapping 标记的方法执行之前执行。

### （七）使用 @ModelAttribute 和 @SessionAttributes 传递和保存数据

SpringMVC 支持使用 @ModelAttribute 和 @SessionAttributes 在不同的模型和控制器之间共享数据。 @ModelAttribute 主要有两种使用方式，一种是标注在方法上，一种是标注在 Controller 方法参数上。  
当 @ModelAttribute 标记在方法上的时候，该方法将在处理器方法执行之前执行，然后把返回的对象存放在 session 或模型属性中，属性名称可以使用 @ModelAttribute(“attributeName”) 在标记方法的时候指定，若未指定，则使用返回类型的类名称（首字母小写）作为属性名称。关于 @ModelAttribute 标记在方法上时对应的属性是存放在 session 中还是存放在模型中，我们来做一个实验，看下面一段代码。

    @Controller
    @RequestMapping ( "/myTest" )
    public class MyController {
     
        @ModelAttribute ( "hello" )
        public String getModel() {
           System. out .println( "-------------Hello---------" );
           return "world" ;
        }
     
        @ModelAttribute ( "intValue" )
        public int getInteger() {
           System. out .println( "-------------intValue---------------" );
           return 10;
        }
     
        @RequestMapping ( "sayHello" )
        public void sayHello( @ModelAttribute ( "hello" ) String hello, @ModelAttribute ( "intValue" ) int num, @ModelAttribute ( "user2" ) User user, Writer writer, HttpSession session) throws IOException {
           writer.write( "Hello " + hello + " , Hello " + user.getUsername() + num);
           writer.write( "\\r" );
           Enumeration enume \= session.getAttributeNames();
           while (enume.hasMoreElements())
               writer.write(enume.nextElement() + "\\r" );
        }
     
        @ModelAttribute ( "user2" )
        public User getUser() {
           System. out .println( "---------getUser-------------" );
           return new User(3, "user2" );
        }
    }

当我们请求 /myTest/sayHello.do 的时候使用 @ModelAttribute 标记的方法会先执行，然后把它们返回的对象存放到模型中。最终访问到 sayHello 方法的时候，使用 @ModelAttribute 标记的方法参数都能被正确的注入值。执行结果如下图所示：

> Hello world , Hello user210

由执行结果我们可以看出来，此时 session 中没有包含任何属性，也就是说上面的那些对象都是存放在模型属性中，而不是存放在 session 属性中。那要如何才能存放在 session 属性中呢？这个时候我们先引入一个新的概念 @SessionAttributes ，它的用法会在讲完 @ModelAttribute 之后介绍，这里我们就先拿来用一下。我们在 MyController 类上加上 @SessionAttributes 属性标记哪些是需要存放到 session 中的。看下面的代码：

    @Controller
    @RequestMapping ( "/myTest" )
    @SessionAttributes (value={ "intValue" , "stringValue" }, types={User. class })
    public class MyController {
     
        @ModelAttribute ( "hello" )
        public String getModel() {
           System. out .println( "-------------Hello---------" );
           return "world" ;
        }
     
        @ModelAttribute ( "intValue" )
        public int getInteger() {
           System. out .println( "-------------intValue---------------" );
           return 10;
        }
     
        @RequestMapping ( "sayHello" )
        public void sayHello(Map<String, Object> map, @ModelAttribute ( "hello" ) String hello, @ModelAttribute ( "intValue" ) int num, @ModelAttribute ( "user2" ) User user, Writer writer, HttpServletRequest request) throws IOException {
           map.put( "stringValue" , "String" );
           writer.write( "Hello " + hello + " , Hello " + user.getUsername() + num);
           writer.write( "\\r" );
           HttpSession session \= request.getSession();
           Enumeration enume \= session.getAttributeNames();
           while (enume.hasMoreElements())
               writer.write(enume.nextElement() + "\\r" );
           System. out .println(session);
        }
     
        @ModelAttribute ( "user2" )
        public User getUser() {
           System. out .println( "---------getUser-------------" );
           return new User(3, "user2" );
        }
    }

在上面代码中我们指定了属性为 intValue 或 stringValue 或者类型为 User 的都会放到 Session 中，利用上面的代码当我们访问 /myTest/sayHello.do 的时候，结果如下：

> Hello world , Hello user210

仍然没有打印出任何 session 属性，这是怎么回事呢？怎么定义了把模型中属性名为 intValue 的对象和类型为 User 的对象存到 session 中，而实际上没有加进去呢？难道我们错啦？我们当然没有错，只是在第一次访问 /myTest/sayHello.do 的时候 @SessionAttributes 定义了需要存放到 session 中的属性，而且这个模型中也有对应的属性，但是这个时候还没有加到 session 中，所以 session 中不会有任何属性，等处理器方法执行完成后 Spring 才会把模型中对应的属性添加到 session 中。所以当请求第二次的时候就会出现如下结果：

> Hello world , Hello user210

user2  
intValue  
stringValue  
当 @ModelAttribute 标记在处理器方法参数上的时候，表示该参数的值将从模型或者 Session 中取对应名称的属性值，该名称可以通过 @ModelAttribute(“attributeName”) 来指定，若未指定，则使用参数类型的类名称（首字母小写）作为属性名称。

    @Controller
    @RequestMapping ( "/myTest" )
    public class MyController {
     
        @ModelAttribute ( "hello" )
        public String getModel() {
           return "world" ;
        }
     
        @RequestMapping ( "sayHello" )
        public void sayHello( @ModelAttribute ( "hello" ) String hello, Writer writer) throws IOException {
           writer.write( "Hello " + hello);
        }  
    }

在上面代码中，当我们请求 / myTest/sayHello.do 的时候，由于 MyController 中的方法 getModel 使用了注解 @ModelAttribute 进行标记，所以在执行请求方法 sayHello 之前会先执行 getModel 方法，这个时候 getModel 方法返回一个字符串 world 并把它以属性名 hello 保存在模型中，接下来访问请求方法 sayHello 的时候，该方法的 hello 参数使用 @ModelAttribute(“hello”) 进行标记，这意味着将从 session 或者模型中取属性名称为 hello 的属性值赋给 hello 参数，所以这里 hello 参数将被赋予值 world ，所以请求完成后将会在页面上看到 Hello world 字符串。  
@SessionAttributes 用于标记需要在 Session 中使用到的数据，包括从 Session 中取数据和存数据。@SessionAttributes 一般是标记在 Controller 类上的，可以通过名称、类型或者名称加类型的形式来指定哪些属性是需要存放在 session 中的。

    @Controller
    @RequestMapping ( "/myTest" )
    @SessionAttributes (value={ "user1" , "blog1" }, types={User. class , Blog. class })
    public class MyController {
     
        @RequestMapping ( "setSessionAttribute" )
        public void setSessionAttribute(Map<String, Object> map, Writer writer) throws IOException {
           User user \= new User(1, "user" );
           User user1 \= new User(2, "user1" );
           Blog blog \= new Blog(1, "blog" );
           Blog blog1 \= new Blog(2, "blog1" );
           map.put( "user" , user);
           map.put( "user1" , user1);
           map.put( "blog" , blog);
           map.put( "blog1" , blog1);
           writer.write( "over." );
        }
     
     
     
        @RequestMapping ( "useSessionAttribute" )
        public void useSessionAttribute(Writer writer, @ModelAttribute ( "user1" ) User user1, @ModelAttribute ( "blog1" ) Blog blog1) throws IOException {
           writer.write(user1.getId() + "--------" + user1.getUsername());
           writer.write( "\\r" );
           writer.write(blog1.getId() + "--------" + blog1.getTitle());
        }
     
        @RequestMapping ( "useSessionAttribute2" )
        public void useSessionAttribute(Writer writer, @ModelAttribute ( "user1" ) User user1, @ModelAttribute ( "blog1" ) Blog blog1, @ModelAttribute User user, HttpSession session) throws IOException {
           writer.write(user1.getId() + "--------" + user1.getUsername());
           writer.write( "\\r" );
           writer.write(blog1.getId() + "--------" + blog1.getTitle());
           writer.write( "\\r" );
           writer.write(user.getId() + "---------" + user.getUsername());
           writer.write( "\\r" );
           Enumeration enume \= session.getAttributeNames();
           while (enume.hasMoreElements())
               writer.write(enume.nextElement() + " \\r" );
        }
     
        @RequestMapping ( "useSessionAttribute3" )
        public void useSessionAttribute( @ModelAttribute ( "user2" ) User user) {
     
        }
    }

在上面代码中我们可以看到在 MyController 上面使用了 @SessionAttributes 标记了需要使用到的 Session 属性。可以通过名称和类型指定需要存放到 Session 中的属性，对应 @SessionAttributes 注解的 value 和 types 属性。当使用的是 types 属性的时候，那么使用的 Session 属性名称将会是对应类型的名称（首字母小写）。当 value 和 types 两个属性都使用到了的时候，这时候取的是它们的并集，而不是交集，所以上面代码中指定要存放在 Session 中的属性有名称为 user1 或 blog1 的对象，或类型为 User 或 Blog 的对象。在上面代码中我们首先访问 / myTest/setSessionAttribute.do ，该请求将会请求到 MyController 的 setSessionAttribute 方法，在该方法中，我们往模型里面添加了 user 、user1 、blog 和 blog1 四个属性，因为它们或跟类上的 @SessionAttributes 定义的需要存到 session 中的属性名称相同或类型相同，所以在请求完成后这四个属性都将添加到 session 属性中。接下来访问 / myTest/useSessionAttribute.do ，该请求将会请求 MyController 的 useSessionAttribute(Writer writer, @ModelAttribute(“user1”) User user1, @ModelAttribute(“blog1”) Blog blog) 方法，该方法参数中用 @ModelAttribute 指定了参数 user1 和参数 blog1 是需要从 session 或模型中绑定的，恰好这个时候 session 中已经有了这两个属性，所以这个时候在方法执行之前会先绑定这两个参数。执行结果如下图所示：

> 2---user1  
> 2---blog1

接下来访问 / myTest/useSessionAttribute2.do，这个时候请求的是上面代码中对应的第二个 useSessionAttribute 方法，方法参数 user 、user1 和 blog1

> 用 @ModelAttribute 声明了需要 session  
> 或模型属性注入，我们知道在请求 / myTest/setSessionAttribute.do 的时候这些属性都已经添加到了 session  
> 中，所以该请求的结果会如下图所示：  
> 2---user1 2---blog1 1---user blog user user1 blog1  
> 接下来访问 / myTest/useSessionAttribute3.do  
> ，这个时候请求的是上面代码中对应的第三个 useSessionAttribute 方法，我们可以看到该方法的方法参数 user  
> 使用了 @ModelAttribute(“user2”) 进行标记，表示 user 需要 session 中的 user2  
> 属性来注入，但是这个时候我们知道 session 中是不存在 user2 属性的，所以这个时候就会报错了。执行结果如图所示：  
> ![](https://segmentfault.com/img/bVxXnG)

### （八）定制自己的类型转换器

在通过处理器方法参数接收 request 请求参数绑定数据的时候，对于一些简单的数据类型 Spring 会帮我们自动进行类型转换，而对于一些复杂的类型由于 Spring 没法识别，所以也就不能帮助我们进行自动转换了，这个时候如果我们需要 Spring 来帮我们自动转换的话就需要我们给 Spring 注册一个对特定类型的识别转换器。 Spring 允许我们提供两种类型的识别转换器，一种是注册在 Controller 中的，一种是注册在 SpringMVC 的配置文件中。聪明的读者看到这里应该可以想到它们的区别了，定义在 Controller 中的是局部的，只在当前 Controller 中有效，而放在 SpringMVC 配置文件中的是全局的，所有 Controller 都可以拿来使用。

#### 1. 在 @InitBinder 标记的方法中定义局部的类型转换器

         我们可以使用 @InitBinder 注解标注在 Controller 方法上，然后在方法体里面注册数据绑定的转换器，这主要是通过 WebDataBinder 进行的。我们可以给需要注册数据绑定的转换器的方法一个 WebDataBinder 参数，然后给该方法加上 @InitBinder 注解，这样当该 Controller 中在处理请求方法时如果发现有不能解析的对象的时候，就会看该类中是否有使用 @InitBinder 标记的方法，如果有就会执行该方法，然后看里面定义的类型转换器是否与当前需要的类型匹配。

    @Controller
    @RequestMapping ( "/myTest" )
    public class MyController {
     
        @InitBinder
        public void dataBinder(WebDataBinder binder) {
           DateFormat dateFormat \= new SimpleDateFormat( "yyyyMMdd" );
           PropertyEditor propertyEditor \= new CustomDateEditor(dateFormat, true ); 
           binder.registerCustomEditor(Date. class , propertyEditor);
        }
     
        @RequestMapping ( "dataBinder/{date}" )
        public void testDate( @PathVariable Date date, Writer writer) throws IOException {
           writer.write(String.valueOf (date.getTime()));
        }
     
    }

在上面的代码中当我们请求 /myTest/dataBinder/20121212.do 的时候， Spring 就会利用 @InitBinder 标记的方法里面定义的类型转换器把字符串 20121212 转换为一个 Date 对象。这样定义的类型转换器是局部的类型转换器，一旦出了这个 Controller 就不会再起作用。类型转换器是通过 WebDataBinder 对象的 registerCustomEditor 方法来注册的，要实现自己的类型转换器就要实现自己的 PropertyEditor 对象。 Spring 已经给我们提供了一些常用的属性编辑器，如 CustomDateEditor 、 CustomBooleanEditor 等。

       PropertyEditor 是一个接口，要实现自己的 PropertyEditor 类我们可以实现这个接口，然后实现里面的方法。但是 PropertyEditor 里面定义的方法太多了，这样做比较麻烦。在 java 中有一个封装类是实现了 PropertyEditor 接口的，它是 PropertyEditorSupport 类。所以如果需要实现自己的 PropertyEditor 的时候只需要继承 PropertyEditorSupport 类，然后重写其中的一些方法。一般就是重写 setAsText 和 getAsText 方法就可以了， setAsText 方法是用于把字符串类型的值转换为对应的对象的，而 getAsText 方法是用于把对象当做字符串来返回的。在 setAsText 中我们一般先把字符串类型的对象转为特定的对象，然后利用 PropertyEditor 的 setValue 方法设定转换后的值。在 getAsText 方法中一般先使用 getValue 方法取代当前的对象，然后把它转换为字符串后再返回给 getAsText 方法。下面是一个示例：

        @InitBinder
        public void dataBinder(WebDataBinder binder) {
           
           PropertyEditor userEditor \= new PropertyEditorSupport() {
     
               @Override
               public String getAsText() {
                  
                  User user \= (User) getValue();
                  return user.getUsername();
               }
     
               @Override
               public void setAsText(String userStr) throws IllegalArgumentException {
                  
                  User user \= new User(1, userStr);
                  setValue(user);
               }
           };
           
           binder.registerCustomEditor(User. class , userEditor);
        }

#### 2. 实现 WebBindingInitializer 接口定义全局的类型转换器

       如果需要定义全局的类型转换器就需要实现自己的 WebBindingInitializer 对象，然后把该对象注入到 AnnotationMethodHandlerAdapter 中，这样 Spring 在遇到自己不能解析的对象的时候就会到全局的 WebBindingInitializer 的 initBinder 方法中去找，每次遇到不认识的对象时， initBinder 方法都会被执行一遍。

    public class MyWebBindingInitializer implements WebBindingInitializer {
     
        @Override
        public void initBinder(WebDataBinder binder, WebRequest request) {
           
           DateFormat dateFormat \= new SimpleDateFormat( "yyyyMMdd" );
           PropertyEditor propertyEditor \= new CustomDateEditor(dateFormat, true );
           binder.registerCustomEditor(Date. class , propertyEditor);
        }
     
    }

定义了这么一个 WebBindingInitializer 对象之后 Spring 还是不能识别其中指定的对象，这是因为我们只是定义了 WebBindingInitializer 对象，还没有把它交给 Spring ， Spring 不知道该去哪里找解析器。要让 Spring 能够识别还需要我们在 SpringMVC 的配置文件中定义一个 AnnotationMethodHandlerAdapter 类型的 bean 对象，然后利用自己定义的 WebBindingInitializer 覆盖它的默认属性 webBindingInitializer 。

        < bean class \= "org.springframework.web.servlet.mvc.annotation.AnnotationMethodHandlerAdapter" >
           < property name \= "webBindingInitializer" >
               < bean class \= "com.host.app.web.util.MyWebBindingInitializer" />
           </ property >
        </ bean >

#### 3. 触发数据绑定方法的时间

当 Controller 处理器方法参数使用 @RequestParam、@PathVariable、@RequestHeader、@CookieValue 和 @ModelAttribute 标记的时候都会触发 initBinder 方法的执行，这包括使用 WebBindingInitializer 定义的全局方法和在 Controller 中使用 @InitBinder 标记的局部方法。而且每个使用了这几个注解标记的参数都会触发一次 initBinder 方法的执行，这也意味着有几个参数使用了上述注解就会触发几次 initBinder 方法的执行。

### （八）使用 @Value 用法

Spring 通过注解获取\*.porperties 文件的内容，除了 xml 配置外，还可以通过 @value 方式来获取。  
使用方式必须在当前类使用 @Component 或者 @Controller，xml 文件内配置的是通过 pakage 扫描方式例如：&lt;context:component-scan base-package="pakage_name" />  
![](https://segmentfault.com/img/bVxXnv)

**更多内容可以关注微信公众号，或者访问[AppZone](https://link.segmentfault.com/?url=http%3A%2F%2Fzoeminghong.github.io%2F)网站**

![](http://7xp64w.com1.z0.glb.clouddn.com/qrcode_for_gh_3e33976a25c9_258.jpg) 
 [https://segmentfault.com/a/1190000005670764](https://segmentfault.com/a/1190000005670764)
