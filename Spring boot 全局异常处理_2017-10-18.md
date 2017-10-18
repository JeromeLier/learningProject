### Spring boot 全局异常处理

Spring 统一异常处理有 3 种方式，分别为：

1. 使用 @ ExceptionHandler 注解
2. 实现 HandlerExceptionResolver 接口
3. 使用 @controlleradvice 注解



#### 方法一 @ ExceptionHandler 注解

使用该注解有一个不好的地方就是：**_进行异常处理的方法必须与出错的方法在同一个Controller里面。**_ 使用如下：



```java
@Controller      
public class GlobalController {               

   /**    
     * 用于处理异常的    
     * @return    
     */      
    @ExceptionHandler({MyException.class})       
    public String exception(MyException e) {       
        System.out.println(e.getMessage());       
        e.printStackTrace();       
        return "exception";       
    }       

    @RequestMapping("test")       
    public void test() {       
        throw new MyException("出错了！");       
    }                    
}    
```

**可以看到，这种方式最大的缺陷就是不能全局控制异常。**_每个类都要写一遍。



#### HandlerExceptionResolver  注解

 这种方式可以实现**全局的异常控制**

```java
@Component
public class ControllerExceptionFilter implements HandlerExceptionResolver{

    private static final Logger LOGGER = LoggerFactory.getLogger(ControllerExceptionFilter.class);

    @Override
    public ModelAndView resolveException(HttpServletRequest request, HttpServletResponse response,
            Object handler, Exception ex) {
        try {
            if (ex instanceof ClientAbortException) { // 用户在浏览器取消的异常 也同样可以捕获
                LOGGER.error("用户浏览器请求取消", ex.getMessage());
            } else {
                LOGGER.error("请求服务异常request Exception", ex);
            }
            return null;
        }catch (Exception e) {
            LOGGER.error("过滤filter 异常,", e);
        }
        return null;
    }
}
```



####  @controlleradvice 注解

上文说到 @ ExceptionHandler 需要 **_进行异常处理的方法必须与出错的方法在同一个Controller里面。**_ 那么当代码加入了 @controlleradvice，则不需要必须在同一个 controller 中了。这也是 Spring 3.2 带来的新特性。 也就是说，@controlleradvice + @ ExceptionHandler 也可以实现全局的异常捕捉。

那么，在实际中，就可以使用 @controlleradvice + @ ExceptionHandler，继承 ResponseEntityExceptionHandler 类来实现针对 Rest 接口 的全局异常捕获，并且可以返回自定义格式：



我的项目本身就是这么用的：

```java
@ResponseBody
@SuppressWarnings("all")
@ControllerAdvice
public class ControllerExceptionHandle {

    private static final Logger LOGGER = LoggerFactory.getLogger(ControllerExceptionHandle.class);

    @ExceptionHandler
    private  <T> ApiResponse<T>  exceptionHandle(Exception e) {
        LOGGER.error("请求controller 发生异常", e);
        if (e.getMessage().equals(ResponseCodeEnum.AUTH_FAIL.getMessage())) {
            return ApiResponse.fail(ResponseCodeEnum.AUTH_FAIL, "请到登录页登录或者通过管理员查看自己的权限");
        }
        if (e instanceof NullPointerException) {
            return ApiResponse.fail(ResponseCodeEnum.SYSTEM_ERROR, e.getMessage());
        }
        if (e instanceof MissingServletRequestParameterException) {
            return ApiResponse.fail(ResponseCodeEnum.PARAM_ERROR, e.getMessage());
        }
        return ApiResponse.fail(ResponseCodeEnum.SYSTEM_ERROR);
    }
}
```





参考链接： https://github.com/pzxwhc/MineKnowContainer/issues/29

http://www.baeldung.com/exception-handling-for-rest-with-spring